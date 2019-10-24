# ingress-nginx 中文文档

## mkdoc安装

```bash 
pip install mkdocs-material
mkdocs serve
```
访问: [http://127.0.0.1:8000](http://127.0.0.1:8000)


## 目录
* 欢迎:
    * [欢迎](./docs/index.md)
    * [如何工作](./docs/how-it-works.md)
    * [故障排查](./docs/troubleshooting.md)
    * [kubectl 插件](./docs/kubectl-plugin.md)
    * [开发](./docs/development.md)
* 部署:
    * [安装指南](./docs/deploy/index.md)
    * [裸机注意事项](./docs/deploy/baremetal.md)
    * [基于角色的访问控制(RBAC)](./docs/deploy/rbac.md)
    * [Validating Webhook (准入控制器)](./docs/deploy/validating-webhook.md)
    * [升级](./docs/deploy/upgrade.md)
* 用户指南:
    * nginx 配置:
        * [介绍](./docs/user-guide/nginx-configuration/index.md)
        * [基本用法](./docs/user-guide/basic-usage.md)
        * [注解](./docs/user-guide/nginx-configuration/annotations.md)
        * [ConfigMap](./docs/user-guide/nginx-configuration/configmap.md)
        * [自定义nginx模板](./docs/user-guide/nginx-configuration/custom-template.md)
        * [日志格式](./docs/user-guide/nginx-configuration/log-format.md)
    * [命令行参数](./docs/user-guide/cli-arguments.md)
    * [自定义错误](./docs/user-guide/custom-errors.md)
    * [默认后端](./docs/user-guide/default-backend.md)
    * [暴露TCP或UDP服务](./docs/user-guide/exposing-tcp-udp-services.md)
    * [暴露FCGI服务](./docs/user-guide/fcgi-services.md)
    * [路径中的正则表达式](user-guide/ingress-path-matching.md)
    * [扩展阅读](./docs/user-guide/external-articles.md)
    * [杂项](./docs/user-guide/miscellaneous.md)
    * [安装Prometheus 和 Grafana](./docs/user-guide/monitoring.md)
    * [多Ingress控制器](./docs/user-guide/multiple-ingress.md)
    * [TLS/HTTPS](./docs/user-guide/tls.md)
    * 第三方插件:
        * [ModSecurity Web应用程序防火墙](./docs/user-guide/third-party-addons/modsecurity.md)
        * [OpenTracing](./docs/user-guide/third-party-addons/opentracing.md)
* 示例:
    * [介绍](./docs/examples/index.md)
    * [依赖](./docs/examples/PREREQUISITES.md)
    * [回话粘滞性](./docs/examples/affinity/cookie/README.md)
    * 认证:
        * [基础认证](./docs/examples/auth/basic/README.md)
        * [客户端证书认证](./docs/examples/auth/client-certs/README.md)
        * [扩展基础认证](./docs/examples/auth/external-auth/README.md)
        * [扩展 OAUTH 认证](./docs/examples/auth/oauth-external-auth/README.md)
    * 定制化:
        * [配置片段](./docs/examples/customization/configuration-snippets/README.md)
        * [定义配置](./docs/examples/customization/custom-configuration/README.md)
        * [定义错误](./docs/examples/customization/custom-errors/README.md)
        * [定义Headers](./docs/examples/customization/custom-headers/README.md)
        * [扩展认证](./docs/examples/customization/external-auth-headers/README.md)
        * [自定义DH参数以实现完美的前向保密性](./docs/examples/customization/ssl-dh-param/README.md)
        * [系统调整](./docs/examples/customization/sysctl/README.md)
    * [Docker仓库](./docs/examples/docker-registry/README.md)
    * [gRPC](./docs/examples/grpc/README.md)
    * [多证书结束](./docs/examples/multi-tls/README.md)
    * [Rewrite](./docs/examples/rewrite/README.md)
    * [静态 IPs](./docs/examples/static-ip/README.md)
    * [TLS 种植](./docs/examples/tls-termination/README.md)
    * [Pod安全策略 (PSP)](./docs/examples/psp/README.md)
