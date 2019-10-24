# 杂项

## 源IP

默认情况下，NGINX使用标头"X-Forwarded-For"的内容作为事实来源，以获取有关客户端IP地址的信息。 如果我们使用受信任的外部负载均衡器的IP /网络地址的正确信息配置设置`proxy-real-ip-cidr` **，这在L7中不会出现问题。

如果  ingress controller在AWS中运行，则需要使用VPC IPv4 CIDR。

另一个选择是使用`use-proxy-protocol:"true"`启用代理协议。

在这种模式下，NGINX不使用标头的内容来获取连接的源IP地址。

## Proxy Protocol

如果您使用L4代理将流量转发到NGINX吊舱并在那里终止HTTP /HTTPS，则将丢失远程端点的IP地址。 为了防止这种情况，
您可以使用[代理协议](http://www.haproxy.org/download/1.5/doc/proxy-protocol.txt)转发流量，这将在转发实际的TCP连接之前发送连接详细信息 本身。

在其他代理中 [ELBs in AWS](http://docs.aws.amazon.com/ElasticLoadBalancing/latest/DeveloperGuide/enable-proxy-protocol.html) 和 [HAProxy](http://www.haproxy.org/) 支持代理协议.

## Websockets

NGINX提供了对websockets的支持。 无需特殊配置。

避免关闭连接的唯一要求是增加"proxy-read-timeout"和"proxy-send-timeout"的值。

此设置的默认值为"60秒"。

支持websocket的更合适的值是大于一小时的值("3600")。

!!! 重要
    如果nginx ingress controller通过服务`type = LoadBalancer`公开，请确保负载均衡器和NGINX之间的协议为TCP。

## Optimizing TLS Time To First Byte (TTTFB)

NGINX提供了配置选项 [ssl_buffer_size](http://nginx.org/en/docs/http/ngx_http_ssl_module.html#ssl_buffer_size) 以允许优化TLS记录大小

这改善了 [TLS Time To First Byte](https://www.igvita.com/2013/12/16/optimizing-nginx-tls-time-to-first-byte/) (TTTFB).
Ingress控制器的默认值为4k(NGINX默认值为16k)。

## Retries in non-idempotent methods

从1.9.13开始，NGINX不会在发生错误的情况下重试非幂等请求(POST，LOCK，PATCH)。
可以使用ConfigMap中的`retry-non-idempotent = true`恢复以前的行为。

## Limitations

- TLS的入口规则要求定义`主机`字段

## Why endpoints and not services

nginx ingress controller未使用 [Services](http://kubernetes.io/docs/user-guide/services) 将流量路由到豆荚。 相反，它使用Endpoints API来绕过[kube-proxy](http://kubernetes.io/docs/admin/kube-proxy/)，以允许NGINX功能，例如会话亲和力和自定义负载平衡算法。 它还消除了一些开销，例如iptables DNAT的conntrack条目。