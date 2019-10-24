# Annotations

您可以将这些Kubernetes批注添加到特定的Ingress对象以自定义其行为

!!! 小提示
    注释键和值只能是字符串。必须引用其他类型，例如布尔值或数字值，即"true"，"false"，"100"。.

!!! 注意
    可以使用
    [`--annotations-prefix` command line argument](../cli-arguments.md),
    命令行参数更改注释前缀，但是默认值为`nginx.ingress.kubernetes.io`，如下表所述。.

|Name                       | type |
|---------------------------|------|
|[nginx.ingress.kubernetes.io/app-root](#rewrite)|string|
|[nginx.ingress.kubernetes.io/affinity](#session-affinity)|cookie|
|[nginx.ingress.kubernetes.io/affinity-mode](#session-affinity)|"balanced" or "persistent"|
|[nginx.ingress.kubernetes.io/auth-realm](#authentication)|string|
|[nginx.ingress.kubernetes.io/auth-secret](#authentication)|string|
|[nginx.ingress.kubernetes.io/auth-secret-type](#authentication)|string|
|[nginx.ingress.kubernetes.io/auth-type](#authentication)|basic or digest|
|[nginx.ingress.kubernetes.io/auth-tls-secret](#client-certificate-authentication)|string|
|[nginx.ingress.kubernetes.io/auth-tls-verify-depth](#client-certificate-authentication)|number|
|[nginx.ingress.kubernetes.io/auth-tls-verify-client](#client-certificate-authentication)|string|
|[nginx.ingress.kubernetes.io/auth-tls-error-page](#client-certificate-authentication)|string|
|[nginx.ingress.kubernetes.io/auth-tls-pass-certificate-to-upstream](#client-certificate-authentication)|"true" or "false"|
|[nginx.ingress.kubernetes.io/auth-url](#external-authentication)|string|
|[nginx.ingress.kubernetes.io/auth-cache-key](#external-authentication)|string|
|[nginx.ingress.kubernetes.io/auth-cache-duration](#external-authentication)|string|
|[nginx.ingress.kubernetes.io/auth-proxy-set-headers](#external-authentication)|string|
|[nginx.ingress.kubernetes.io/auth-snippet](#external-authentication)|string|
|[nginx.ingress.kubernetes.io/enable-global-auth](#external-authentication)|"true" or "false"|
|[nginx.ingress.kubernetes.io/backend-protocol](#backend-protocol)|string|HTTP,HTTPS,GRPC,GRPCS,AJP|
|[nginx.ingress.kubernetes.io/canary](#canary)|"true" or "false"|
|[nginx.ingress.kubernetes.io/canary-by-header](#canary)|string|
|[nginx.ingress.kubernetes.io/canary-by-header-value](#canary)|string
|[nginx.ingress.kubernetes.io/canary-by-cookie](#canary)|string|
|[nginx.ingress.kubernetes.io/canary-weight](#canary)|number|
|[nginx.ingress.kubernetes.io/client-body-buffer-size](#client-body-buffer-size)|string|
|[nginx.ingress.kubernetes.io/configuration-snippet](#configuration-snippet)|string|
|[nginx.ingress.kubernetes.io/custom-http-errors](#custom-http-errors)|[]int|
|[nginx.ingress.kubernetes.io/default-backend](#default-backend)|string|
|[nginx.ingress.kubernetes.io/enable-cors](#enable-cors)|"true" or "false"|
|[nginx.ingress.kubernetes.io/cors-allow-origin](#enable-cors)|string|
|[nginx.ingress.kubernetes.io/cors-allow-methods](#enable-cors)|string|
|[nginx.ingress.kubernetes.io/cors-allow-headers](#enable-cors)|string|
|[nginx.ingress.kubernetes.io/cors-allow-credentials](#enable-cors)|"true" or "false"|
|[nginx.ingress.kubernetes.io/cors-max-age](#enable-cors)|number|
|[nginx.ingress.kubernetes.io/force-ssl-redirect](#server-side-https-enforcement-through-redirect)|"true" or "false"|
|[nginx.ingress.kubernetes.io/from-to-www-redirect](#redirect-from-to-www)|"true" or "false"|
|[nginx.ingress.kubernetes.io/http2-push-preload](#http2-push-preload)|"true" or "false"|
|[nginx.ingress.kubernetes.io/limit-connections](#rate-limiting)|number|
|[nginx.ingress.kubernetes.io/limit-rps](#rate-limiting)|number|
|[nginx.ingress.kubernetes.io/permanent-redirect](#permanent-redirect)|string|
|[nginx.ingress.kubernetes.io/permanent-redirect-code](#permanent-redirect-code)|number|
|[nginx.ingress.kubernetes.io/temporal-redirect](#temporal-redirect)|string|
|[nginx.ingress.kubernetes.io/proxy-body-size](#custom-max-body-size)|string|
|[nginx.ingress.kubernetes.io/proxy-cookie-domain](#proxy-cookie-domain)|string|
|[nginx.ingress.kubernetes.io/proxy-cookie-path](#proxy-cookie-path)|string|
|[nginx.ingress.kubernetes.io/proxy-connect-timeout](#custom-timeouts)|number|
|[nginx.ingress.kubernetes.io/proxy-send-timeout](#custom-timeouts)|number|
|[nginx.ingress.kubernetes.io/proxy-read-timeout](#custom-timeouts)|number|
|[nginx.ingress.kubernetes.io/proxy-next-upstream](#custom-timeouts)|string|
|[nginx.ingress.kubernetes.io/proxy-next-upstream-timeout](#custom-timeouts)|number|
|[nginx.ingress.kubernetes.io/proxy-next-upstream-tries](#custom-timeouts)|number|
|[nginx.ingress.kubernetes.io/proxy-request-buffering](#custom-timeouts)|string|
|[nginx.ingress.kubernetes.io/proxy-redirect-from](#proxy-redirect)|string|
|[nginx.ingress.kubernetes.io/proxy-redirect-to](#proxy-redirect)|string|
|[nginx.ingress.kubernetes.io/proxy-http-version](#proxy-http-version)|"1.0" or "1.1"|
|[nginx.ingress.kubernetes.io/proxy-ssl-secret](#backend-certificate-authentication)|string|
|[nginx.ingress.kubernetes.io/proxy-ssl-ciphers](#backend-certificate-authentication)|string|
|[nginx.ingress.kubernetes.io/proxy-ssl-protocols](#backend-certificate-authentication)|string|
|[nginx.ingress.kubernetes.io/proxy-ssl-verify](#backend-certificate-authentication)|string|
|[nginx.ingress.kubernetes.io/proxy-ssl-verify-depth](#backend-certificate-authentication)|number|
|[nginx.ingress.kubernetes.io/enable-rewrite-log](#enable-rewrite-log)|"true" or "false"|
|[nginx.ingress.kubernetes.io/rewrite-target](#rewrite)|URI|
|[nginx.ingress.kubernetes.io/satisfy](#satisfy)|string|
|[nginx.ingress.kubernetes.io/secure-verify-ca-secret](#secure-backends)|string|
|[nginx.ingress.kubernetes.io/server-alias](#server-alias)|string|
|[nginx.ingress.kubernetes.io/server-snippet](#server-snippet)|string|
|[nginx.ingress.kubernetes.io/service-upstream](#service-upstream)|"true" or "false"|
|[nginx.ingress.kubernetes.io/session-cookie-name](#cookie-affinity)|string|
|[nginx.ingress.kubernetes.io/session-cookie-path](#cookie-affinity)|string|
|[nginx.ingress.kubernetes.io/session-cookie-change-on-failure](#cookie-affinity)|"true" or "false"|
|[nginx.ingress.kubernetes.io/ssl-redirect](#server-side-https-enforcement-through-redirect)|"true" or "false"|
|[nginx.ingress.kubernetes.io/ssl-passthrough](#ssl-passthrough)|"true" or "false"|
|[nginx.ingress.kubernetes.io/upstream-hash-by](#custom-nginx-upstream-hashing)|string|
|[nginx.ingress.kubernetes.io/x-forwarded-prefix](#x-forwarded-prefix-header)|string|
|[nginx.ingress.kubernetes.io/load-balance](#custom-nginx-load-balancing)|string|
|[nginx.ingress.kubernetes.io/upstream-vhost](#custom-nginx-upstream-vhost)|string|
|[nginx.ingress.kubernetes.io/whitelist-source-range](#whitelist-source-range)|CIDR|
|[nginx.ingress.kubernetes.io/proxy-buffering](#proxy-buffering)|string|
|[nginx.ingress.kubernetes.io/proxy-buffers-number](#proxy-buffers-number)|number|
|[nginx.ingress.kubernetes.io/proxy-buffer-size](#proxy-buffer-size)|string|
|[nginx.ingress.kubernetes.io/ssl-ciphers](#ssl-ciphers)|string|
|[nginx.ingress.kubernetes.io/connection-proxy-header](#connection-proxy-header)|string|
|[nginx.ingress.kubernetes.io/enable-access-log](#enable-access-log)|"true" or "false"|
|[nginx.ingress.kubernetes.io/lua-resty-waf](#lua-resty-waf)|string|
|[nginx.ingress.kubernetes.io/lua-resty-waf-debug](#lua-resty-waf)|"true" or "false"|
|[nginx.ingress.kubernetes.io/lua-resty-waf-ignore-rulesets](#lua-resty-waf)|string|
|[nginx.ingress.kubernetes.io/lua-resty-waf-extra-rules](#lua-resty-waf)|string|
|[nginx.ingress.kubernetes.io/lua-resty-waf-allow-unknown-content-types](#lua-resty-waf)|"true" or "false"|
|[nginx.ingress.kubernetes.io/lua-resty-waf-score-threshold](#lua-resty-waf)|number|
|[nginx.ingress.kubernetes.io/lua-resty-waf-process-multipart-body](#lua-resty-waf)|"true" or "false"|
|[nginx.ingress.kubernetes.io/enable-influxdb](#influxdb)|"true" or "false"|
|[nginx.ingress.kubernetes.io/influxdb-measurement](#influxdb)|string|
|[nginx.ingress.kubernetes.io/influxdb-port](#influxdb)|string|
|[nginx.ingress.kubernetes.io/influxdb-host](#influxdb)|string|
|[nginx.ingress.kubernetes.io/influxdb-server-name](#influxdb)|string|
|[nginx.ingress.kubernetes.io/use-regex](#use-regex)|bool|
|[nginx.ingress.kubernetes.io/enable-modsecurity](#modsecurity)|bool|
|[nginx.ingress.kubernetes.io/enable-owasp-core-rules](#modsecurity)|bool|
|[nginx.ingress.kubernetes.io/modsecurity-transaction-id](#modsecurity)|string|
|[nginx.ingress.kubernetes.io/modsecurity-snippet](#modsecurity)|string|
|[nginx.ingress.kubernetes.io/mirror-uri](#mirror)|string|
|[nginx.ingress.kubernetes.io/mirror-request-body](#mirror)|string|

### Canary

在某些情况下，您可能希望通过向少量与生产服务不同的服务发送少量请求来"取消"一组新的更改。 Canary注释使Ingress规范可以充当路由请求的替代服务，具体取决于所应用的规则
在设置nginx.ingress.kubernetes.io/canary之后，可以启用以下配置金丝雀的注释:设置为"true":

* `nginx.ingress.kubernetes.io/canary-by-header`: 用于通知Ingress将请求路由到Canary Ingress中指定的服务的标头。当请求标头设置为始终时，它将被路由到Canary。当标头设置为从不时，它将永远不会路由到金丝雀。对于任何其他值，标头将被忽略，并且按优先级将请求与其他金丝雀规则进行比较

* `nginx.ingress.kubernetes.io/canary-by-header-value`: 匹配的报头值，用于通知Ingress将请求路由到Canary Ingress中指定的服务。当请求标头设置为此值时，它将被路由到Canary。对于任何其他标头值，标头将被忽略，并按优先级将请求与其他金丝雀规则进行比较。此注释必须与一起使用。注释是nginx.ingress.kubernetes.io/canary-by-header的扩展，允许自定义标题值，而不使用硬编码值。如果未定义nginx.ingress.kubernetes.io/canary-by-header批注，则没有任何效果。

* `nginx.ingress.kubernetes.io/canary-by-cookie`: 用于通知Ingress将请求路由到Canary Ingress中指定的服务的cookie。当cookie值设置为always时，它将被路由到canary。当Cookie设置为"永不"时，它将永远不会路由到Canary。对于任何其他值，将忽略cookie，并按优先级将请求与其他canary规则进行比较。

* `nginx.ingress.kubernetes.io/canary-weight`: 基于整数的(0-100)百分比的随机请求，应将其路由到canary Ingress中指定的服务。权重0表示此Canary规则不会在Canary入口中将任何请求发送到服务。权重为100表示​​所有请求都将发送到Ingress中指定的替代服务。

Canary rules are evaluated in order of precedence. Precedence is as follows:
`canary-by-header -> canary-by-cookie -> canary-weight`

**Note** 当您将某个入口标记为Canary时，所有其他非Canary注释将被忽略 (从相应的主要入口继承)， 
`nginx.ingress.kubernetes.io/load-balance` 和 `nginx.ingress.kubernetes.io/upstream-hash-by`除外.

**已知局限性**

目前，每个Ingress规则最多可以应用一个Canary Ingress。

### Rewrite

在某些情况下，后端服务中公开的URL与Ingress规则中指定的路径不同. 没有重写，任何请求都将返回404.
设置 `nginx.ingress.kubernetes.io/rewrite-target` 注解到服务期望的路径.

如果应用程序根目录暴露在其他路径中，并且需要重定向，请设置注释
 `nginx.ingress.kubernetes.io/app-root` 重定向请求到 `/`.

!!! 例子
    请检查 [rewrite](../../examples/rewrite/README.md) 示例.

### 会话保持

`nginx.ingress.kubernetes.io/affinity` 注解在Ingress的所有上游中启用并设置相似性类型. 
这样，请求将始终定向到同一上游服务器
NGINX唯一可用的相似性类型是 `cookie`.

`nginx.ingress.kubernetes.io/affinity-mode` 注解定义会话的粘性. 
如果将部署规模扩大，则将其设置为`balanced`(默认)将重新分配一些会话, 因此，重新平衡服务器上的负载。将此设置为持久将不会重新平衡与新服务器的会话，因此可提供最大的粘性。

!!! 注意
    如果为一个主机定义了多个Ingress，并且至少使用一个Ingress `nginx.ingress.kubernetes.io/affinity: cookie`, 
    那么只有Ingress上的路径使用 `nginx.ingress.kubernetes.io/affinity` 将使用会话Cookie相似性。主机的其他Ingress上定义的所有路径都将通过随机选择后端服务器进行负载均衡
    
!!! 例子
    请检查 [affinity](../../examples/affinity/cookie/README.md) 示例.

#### Cookie affinity

如果你使用``cookie`` 亲和性类型， 您还可以通过`nginx.ingress.kubernetes.io/session-cookie-name`注解指定用于请求路由的cookie名称， 默认是创建一个名为 'INGRESSCOOKIE'的cookie.

 nginx 注解 `nginx.ingress.kubernetes.io/session-cookie-path` 定义将在哪些路径上设置cookie. 除非注解 `nginx.ingress.kubernetes.io/use-regex` 设置为true否则该选项是可选的; Session cookie 路径不支持正则.


### Authentication

可以添加身份验证，并在Ingress规则中添加其他注解。 身份验证的来源是包含用户名和密码的secret。

注解是:
```
nginx.ingress.kubernetes.io/auth-type: [basic|digest]
```

如何表示 [HTTP身份验证类型:基本或摘要访问身份验证](https://tools.ietf.org/html/rfc2617).

```
nginx.ingress.kubernetes.io/auth-secret: secretName
```

Secret的名称，其中包含用户名和密码，这些用户名和密码被授予对Ingress规则中定义的path的访问权限。
此注解还接受替代形式'namespace/secretName'，在这种情况下，查找secret是在引用的名称空间而不是Ingress名称空间中执行的。
```
nginx.ingress.kubernetes.io/auth-secret-type: [auth-file|auth-map]
```

`auth-secret` 有两种格式:

- `auth-file` - 默认情况下，密钥'auth'中的htpasswd文件在密钥内
- `auth-map` - 密钥的密钥是用户名，值是哈希密码

```
nginx.ingress.kubernetes.io/auth-realm: "realm string"
```

!!! 例子
    Please check the [auth](../../examples/auth/basic/README.md) example.

###  nginx upstream hashing

NGINX支持基于通过指定key实现client-server映射的负载均衡 [一致性hash](http://nginx.org/en/docs/http/ngx_http_upstream_module.html#hash) . 键可以包含文本，变量或其任意组合. 
此功能实现请求粘性而不通过客户端IP或Cookie. 将使用 [ketama](http://www.last.fm/user/RJ/journal/2007/04/10/392555/) 
一致性哈希法，该方法可确保在上游组更改时仅将少数密钥重新映射到不同的服务器

upstream hashing有一种特殊的模式，称为 subset. 在这种模式下，上游服务器被分组为子集, 粘滞性通过将密钥映射到一个子集而不是单个上游服务器而起作用. 从选定的粘性子集中随机选择特定的服务器. 它在粘性和负载分配之间达到平衡.

为后端启用一致性哈希:

`nginx.ingress.kubernetes.io/upstream-hash-by`: nginx变量，文本值或它们的任意组合以用于一致的哈希. 例如 `nginx.ingress.kubernetes.io/upstream-hash-by: "$request_uri"` 以通过当前请求URI实现上游请求的一致性哈希。

"subset" 可以通过 `nginx.ingress.kubernetes.io/upstream-hash-by-subset`: "true"开启. 这会将请求映射到节点的子集，而不是单个节点. `upstream-hash-by-subset-size` 确定每个子集的大小 (default 3).

请查看 [chashsubset](../../examples/chashsubset/deployment.yaml) 示例.

### Custom nginx load balancing

这与 [`load-balance` in ConfigMap](./configmap.md#load-balance)相同, 但会为每个入口配置负载平衡算法.
>注意 `nginx.ingress.kubernetes.io/upstream-hash-by` 优先于此配置. 如果这与 `nginx.ingress.kubernetes.io/upstream-hash-by` 都未设置， 我们回退到使用全局配置的负载平衡算法.

### Custom nginx upstream vhost

此配置设置使您可以在以下语句中控制主机的值: `proxy_set_header Host $host`,构成location区的一部分.  如果您需要通过其他方式调用上游服务器，这将比`$host`有用.

### Client Certificate Authentication

可以使用入口规则中的其他注解启用客户端证书身份验证.

注解为:

* `nginx.ingress.kubernetes.io/auth-tls-secret: secretName`:
  包含完整证书颁发机构链`ca.crt`的密钥的名称，该证书链可根据此Ingress进行身份验证.
  此注解还接受替代形式"namespace/secretName"，在这种情况下，secret查找是在引用的名称空间而不是Ingress名称空间中执行的。
* `nginx.ingress.kubernetes.io/auth-tls-verify-depth`:
  提供的客户证书和证书颁发机构链之间的验证深度.
* `nginx.ingress.kubernetes.io/auth-tls-verify-client`:
  启用客户端证书的验证。
* `nginx.ingress.kubernetes.io/auth-tls-error-page`:
  如果发生证书身份验证错误，应重定向用户的URL/page
* `nginx.ingress.kubernetes.io/auth-tls-pass-certificate-to-upstream`:
  指定是否将接收到的证书传递给上游服务器。 默认情况下是禁用的。

!!! 例子
    请检查 [client-certs](../../examples/auth/client-certs/README.md) 示例.

!!! 注意
    在Cloudflare中**不可能**进行带有客户端身份验证的TLS，这可能会导致意外行为.

    Cloudflare仅允许使用Authenticated Origin Pulls，并且需要使用自己的证书: [https://blog.cloudflare.com/protecting-the-origin-with-tls-authenticated-origin-pulls/](https://blog.cloudflare.com/protecting-the-origin-with-tls-authenticated-origin-pulls/)

    只允许通过身份验证的原始请求，并可以按照其教程进行配置: [https://support.cloudflare.com/hc/en-us/articles/204494148-Setting-up-NGINX-to-use-TLS-Authenticated-Origin-Pulls](https://support.cloudflare.com/hc/en-us/articles/204494148-Setting-up-NGINX-to-use-TLS-Authenticated-Origin-Pulls)

### Backend Certificate Authentication

使用Ingress Rule中添加注解，可以使用证书对代理的HTTPS后端进行身份验证.

* `nginx.ingress.kubernetes.io/proxy-ssl-secret: secretName`:
  指定secret包含证书 `tls.crt`, key `tls.key` 以PEM格式用于对代理HTTPS服务器的身份验证. 它还应包含PEM格式的可信CA证书`ca.crt`，用于验证代理HTTPS服务器的证书。.
  此注解还接受替代形式"namespace/secretName"，在这种情况下，secret查找是在引用的namespace而不是Ingress namespace中执行的。
* `nginx.ingress.kubernetes.io/proxy-ssl-verify`:
  启用或禁用对代理HTTPS服务器证书的验证. (default: off)
* `nginx.ingress.kubernetes.io/proxy-ssl-verify-depth`:
  设置代理HTTPS服务器证书链中的验证深度. (default: 1)
* `nginx.ingress.kubernetes.io/proxy-ssl-ciphers`:
  指定代理到服务器的请求 [ciphers](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_ssl_ciphers). 指纹以OpenSSL库理解的格式指定.
* `nginx.ingress.kubernetes.io/proxy-ssl-protocols`:
  开启指定 [protocols](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_ssl_protocols) 用于代理到其他服务器.

### Configuration snippet

使用此注释，您可以将其他配置添加到NGINX位置。 例如:

```yaml
nginx.ingress.kubernetes.io/configuration-snippet: |
  more_set_headers "Request-Id: $req_id";
```

### Custom HTTP Errors

像 configmap中[`custom-http-errors`](./configmap.md#custom-http-errors)的值, 注解讲设置 `proxy-intercept-errors`, 但仅适用于与此入口相关联的nginx location. 如果 [default backend annotation](#default-backend) 在入口上指定时，错误将被路由到该注释的默认后端服务(而不是全局默认后端)。
不同的入口可以指定不同的错误代码集. 即使多个入口对象共享相同的主机名, 此注解可用于为每个入口拦截不同的错误代码 (例如，对于同一主机名上的不同路径，将截获不同的错误代码, 如果每个路径都是不同的ingress).
如果 `custom-http-errors` 也在全局指定的, 此注解中指定的错误值将覆盖给定入口的主机名和路径的全局值.

用法示例:
```
nginx.ingress.kubernetes.io/custom-http-errors: "404,415"
```

### Default Backend

该注释的形式为 `nginx.ingress.kubernetes.io/default-backend: <svc name>` 从而指定自定义后端.  该 `<svc name>` 是您应用此注解的相同名称空间中对服务的引用. 此注释将覆盖全局默认后端.

当Ingress规则中的服务没有活动端点时，将处理该服务的响应. 它还将处理错误响应 如果此注释和 [custom-http-errors annotation](#custom-http-errors) 同时设置.

### Enable CORS

在ingress中启用跨域资源共享 (CORS),添加注解
`nginx.ingress.kubernetes.io/enable-cors: "true"`. 这将在服务器中添加一个部分启用此功能的位置。

可以通过以下注释控制CORS:

* `nginx.ingress.kubernetes.io/cors-allow-methods`
  控制接受哪些方法。 这是一个多值字段，以','分割且仅接受字母(大写和小写)。
  - 默认: `GET, PUT, POST, DELETE, PATCH, OPTIONS`
  - 示例: `nginx.ingress.kubernetes.io/cors-allow-methods: "PUT, GET, POST, OPTIONS"`

* `nginx.ingress.kubernetes.io/cors-allow-headers`
  控制接受哪些header. 这是一个多值字段，以','分割且 接受字母数字，_和-,
  - 默认: `DNT,X-CustomHeader,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Authorization`
  - 示例: `nginx.ingress.kubernetes.io/cors-allow-headers: "X-Forwarded-For, X-app123-XPTO"`

* `nginx.ingress.kubernetes.io/cors-allow-origin`
  控制CORS接受的远端.
  这是一个单字段值，格式如下: `http(s)://origin-site.com` or `http(s)://origin-site.com:port`
  - 默认: `*`
  - 示例: `nginx.ingress.kubernetes.io/cors-allow-origin: "https://origin-site.com:4443"`

* `nginx.ingress.kubernetes.io/cors-allow-credentials`
  控制在CORS操作期间是否可以传递凭据.
  - 默认: `true`
  - 示例: `nginx.ingress.kubernetes.io/cors-allow-credentials: "false"`

* `nginx.ingress.kubernetes.io/cors-max-age`
  控制可以将预检请求缓存多长时间.
  默认: `1728000`
  示例: `nginx.ingress.kubernetes.io/cors-max-age: 600`

!!! 注意
    更多信息请查看 [https://enable-cors.org](https://enable-cors.org/server_nginx.html)

### HTTP2 Push Preload.

启用将'链接'响应标题字段中指定的预加载链接自动转换为推送请求的功能.

!!! 示例

    * `nginx.ingress.kubernetes.io/http2-push-preload: "true"`

### Server Alias

允许使用注释在NGINX配置的服务器定义中定义一个或多个别名 `nginx.ingress.kubernetes.io/server-alias: "<alias 1>,<alias 2>"`.
这将创建具有相同配置的服务器, 不过为 `server_name` 添加新的值.

!!! 注意
	  server-alias 名称不能与现有服务器的主机名冲突. 如果是这样，服务器别名注释将被忽略.
    如果创建了服务器别名，然后又创建了具有相同主机名的新服务器，则新服务器配置将采用别名配置上的配置.

查看更多信息 [the `server_name` documentation](http://nginx.org/en/docs/http/ngx_http_core_module.html#server_name).

### Server snippet

使用注解 `nginx.ingress.kubernetes.io/server-snippet` 可以在服务器配置块中添加自定义配置.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  annotations:
    nginx.ingress.kubernetes.io/server-snippet: |
        set $agentflag 0;

        if ($http_user_agent ~* "(Mobile)" ){
          set $agentflag 1;
        }

        if ( $agentflag = 1 ) {
          return 301 https://m.example.com;
        }
```

!!! 注意
    每个host只能配置一个该注解.

### Client Body Buffer Size

设置缓冲区大小，以读取每个位置的客户端请求正文。 如果请求主体大于缓冲区，
整个身体或只将其一部分写入一个临时文件。 默认情况下，缓冲区大小等于两个内存页。
在x86，其他32位平台和x86-64上为8K。 在其他64位平台上，通常为16K。 该注解是
应用于入口规则中提供的每个位置。.

!!! 注意
    注释值必须以Nginx可以理解的格式给出.

!!! 例子

    * `nginx.ingress.kubernetes.io/client-body-buffer-size: "1000"` # 1000 bytes
    * `nginx.ingress.kubernetes.io/client-body-buffer-size: 1k` # 1 kilobyte
    * `nginx.ingress.kubernetes.io/client-body-buffer-size: 1K` # 1 kilobyte
    * `nginx.ingress.kubernetes.io/client-body-buffer-size: 1m` # 1 megabyte
    * `nginx.ingress.kubernetes.io/client-body-buffer-size: 1M` # 1 megabyte

查看更多信息 [http://nginx.org](http://nginx.org/en/docs/http/ngx_http_core_module.html#client_body_buffer_size)

### External Authentication

要使用提供身份验证的现有服务，可以使用以下注释来注释Ingress规则 `nginx.ingress.kubernetes.io/auth-url` 指示应该将HTTP请求发送到的URL.

```yaml
nginx.ingress.kubernetes.io/auth-url: "URL to the authentication service"
```

Additionally it is possible to set:

* `nginx.ingress.kubernetes.io/auth-method`:
  `<Method>` 指定要使用的HTTP方法。
* `nginx.ingress.kubernetes.io/auth-signin`:
  `<SignIn_URL>` 指定错误页面的location。
* `nginx.ingress.kubernetes.io/auth-response-headers`:
  `<Response_Header_1, ..., Response_Header_n>` 指定在身份验证请求完成后传递给后端的标头.
* `nginx.ingress.kubernetes.io/auth-proxy-set-headers`:
  `<ConfigMap>` ConfigMap的名称，该名称指定要传递给身份验证服务的标头
* `nginx.ingress.kubernetes.io/auth-request-redirect`:
  `<Request_Redirect_URL>`  指定X-Auth-Request-Redirect标头值。.
* `nginx.ingress.kubernetes.io/auth-cache-key`:
  `<Cache_Key>` 为认证请求开启缓存策略. 指定身份验证响应的查找键. 例如. `$remote_user$http_authorization`.每个server和location都有其自己的密钥空间. 因此，缓存的响应仅在按服务器和按位置的基础上有效.
* `nginx.ingress.kubernetes.io/auth-cache-duration`:
  `<Cache_duration>` 根据验证响应的响应代码为其指定缓存时间s, 例如. `200 202 30m`. 查看 [proxy_cache_valid](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_cache_valid) 获取详细信息. 您可以指定多个逗号分隔的值: `200 202 10m, 401 5m`. 默认为 `200 202 401 5m`.
* `nginx.ingress.kubernetes.io/auth-snippet`:
  `<Auth_Snippet>` 指定用于外部身份验证的自定义代码段, 例如

```yaml
nginx.ingress.kubernetes.io/auth-url: http://foo.com/external-auth
nginx.ingress.kubernetes.io/auth-snippet: |
    proxy_set_header Foo-Header 42;
```
> 注意: `nginx.ingress.kubernetes.io/auth-snippet` 为可选注解. 他只能和 `nginx.ingress.kubernetes.io/auth-url`一起配置 且当 `nginx.ingress.kubernetes.io/auth-url` 配置时被忽略

!!! 例子
    请查看 [external-auth](../../examples/auth/external-auth/README.md) 示例.

#### Global External Authentication

默认情况下，如果在nginx ConfigMap中设置了`global-auth-url`，则控制器会将所有请求重定向到提供身份验证的现有服务. 如果您想为该ingress禁用此行为, 你可以在nginx configmap中指定 `enable-global-auth: "false"`.
`nginx.ingress.kubernetes.io/enable-global-auth`:
   指示是否应将GlobalExternalAuth配置应用于此Ingress规则. 默认设置为 `"true"`.

!!! 更多信息请查看 [global-auth-url](./configmap.md#global-auth-url).

### Rate limiting

这些注解定义了对连接和传输速率的限制.  这些可以用来减轻 [DDoS Attacks](https://www.nginx.com/blog/mitigating-ddos-attacks-with-nginx-and-nginx-plus).

* `nginx.ingress.kubernetes.io/limit-connections`: 一个IP地址允许的并发连接数。 超出此限制时，将返回503错误.
* `nginx.ingress.kubernetes.io/limit-rps`: 每秒从给定IP接受的请求数. 突发限制设置为限制的5倍。 当客户超过此限制,  [limit-req-status-code](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/configmap/#limit-req-status-code) ***默认:*** 返回503.
* `nginx.ingress.kubernetes.io/limit-rpm`: 每分钟从给定IP接受的请求数. 突发限制设置为限制的5倍。 当客户超过此限制,  [limit-req-status-code](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/configmap/#limit-req-status-code) ***默认:*** 返回503.
* `nginx.ingress.kubernetes.io/limit-rate-after`: 初始kb，之后将进一步传输对给定连接的响应的速率。
* `nginx.ingress.kubernetes.io/limit-rate`: 每秒发送给给定连接的kb。 零值禁用速率限制
* `nginx.ingress.kubernetes.io/limit-whitelist`: 客户端IP源范围要从速率限制中排除。 该值是逗号分隔的CIDR列表

如果您在一个Ingress规则中指定多个注解，则会按顺序应用限制 `limit-connections`, `limit-rpm`, `limit-rps`.

为所有Ingress规则全局配置设置, the `limit-rate-after` 和 `limit-rate` 必须在 [nginx ConfigMap](./configmap.md#limit-rate)中设置.  在Ingress注解中设置的值将覆盖全局设置.

客户端IP地址将根据使用情况进行设置 [PROXY protocol](./configmap.md#use-proxy-protocol) 或根据 `X-Forwarded-For` 头的值进行设置如果开启 [use-forwarded-headers](./configmap.md#use-forwarded-headers).

### Permanent Redirect

此注释允许返回永久重定向，而不是向上游发送数据.  例如 `nginx.ingress.kubernetes.io/permanent-redirect: https://www.google.com` 会将所有内容重定向到Google.

### Permanent Redirect Code

此注释使您可以修改用于永久重定向的状态代码。 例如 `nginx.ingress.kubernetes.io/permanent-redirect-code: '308'` 会以308返回您的永久重定向

### Temporal Redirect
此注释使您可以返回时间重定向(返回代码302)，而不是将数据发送到上游。. 例如 `nginx.ingress.kubernetes.io/temporal-redirect: https://www.google.com` 会将所有内容重定向到Google，返回码为302(临时重定向)

### SSL Passthrough

该注解 `nginx.ingress.kubernetes.io/ssl-passthrough` 指示控制器直接发送TLS连接到后端，而不是让NGINX解密通信. 另参见 [TLS/HTTPS](../tls.md#ssl-passthrough) 用户指南

!!! 注意
    SSL Passthrough 被 **默认禁用** 并需要使用以下命令参数启动控制器
    [`--enable-ssl-passthrough`](../cli-arguments.md).

!!! 注意
    由于SSL Passthrough在OSI模型(TCP)的第4层而不是第7层(HTTP)上起作用，因此使用SSL Passthrough 使Ingress对象上设置的所有其他注释无效

### Service Upstream

默认情况下，nginx ingress controller使用NGINX上游配置中所有端点(Pod IP /端口)的列表

`nginx.ingress.kubernetes.io/service-upstream` 注解会禁用该行为，而是使用NGINX中的单个上游，service的群集IP和端口.

对于零停机时间部署之类的事情，这可能是理想的，因为它减少了Pod上下时重新加载NGINX配置的需求. 查看 issue [#257](https://github.com/kubernetes/ingress-nginx/issues/257).

#### Known Issues

如果 `service-upstream` 注解被设置需要考虑到以下事项:

* Sticky Sessions 将不起作用，因为仅支持循环负载平衡.
* `proxy_next_upstream` 指令将不会有任何影响，因为如果出错，该请求将不会分派到另一个上游。.

### Server-side HTTPS enforcement through redirect

默认情况下，如果为该入口启用了TLS，则控制器将重定向(308)到HTTPS。
如果要全局禁用此行为，,你可以在 nginx [ConfigMap](./configmap.md#ssl-redirect)设置`ssl-redirect: "false"` .

为特定的入口资源配置此功能， 你可以在特定资源中使用 `nginx.ingress.kubernetes.io/ssl-redirect: "false"`注解


在群集之外(例如AWS ELB)使用SSL卸载时，强制重定向到HTTPS可能很有用
即使没有TLS证书也是如此。
这可以通过在特定资源中使用nginx.ingress.kubernetes.io/force-ssl-redirect:"true"注解来实现。

### Redirect from/to www

在某些情况下，需要从`www.domain.com` 重定向到 `domain.com` ，反之亦然.
使用 `nginx.ingress.kubernetes.io/from-to-www-redirect: "true"`注解开启此功能

!!! 注意
    如果在某个时候创建了一个新的Ingress，且其主机等于选项之一(例如domain.com)，则注释将被省略.

!!! 注意
    对于从HTTPS到HTTPS的重定向是强制性的，在Ingress TLS部分中的Secret中定义的SSL证书包含两个通用名称的FQDN

### Whitelist source range

你可以指定一个客户端IP列表通过 `nginx.ingress.kubernetes.io/whitelist-source-range` 注解.
该值是逗号分隔的列表 [CIDRs](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing), 例如  `10.0.0.0/24,172.10.0.1`.

为所有Ingress规则全局配置此设置, 需要在 [nginx ConfigMap](./configmap.md#whitelist-source-range)中设置`whitelist-source-range`.

!!! 注意
    向Ingress规则添加注释会覆盖所有全局限制。

### Custom timeouts

使用配置configmap可以为与上游服务器的连接设置默认的全局超时。
在某些情况下，要求具有不同的值。 为此，我们提供了允许进行此自定义的注释:

- `nginx.ingress.kubernetes.io/proxy-connect-timeout`
- `nginx.ingress.kubernetes.io/proxy-send-timeout`
- `nginx.ingress.kubernetes.io/proxy-read-timeout`
- `nginx.ingress.kubernetes.io/proxy-next-upstream`
- `nginx.ingress.kubernetes.io/proxy-next-upstream-timeout`
- `nginx.ingress.kubernetes.io/proxy-next-upstream-tries`
- `nginx.ingress.kubernetes.io/proxy-request-buffering`

### Proxy redirect

使用 `nginx.ingress.kubernetes.io/proxy-redirect-from` 和 `nginx.ingress.kubernetes.io/proxy-redirect-to` 注解可以修改被 `Location` 和 `Refresh` 头字段设置的文本通过 [proxied server response](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_redirect)

设置 注解`nginx.ingress.kubernetes.io/proxy-redirect-from`为"off" 或 "default" 禁用 `nginx.ingress.kubernetes.io/proxy-redirect-to`,
否则，必须同时使用两个注释。 请注意，每个注释必须是不带空格的字符串。

默认情况下注解值都为 "off".

### Custom max body size

对于NGINX, 当请求中的大小超过客户端请求正文的最大允许大小时，将向客户端返回413错误. 大小可以通过 [`client_max_body_size`](http://nginx.org/en/docs/http/ngx_http_core_module.html#client_max_body_size)参数配置.

想要对所有ingress规则进行全局设置, `proxy-body-size` 可以在 [nginx ConfigMap](./configmap.md#proxy-body-size)中设置.
要在Ingress规则中使用自定义值，请定义以下注解:

```yaml
nginx.ingress.kubernetes.io/proxy-body-size: 8m
```

### Proxy cookie domain

对代理服务器返回结果设置 [应该在domain属性中更改](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_cookie_domain) "Set-Cookie" 头字段.

想要对所有ingress规则进行全局设置, `proxy-cookie-domain` 可以在[nginx ConfigMap](./configmap.md#proxy-cookie-domain)中设置.

### Proxy cookie path

对代理服务器返回结果设置 [应该在path属性中更改](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_cookie_path) "Set-Cookie" 头字段.

想要对所有ingress规则进行全局设置, `proxy-cookie-path` 可以在 [nginx ConfigMap](./configmap.md#proxy-cookie-path)中设置.

### Proxy buffering

开启或禁用 [`代理缓冲`](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_buffering).
默认情况下，NGINX配置中禁用代理缓冲.

想要对所有ingress规则进行全局设置, `proxy-buffering` 可以在 [nginx ConfigMap](./configmap.md#proxy-buffering)中设置.
要在Ingress规则中使用自定义值，请定义以下注解:

```yaml
nginx.ingress.kubernetes.io/proxy-buffering: "on"
```

### Proxy buffers Number

设置缓冲区数， [`proxy_buffers`](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_buffers) 用于读取从代理服务器收到的响应的第一部分.
默认情况下，代理缓冲区数设置为4

全局配置此设置, `proxy-buffers-number` 可以在 [nginx ConfigMap](./configmap.md#proxy-buffers-number)中设置. 要在Ingress规则中使用自定义值，请定义以下注解:
```yaml
nginx.ingress.kubernetes.io/proxy-buffers-number: "4"
```

### Proxy buffer size

设置缓冲区的大小 [`proxy_buffer_size`](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_buffer_size) 用于读取从代理服务器收到的响应的第一部分.
默认情况下，代理缓冲区大小设置为 "4k"

全局配置此设置,  `proxy-buffer-size` 可以在 [nginx ConfigMap](./configmap.md#proxy-buffer-size)中设置. 要在Ingress规则中使用自定义值，请定义以下注解:
```yaml
nginx.ingress.kubernetes.io/proxy-buffer-size: "8k"
```

### Proxy HTTP version

使用该功能配置用于Nginx反向代理将用于与后端进行通信的 [`proxy_http_version`](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_http_version)
默认情况下，代理缓冲区大小设置为 "1.1".

```yaml
nginx.ingress.kubernetes.io/proxy-http-version: "1.0"
```

### SSL ciphers

指定 [开启 ciphers](http://nginx.org/en/docs/http/ngx_http_ssl_module.html#ssl_ciphers).

使用这个注释将在服务器级别设置`ssl_ciphers`指令。 该配置对主机中的所有路径均有效。

```yaml
nginx.ingress.kubernetes.io/ssl-ciphers: "ALL:!aNULL:!EXPORT56:RC4+RSA:+HIGH:+MEDIUM:+LOW:+SSLv2:+EXP"
```

### Connection proxy header

使用此注释将覆盖NGINX设置的默认连接头.
要在Ingress规则中使用自定义值，请定义注释:

```yaml
nginx.ingress.kubernetes.io/connection-proxy-header: "keep-alive"
```

### Enable Access Log

默认情况下，访问日志处于启用状态，但是在某些情况下，可能需要为给定ingress禁用访问日志
。 为此，请使用注释:

```yaml
nginx.ingress.kubernetes.io/enable-access-log: "false"
```

### Enable Rewrite Log

默认情况下，未启用重写日志。 在某些情况下，可能需要启用NGINX重写日志。
请注意，重写日志将在notice级别发送到error_log文件。 要启用此功能，请使用注释:

```yaml
nginx.ingress.kubernetes.io/enable-rewrite-log: "true"
```

### X-Forwarded-Prefix Header
要将非标准的`X-Forwarded-Prefix`标头添加到带有字符串值的上游请求中，可以使用以下注释:

```yaml
nginx.ingress.kubernetes.io/x-forwarded-prefix: "/path"
```

### Lua Resty WAF

使用`lua-resty-waf- *`注释，我们可以启用和控制 [lua-resty-waf](https://github.com/p0pr0ck5/lua-resty-waf)
每个location的Web应用防火墙.

以下配置将为相应ingress中定义的路径启用WAF:

```yaml
nginx.ingress.kubernetes.io/lua-resty-waf: "active"
```

除了以上配置，为了在调试模式下运行它，您可以设置 `nginx.ingress.kubernetes.io/lua-resty-waf-debug` 为 `"true"` .
其他可能值 `nginx.ingress.kubernetes.io/lua-resty-waf` 是 `inactive` 和 `simulate`.
在`inactive`模式下，WAF不会执行任何操作，而在`simulate`模式下，如果给定请求有匹配的WAF规则，它将记录一条警告消息。 这对于调试规则并在完全部署之前消除可能的误报很有用。

`lua-resty-waf` 带有预定义的规则集 [https://github.com/p0pr0ck5/lua-resty-waf/tree/84b4f40362500dd0cb98b9e71b5875cb1a40f1ad/rules](https://github.com/p0pr0ck5/lua-resty-waf/tree/84b4f40362500dd0cb98b9e71b5875cb1a40f1ad/rules) 涵盖了ModSecurity CRS.
你可以使用 `nginx.ingress.kubernetes.io/lua-resty-waf-ignore-rulesets` 忽略这些规则集的子集. 举例:

```yaml
nginx.ingress.kubernetes.io/lua-resty-waf-ignore-rulesets: "41000_sqli, 42000_xss"
```

将忽略两个提到的规则集.

还可以使用`nginx.ingress.kubernetes.io/lua-resty-waf-extra-rules`注解为每个入口配置自定义WAF规则. 例如，以下代码片段将配置WAF规则，以拒绝查询字符串值包含单词`foo`的请求:


```yaml
nginx.ingress.kubernetes.io/lua-resty-waf-extra-rules: '[=[ { "access": [ { "actions": { "disrupt" : "DENY" }, "id": 10001, "msg": "my custom rule", "operator": "STR_CONTAINS", "pattern": "foo", "vars": [ { "parse": [ "values", 1 ], "type": "REQUEST_ARGS" } ] } ], "body_filter": [], "header_filter":[] } ]=]'
```

由于默认允许的内容是 `"text/html", "text/json", "application/json"`
我们可以启用以下注释以允许所有内容类型:


```yaml
nginx.ingress.kubernetes.io/lua-resty-waf-allow-unknown-content-types: "true"
```

lua-resty-waf的默认分数是5，通常会在命中2条默认规则时触发，您可以使用以下注释修改分数阈值:


```yaml
nginx.ingress.kubernetes.io/lua-resty-waf-score-threshold: "10"
```

当在端点中启用HTTPS时，由于resty-lua在处理`multipart`contents时将返回500错误
引用自 [issue](https://github.com/p0pr0ck5/lua-resty-waf/issues/166)

默认设置为 "true"

您可以启用以下注释来解决:

```yaml
nginx.ingress.kubernetes.io/lua-resty-waf-process-multipart-body: "false"
```

有关如何编写WAF规则的详细信息，请参阅 [https://github.com/p0pr0ck5/lua-resty-waf](https://github.com/p0pr0ck5/lua-resty-waf).

[configmap]: ./configmap.md

### ModSecurity

[ModSecurity](http://modsecurity.org/) 是一个开源Web应用程序防火墙. 可以为特定集合启用它ingress locations. 首先必须通过[ConfigMap](./configmap.md#enable-modsecurity)中启用ModSecurity来启用ModSecurity模块。
请注意，这将为所有路径启用ModSecurity，并且必须手动禁用每个路径。

可以使用以下注释启用它:
```yaml
nginx.ingress.kubernetes.io/enable-modsecurity: "true"
```
ModSecurity 将使用以下[recommended configuration](https://github.com/SpiderLabs/ModSecurity/blob/v3/master/modsecurity.conf-recommended)以 "Detection-Only"运行.

你可以通过设置以下注解加载 [OWASP Core Rule Set](https://www.modsecurity.org/CRS/Documentation/)
```yaml
nginx.ingress.kubernetes.io/enable-owasp-core-rules: "true"
```

您可以通过设置以下内容从nginx传递transactionID:
```yaml
nginx.ingress.kubernetes.io/modsecurity-transaction-id: "$request_id"
```

您还可以通过代码段添加自己的modsecurity规则集:
```yaml
nginx.ingress.kubernetes.io/modsecurity-snippet: |
SecRuleEngine On
SecDebugLog /tmp/modsec_debug.log
```

注意: 不过你同时使用 `enable-owasp-core-rules` 和 `modsecurity-snippet` , 仅有
`modsecurity-snippet` 生效. 如果你想包含[OWASP Core Rule Set](https://www.modsecurity.org/CRS/Documentation/) 或
[recommended configuration](https://github.com/SpiderLabs/ModSecurity/blob/v3/master/modsecurity.conf-recommended) 只需要简单声明:
```yaml
nginx.ingress.kubernetes.io/modsecurity-snippet: |
Include /etc/nginx/owasp-modsecurity-crs/nginx-modsecurity.conf
Include /etc/nginx/modsecurity/modsecurity.conf
```

### InfluxDB

我们可以通过使用 `influxdb-*` 注解通过 [nginx-influxdb-module](https://github.com/influxdata/nginx-influxdb-module/) 发送监控请求到influxDB后端暴露的UDP socket.

```yaml
nginx.ingress.kubernetes.io/enable-influxdb: "true"
nginx.ingress.kubernetes.io/influxdb-measurement: "nginx-reqs"
nginx.ingress.kubernetes.io/influxdb-port: "8089"
nginx.ingress.kubernetes.io/influxdb-host: "127.0.0.1"
nginx.ingress.kubernetes.io/influxdb-server-name: "nginx-ingress"
```

对于 `influxdb-host` 参数有两个选项:

- 使用配置了 [UDP protocol](https://docs.influxdata.com/influxdb/v1.5/supported_protocols/udp/) 的InfluxDB服务器.
- 将Telegraf作为Sidecar代理部署到配置有侦听UDP的Ingress控制器 [socket listener input](https://github.com/influxdata/telegraf/tree/release-1.6/plugins/inputs/socket_listener)，
然后使用 [outputs plugins](https://github.com/influxdata/telegraf/tree/release-1.7/plugins/outputs) like InfluxDB, Apache Kafka,
Prometheus, etc.. (推荐)等输出

重要的是要记住，此阶段没有DNS解析器，因此您必须进行配置
配置一个IP通过`nginx.ingress.kubernetes.io/influxdb-host`. 如果您将Influx或Telegraf部署为sidecar(同一pod中的另一个容器)，这将变得很简单，因为您可以直接使用`127.0.0.1`。

### Backend Protocol

使用`backend-protocol`注释可以指示NGINX应该如何与后端服务通信。 (替换旧版本中的`secure-backends`)
允许的值: HTTP, HTTPS, GRPC, GRPCS and AJP

默认nginx使用 `HTTP`.

例如:

```yaml
nginx.ingress.kubernetes.io/backend-protocol: "HTTPS"
```

### Use Regex

!!! 注意
当此注解和设置为`cookie`的 `nginx.ingress.kubernetes.io/affinity`注解同时使用时,  `nginx.ingress.kubernetes.io/session-cookie-path` 必须被设置; session Cookie路径不支持正则表达式.

使用 `nginx.ingress.kubernetes.io/use-regex` 注解将表明Ingress上定义的路径是否使用正则表达式.  默认为 `false`.

以下内容表明正在使用正则表达式路径:
```yaml
nginx.ingress.kubernetes.io/use-regex: "true"
```

以下内容将表明正则表达式路径为 __不__ 被使用:
```yaml
nginx.ingress.kubernetes.io/use-regex: "false"
```

方此注解设置为 `true`, 不区分大小写的正则表达式 [location modifier](https://nginx.org/en/docs/http/ngx_http_core_module.html#location) 将在给定主机的所有路径上强制执行，无论它们在什么ingress定义.

另外, 如果 [`rewrite-target` 注解](#rewrite) 对于任何给定host的ingress, 然后不区分大小写的正则表达式 [location modifier](https://nginx.org/en/docs/http/ngx_http_core_module.html#location) 
将在给定主机的所有路径上强制执行，无论它们在哪个ingress定义.

请在使用该修饰符之前阅读[ingress path matching](../ingress-path-matching.md) .

### Satisfy

默认情况下，请求将需要满足所有身份验证要求才能被允许。 通过使用此注释，
根据配置值，允许满足任何或所有身份验证要求的请求。

```yaml
nginx.ingress.kubernetes.io/satisfy: "any"
```

### Mirror

允许将请求镜像到镜像后端。 镜像后端的响应将被忽略。 此功能很有用，可以查看请求在"test"后端中的反应。

您可以通过应用以下内容将请求镜像到入口上的`/mirror`路径:

```yaml
nginx.ingress.kubernetes.io/mirror-uri: "/mirror"
```

镜像路径可以定义为单独的入口资源:

```
location = /mirror {
    internal;
    proxy_pass http://test_backend;
}
```

默认情况下，请求正文被发送到镜像后端，但是可以通过应用将其关闭:

```yaml
nginx.ingress.kubernetes.io/mirror-request-body: "off"
```

**注意:** mirror指令将应用于入口资源内的所有路径。
          
发送到镜像的请求链接到原始请求。 如果您的镜像后端速度较慢，那么原始请求将受到限制

获取 mirror module 的更多信息请查看 https://nginx.org/en/docs/http/ngx_http_mirror_module.html
