# 如何工作

本文档的目的是解释nginx Ingress控制器的工作原理，特别是NGINX模型的构建方式，以及为什么我们需要一个模板。

## nginx 配置

此Ingress控制器的目标是生成配置文件(nginx.conf)，此要求的主要目的是配置文件内容进行任何更改后重新加载NGINX，
需要特别注意的是，我们不会在仅上游配置发生更改时重新加载Nginx(i.e Endpoints再应用发布时发生变化)
我们使用[lua-nginx-module](https://github.com/openresty/lua-nginx-module)实现这一目标。
请查看[以下内容](https://kubernetes.github.io/ingress-nginx/how-it-works/#avoiding-reloads-on-endpoints-changes)，以了解有功能实现细节

## nginx 模型

通常，Kubernetes控制器利用[同步循环模式](https://coreos.com/kubernetes/docs/latest/replication-controller.html#the-reconciliation-loop-in-detail)来检查控制器中所需的状态是否已更新或需要更改,
为此，我们需要使用来自集群的不同对象来构建模型,特别是(无指定顺序)Ingresses，Services，Endpoints，Secrets，Configmap，
以生成反映当前群集状态的配置文件

为了从集群中获取该对象, 我们使用 [Kubernetes Informers][2], 特别是, `FilteredSharedInformer`. 当添加，修改或删除新对象时，此通知程序允许使用[回调][3] 对单个更改进行响应。不幸的是，没有办法知道特定更改是否会影响最终配置文件。因此，每次更改时，我们都必须根据集群的状态从头开始重建一个新模型，并将其与当前模型进行比较。如果新模型等于当前模型，那么我们避免生成新的NGINX配置并触发重新加载。否则，我们检查差异是否仅与端点有关。如果是这样，我们然后使用HTTP POST请求将新的端点列表发送到在Nginx内运行的Lua处理程序，并再避免重新生成新的NGINX配置的条件下触发重新加载。如果运行模型和新模型之间的差异不只是端点，我们将基于新模型创建新的NGINX配置，替换当前模型并触发重新加载。

该模型的用途之一是在状态没有变化时避免不必要的重新加载，并检测定义中的冲突。

NGINX配置的最终表示是从 [Go template][6] 生成的，使用新模型作为模板所需变量的输入。

## 构建NGINX模型

建立模型是一项昂贵的操作，因此，必须使用同步循环。 通过使用 [工作队列][4] 可能不会丢失更改并删除[sync.Mutex][5]来强制执行一次同步循环 ， 此外，还可以在同步循环的开始和结束之间创建一个时间窗口从而允许我们丢弃不必要的更新。需要重点了解的是，集群中的任何更改都可能生成events，informer将发送给控制器的事件，以及[工作排队][4]的原因之一.

建立模型的操作:

- 按 `CreationTimestamp` 字段对入口规则进行排序，即旧规则优先.

    - 如果在多个Ingress中为同一主机定义了相同路径，则最早的规则将获胜
    - 如果多个Ingress包含同一主机的TLS部分，则最早的规则将获胜。
    - 如果多个Ingress定义了一个影响Server块配置的注释，则最早的规则将获胜

  - 创建NGINX服务器列表(按主机名)
  - 如果多个入口为同一主机定义了不同的路径，则  ingress controller将合并这些定义。
  - 注释将应用于Ingress中的所有路径
  - 多个Ingress可以定义不同的注释。这些定义在Ingress之间不共享

## 需要重新加载时

下面的列表描述了需要重新加载的情况:

 - 创建了新的Ingress资源
 - TLS选项添加到现有Ingress
 - Ingress注解的更改比上游配置更改影响更大。例如，`load-balance`注解不需要重新加载
 - 从Ingress添加/删除路径
 - Ingress, Service, Secret 被删除
 - Ingress缺少的引用对象变为可用状态，例如Service或Secret
 - Secret被更新

## 避免重载

 在某些情况下，可以避免重新加载，尤其是在端点发生更改时，例如 pod启动或replace
 完全删除重新加载超出了Ingress控制器的范围.
 这将需要大量工作，并且在某些时候没有任何意义.
 仅当NGINX更改了读取新配置的方式时，这才可以更改，通常，新的更改不会替代工作进程.

### 避免在端点更改上重新加载

 在每个endpoint更改上，控制器从其watch的所有服务中获取endpoints并生成相应的Backend对象。
 然后将这些对象发送到在Nginx内部运行的Lua处理程序。
 Lua代码又将这些后端存储在共享内存区域中。
 然后，对于在[`balancer_by_lua`](https://github.com/openresty/lua-resty-core/blob/master/lib/ngx/balancer.md) 上下文中运行的每个请求，Lua代码将检测到应该从哪个端点选择上游节点，并应用配置的负载均衡算法来选择节点然后Nginx负责其余的工作。这样，我们避免在端点更改时重新加载Nginx。请注意，这包括注释更改，这些更改也仅影响Nginx中的`upstream`配置。.

 在具有频繁部署应用程序的相对较大的集群中，此功能节省了大量Nginx重载，否则可能会影响响应延迟、
 负载平衡质量(每次重新加载后，Nginx都会重置负载均衡状态)等

### 避免因错误的配置而中断

由于ingress controller使用 [synchronization loop pattern](https://coreos.com/kubernetes/docs/latest/replication-controller.html#the-reconciliation-loop-in-detail)模式工作，因此它将配置用于所有匹配的对象 如果某些Ingress对象的配置损坏，例如`nginx.ingress.kubernetes.io/configuration-snippet`注解中的语法错误，生成的配置无效，不会重新加载，因此不会有更多的ingresses被考虑在内

 为了防止这种情况的发生，nginx ingress controller可以选择发布一个  [validating admission webhook server][8] 以确保传入的入口对象的有效性该webhook将传入的入口对象追加到入口列表中，生成配置并调用nginx以确保配置没有语法错误

[0]: https://github.com/openresty/lua-nginx-module/pull/1259
[1]: https://coreos.com/kubernetes/docs/latest/replication-controller.html#the-reconciliation-loop-in-detail
[2]: https://godoc.org/k8s.io/client-go/informers#NewFilteredSharedInformerFactory
[3]: https://godoc.org/k8s.io/client-go/tools/cache#ResourceEventHandlerFuncs
[4]: https://github.com/kubernetes/ingress-nginx/blob/master/internal/task/queue.go#L38
[5]: https://golang.org/pkg/sync/#Mutex
[6]: https://github.com/kubernetes/ingress-nginx/blob/master/rootfs/etc/nginx/template/nginx.tmpl
[7]: http://nginx.org/en/docs/beginners_guide.html#control
[8]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/#validatingadmissionwebhook
