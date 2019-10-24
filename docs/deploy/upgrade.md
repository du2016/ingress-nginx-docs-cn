# 升级

!!! 重要
    无论您使用哪种升级方法，如果使用模板替代，请确保您的模板与新版本的ingress-nginx兼容。
    
## 不使用Helm

要升级您的ingress-nginx安装，更改控制器部署中映像的版本就足够了

假如您的deployment资源看起来像(部分示例):

```yaml
kind: Deployment
metadata:
  name: nginx-ingress-controller
  namespace: ingress-nginx
spec:
  replicas: 1
  selector: ...
  template:
    metadata: ...
    spec:
      containers:
        - name: nginx-ingress-controller
          image: quay.io/kubernetes-ingress-controller/nginx-ingress-controller:0.9.0
          args: ...
```

只需将`0.9.0`标记更改为要升级到的版本。最简单的方法是(请注意，您可能需要根据安装更改名称参数):

```
kubectl set image deployment/nginx-ingress-controller \
  nginx-ingress-controller=quay.io/kubernetes-ingress-controller/nginx-ingress-controller:0.18.0
```

对于交互式编辑，请使用 `kubectl edit deployment nginx-ingress-controller`.

## 使用helm

如果您在部署文档中使用Helm命令安装了ingress-nginx，因此其名称为ngx-ingress，则可以这样升级

```shell
helm upgrade --reuse-values ngx-ingress stable/nginx-ingress
```
