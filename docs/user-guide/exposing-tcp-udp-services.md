# Exposing TCP and UDP services

ingress不支持TCP或UDP服务。 因此，此Ingress控制器使用标志 `--tcp-services-configmap` and `--udp-services-configmap` 指向现有的配置映射，其中的键是要使用的外部端口，并且该值指示使用以下格式公开的服务:
`<namespace/service name>:<service port>:[PROXY]:[PROXY]`

也可以使用端口号或名称。 最后两个字段是可选的。.
在最后两个字段中的一个或两个中添加"PROXY"，我们可以在TCP服务中使用代理协议解码(侦听)和/或编码(proxy_pass) https://www.nginx.com/resources/admin-guide/proxy-protocol

下面示例展示如何暴露运行在`default`命名空间的 `example-go` 服务在端口8080中使用端口9000

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: tcp-services
  namespace: ingress-nginx
data:
  9000: "default/example-go:8080"
```

1.9.13开始 NGINX提供 [UDP Load Balancing](https://www.nginx.com/blog/announcing-udp-load-balancing/).
下一个示例显示如何使用端口`53`暴露端口`53`，在名称空间`kube-system`中运行的服务`kube-dns`。

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: udp-services
  namespace: ingress-nginx
data:
  53: "kube-system/kube-dns:53"
```

If TCP/UDP proxy support is used, then those ports need to be exposed in the Service defined for the Ingress.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: ingress-nginx
  namespace: ingress-nginx
  labels:
    app.kubernetes.io/name: ingress-nginx
    app.kubernetes.io/part-of: ingress-nginx
spec:
  type: LoadBalancer
  ports:
    - name: http
      port: 80
      targetPort: 80
      protocol: TCP
    - name: https
      port: 443
      targetPort: 443
      protocol: TCP
    - name: proxied-tcp-9000
      port: 9000
      targetPort: 9000
      protocol: TCP
  selector:
    app.kubernetes.io/name: ingress-nginx
    app.kubernetes.io/part-of: ingress-nginx
```
