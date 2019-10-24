# nginx Configuration

There are three ways to customize NGINX:

1. [ConfigMap](./configmap.md): 使用Configmap在NGINX中设置全局配置
2. [Annotations](./annotations.md): 如果您想要特定的Ingress规则的特定配置，请使用此选项
3. [Custom template](./custom-template.md): 当需要更特定的设置,例如open_file_cache，调整侦听选项如rcvbuf或无法通过ConfigMap更改配置时。
