# Custom errors

当开启 [`custom-http-errors`][cm-custom-http-errors] 参数, Ingress控制器配置了NGINX，因此
在发生错误的情况下，它将多个HTTP标头向下传递到它的`default-backend`:

| 头           | 值                                                               |
| ---------------- | ------------------------------------------------------------------- |
| `X-Code`         | 请求重新调整的HTTP状态代码                            |
| `X-Format`       | 客户端发送的`Accept`标头的值                     |
| `X-Original-URI` | 导致错误的URI                                           |
| `X-Namespace`    | 后端服务所在的命名空间                     |
| `X-Ingress-Name` | 定义后端的Ingress名称                    |
| `X-Service-Name` | 支持后端的service名称                             |
| `X-Service-Port` | 支持后端的服务的端口号                     |
| `X-Request-ID`   | 标识请求的唯一ID-与后端服务相同 |

定制错误后端可以使用此信息来返回错误页面的最佳表示形式。 对于
例如，如果客户端发送的"Accept"标头的值是"application /json"，则精心设计的后端
可以决定将错误有效载荷作为JSON文档而不是HTML返回。

!!! 重要
    自定义后端应返回正确的HTTP状态代码，而不是'200'。NGINX不会更改来自自定义默认后端的响应。

此类自定义后端的示例可在以下资源库中找到: [images/custom-error-pages][img-custom-error-pages].

获取更多查看 [Custom errors][example-custom-errors] example.

[cm-custom-http-errors]: ./nginx-configuration/configmap.md#custom-http-errors
[img-custom-error-pages]: https://github.com/kubernetes/ingress-nginx/tree/master/images/custom-error-pages
[example-custom-errors]: ../examples/customization/custom-errors
