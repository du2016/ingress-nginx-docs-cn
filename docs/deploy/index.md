# 安装指南

## 内容

- [先决条件通用部署命令](#先决条件通用部署命令)
  - [不同provider特定步骤](#不同provider特定步骤)
    - [Docker for Mac](#docker-for-mac)
    - [minikube](#minikube)
    - [AWS](#aws)
    - [GCE - GKE](#gce-gke)
    - [Azure](#azure)
    - [Bare-metal](#bare-metal)
  - [Verify installation](#verify-installation)
  - [Detect installed version](#detect-installed-version)
- [Using Helm](#using-helm)

## 先决条件通用部署命令

!!! 注意
    默认配置从所有名称空间监视Ingress对象。要更改此行为，请使用标志`--watch-namespace`将范围限制为特定的名称空间。

!!! warning
    如果多个入口为同一主机定义了不同的路径，则  ingress controller将合并这些定义。

!!! 注意
    如果您使用的是GKE，则需要使用以下命令将用户初始化为集群管理员:
    ```console
    kubectl create clusterrolebinding cluster-admin-binding \
      --clusterrole cluster-admin \
      --user $(gcloud config get-value account)
    ```

所有部署都需要以下 **强制性命令**.

```console
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/mandatory.yaml
```

!!! 提醒
    你过你在使用1.14之前的版本, 你需要更改 `kubernetes.io/os` 为 `beta.kubernetes.io/os` 在217行 [mandatory.yaml](https://github.com/kubernetes/ingress-nginx/blob/master/deploy/static/mandatory.yaml#L217), see [Labels details](https://kubernetes.io/docs/reference/kubernetes-api/labels-annotations-taints/).

### 不同provider特定步骤

There are cloud provider specific yaml files.

#### Docker for Mac

Kubernetes在Mac的Docker中可用 (从 [18.06.0-ce版本](https://docs.docker.com/docker-for-mac/release-notes/#stable-releases-of-2018))

[enable]: https://docs.docker.com/docker-for-mac/#kubernetes

创建服务

```console
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/provider/cloud-generic.yaml
```

#### minikube

对于标准用法:

```console
minikube addons enable ingress
```

对于开发:

1. 禁用ingress插件:

```console
minikube addons disable ingress
```

2. 执行 `make dev-env`
3. 确认`nginx-ingress-controller`deployment 存在

```console
$ kubectl get pods -n ingress-nginx
NAME                                       READY     STATUS    RESTARTS   AGE
default-http-backend-66b447d9cf-rrlf9      1/1       Running   0          12s
nginx-ingress-controller-fdcdcd6dd-vvpgs   1/1       Running   0          11s
```

#### AWS

在AWS中，我们使用Elastic Load Balancer(ELB)将nginx Ingress控制器暴露在Type = LoadBalancer标签服务的后面，
从Kubernetes v1.9.0开始，可以使用经典负载均衡器(ELB)或网络负载均衡器(NLB)请检查 [AWS弹性负载均衡页面](https://aws.amazon.com/elasticloadbalancing/details/)

##### Elastic Load Balancer - ELB

此设置要求选择我们要配置ELB的层(L4或L7)

- [Layer 4](https://en.wikipedia.org/wiki/OSI_model#Layer_4:_Transport_Layer): 使用TCP作为端口80和443的侦听器协议
- [Layer 7](https://en.wikipedia.org/wiki/OSI_model#Layer_7:_Application_Layer): 使用HTTP作为端口80的侦听器协议并在ELB中终止TLS

对于4层:

检查是否有必要更改ELB空闲超时时间.
在某些情况下, 用户可能想要修改ELB空闲超时时间，
因此，请查看[ELB空闲超时](https://kubernetes.github.io/ingress-nginx/deploy/#elb-idle-timeouts)部分以获取更多信息
如果需要更改，用户将需要更新`provider/aws/service-l4.yaml`中的`service.beta.kubernetes.io/aws-load-balancer-connection-idle-timeout`

然后执行:

```console
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/provider/aws/service-l4.yaml
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/provider/aws/patch-configmap-l4.yaml
```

对于L7:

修改`provider/aws/service-l7.yaml`内容使用用有效的ID替换虚拟ID`"arn:aws:acm:us-west-2:XXXXXXXX:certificate/XXXXXX-XXXXXXX-XXXXXXX-XXXXXXXX"`

检查是否不需要更改ELB空闲超时时间，在某些情况下，用户可能想要修改ELB空闲超时时间，因此，请查看[ELB空闲超时](https://kubernetes.github.io/ingress-nginx/deploy/#elb-idle-timeouts)部分以获取更多信息
如果需要更改，用户将需要更新`provider/aws/service-l7.yaml`中的`service.beta.kubernetes.io/aws-load-balancer-connection-idle-timeout`

然后执行:

```console
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/provider/aws/service-l7.yaml
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/provider/aws/patch-configmap-l7.yaml
```

本示例创建一个只有两个侦听器的ELB，一个在端口80中，另一个在端口443中。

![Listeners](../images/elb-l7-listener.png)

##### ELB空闲超时时间
在某些情况下，用户将需要修改ELB空闲超时的值。
用户需要确保空闲超时小于为NGINX配置的keepalive_timeout。默认情况下，nginx keepalive_timeout设置为75s。

除非已修改nginx keepalive_timeout，否则默认的ELB空闲超时将适用于大多数情况。
在这种情况下，将需要修改service.beta.kubernetes.io/aws-load-balancer-connection-idle-timeout，以确保它小于用户配置的keepalive_timeout

_请注意: 使用WebSocket时，建议空闲超时为`3600s`._

有关您的负载均衡器的空闲超时的更多信息，可以在官方的AWS文档中找到。

##### 网络负载平衡器 (NLB)

从v1.10.0开始，此类型的负载均衡器已被支持为ALPHA功能

```console
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/provider/aws/service-nlb.yaml
```

#### GCE-GKE

```console
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/provider/cloud-generic.yaml
```

**重要说明:** GCE/GKE不支持代理协议

#### Azure

```console
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/provider/cloud-generic.yaml
```

#### Bare-metal

使用 [NodePort](https://kubernetes.io/docs/concepts/services-networking/service/#type-nodeport):

```console
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/provider/baremetal/service-nodeport.yaml
```

!!! 小提示
    有关在裸机上部署的扩展说明，请参见 [裸机注意事项](./baremetal.md).

### Verify installation

要检查  ingress controller容器是否已启动，请运行以下命令

```console
kubectl get pods --all-namespaces -l app.kubernetes.io/name=ingress-nginx --watch
```

操作的pod运行后，可以通过按Ctrl + C取消上述命令。现在，您准备创建第一个ingress

### Detect installed version

要检查正在运行哪个版本的ingress controller，请在pod中执行并运行nginx-ingress-controller version命令

```console
POD_NAMESPACE=ingress-nginx
POD_NAME=$(kubectl get pods -n $POD_NAMESPACE -l app.kubernetes.io/name=ingress-nginx -o jsonpath='{.items[0].metadata.name}')

kubectl exec -it $POD_NAME -n $POD_NAMESPACE -- /nginx-ingress-controller --version
```

## Using Helm

nginx Ingress controller 可以通过 [Helm](https://helm.sh/) 使用官方 [stable/nginx-ingress](https://github.com/kubernetes/charts/tree/master/stable/nginx-ingress) 进行安装.
以发行版名称`my-nginx`安装图表

```console
helm install stable/nginx-ingress --name my-nginx
```

如果kubernetes集群启用了RBAC，则运行

```console
helm install stable/nginx-ingress --name my-nginx --set rbac.create=true
```

检查安装的版本:

```console
POD_NAME=$(kubectl get pods -l app.kubernetes.io/name=ingress-nginx -o jsonpath='{.items[0].metadata.name}')
kubectl exec -it $POD_NAME -- /nginx-ingress-controller --version
```
