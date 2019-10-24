# Ingress Path Matching

## 正则表达式支持

!!! 重要 
    `spec.rules.host`字段不支持正则表达式和通配符。 必须使用完整的主机名

  ingress controller在`spec.rules.http.paths.path`字段中支持 **不区分大小写** 的正则表达式。
可以通过将`nginx.ingress.kubernetes.io/use-regex`注释设置为`true`来启用(默认为`false`)。

查看`use-regex`的 [描述](./nginx-configuration/annotations.md#use-regex) 获取更多信息.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: test-ingress
  annotations:
    nginx.ingress.kubernetes.io/use-regex: "true"
spec:
  rules:
  - host: test.com
    http:
      paths:
      - path: /foo/.*
        backend:
          serviceName: test
          servicePort: 80
```

前面的入口定义将转换为`test.com`服务器的NGINX配置中的以下location块:

```txt
location ~* "^/foo/.*" {
  ...
}
```

## Path Priority

在NGINX中，正则表达式遵循 **首次匹配** 策略。 为了实现更精确的路径匹配，在将路径作为位置块写入NGINX模板之前，ingress-nginx首先将路径按降序排序。

**在你使用正则表达式之前请查看 [warning](#warning).**

### Example

创建以下两个入口定义:

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: test-ingress-1
spec:
  rules:
  - host: test.com
    http:
      paths:
      - path: /foo/bar
        backend:
          serviceName: service1
          servicePort: 80
      - path: /foo/bar/
        backend:
          serviceName: service2
          servicePort: 80
```

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: test-ingress-2
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  rules:
  - host: test.com
    http:
      paths:
      - path: /foo/bar/(.+)
        backend:
          serviceName: service3
          servicePort: 80
```

  ingress controller将在`test.com`服务器的NGINX模板中按长度降序定义以下位置块:

```txt
location ~* ^/foo/bar/.+ {
  ...
}

location ~* "^/foo/bar/" {
  ...
}

location ~* "^/foo/bar" {
  ...
}
```

以下请求URI将与相应的位置块匹配:

- `test.com/foo/bar/1` 匹配 `~* ^/foo/bar/.+` 将被路由到 service 3.
- `test.com/foo/bar/` 匹配 `~* ^/foo/bar/` 将被路由到 service 2.
- `test.com/foo/bar` 匹配 `~* ^/foo/bar` 将被路由到 service 1.

**重要提醒**:

- 如果 `use-regex` 或 `rewrite-target` 被一个给定的host使用, 然后不区分大小写的正则表达式 [location modifier](https://nginx.org/en/docs/http/ngx_http_core_module.html#location) will be enforced on ALL paths for a given host regardless of what Ingress they are defined on.

## Warning

下面的示例描述了一种可能导致不必要的路径匹配行为的情况。

这种情况是预料之中的，这是NGINX针对使用正则表达式的路径的第一个匹配策略的结果 [location modifier](https://nginx.org/en/docs/http/ngx_http_core_module.html#location). For more information about how a path is chosen, please read the following article: ["Understanding nginx Server and Location Block Selection Algorithms"](https://www.digitalocean.com/community/tutorials/understanding-nginx-server-and-location-block-selection-algorithms).

### Example

定义以下入口:

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: test-ingress-3
  annotations:
    nginx.ingress.kubernetes.io/use-regex: "true"
spec:
  rules:
  - host: test.com
    http:
      paths:
      - path: /foo/bar/bar
        backend:
          serviceName: test
          servicePort: 80
      - path: /foo/bar/[A-Z0-9]{3}
        backend:
          serviceName: test
          servicePort: 80
```

  ingress controller将在NGINX模板中为`test.com`服务器定义以下位置块(按此顺序):

```txt
location ~* "^/foo/bar/[A-Z0-9]{3}" {
  ...
}

location ~* "^/foo/bar/bar" {
  ...
}
```

请求 `test.com/foo/bar/bar` 将匹配 `^/foo/[A-Z0-9]{3}` location 块而不是最长的完全匹配路径.
