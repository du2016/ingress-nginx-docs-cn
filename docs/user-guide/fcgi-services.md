

# 暴露FastCGI服务器

> **FastCGI** 是一个用于交互程序与[web server](https://en.wikipedia.org/wiki/Web_server "Web server")交互的 [二进制协议](https://en.wikipedia.org/wiki/Binary_protocol "Binary protocol") 的交互程序接口. [...] 目的是减少与Web服务器和CGI程序之间的接口相关的开销，使服务器每单位时间处理更多的Web页面请求。
> &mdash; Wikipedia

_ingress-nginx_   ingress controller可用于直接暴露 [FastCGI](https://en.wikipedia.org/wiki/FastCGI) 服务.  在Ingress中启用FastCGI只需要将_backend-protocol_注释设置为`FCGI`，并且通过添加更多注释，您可以自定义_ingress-nginx_处理与FastCGI _server_的通信的方式。

## 公开FastCGI Pod的示例对象

下面的_Pod_示例对象公开了端口`9000`，这是常规的FastCGI端口.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: example-app
labels:
  app: example-app
spec:
  containers:
  - name: example-app
    image: example-app:1.0
    ports:
    - containerPort: 9000
      name: fastcgi
```

下面的 _Service_ 对象示例与上面的_Pod_对象的端口9000相匹配.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: example-service
spec:
  selector:
    app: example-app
  ports:
  - port: 9000
    targetPort: 9000
    name: fastcgi
```

下面的_Ingress_和_ConfigMap_对象演示了受支持的_FastCGI_特定注释(NGINX实际上具有50个FastCGI指令，所有这些指令都尚未在入口中公开)，并且与服务`example-service`和名为`fastcgi的端口相匹配。 `从上面。 必须首先创建_ConfigMap_ **，以便_Ingress Controller_能够在创建_Ingress_对象时找到它，否则，您将需要重新启动 _Ingress Controller_pods。
```yaml
# 必须首先创建ConfigMap，以便Ingress控制器在创建Ingress对象时能够找到它。

apiVersion: v1
kind: ConfigMap
metadata:
  name: example-cm
data:
  SCRIPT_FILENAME: "/example/index.php"

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  annotations:
    kubernetes.io/ingress.class: "nginx"
    nginx.ingress.kubernetes.io/backend-protocol: "FCGI"
    nginx.ingress.kubernetes.io/fastcgi-index: "index.php"
    nginx.ingress.kubernetes.io/fastcgi-params-configmap: "example-cm"
  name: example-app
spec:
  rules:
  - host: app.example.com
    http:
      paths:
      - backend:
          serviceName: example-service
          servicePort: fastcgi
```

## FastCGIingress注释

### `nginx.ingress.kubernetes.io/backend-protocol` 注解

为开启 FastCGI, `backend-protocol` 需要设置为 `FCGI`, 将覆盖默认的 `HTTP`.

> `nginx.ingress.kubernetes.io/backend-protocol: "FCGI"`

为开启全局 _Ingress_ 对象模式 FastCGI _FastCGI_ 模式.

### The `nginx.ingress.kubernetes.io/fastcgi-index` Annotation

指定一个索引文件,`fastcgi-index` 注释值可以选择设置.  下面设置, 值设置为 `index.php`.  该注释对应于 [the _NGINX_ `fastcgi_index` directive](http://nginx.org/en/docs/http/ngx_http_fastcgi_module.html#fastcgi_index).

> `nginx.ingress.kubernetes.io/fastcgi-index: "index.php"`

### The `nginx.ingress.kubernetes.io/fastcgi-params-configmap` Annotation

为指定 [_NGINX_ `fastcgi_param` directives](http://nginx.org/en/docs/http/ngx_http_fastcgi_module.html#fastcgi_param), `fastcgi-params-configmap` 注解将被使用, 反过来必然导致 _ConfigMap_ 对象包含 _NGINX_ `fastcgi_param` 指令作为键/值.

> `nginx.ingress.kubernetes.io/fastcgi-params: "example-configmap"`

和 _ConfigMap_ 对象 用于指定 `SCRIPT_FILENAME` 和 `HTTP_PROXY`  _NGINX's_ `fastcgi_param` 指令将如下所示:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: example-configmap
data:
  SCRIPT_FILENAME: "/example/index.php"
  HTTP_PROXY: ""
```
使用 _namespace/_ prefix 也被支持, for example:

> `nginx.ingress.kubernetes.io/fastcgi-params: "example-namespace/example-configmap"`
