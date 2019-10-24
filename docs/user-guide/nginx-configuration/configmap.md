# ConfigMaps

ConfigMap允许您将配置工件与图像内容分离，以使容器化的应用程序具有可移植性。

ConfigMap API资源将配置数据存储为键值对。 数据提供系统的配置
Nginx控制器的组件。

为了覆盖nginx-controller配置值，可以在 [config.go](https://github.com/kubernetes/ingress-nginx/blob/master/internal/ingress/controller/config/config.go)中看到,
您可以将键值对添加到config-map的data部分。 例如

```yaml
data:
  map-hash-bucket-size: "128"
  ssl-protocols: SSLv2
```

!!! 重要
    ConfigMap中的键和值只能是字符串。这意味着我们想要一个带有布尔值的值，我们需要用引号将它们引号，例如'true'或'false'。 数字也一样，例如'100'。

    "Slice" 类型 (可以定义为 `[]string` 或 `[]int` 可以以逗号分隔的字符串形式提供.

## Configuration options

下表显示了配置选项的名称，类型和默认值:

|name|type|default|
|:---|:---|:------|
|[add-headers](#add-headers)|string|""|
|[allow-backend-server-header](#allow-backend-server-header)|bool|"false"|
|[hide-headers](#hide-headers)|string array|empty|
|[access-log-params](#access-log-params)|string|""|
|[access-log-path](#access-log-path)|string|"/var/log/nginx/access.log"|
|[enable-access-log-for-default-backend](#enable-access-log-for-default-backend)|bool|"false"|
|[error-log-path](#error-log-path)|string|"/var/log/nginx/error.log"|
|[enable-modsecurity](#enable-modsecurity)|bool|"false"|
|[modsecurity-snippet](#modsecurity-snippet)|string|""|
|[enable-owasp-modsecurity-crs](#enable-owasp-modsecurity-crs)|bool|"false"|
|[client-header-buffer-size](#client-header-buffer-size)|string|"1k"|
|[client-header-timeout](#client-header-timeout)|int|60|
|[client-body-buffer-size](#client-body-buffer-size)|string|"8k"|
|[client-body-timeout](#client-body-timeout)|int|60|
|[disable-access-log](#disable-access-log)|bool|false|
|[disable-ipv6](#disable-ipv6)|bool|false|
|[disable-ipv6-dns](#disable-ipv6-dns)|bool|false|
|[enable-underscores-in-headers](#enable-underscores-in-headers)|bool|false|
|[ignore-invalid-headers](#ignore-invalid-headers)|bool|true|
|[retry-non-idempotent](#retry-non-idempotent)|bool|"false"|
|[error-log-level](#error-log-level)|string|"notice"|
|[http2-max-field-size](#http2-max-field-size)|string|"4k"|
|[http2-max-header-size](#http2-max-header-size)|string|"16k"|
|[http2-max-requests](#http2-max-requests)|int|1000|
|[hsts](#hsts)|bool|"true"|
|[hsts-include-subdomains](#hsts-include-subdomains)|bool|"true"|
|[hsts-max-age](#hsts-max-age)|string|"15724800"|
|[hsts-preload](#hsts-preload)|bool|"false"|
|[keep-alive](#keep-alive)|int|75|
|[keep-alive-requests](#keep-alive-requests)|int|100|
|[large-client-header-buffers](#large-client-header-buffers)|string|"4 8k"|
|[log-format-escape-json](#log-format-escape-json)|bool|"false"|
|[log-format-upstream](#log-format-upstream)|string|`$remote_addr - $remote_user [$time_local] "$request" $status $body_bytes_sent "$http_referer" "$http_user_agent" $request_length $request_time [$proxy_upstream_name] [$proxy_alternative_upstream_name] $upstream_addr $upstream_response_length $upstream_response_time $upstream_status $req_id`|
|[log-format-stream](#log-format-stream)|string|`[$remote_addr] [$time_local] $protocol $status $bytes_sent $bytes_received $session_time`|
|[enable-multi-accept](#enable-multi-accept)|bool|"true"|
|[max-worker-connections](#max-worker-connections)|int|16384|
|[max-worker-open-files](#max-worker-open-files)|int|0|
|[map-hash-bucket-size](#max-hash-bucket-size)|int|64|
|[nginx-status-ipv4-whitelist](#nginx-status-ipv4-whitelist)|[]string|"127.0.0.1"|
|[nginx-status-ipv6-whitelist](#nginx-status-ipv6-whitelist)|[]string|"::1"|
|[proxy-real-ip-cidr](#proxy-real-ip-cidr)|[]string|"0.0.0.0/0"|
|[proxy-set-headers](#proxy-set-headers)|string|""|
|[server-name-hash-max-size](#server-name-hash-max-size)|int|1024|
|[server-name-hash-bucket-size](#server-name-hash-bucket-size)|int|`<size of the processor’s cache line>`
|[proxy-headers-hash-max-size](#proxy-headers-hash-max-size)|int|512|
|[proxy-headers-hash-bucket-size](#proxy-headers-hash-bucket-size)|int|64|
|[reuse-port](#reuse-port)|bool|"true"|
|[server-tokens](#server-tokens)|bool|"true"|
|[ssl-ciphers](#ssl-ciphers)|string|"ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256"|
|[ssl-ecdh-curve](#ssl-ecdh-curve)|string|"auto"|
|[ssl-dh-param](#ssl-dh-param)|string|""|
|[ssl-protocols](#ssl-protocols)|string|"TLSv1.2"|
|[ssl-session-cache](#ssl-session-cache)|bool|"true"|
|[ssl-session-cache-size](#ssl-session-cache-size)|string|"10m"|
|[ssl-session-tickets](#ssl-session-tickets)|bool|"true"|
|[ssl-session-ticket-key](#ssl-session-ticket-key)|string|`<Randomly Generated>`
|[ssl-session-timeout](#ssl-session-timeout)|string|"10m"|
|[ssl-buffer-size](#ssl-buffer-size)|string|"4k"|
|[use-proxy-protocol](#use-proxy-protocol)|bool|"false"|
|[proxy-protocol-header-timeout](#proxy-protocol-header-timeout)|string|"5s"|
|[use-gzip](#use-gzip)|bool|"true"|
|[use-geoip](#use-geoip)|bool|"true"|
|[use-geoip2](#use-geoip2)|bool|"false"|
|[enable-brotli](#enable-brotli)|bool|"false"|
|[brotli-level](#brotli-level)|int|4|
|[brotli-types](#brotli-types)|string|"application/xml+rss application/atom+xml application/javascript application/x-javascript application/json application/rss+xml application/vnd.ms-fontobject application/x-font-ttf application/x-web-app-manifest+json application/xhtml+xml application/xml font/opentype image/svg+xml image/x-icon text/css text/javascript text/plain text/x-component"|
|[use-http2](#use-http2)|bool|"true"|
|[gzip-level](#gzip-level)|int|5|
|[gzip-types](#gzip-types)|string|"application/atom+xml application/javascript application/x-javascript application/json application/rss+xml application/vnd.ms-fontobject application/x-font-ttf application/x-web-app-manifest+json application/xhtml+xml application/xml font/opentype image/svg+xml image/x-icon text/css text/javascript text/plain text/x-component"|
|[worker-processes](#worker-processes)|string|`<Number of CPUs>`|
|[worker-cpu-affinity](#worker-cpu-affinity)|string|""|
|[worker-shutdown-timeout](#worker-shutdown-timeout)|string|"240s"|
|[load-balance](#load-balance)|string|"round_robin"|
|[variables-hash-bucket-size](#variables-hash-bucket-size)|int|128|
|[variables-hash-max-size](#variables-hash-max-size)|int|2048|
|[upstream-keepalive-connections](#upstream-keepalive-connections)|int|32|
|[upstream-keepalive-timeout](#upstream-keepalive-timeout)|int|60|
|[upstream-keepalive-requests](#upstream-keepalive-requests)|int|100|
|[limit-conn-zone-variable](#limit-conn-zone-variable)|string|"$binary_remote_addr"|
|[proxy-stream-timeout](#proxy-stream-timeout)|string|"600s"|
|[proxy-stream-responses](#proxy-stream-responses)|int|1|
|[bind-address](#bind-address)|[]string|""|
|[use-forwarded-headers](#use-forwarded-headers)|bool|"false"|
|[forwarded-for-header](#forwarded-for-header)|string|"X-Forwarded-For"|
|[compute-full-forwarded-for](#compute-full-forwarded-for)|bool|"false"|
|[proxy-add-original-uri-header](#proxy-add-original-uri-header)|bool|"false"|
|[generate-request-id](#generate-request-id)|bool|"true"|
|[enable-opentracing](#enable-opentracing)|bool|"false"|
|[zipkin-collector-host](#zipkin-collector-host)|string|""|
|[zipkin-collector-port](#zipkin-collector-port)|int|9411|
|[zipkin-service-name](#zipkin-service-name)|string|"nginx"|
|[zipkin-sample-rate](#zipkin-sample-rate)|float|1.0|
|[jaeger-collector-host](#jaeger-collector-host)|string|""|
|[jaeger-collector-port](#jaeger-collector-port)|int|6831|
|[jaeger-service-name](#jaeger-service-name)|string|"nginx"|
|[jaeger-sampler-type](#jaeger-sampler-type)|string|"const"|
|[jaeger-sampler-param](#jaeger-sampler-param)|string|"1"|
|[jaeger-sampler-host](#jaeger-sampler-host)|string|"http://127.0.0.1"|
|[jaeger-sampler-port](#jaeger-sampler-port)|int|5778|
|[jaeger-trace-context-header-name](#jaeger-trace-context-header-name)|string|uber-trace-id|
|[jaeger-debug-header](#jaeger-debug-header)|string|uber-debug-id|
|[jaeger-baggage-header](#jaeger-baggage-header)|string|jaeger-baggage|
|[jaeger-trace-baggage-header-prefix](#jaeger-trace-baggage-header-prefix)|string|uberctx-|
|[main-snippet](#main-snippet)|string|""|
|[http-snippet](#http-snippet)|string|""|
|[server-snippet](#server-snippet)|string|""|
|[location-snippet](#location-snippet)|string|""|
|[custom-http-errors](#custom-http-errors)|[]int|[]int{}|
|[proxy-body-size](#proxy-body-size)|string|"1m"|
|[proxy-connect-timeout](#proxy-connect-timeout)|int|5|
|[proxy-read-timeout](#proxy-read-timeout)|int|60|
|[proxy-send-timeout](#proxy-send-timeout)|int|60|
|[proxy-buffers-number](#proxy-buffers-number)|int|4|
|[proxy-buffer-size](#proxy-buffer-size)|string|"4k"|
|[proxy-cookie-path](#proxy-cookie-path)|string|"off"|
|[proxy-cookie-domain](#proxy-cookie-domain)|string|"off"|
|[proxy-next-upstream](#proxy-next-upstream)|string|"error timeout"|
|[proxy-next-upstream-timeout](#proxy-next-upstream-timeout)|int|0|
|[proxy-next-upstream-tries](#proxy-next-upstream-tries)|int|3|
|[proxy-redirect-from](#proxy-redirect-from)|string|"off"|
|[proxy-request-buffering](#proxy-request-buffering)|string|"on"|
|[ssl-redirect](#ssl-redirect)|bool|"true"|
|[whitelist-source-range](#whitelist-source-range)|[]string|[]string{}|
|[skip-access-log-urls](#skip-access-log-urls)|[]string|[]string{}|
|[limit-rate](#limit-rate)|int|0|
|[limit-rate-after](#limit-rate-after)|int|0|
|[lua-shared-dicts](#lua-shared-dicts)|string|""|
|[http-redirect-code](#http-redirect-code)|int|308|
|[proxy-buffering](#proxy-buffering)|string|"off"|
|[limit-req-status-code](#limit-req-status-code)|int|503|
|[limit-conn-status-code](#limit-conn-status-code)|int|503|
|[no-tls-redirect-locations](#no-tls-redirect-locations)|string|"/.well-known/acme-challenge"|
|[global-auth-url](#global-auth-url)|string|""|
|[global-auth-method](#global-auth-method)|string|""|
|[global-auth-signin](#global-auth-signin)|string|""|
|[global-auth-response-headers](#global-auth-response-headers)|string|""|
|[global-auth-request-redirect](#global-auth-request-redirect)|string|""|
|[global-auth-snippet](#global-auth-snippet)|string|""|
|[global-auth-cache-key](#global-auth-cache-key)|string|""|
|[global-auth-cache-duration](#global-auth-cache-duration)|string|"200 202 401 5m"|
|[no-auth-locations](#no-auth-locations)|string|"/.well-known/acme-challenge"|
|[block-cidrs](#block-cidrs)|[]string|""|
|[block-user-agents](#block-user-agents)|[]string|""|
|[block-referers](#block-referers)|[]string|""|

## add-headers

在将流量发送到客户端之前，从命名的configmap设置自定义标头. 查看 [proxy-set-headers](#proxy-set-headers). [example](https://github.com/kubernetes/ingress-nginx/tree/master/docs/examples/customization/custom-headers)

## allow-backend-server-header

启用从后端返回标头服务器，而不是从通用nginx字符串返回. _**默认:**_ 禁用

## hide-headers

设置不会从上游服务器传递到客户端响应的其他标头.
_**default:**_ 空

_引用:_
[http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_hide_header](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_hide_header)

## access-log-params

access_log的其他参数。 例如, buffer=16k, gzip, flush=1m

_引用:_
[http://nginx.org/en/docs/http/ngx_http_log_module.html#access_log](http://nginx.org/en/docs/http/ngx_http_log_module.html#access_log)

## access-log-path

访问日志路径。 默认查看 `/var/log/nginx/access.log` .

__Note:__ 该文件 `/var/log/nginx/access.log` 是 `/dev/stdout`的软连接

## enable-access-log-for-default-backend

启用对默认后端的日志记录访问. _**default:**_ is disabled.

## error-log-path

错误日志路径. 默认查看 `/var/log/nginx/error.log`.

__Note:__ 该文件 `/var/log/nginx/error.log` 是 `/dev/stderr`的软连接

_引用:_
[http://nginx.org/en/docs/ngx_core_module.html#error_log](http://nginx.org/en/docs/ngx_core_module.html#error_log)

## enable-modsecurity

为NGINX启用modsecurity模块. _**default:**_ is disabled

## enable-owasp-modsecurity-crs

启用OWASP ModSecurity核心规则集 (CRS). _**default:**_ is disabled

## modsecurity-snippet

Adds custom rules to modsecurity section of nginx configration

## client-header-buffer-size

向Nginx配置的modsecurity部分添加自定义规则

_References:_
[http://nginx.org/en/docs/http/ngx_http_core_module.html#client_header_buffer_size](http://nginx.org/en/docs/http/ngx_http_core_module.html#client_header_buffer_size)

## client-header-timeout

定义读取客户端请求标头的超时(以秒为单位)

_References:_
[http://nginx.org/en/docs/http/ngx_http_core_module.html#client_header_timeout](http://nginx.org/en/docs/http/ngx_http_core_module.html#client_header_timeout)

## client-body-buffer-size

设置用于读取客户端请求正文的缓冲区大小。

_References:_
[http://nginx.org/en/docs/http/ngx_http_core_module.html#client_body_buffer_size](http://nginx.org/en/docs/http/ngx_http_core_module.html#client_body_buffer_size)

## client-body-timeout

62/5000
定义读取客户端请求正文的超时时间(以秒为单位)。

_References:_
[http://nginx.org/en/docs/http/ngx_http_core_module.html#client_body_timeout](http://nginx.org/en/docs/http/ngx_http_core_module.html#client_body_timeout)

## disable-access-log

禁用整个Ingress Controller的访问日志. _**default:**_ '"false"'

_References:_
[http://nginx.org/en/docs/http/ngx_http_log_module.html#access_log](http://nginx.org/en/docs/http/ngx_http_log_module.html#access_log)

## disable-ipv6

禁用监听IPV6. _**default:**_ `false`; IPv6 listening is enabled

## disable-ipv6-dns

为nginx DNS解析器禁用IPV6. _**default:**_ `false`; IPv6 resolving enabled.

## enable-underscores-in-headers

在header中启用下划线. _**default:**_ is disabled

## ignore-invalid-headers

设置是否忽略具有无效名称的头字段。
_**default:**_ is enabled

## retry-non-idempotent

从1.9.13开始，如果上游服务器发生错误，NGINX将不重试非幂等请求(POST，LOCK，PATCH)。 可以使用值'true'恢复以前的行为。

## error-log-level

配置错误的日志记录级别。 上面的日志级别按严重性从高到低的顺序列出。

_References:_
[http://nginx.org/en/docs/ngx_core_module.html#error_log](http://nginx.org/en/docs/ngx_core_module.html#error_log)

## http2-max-field-size

限制HPACK压缩的请求标头字段的最大大小。

_References:_
[https://nginx.org/en/docs/http/ngx_http_v2_module.html#http2_max_field_size](https://nginx.org/en/docs/http/ngx_http_v2_module.html#http2_max_field_size)

## http2-max-header-size

限制HPACK解压缩后整个请求标头列表的最大大小。

_References:_
[https://nginx.org/en/docs/http/ngx_http_v2_module.html#http2_max_header_size](https://nginx.org/en/docs/http/ngx_http_v2_module.html#http2_max_header_size)

## http2-max-requests

设置可以通过一个HTTP /2连接提供服务的最大请求数(包括推送请求)，此后下一个客户端请求将导致连接关闭以及需要建立新连接。

_References:_
[http://nginx.org/en/docs/http/ngx_http_v2_module.html#http2_max_requests](http://nginx.org/en/docs/http/ngx_http_v2_module.html#http2_max_requests)

## hsts

在运行SSL的服务器中启用或禁用标头HSTS。
HTTP严格传输安全性(通常缩写为HSTS)是一项安全功能(HTTP标头)，它告诉浏览器仅应使用HTTPS而不是HTTP进行通信。 它提供了针对协议降级攻击和cookie盗窃的保护。

_References:_

- [https://developer.mozilla.org/en-US/docs/Web/Security/HTTP_strict_transport_security](https://developer.mozilla.org/en-US/docs/Web/Security/HTTP_strict_transport_security)
- [https://blog.qualys.com/securitylabs/2016/03/28/the-importance-of-a-proper-http-strict-transport-security-implementation-on-your-web-server](https://blog.qualys.com/securitylabs/2016/03/28/the-importance-of-a-proper-http-strict-transport-security-implementation-on-your-web-server)

## hsts-include-subdomains

在服务器名称的所有子域中启用或禁用HSTS。

## hsts-max-age

设置浏览器记住该站点仅使用HTTPS访问的时间(以秒为单位)。

## hsts-preload

启用或禁用HSTS功能中的preload属性(启用时)

## keep-alive

设置保持活动的客户端连接在服务器端保持打开状态的时间。 零值将禁用保持活动状态的客户端连接。

_References:_
[http://nginx.org/en/docs/http/ngx_http_core_module.html#keepalive_timeout](http://nginx.org/en/docs/http/ngx_http_core_module.html#keepalive_timeout)

## keep-alive-requests

设置可以通过一个保持活动连接服务的最大请求数。

_References:_
[http://nginx.org/en/docs/http/ngx_http_core_module.html#keepalive_requests](http://nginx.org/en/docs/http/ngx_http_core_module.html#keepalive_requests)

## large-client-header-buffers

设置用于读取大型客户端请求标头的缓冲区的最大数量和大小. _**default:**_ 4 8k

_References:_
[http://nginx.org/en/docs/http/ngx_http_core_module.html#large_client_header_buffers](http://nginx.org/en/docs/http/ngx_http_core_module.html#large_client_header_buffers)

## log-format-escape-json

设置转义参数是否允许JSON("true")或在变量中转义的默认字符("false")设置nginx [日志格式](http://nginx.org/en/docs/http/ngx_http_log_module.html#log_format).

## log-format-upstream

设置nginx [日志格式](http://nginx.org/en/docs/http/ngx_http_log_module.html#log_format).
例如json输出

```json

log-format-upstream: '{"time": "$time_iso8601", "remote_addr": "$proxy_protocol_addr", "x-forward-for": "$proxy_add_x_forwarded_for", "request_id": "$req_id",
  "remote_user": "$remote_user", "bytes_sent": $bytes_sent, "request_time": $request_time, "status":$status, "vhost": "$host", "request_proto": "$server_protocol",
  "path": "$uri", "request_query": "$args", "request_length": $request_length, "duration": $request_time,"method": "$request_method", "http_referrer": "$http_referer",
  "http_user_agent": "$http_user_agent" }'
```

请查看 [log-format](log-format.md) 从而定义各个字段.

## log-format-stream

查看nginx [stream format](https://nginx.org/en/docs/stream/ngx_stream_log_module.html#log_format).

## enable-multi-accept

如果禁用，worker进程将一次接受一个新连接。 否则，工作进程将一次接受所有新连接。
_**default:**_ true

_References:_
[http://nginx.org/en/docs/ngx_core_module.html#multi_accept](http://nginx.org/en/docs/ngx_core_module.html#multi_accept)

## max-worker-connections

设置 单个worker打开的[最大同时连接数](http://nginx.org/en/docs/ngx_core_module.html#worker_connections).
0 讲使用值 [max-worker-open-files](#max-worker-open-files).
_**default:**_ 16384

!!! 小提示
    在高负载的情况下使用0可以提高性能，但以增加RAM利用率(甚至在空闲时)为代价。

## max-worker-open-files

设置每个工作进程可以打开的 [最大文件数](http://nginx.org/en/docs/ngx_core_module.html#worker_rlimit_nofile) that can be opened by each worker process.
默认为0以为着 "最大打开文件数 (系统限制) /[worker-processes](#worker-processes) - 1024".
_**default:**_ 0

## map-hash-bucket-size

28/5000
设置桶的尺寸 [map variables hash tables](http://nginx.org/en/docs/http/ngx_http_map_module.html#map_hash_bucket_size). 单独设置哈希表的详细信息 [文档](http://nginx.org/en/docs/hash.html).

## proxy-real-ip-cidr

如果启用了use-proxy-protocol，则proxy-real-ip-cidr定义默认的外部负载均衡器的IP /网络地址

## proxy-set-headers

在将流量发送到后端之前，从命名的configmap设置自定义标头。 值格式为名称空间/名称.  查看 [example](https://github.com/kubernetes/ingress-nginx/tree/master/docs/examples/customization/custom-headers)

## server-name-hash-max-size

设置最大尺寸 [server names hash tables](http://nginx.org/en/docs/http/ngx_http_core_module.html#server_names_hash_max_size) 用于服务器名称，map指令的值，MIME类型，请求标头字符串的名称等。.

_References:_
[http://nginx.org/en/docs/hash.html](http://nginx.org/en/docs/hash.html)

## server-name-hash-bucket-size

设置服务器名称哈希表的存储桶大小。

_References:_

- [http://nginx.org/en/docs/hash.html](http://nginx.org/en/docs/hash.html)
- [http://nginx.org/en/docs/http/ngx_http_core_module.html#server_names_hash_bucket_size](http://nginx.org/en/docs/http/ngx_http_core_module.html#server_names_hash_bucket_size)

## proxy-headers-hash-max-size

设置代理标头哈希表的最大大小。

_References:_

- [http://nginx.org/en/docs/hash.html](http://nginx.org/en/docs/hash.html)
- [https://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_headers_hash_max_size](https://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_headers_hash_max_size)

## reuse-port

指示NGINX为每个工作进程创建一个单独的侦听套接字(使用SO_REUSEPORT套接字选项)，从而允许内核在工作进程之间分配传入的连接
_**default:**_ true

## proxy-headers-hash-bucket-size

设置代理标头哈希表的存储桶大小。

_References:_

- [http://nginx.org/en/docs/hash.html](http://nginx.org/en/docs/hash.html)
- [https://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_headers_hash_bucket_size](https://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_headers_hash_bucket_size)

## server-tokens

在响应中发送nginx Server标头，并在错误页面中显示NGINX版本. _**default:**_ is enabled

## ssl-ciphers

设置启用的 [ciphers](http://nginx.org/en/docs/http/ngx_http_ssl_module.html#ssl_ciphers) 列表. ciphers以OpenSSL库可以理解的格式指定.

默认cipher列表:
 `ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256`.

密码套件的排序非常重要，因为它决定了优先选择哪些算法. 上面的建议优先考虑了提供完美 [forward secrecy](https://wiki.mozilla.org/Security/Server_Side_TLS#Forward_Secrecy)算法.

请检查 [Mozilla SSL Configuration Generator](https://mozilla.github.io/server-side-tls/ssl-config-generator/).

## ssl-ecdh-curve

指定ECDHE ciphers的曲线.

_References:_
[http://nginx.org/en/docs/http/ngx_http_ssl_module.html#ssl_ecdh_curve](http://nginx.org/en/docs/http/ngx_http_ssl_module.html#ssl_ecdh_curve)

## ssl-dh-param

设置包含Diffie-Hellman密钥的机密名称，以帮助实现"Perfect Forward Secrecy"。

_References:_

- [https://wiki.openssl.org/index.php/Diffie-Hellman_parameters](https://wiki.openssl.org/index.php/Diffie-Hellman_parameters)
- [https://wiki.mozilla.org/Security/Server_Side_TLS#DHE_handshake_and_dhparam](https://wiki.mozilla.org/Security/Server_Side_TLS#DHE_handshake_and_dhparam)
- [http://nginx.org/en/docs/http/ngx_http_ssl_module.html#ssl_dhparam](http://nginx.org/en/docs/http/ngx_http_ssl_module.html#ssl_dhparam)

## ssl-protocols

设置要使用的 [SSL protocols](http://nginx.org/en/docs/http/ngx_http_ssl_module.html#ssl_protocols) . 默认值为: `TLSv1.2`.

请使用 `https://ssllabs.com/ssltest/analyze.html` 或 `https://testssl.sh`检查配置结果.

## ssl-early-data

启用或禁用TLS 1.3 [early data](https://tools.ietf.org/html/rfc8446#section-2.3)

这要求 `ssl-protocols` 已启用 `TLSv1.3`.

[ssl_early_data](http://nginx.org/en/docs/http/ngx_http_ssl_module.html#ssl_early_data). The default is: `false`.

## ssl-session-cache

启用或禁用工作进程之间共享 [SSL cache](http://nginx.org/en/docs/http/ngx_http_ssl_module.html#ssl_session_cache) 的使用.

## ssl-session-cache-size

设置所有工作进程之间的 [SSL shared session cache](http://nginx.org/en/docs/http/ngx_http_ssl_module.html#ssl_session_cache) between all worker processes.

## ssl-session-tickets

启用或禁用会话恢复通过 [TLS session tickets](http://nginx.org/en/docs/http/ngx_http_ssl_module.html#ssl_session_tickets).

## ssl-session-ticket-key

设置用于加密和解密TLS会话票证的密钥。该值必须是有效的base64字符串。要创建票证: `openssl rand 80 | openssl enc -A -base64`

[TLS session ticket-key](http://nginx.org/en/docs/http/ngx_http_ssl_module.html#ssl_session_tickets), by default, a randomly generated key is used.

## ssl-session-timeout

设置客户可以在这段时间内 [重用 session](http://nginx.org/en/docs/http/ngx_http_ssl_module.html#ssl_session_timeout) 存储在缓存中的参数.

## ssl-buffer-size

设置 [SSL buffer](http://nginx.org/en/docs/http/ngx_http_ssl_module.html#ssl_buffer_size) 发送数据的缓冲大小. 默认的4k可以帮助NGINX缩短TLS首个字节的时间 (TTTFB).

_引用:_
[https://www.igvita.com/2013/12/16/optimizing-nginx-tls-time-to-first-byte/](https://www.igvita.com/2013/12/16/optimizing-nginx-tls-time-to-first-byte/)

## use-proxy-protocol

启用或禁用 [PROXY protocol](https://www.nginx.com/resources/admin-guide/proxy-protocol/) 接收客户端IP (真实IP地址) 信息通过代理服务器和负载均衡器(例如HAProxy和Amazon Elastic Load Balancer)传递 (ELB).

## proxy-protocol-header-timeout

设置用于接收代理协议标头的超时值. 默认值为5秒可防止TLS直通处理程序无限期地等待断开的连接。.
_**default:**_ 5s

## use-gzip

使用启用或禁用HTTP响应的压缩 ["gzip" module](http://nginx.org/en/docs/http/ngx_http_gzip_module.html). 要压缩的MIME类型由由 [gzip-types](#gzip-types)控制. _**default:**_ true

## use-geoip

启用或禁用 ["geoip" module](http://nginx.org/en/docs/http/ngx_http_geoip_module.html) 它使用预编译的MaxMind数据库创建变量，其值取决于客户端IP地址。.
_**default:**_ true

> __Note:__ MaxMind legacy databases are discontinued and will not receive updates after 2019-01-02, cf. [discontinuation notice](https://support.maxmind.com/geolite-legacy-discontinuation-notice/). Consider [use-geoip2](#use-geoip2) below.

## use-geoip2

为NGINX启用 [geoip2 module](https://github.com/leev/ngx_http_geoip2_module).
_**default:**_ false

## enable-brotli

使用启用或禁用HTTP响应的压缩 ["brotli" module](https://github.com/google/ngx_brotli).
要压缩的默认mime类型列表为: `application/xml+rss application/atom+xml application/javascript application/x-javascript application/json application/rss+xml application/vnd.ms-fontobject application/x-font-ttf application/x-web-app-manifest+json application/xhtml+xml application/xml font/opentype image/svg+xml image/x-icon text/css text/plain text/x-component`. _**default:**_ is disabled

> __Note:__ Brotli 不能在 Safari < 11运行. 获取更多信息查看 [https://caniuse.com/#feat=brotli](https://caniuse.com/#feat=brotli)

## brotli-level

设置将使用的Brotli压缩级别. _**default:**_ 4

## brotli-types

设置将由brotli即时压缩的MIME类型.
_**default:**_ `application/xml+rss application/atom+xml application/javascript application/x-javascript application/json application/rss+xml application/vnd.ms-fontobject application/x-font-ttf application/x-web-app-manifest+json application/xhtml+xml application/xml font/opentype image/svg+xml image/x-icon text/css text/plain text/x-component`

## use-http2

开启或禁用 [HTTP/2](http://nginx.org/en/docs/http/ngx_http_v2_module.html) support in secure connections.

## gzip-level

设置将使用的gzip压缩级别. _**default:**_ 5

## gzip-types

设置除"text/html"之外的MIME类型以进行压缩。 特殊值"\*"与任何MIME类型匹配。 如果启用了[use-gzip](＃use-gzip)`，则始终会压缩'text/html'类型的响应。
_**default:**_ `application/atom+xml application/javascript application/x-javascript application/json application/rss+xml application/vnd.ms-fontobject application/x-font-ttf application/x-web-app-manifest+json application/xhtml+xml application/xml font/opentype image/svg+xml image/x-icon text/css text/plain text/x-component`.

## worker-processes

设置 [worker processes](http://nginx.org/en/docs/ngx_core_module.html#worker_processes)数量.
默认值"auto"表示可用的CPU内核数.

## worker-cpu-affinity

将工作进程绑定到CPU组. [worker_cpu_affinity](http://nginx.org/en/docs/ngx_core_module.html#worker_cpu_affinity).
默认情况下，辅助进程未绑定到任何特定的CPU。 该值可以是:

- "": 空字符串表示未应用亲和力.
- cpumask: e.g. `0001 0010 0100 1000` 将进程绑定到特定的CPU.
- auto: 自动将工作进程绑定到可用的CPU.

## worker-shutdown-timeout

设置nginx [等待worker优雅停止的超时时间](http://nginx.org/en/docs/ngx_core_module.html#worker_shutdown_timeout). _**default:**_ "240s"

## load-balance

设置用于负载均衡的算法.
该值可以是:

- round_robin: 使用默认 round robin loadbalancer
- ewma: 使用Peak EWMA 用于路由 ([implementation](https://github.com/kubernetes/ingress-nginx/blob/master/rootfs/etc/nginx/lua/balancer/ewma.lua))

默认是`round_robin`.

- 要使用IP或其他变量的一致哈希进行负载平衡，请考虑 `nginx.ingress.kubernetes.io/upstream-hash-by` 注解.
- 要使用会话Cookie进行负载均衡，请考虑 `nginx.ingress.kubernetes.io/affinity` 注解.

_References:_
[http://nginx.org/en/docs/http/load_balancing.html](http://nginx.org/en/docs/http/load_balancing.html)

## variables-hash-bucket-size

设置变量哈希表的存储桶大小.

_References:_
[http://nginx.org/en/docs/http/ngx_http_map_module.html#variables_hash_bucket_size](http://nginx.org/en/docs/http/ngx_http_map_module.html#variables_hash_bucket_size)

## variables-hash-max-size

设置变量哈希表的最大大小.

_References:_
[http://nginx.org/en/docs/http/ngx_http_map_module.html#variables_hash_max_size](http://nginx.org/en/docs/http/ngx_http_map_module.html#variables_hash_max_size)

## upstream-keepalive-connections

激活缓存以连接到上游服务器。 connections参数设置最大空闲数
与上游服务器的保持活动连接，这些连接保留在每个工作进程的缓存中。 当这个数字是
如果超过，则关闭最近最少使用的连接
_**default:**_ 32

_References:_
[http://nginx.org/en/docs/http/ngx_http_upstream_module.html#keepalive](http://nginx.org/en/docs/http/ngx_http_upstream_module.html#keepalive)


## upstream-keepalive-timeout

设置一个超时，在此超时期间，与上游服务器的空闲keepalive连接将保持打开状态。
 _**default:**_ 60

_References:_
[http://nginx.org/en/docs/http/ngx_http_upstream_module.html#keepalive_timeout](http://nginx.org/en/docs/http/ngx_http_upstream_module.html#keepalive_timeout)


## upstream-keepalive-requests

设置可以通过一个Keepalive连接处理的最大请求数。 后最大数量
发出请求后，连接已关闭。
_**default:**_ 100


_References:_
[http://nginx.org/en/docs/http/ngx_http_upstream_module.html#keepalive_requests](http://nginx.org/en/docs/http/ngx_http_upstream_module.html#keepalive_requests)


## limit-conn-zone-variable

设置共享内存区域的参数，该参数将保持 [limit_conn_zone](http://nginx.org/en/docs/http/ngx_http_limit_conn_module.html#limit_conn_zone)各个键的状态. "$binary_remote_addr" 变量默认总是为IPV4地址设置 4 bytes 为IPv6 地址设置16bytes.

## proxy-stream-timeout

设置客户端或代理服务器连接上的两次连续读取或写入操作之间的超时.如果在此时间内没有数据传输，则连接将关闭。

_References:_
[http://nginx.org/en/docs/stream/ngx_stream_proxy_module.html#proxy_timeout](http://nginx.org/en/docs/stream/ngx_stream_proxy_module.html#proxy_timeout)

## proxy-stream-responses

如果使用UDP协议，则设置代理服务器响应客户端请求而期望的数据报数。

_References:_
[http://nginx.org/en/docs/stream/ngx_stream_proxy_module.html#proxy_responses](http://nginx.org/en/docs/stream/ngx_stream_proxy_module.html#proxy_responses)

## bind-address

设置服务器将接受请求而不是*的地址。 应该注意的是，这些地址必须存在于运行时环境中，否则控制器将崩溃。

## use-forwarded-headers

如果为true，NGINX将传入的X-Forwarded- *标头传递给上游。 当NGINX在另一个正在设置这些标头的L7代理/负载平衡器之后时，请使用此选项。

如果为false，NGINX将忽略传入的X-Forwarded- *标头，并用看到的请求信息填充标头。 如果NGINX直接暴露于Internet或位于基于L3 /数据包的负载均衡器之后，而该负载均衡器不会更改数据包中的源IP，请使用此选项。

## forwarded-for-header

设置标头字段以标识客户端的原始IP地址, _**default:**_ X-Forwarded-For

## compute-full-forwarded-for

将远程地址附加到X-Forwarded-For标头，而不是替换它。 启用此选项后，上游应用程序将根据其自己的受信任代理列表提取客户端IP。

## proxy-add-original-uri-header

将具有原始请求URI的X-Original-Uri标头添加到后端请求

## generate-request-id

如果请求中没有X-Request-ID，请确保X-Request-ID默认为随机值

## enable-opentracing

启用nginx Opentracing扩展. _**default:**_ is disabled

_References:_
[https://github.com/opentracing-contrib/nginx-opentracing](https://github.com/opentracing-contrib/nginx-opentracing)

## zipkin-collector-host

指定uploading traces时要使用的主机。 它必须是一个有效的URL。.

## zipkin-collector-port

指定上载traces要使用的端口. _**default:**_ 9411

## zipkin-service-name

指定用于创建的任何traces的服务名称。 _**default:**_ nginx

## zipkin-sample-rate

指定创建的任何traces的采样率。 _**default:**_ 1.0

## jaeger-collector-host

指定上载traces时要使用的主机. It must be a valid URL.

## jaeger-collector-port

指定上载跟踪时要使用的端口。 _**default:**_ 6831

## jaeger-service-name

指定用于创建的任何跟踪的服务名称 _**default:**_ nginx

## jaeger-sampler-type

指定采样轨迹时要使用的采样器。 可用的采样器为:const，概率，速率限制，远程。_**default:**_ const

## jaeger-sampler-param

指定要传递给采样器构造函数的参数。 必须是数字。
对于const，应为0(从不采样)和1(始终采样)。 _**default:**_ 1

## jaeger-sampler-host

指定要传递给采样器构造函数的自定义远程采样器主机。 必须是有效的网址。
保留空白以使用默认值(localhost)。 _**default:**_ http://127.0.0.1

## jaeger-sampler-port

指定要传递给采样器构造函数的自定义远程采样器端口。 必须是数字。 _**default:**_ 5778

## jaeger-trace-context-header-name

指定用于传递跟踪上下文的标头名称。 _**default:**_ uber-trace-id

## jaeger-debug-header

指定用于强制采样的标题名称 _**default:**_ jaeger-debug-id

## jaeger-baggage-header

指定没有根跨度时用于提交行李的标头名称。_**default:**_ jaeger-baggage

## jaeger-tracer-baggage-header-prefix

指定用于传播行李的标题前缀。_**default:**_ uberctx-

## main-snippet

将自定义配置添加到nginx配置的主要部分。

## http-snippet

将自定义配置添加到nginx配置的http部分。

## server-snippet

将自定义配置添加到nginx配置中的所有server。

## location-snippet

将自定义配置添加到nginx配置中的所有location。

您不能使用它来添加代理到Kubernetes容器的新位置, 因为该代码段无法访问Go模板功能. 如果要添加自定义位置，则必须 [提供你自己的 nginx.tmpl](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/custom-template/).

## custom-http-errors

启用应传递哪些HTTP代码以使用 [error_page directive](http://nginx.org/en/docs/http/ngx_http_core_module.html#error_page)

设置至少一个代码也会启用 [proxy_intercept_errors](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_intercept_errors).处理error_page需要哪些

例子: `custom-http-errors: 404,415`

## proxy-body-size

设置客户端请求正文的最大允许大小。
查看 [client_max_body_size](http://nginx.org/en/docs/http/ngx_http_core_module.html#client_max_body_size).

## proxy-connect-timeout

为 [保持与代理服务器建立连接](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_connect_timeout)设置超时时间. 请注意，此超时通常不能超过75秒.

## proxy-read-timeout

以秒为单位为 [从代理服务器读取相应](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_read_timeout)设置超时. 超时仅在两次连续的读取操作之间设置，而不用于传输整个响应。

## proxy-send-timeout

以秒为单位为 [向代理服务器发送请求](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_send_timeout)设置超时时间. 超时仅在两次连续的写操作之间设置，而不用于传输整个请求.

## proxy-buffers-number

为[从响应读取第一部分](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_buffers) 从代理服务器接收的缓冲器数量. 这部分通常包含一个小的响应头.

## proxy-buffer-size

为 [r从响应读取第一部分](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_buffer_size) 从代理服务器接收的缓冲区大小. 这部分通常包含一个小的响应头.

## proxy-cookie-path

为 [应该在path属性中更改](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_cookie_path) 设置响应代理服务器投资端"Set-Cookie" 设置文本.

## proxy-cookie-domain

为 [应该在domain属性中更改](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_cookie_domain) 设置响应代理服务器投资端"Set-Cookie" 设置文本.

## proxy-next-upstream

指定 [哪些情况](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_next_upstream) 应该允许访问下一个服务器.

## proxy-next-upstream-timeout

以秒为单温[限制时间](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_next_upstream_timeout) 当一个请求代理到下一个server.

## proxy-next-upstream-tries

限制 [允许重试](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_next_upstream_tries)次数，  当一个请求代理到下一个server.

## proxy-redirect-from

设置应在代理服务器响应的"位置"和"刷新"标头字段中更改的原始文本. _**default:**_ off

_引用:_
[http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_redirect](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_redirect)

## proxy-request-buffering

加载或禁用 [缓存一个请求的请求体](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_request_buffering).

## ssl-redirect

如果服务器具有TLS证书(在Ingress规则中定义)，则将重定向(301)的全局值设置为HTTPS.
_**default:**_ "true"

## whitelist-source-range

为每个"服务器"块设置默认列入白名单的IP。 这可以由Ingress规则上的注释覆盖。
See [ngx_http_access_module](http://nginx.org/en/docs/http/ngx_http_access_module.html).

## skip-access-log-urls

设置不应出现在NGINX访问日志中的URL列表。 这对于像"/health"或"health-check"这样的使"复杂"的日志读取网址很有用。 _**默认值:**_为空

## limit-rate

限制向客户端传输响应的速率。 该速率以每秒字节数指定。 零值禁用速率限制。 该限制是根据请求设置的，因此，如果客户端同时打开两个连接，则总速率将是指定限制的两倍。

_References:_
[http://nginx.org/en/docs/http/ngx_http_core_module.html#limit_rate](http://nginx.org/en/docs/http/ngx_http_core_module.html#limit_rate)

## limit-rate-after

设置初始数量，之后将进一步限制对客户端的响应的进一步传输。

## lua-shared-dicts

自定义默认的Lua共享字典或定义更多。 您可以使用以下语法来这样做:

```
lua-shared-dicts: "<my dict name>: <my dict size>, [<my dict name>: <my dict size>], ..."
```

例如，下面的命令会将默认的"certificate_data"字典设置为"100M"，并引入一个名为
`my_custom_plugin`:

```
lua-shared-dicts: "certificate_data: 100, my_custom_plugin: 5"
```

_References:_
[http://nginx.org/en/docs/http/ngx_http_core_module.html#limit_rate_after](http://nginx.org/en/docs/http/ngx_http_core_module.html#limit_rate_after)

## http-redirect-code

设置要在重定向中使用的HTTP状态代码。
支持的状态为有 [301](https://developer.mozilla.org/docs/Web/HTTP/Status/301),[302](https://developer.mozilla.org/docs/Web/HTTP/Status/302),[307](https://developer.mozilla.org/docs/Web/HTTP/Status/307) and [308](https://developer.mozilla.org/docs/Web/HTTP/Status/308)
_**default:**_ 308

> __为什么默认代码是308?__

> [RFC 7238](https://tools.ietf.org/html/rfc7238) 被创建以定义308(永久重定向)状态代码，该状态代码与301(永久移动)相似，但是将有效负载保留在重定向中。 如果我们以POST之类的方法发送重定向，这一点很重要。

## proxy-buffering

加载或禁用 [缓冲来自代理服务器的响应](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_buffering).

## limit-req-status-code

设置[被拒绝请求返回的状态码](http://nginx.org/en/docs/http/ngx_http_limit_req_module.html#limit_req_status). _**default:**_ 503

## limit-conn-status-code

设置 [被拒绝链接返回的状态码](http://nginx.org/en/docs/http/ngx_http_limit_conn_module.html#limit_conn_status). _**default:**_ 503

## no-tls-redirect-locations

以逗号分隔的位置列表，在这些位置上，http请求将永远不会重定向到其https副本.
_**default:**_ "/.well-known/acme-challenge"

## global-auth-url

提供对所有位置进行身份验证的现有服务的URL.
与`nginx.ingress.kubernetes.io/auth-url`注解类似.
可以可出不应通过身份验证请求的location `no-auth-locations` 查看 [no-auth-locations](#no-auth-locations). 另外，可以通过设置enable-global-auth注解为false将每个服务从身份验证中排除。.
_**default:**_ ""

_References:_ [https://github.com/kubernetes/ingress-nginx/blob/master/docs/user-guide/nginx-configuration/annotations.md#external-authentication](https://github.com/kubernetes/ingress-nginx/blob/master/docs/user-guide/nginx-configuration/annotations.md#external-authentication)

## global-auth-method

一种用于现有服务的HTTP方法，该服务为所有位置提供身份验证。
与 `nginx.ingress.kubernetes.io/auth-method`注解类似.
_**default:**_ ""

## global-auth-signin

设置现有服务的错误页面的位置，该服务为所有位置提供身份验证。
与 `nginx.ingress.kubernetes.io/auth-signin`注解类似.
_**default:**_ ""

## global-auth-response-headers

将头设置为在身份验证请求完成后传递到后端。 应用于所有location。
与 `nginx.ingress.kubernetes.io/auth-response-headers`注解类似.
_**default:**_ ""

## global-auth-request-redirect

设置X-Auth-Request-Redirect标头值。 适用于所有location
与 `nginx.ingress.kubernetes.io/auth-request-redirect`注解类似..
_**default:**_ ""

## global-auth-snippet

设置自定义代码段以与外部身份验证一起使用。 适用于所有地点
与 `nginx.ingress.kubernetes.io/auth-request-redirect`注解类似.
_**default:**_ ""

## global-auth-cache-key

为全局身份验证请求启用缓存。 指定身份验证响应的查找键, e.g. `$remote_user$http_authorization`.

## global-auth-cache-duration

根据验证响应的响应代码设置它们的缓存时间, e.g. `200 202 30m`. See [proxy_cache_valid](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_cache_valid) for details. You may specify multiple, comma-separated values: `200 202 10m, 401 5m`. defaults to `200 202 401 5m`.

## no-auth-locations

以逗号分隔的不应该通过身份验证的location列表。
_**default:**_ "/.well-known/acme-challenge"

## block-cidrs

以逗号分隔的IP地址(或子网)列表，必须全局阻止来自该IP地址(或子网)的请求。

_References:_
[http://nginx.org/en/docs/http/ngx_http_access_module.html#deny](http://nginx.org/en/docs/http/ngx_http_access_module.html#deny)

## block-user-agents

以逗号分隔的User-Agent列表，必须全局阻止来自该列表的请求。
在这里可以使用完整的字符串和正则表达式。 关于有效模式的更多详细信息可以在map Nginx指令文档中找到。

_References:_
[http://nginx.org/en/docs/http/ngx_http_map_module.html#map](http://nginx.org/en/docs/http/ngx_http_map_module.html#map)

## block-referers

以逗号分隔的"引荐来源"列表，必须全局阻止来自其的请求。
在这里可以使用完整的字符串和正则表达式。 关于有效模式的更多详细信息可以在map Nginx指令文档中找到

_References:_
[http://nginx.org/en/docs/http/ngx_http_map_module.html#map](http://nginx.org/en/docs/http/ngx_http_map_module.html#map)
