# Custom nginx template

NGINX模板位于 `/etc/nginx/template/nginx.tmpl`文件中.

使用 [Volume](https://kubernetes.io/docs/concepts/storage/volumes/) 讲自定义模板变为可能.
这包括使用 [Configmap](https://kubernetes.io/docs/concepts/storage/volumes/#example-pod-with-a-secret-a-downward-api-and-a-configmap) 作为模板的源文件

```yaml
        volumeMounts:
          - mountPath: /etc/nginx/template
            name: nginx-template-volume
            readOnly: true
      volumes:
        - name: nginx-template-volume
          configMap:
            name: nginx-template
            items:
            - key: nginx.tmpl
              path: nginx.tmpl
```

**请注意，模板已绑定到Go代码。 不要在变量中更改名称 `$cfg`.**

有关模板语法的更多信息，请检查 [Go template package](https://golang.org/pkg/text/template/).
除了Go软件包提供的内置功能之外，还可以使用以下功能:

- empty: 如果指定的参数(字符串)为空，则返回true
- contains: [strings.Contains](https://golang.org/pkg/strings/#Contains)
- hasPrefix: [strings.HasPrefix](https://golang.org/pkg/strings/#HasPrefix)
- hasSuffix: [strings.HasSuffix](https://golang.org/pkg/strings/#HasSuffix)
- toUpper: [strings.ToUpper](https://golang.org/pkg/strings/#ToUpper)
- toLower: [strings.ToLower](https://golang.org/pkg/strings/#ToLower)
- quote: 将字符串用双引号引起来
- buildLocation: 帮助在每个服务器中构建nginx location部分
- buildProxyPass: 建立反向代理配置
- buildRateLimit: 如果包含速率限制注释，则有助于在位置内建立限制区域

TODO:

- buildAuthLocation:
- buildAuthResponseHeaders:
- buildResolvers:
- buildDenyVariable:
- buildUpstreamName:
- buildForwardedFor:
- buildAuthSignURL:
- buildNextUpstream:
- filterRateLimits:
- formatIP:
- getenv:
- getIngressInformation:
- serverConfig:
- isLocationAllowed:
- isValidClientBodyBufferSize:
