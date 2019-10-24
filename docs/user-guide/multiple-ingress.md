# Multiple Ingress controllers

如果您正在运行多个  ingress controller，或者在本地处理入口的云提供商(例如GKE)上运行，
您需要在所有要让ingress-nginx控制器声明的入口中指定注释`kubernetes.io/ingress.class:"nginx"`。


实例,

```yaml
metadata:
  name: foo
  annotations:
    kubernetes.io/ingress.class: "gce"
```

将以GCE控制器为目标，强制Nginx控制器忽略它，而类似的注释

```yaml
metadata:
  name: foo
  annotations:
    kubernetes.io/ingress.class: "nginx"
```

将以nginx控制器为目标，强制GCE控制器忽略它。

重申一下，将注释设置为与有效的Ingress类不匹配的任何值都将迫使nginx Ingress控制器忽略您的Ingress。
如果仅运行单个nginx ingress controller，则可以通过将注释设置为除"nginx"或空字符串以外的任何值来实现。

如果您希望与NGINX控制器同时使用其他Ingress控制器之一，请执行此操作。

## Multiple ingress-nginx controllers


此机制还为用户提供了运行nginx ingress controller的能力(例如，一个服务于公共流量的控制器，一个服务于`内部`流量的控制器)。
为此，必须将选项`--ingress-class`更改为复制控制器定义内群集的唯一值。
这是一个部分示例:

```yaml
spec:
  template:
     spec:
       containers:
         - name: nginx-ingress-internal-controller
           args:
             - /nginx-ingress-controller
             - '--election-id=ingress-controller-leader-internal'
             - '--ingress-class=nginx-internal'
             - '--configmap=ingress/nginx-ingress-internal-controller'
```

!!! 重要
    部署多个不同类型的Ingress控制器(例如，`ingress-nginx`和`gce`)，并且不指定类注释
    导致两个或所有控制器争分夺秒地满足Ingress，并且所有人都竞相以混乱的方式更新Ingress status字段。
    
    当运行多个ingress-nginx控制器时，如果其中一个控制器使用默认值，它将仅处理未设置的类注释
    --ingress-class`的值(请参阅"internal/ingress/annotations/class/main.go'中的'IsValid'方法)，否则需要使用类注释。
