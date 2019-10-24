#  基本用法 - 基于主机的路由

ingress-nginx可用于各种云提供商内部的许多用例，并支持许多配置。
在本部分中，您可以找到一种常见的使用场景，
其中由ingress-nginx驱动的单个负载均衡器将根据主机名将流量路由到2个不同的HTTP后端服务

首先，按照说明安装ingress-nginx。然后想象您需要公开两个已经安装的HTTP服务:
myServiceA，myServiceB。
假设您要在myServiceA.foo.org公开第一个，而在myServiceB.foo.org公开第二个。一种可能的解决方案是创建两个入口资源:

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: ingress-myServiceA
  annotations:
    # use the shared ingress-nginx
    kubernetes.io/ingress.class: "nginx"
spec:
  rules:
  - host: myServiceA.foo.org
    http:
      paths:
      - path: /
        backend:
          serviceName: myServiceA
          servicePort: 80
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: ingress-myServiceB
  annotations:
    # use the shared ingress-nginx
    kubernetes.io/ingress.class: "nginx"
spec:
  rules:
  - host: myServiceB.foo.org
    http:
      paths:
      - path: /
        backend:
          serviceName: myServiceB
          servicePort: 80
```

当您应用此Yaml时，将创建2个由ingress-nginx实例管理的入口资源。 
Nginx配置为使用kubernetes.io/ingress.class:"nginx"注释自动发现所有入口。
请注意，入口资源应放在后端资源的相同名称空间内

在许多云提供商上，ingress-nginx还将创建相应的负载均衡器资源。您所要做的就是获取外部IP，并在DNS提供程序中添加一个DNS A记录，
该记录将myServiceA.foo.org和myServiceB.foo.org指向nginx外部IP。通过运行以下命令获取外部IP:

```
kubectl get services -n ingress-nginx
```
