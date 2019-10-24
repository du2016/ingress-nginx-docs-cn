<!--
-----------------NOTICE------------------------
This file is referenced in code as
https://github.com/kubernetes/ingress-nginx/blob/master/docs/troubleshooting.md
Do not move it without providing redirects.
-----------------------------------------------
-->

# 排错

## Ingress-Controller日志和事件

有很多方法可以对  ingress controller进行故障排除。以下是获取更多信息的基本故障排除方法。

检查入口资源事件:

```console
$ kubectl get ing -n <namespace-of-ingress-resource>
NAME           HOSTS      ADDRESS     PORTS     AGE
cafe-ingress   cafe.com   10.0.2.15   80        25s

$ kubectl describe ing <ingress-resource-name> -n <namespace-of-ingress-resource>
Name:             cafe-ingress
Namespace:        default
Address:          10.0.2.15
Default backend:  default-http-backend:80 (172.17.0.5:8080)
Rules:
  Host      Path  Backends
  ----      ----  --------
  cafe.com
            /tea      tea-svc:80 (<none>)
            /coffee   coffee-svc:80 (<none>)
Annotations:
  kubectl.kubernetes.io/last-applied-configuration:  {"apiVersion":"extensions/v1beta1","kind":"Ingress","metadata":{"annotations":{},"name":"cafe-ingress","namespace":"default","selfLink":"/apis/networking/v1beta1/namespaces/default/ingresses/cafe-ingress"},"spec":{"rules":[{"host":"cafe.com","http":{"paths":[{"backend":{"serviceName":"tea-svc","servicePort":80},"path":"/tea"},{"backend":{"serviceName":"coffee-svc","servicePort":80},"path":"/coffee"}]}}]},"status":{"loadBalancer":{"ingress":[{"ip":"169.48.142.110"}]}}}

Events:
  Type    Reason  Age   From                      Message
  ----    ------  ----  ----                      -------
  Normal  CREATE  1m    nginx-ingress-controller  Ingress default/cafe-ingress
  Normal  UPDATE  58s   nginx-ingress-controller  Ingress default/cafe-ingress
```

检查  ingress controller日志:

```console
$ kubectl get pods -n <namespace-of-ingress-controller>
NAME                                        READY     STATUS    RESTARTS   AGE
nginx-ingress-controller-67956bf89d-fv58j   1/1       Running   0          1m

$ kubectl logs -n <namespace> nginx-ingress-controller-67956bf89d-fv58j
-------------------------------------------------------------------------------
nginx Ingress controller
  Release:    0.14.0
  Build:      git-734361d
  Repository: https://github.com/kubernetes/ingress-nginx
-------------------------------------------------------------------------------
....
```

检查Nginx配置:
 
```console
$ kubectl get pods -n <namespace-of-ingress-controller>
NAME                                        READY     STATUS    RESTARTS   AGE
nginx-ingress-controller-67956bf89d-fv58j   1/1       Running   0          1m

$ kubectl exec -it -n <namespace-of-ingress-controller> nginx-ingress-controller-67956bf89d-fv58j cat /etc/nginx/nginx.conf
daemon off;
worker_processes 2;
pid /run/nginx.pid;
worker_rlimit_nofile 523264;
worker_shutdown_timeout 240s;
events {
	multi_accept        on;
	worker_connections  16384;
	use                 epoll;
}
http {
....
```

检查使用的服务是否存在

```console
$ kubectl get svc --all-namespaces
NAMESPACE     NAME                   TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)         AGE
default       coffee-svc             ClusterIP   10.106.154.35    <none>        80/TCP          18m
default       kubernetes             ClusterIP   10.96.0.1        <none>        443/TCP         30m
default       tea-svc                ClusterIP   10.104.172.12    <none>        80/TCP          18m
kube-system   default-http-backend   NodePort    10.108.189.236   <none>        80:30001/TCP    30m
kube-system   kube-dns               ClusterIP   10.96.0.10       <none>        53/UDP,53/TCP   30m
kube-system   kubernetes-dashboard   NodePort    10.103.128.17    <none>        80:30000/TCP    30m
```

## 调试日志

使用标志 `--v=XX` 可以增加日志记录级别。这是通过编辑deployment来执行的。


```console
$ kubectl get deploy -n <namespace-of-ingress-controller>
NAME                       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
default-http-backend       1         1         1            1           35m
nginx-ingress-controller   1         1         1            1           35m

$ kubectl edit deploy -n <namespace-of-ingress-controller> nginx-ingress-controller
# Add --v=X to "- args", where X is an integer
```

