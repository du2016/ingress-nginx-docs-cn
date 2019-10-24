# TLS/HTTPS

## TLS Secrets

每当我们引用TLS机密时，我们指的是PEM编码的X.509，RSA(2048)机密。

您可以使用以下方法生成自签名证书和私钥:

```bash
$ openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout ${KEY_FILE} -out ${CERT_FILE} -subj "/CN=${HOST}/O=${HOST}"
```

然后通过以下方式在集群中创建密钥:

```bash
kubectl create secret tls ${CERT_NAME} --key ${KEY_FILE} --cert ${CERT_FILE}
```

产生的秘密将类型为`kubernetes.io/tls`.

## Default SSL Certificate

NGINX提供了将服务器配置为包含所有功能的选项
[服务器名称](http://nginx.org/en/docs/http/server_names.html)
用于与任何已配置的服务器名称都不匹配的请求。
此配置无需HTTP流量即可使用。
对于HTTPS，自然需要证书。

因此，Ingress控制器提供了标志`--default-ssl-certificate`。
此标志引用的机密包含在以下情况下使用的默认证书:
访问通用服务器。
如果未提供此标志，NGINX将使用自签名证书。

例如，如果您在`default`名称空间中有一个TLS秘密`foo-tls`，
在nginx-controller部署中添加--default-ssl-certificate = default/foo-tls。

缺省证书还将用于不包含入口的`tls:`部分
有一个`secretName`选项。

## SSL Passthrough

The [`--enable-ssl-passthrough`](cli-arguments/) flag enables the SSL Passthrough feature, which is disabled by
default. This is required to enable passthrough backends in Ingress objects.

!!! warning
    通过拦截已配置的HTTPS端口(默认值:443)上的 **所有流量**并进行处理来实现此功能。
    将其转移到本地TCP代理。 这完全绕开了NGINX，并引入了不可忽略的性能损失。

SSL Passthrough利用[SNI][SNI]并从TLS协商中读取虚拟域，这需要兼容
客户。 TLS侦听器接受连接后，该连接将由控制器本身处理并通过管道送回
在后端和客户端之间来回。

如果没有与请求的主机名匹配的主机名，该请求将被移交给已配置的NGINX
直通代理端口(默认:442)，它将请求代理到默认后端。

!!! note
    与HTTP后端不同，传递给Passthrough后端的流量被发送到支持服务的* clusterIP *，而不是各个端点。

## HTTP Strict Transport Security

HTTP严格传输安全性(HSTS)是指定的可选安全性增强功能
通过使用特殊的响应头。 一旦支持的浏览器收到
该标头，浏览器将阻止通过它发送任何通信
HTTP到指定的域，并且将通过HTTPS发送所有通信。

默认情况下启用HSTS。

要禁用此行为，请在配置[ConfigMap][ConfigMap]中使用`hsts:"false"`。

## Server-side HTTPS enforcement through redirect

默认情况下，控制器将HTTP客户端重定向到HTTPS端口
如果为该入口启用了TLS，则使用308永久重定向响应443。

可以使用nginx [config map] [ConfigMap]中的`ssl-redirect:"false"`全局禁用它，
或使用`nginx.ingress.kubernetes.io/ssl-redirect:"false"`的每次进入
特定资源中的注释。

!!! tip

    在群集之外(例如AWS ELB)使用SSL卸载时，强制执行
    即使没有可用的TLS证书，也重定向到HTTPS。
    这可以通过使用`nginx.ingress.kubernetes.io/force-ssl-redirect:"true"`来实现。
    特定资源中的注释。

## Automated Certificate Management with Kube-Lego

!!! 小提示
    Kube-Lego 已达到使用寿命，并且正在使用 [cert-manager](https://github.com/jetstack/cert-manager/)替换.

[Kube-Lego] 自动从[Let's Encrypt]以下位置请求丢失或过期的证书 
通过监视ingress资源及其引用的secret。

要为入口资源启用此功能，您必须添加注释:

```console
kubectl annotate ing ingress-demo kubernetes.io/tls-acme="true"
```

要设置Kube-Lego，您可以看一下这个[完整示例][full-kube-lego-example]。
完全支持Kube-Lego的第一个版本是nginx Ingress controller 0.8。

## Default TLS Version and Ciphers

为了提供最安全的基准配置，

nginx-ingress默认仅使用TLS 1.2和[安全的TLS密码集][ssl-ciphers]。

### Legacy TLS

默认配置虽然安全，但不支持某些较旧的浏览器和操作系统。

例如，仅从Android 5.0起默认启用TLS 1.1+。 在撰写本文时，
2018年5月，[大约占Android设备的15％](https://developer.android.com/about/dashboards/#Platform)
与nginx-ingress的默认配置不兼容。

要更改此默认行为，请使用[ConfigMap] [ConfigMap]。

允许这些旧客户端连接的示例ConfigMap片段可能类似于以下内容:

```
kind: ConfigMap
apiVersion: v1
metadata:
  name: nginx-config
data:
  ssl-ciphers: "ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHA:AES256-SHA:AES:CAMELLIA:DES-CBC3-SHA:!aNULL:!eNULL:!EXPORT:!DES:!RC4:!MD5:!PSK:!aECDH:!EDH-DSS-DES-CBC3-SHA:!EDH-RSA-DES-CBC3-SHA:!KRB5-DES-CBC3-SHA"
  ssl-protocols: "TLSv1 TLSv1.1 TLSv1.2"
```



[full-kube-lego-example]:https://github.com/jetstack/kube-lego/tree/master/examples
[Kube-Lego]:https://github.com/jetstack/kube-lego
[Let's Encrypt]:https://letsencrypt.org
[ConfigMap]: ./nginx-configuration/configmap.md
[ssl-ciphers]: ./nginx-configuration/configmap.md#ssl-ciphers
[SNI]: https://en.wikipedia.org/wiki/Server_Name_Indication
