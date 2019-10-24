# Log format

默认配置使用自定义日志记录格式来添加有关上游，响应时间和状态的其他信息.

```
log_format upstreaminfo
    '$remote_addr - $remote_user [$time_local] "$request" '
    '$status $body_bytes_sent "$http_referer" "$http_user_agent" '
    '$request_length $request_time [$proxy_upstream_name] [$proxy_alternative_upstream_name] $upstream_addr '
    '$upstream_response_length $upstream_response_time $upstream_status $req_id';
```

| 占位符 | 描述 |
|-------------|-------------|
| `$proxy_protocol_addr` | 远程地址(如果启用了代理协议) |
| `$remote_addr` | 客户端的源IP地址 |
| `$remote_user` | 基本身份验证随附的用户名 |
| `$time_local` | 通用日志格式的本地时间 |
| `$request` | 完整的原始请求行 |
| `$status` | 相应状态 |
| `$body_bytes_sent` | 发送给客户端的字节数，不计算响应头 |
| `$http_referer` | Referer标头的值 |
| `$http_user_agent` | User-Agent 头的值 |
| `$request_length` | 请求长度 (包括请求行，标头和请求正文) |
| `$request_time` | 从客户端读取第一个字节以来经过的时间 |
| `$proxy_upstream_name` | upstream名称. 格式为 `upstream-<namespace>-<service name>-<service port>` |
| `$proxy_alternative_upstream_name` | alternative upstream名称. 格式为 `upstream-<namespace>-<service name>-<service port>` |
| `$upstream_addr` | 上游服务器的IP地址和端口(或域套接字的路径). 如果在请求处理过程中联系了多个服务器，则其地址之间用逗号分隔. |
| `$upstream_response_length` | 从上游服务器获得的响应的长度 |
| `$upstream_response_time` | 从上游服务器接收响应所花费的时间(以毫秒为单位) |
| `$upstream_status` | 从上游服务器获得的响应的状态码 |
| `$req_id` | 随机生成的请求ID  |

其他可用变量:

| 占位符 | 描述 |
|-------------|-------------|
| `$namespace` |  ingress所在的命名空间 |
| `$ingress_name` | ingress的名称 |
| `$service_name` | service名称 |
| `$service_port` | service端口 |


参考:

- [Upstream variables](http://nginx.org/en/docs/http/ngx_http_upstream_module.html#variables)
- [Embedded variables](http://nginx.org/en/docs/http/ngx_http_core_module.html#variables)