- `--v=2` 显示使用`diff`有关nginx中配置更改的详细信息
- `--v=3` 显示有关服务，入口规则，端点更改的详细信息，并以JSON格式转储nginx配置
- `--v=5` 在[调试模式](http://nginx.org/en/docs/debugging_log.html)配置NGINX

## 对Kubernetes API服务器的身份验证

身份验证过程涉及许多组件，第一步是缩小问题的根源，即是服务身份验证问题还是kubeconfig文件问题。

两种认证都必须有效:

```
+-------------+   service          +------------+
|             |   authentication   |            |
+  apiserver  +<-------------------+  ingress   |
|             |                    | controller |
+-------------+                    +------------+
```

**服务认证n**

Ingress控制器需要来自apiserver的信息。因此，需要身份验证，可以通过两种不同的方式来实现:

1. _Service Account:_ 建议这样做，因为无需进行任何配置。  ingress controller将使用系统提供的信息与API服务器进行通信。有关详情，请参见"服务帐户"部分

2. _Kubeconfig file:_ 在某些Kubernetes环境中，服务帐户不可用. 
   在这种情况下，需要手动配置. 可以在启动Ingress控制器二进制文件时使用 `--kubeconfig` 参数.
   标志的值是文件的路径，该文件指定如何连接到API服务器. 使用 `--kubeconfig` 不需要 `--apiserver-host`参数.
   该文件的格式与`~/.kube/config` 相同，kubectl用于连接到API服务器。有关详细信息，请参见'kubeconfig'部分

3. _Using the flag `--apiserver-host`:_ 使用参数 `--apiserver-host=http://localhost:8080` 可以使用kubectl代理指定不安全的API服务器或访问远程kubernetes集群。请不要在生产中使用这种方法

在下图中，您可以看到带有所有选项的完整身份验证流程，从左下角的浏览器开始

```
Kubernetes                                                  Workstation
+---------------------------------------------------+     +------------------+
|                                                   |     |                  |
|  +-----------+   apiserver        +------------+  |     |  +------------+  |
|  |           |   proxy            |            |  |     |  |            |  |
|  | apiserver |                    |  ingress   |  |     |  |  ingress   |  |
|  |           |                    | controller |  |     |  | controller |  |
|  |           |                    |            |  |     |  |            |  |
|  |           |                    |            |  |     |  |            |  |
|  |           |  service account/ |            |  |     |  |            |  |
|  |           |  kubeconfig        |            |  |     |  |            |  |
|  |           +<-------------------+            |  |     |  |            |  |
|  |           |                    |            |  |     |  |            |  |
|  +------+----+      kubeconfig    +------+-----+  |     |  +------+-----+  |
|         |<--------------------------------------------------------|        |
|                                                   |     |                  |
+---------------------------------------------------+     +------------------+
```

### Service Account

如果使用服务帐户连接到API服务器，则仪表板希望文件`/var/run/secrets/kubernetes.io/serviceaccount/token`存在。
它提供了使用API​​服务器进行身份验证所需的秘密令牌。

使用以下命令进行验证:

```console
# start a container that contains curl
$ kubectl run test --image=tutum/curl -- sleep 10000

# check that container is running
$ kubectl get pods
NAME                   READY     STATUS    RESTARTS   AGE
test-701078429-s5kca   1/1       Running   0          16s

# check if secret exists
$ kubectl exec test-701078429-s5kca ls /var/run/secrets/kubernetes.io/serviceaccount/
ca.crt
namespace
token

# get service IP of master
$ kubectl get services
NAME         CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   10.0.0.1     <none>        443/TCP   1d

# check base connectivity from cluster inside
$ kubectl exec test-701078429-s5kca -- curl -k https://10.0.0.1
Unauthorized

# connect using tokens
$ TOKEN_VALUE=$(kubectl exec test-701078429-s5kca -- cat /var/run/secrets/kubernetes.io/serviceaccount/token)
$ echo $TOKEN_VALUE
eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3Mi....9A
$ kubectl exec test-701078429-s5kca -- curl --cacert /var/run/secrets/kubernetes.io/serviceaccount/ca.crt -H  "Authorization: Bearer $TOKEN_VALUE" https://10.0.0.1
{
  "paths": [
    "/api",
    "/api/v1",
    "/apis",
    "/apis/apps",
    "/apis/apps/v1alpha1",
    "/apis/authentication.k8s.io",
    "/apis/authentication.k8s.io/v1beta1",
    "/apis/authorization.k8s.io",
    "/apis/authorization.k8s.io/v1beta1",
    "/apis/autoscaling",
    "/apis/autoscaling/v1",
    "/apis/batch",
    "/apis/batch/v1",
    "/apis/batch/v2alpha1",
    "/apis/certificates.k8s.io",
    "/apis/certificates.k8s.io/v1alpha1",
    "/apis/networking",
    "/apis/networking/v1beta1",
    "/apis/policy",
    "/apis/policy/v1alpha1",
    "/apis/rbac.authorization.k8s.io",
    "/apis/rbac.authorization.k8s.io/v1alpha1",
    "/apis/storage.k8s.io",
    "/apis/storage.k8s.io/v1beta1",
    "/healthz",
    "/healthz/ping",
    "/logs",
    "/metrics",
    "/swaggerapi/",
    "/ui/",
    "/version"
  ]
}
```

如果不起作用，可能有两个原因:

1. 令牌的内容无效。使用 `kubectl get secrets | grep service-account` 并使用 `kubectl delete secret <name>`删除它. 它将自动重新创建.

2. 您具有非标准的Kubernetes安装，并且包含令牌的文件可能不存在。 
   API服务器将安装包含该文件的 volume，但前提是将API服务器配置为使用ServiceAccount准入控制器。
   如果遇到此错误，请验证您的API服务器正在使用ServiceAccount准入控制器。
   如果要手动配置API服务器，则可以使用`--admission-contro`l参数进行设置
   > 请注意，您还应该使用其他准入控制器。在配置此选项之前，您应该阅读有关准入控制器的信息。

更多信息:

- [用户指南:服务帐户](http://kubernetes.io/docs/user-guide/service-accounts/)
- [集群管理员指南:管理服务帐户](http://kubernetes.io/docs/admin/service-accounts-admin/)

## Kube-Config

如果要使用kubeconfig文件进行身份验证, 请遵循 [部署过程](deploy/index.md) 并将参数 `--kubeconfig=/etc/kubernetes/kubeconfig.yaml` 添加到部署的args部分.

## 在Nginx中使用GDB

[Gdb](https://www.gnu.org/software/gdb/) 可以与nginx一起使用来执行配置转储。这使我们可以查看正在使用的配置以及较旧的配置

注意:以下内容基于nginx [文档](https://docs.nginx.com/nginx/admin-guide/monitoring/debugging/#dumping-nginx-configuration-from-a-running-process).

1. SSH进入worker

```console
$ ssh user@workerIP
```

2. 获取运行nginx的Docker容器

```console
$ docker ps | grep nginx-ingress-controller
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
d9e1d243156a        quay.io/kubernetes-ingress-controller/nginx-ingress-controller   "/usr/bin/dumb-init …"   19 minutes ago      Up 19 minutes                                                                            k8s_nginx-ingress-controller_nginx-ingress-controller-67956bf89d-mqxzt_kube-system_079f31ec-aa37-11e8-ad39-080027a227db_0
```

3. 进入容器执行

```console
$ docker exec -it --user=0 --privileged d9e1d243156a bash
```

4. 确保nginx使用`--with-debug`参数运行

```console
$ nginx -V 2>&1 | grep -- '--with-debug'
```

5. 获取容器上运行的进程列表

```console
$ ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 20:23 ?        00:00:00 /usr/bin/dumb-init /nginx-ingres
root         5     1  0 20:23 ?        00:00:05 /nginx-ingress-controller --defa
root        21     5  0 20:23 ?        00:00:00 nginx: master process /usr/sbin/
nobody     106    21  0 20:23 ?        00:00:00 nginx: worker process
nobody     107    21  0 20:23 ?        00:00:00 nginx: worker process
root       172     0  0 20:43 pts/0    00:00:00 bash
```

7. 将gdb附加到nginx主进程

```console
$ gdb -p 21
....
Attaching to process 21
Reading symbols from /usr/sbin/nginx...done.
....
(gdb)
```

8. 复制并粘贴以下内容:

```console
set $cd = ngx_cycle->config_dump
set $nelts = $cd.nelts
set $elts = (ngx_conf_dump_t*)($cd.elts)
while ($nelts-- > 0)
set $name = $elts[$nelts]->name.data
printf "Dumping %s to nginx_conf.txt\n", $name
append memory nginx_conf.txt \
        $elts[$nelts]->buffer.start $elts[$nelts]->buffer.end
end
```

9. 通过按CTRL + D退出GDB

10. 打开 nginx_conf.txt

```console
cat nginx_conf.txt
```
