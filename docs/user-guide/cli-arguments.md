# Command line arguments

Ingress控制器可执行文件接受以下命令行参数.

它们是在`nginx-ingress-controller`部署清单的容器规范中设置的

| 参数 | 描述 |
|----------|-------------|
| `--alsologtostderr`               | 记录到标准错误以及文件 |
| `--annotations-prefix string`     | NGINX控制器专用的Ingress注释的前缀. (default "nginx.ingress.kubernetes.io") |
| `--apiserver-host string`         | Kubernetes API服务器的地址. 遵循"protocol://address:port"格式. 如果未指定，则假定程序在Kubernetes集群中运行，并且尝试进行本地发现。 |
| `--configmap string`              | 包含控制器的自定义全局配置的ConfigMap的名称. |
| `--default-backend-service string` | 用于服务HTTP请求的服务与任何已知服务器名称都不匹配 (包含所有). 遵循 "namespace/name"格式. 控制器将NGINX配置为将请求转发到该服务的第一个端口。 如果未指定，则将直接从NGINX返回404页面。.|
| `--default-server-port int`       | 当 `default-backend-service` 未指定或指定的服务没有任何端点, 具有此端口的本地端点将用于从Nginx内部提供404页面. |
| `--default-ssl-certificate string` | 包含要由默认HTTPS服务器使用的SSL证书的机密(全部捕获). 遵循 "namespace/name"格式. |
| `--disable-catch-all`             | 禁用对所有Ingress的支持. |
| `--election-id string`            | 用于更新ingress状态的选举ID. (default "ingress-controller-leader") |
| `--enable-dynamic-certificates`   | 创建，更新或删除证书时，动态提供证书，而不是重新加载NGINX。 当前不支持OCSP装订, 因此-enable-ssl-chain-completion必须关闭(默认行为). 假设证书是使用2048位RSA密钥/证书对生成的，则此功能可以存储大约5000个证书. 一旦支持Lua的共享字典"certificate_data"已满，最近使用最少的证书将被删除以存储新证书。 (默认启用) |
| `--enable-metrics`                | 启用指标集以供Prometheus抓取 (default true) |
| `--enable-ssl-chain-completion`   | 缺少中间CA证书的自动完成SSL证书链.需要有效的证书链才能启用OCSP装订，上载到Kubernetes的证书必须具有"Authority Information Access" X.509 v3扩展名才能成功. (default true) |
| `--enable-ssl-passthrough`        | 开启 SSL Passthrough. |
| `--health-check-path string`      | 健康检查端点的URL路径。 在NGINX状态服务器中配置。 在healthz-port参数定义的端口上收到的所有请求都在内部转发到此路径. (default "/healthz") |
| `--health-check-timeout duration` | 成功检查健康检查路径的时间限制(以秒为单位) (default 10) |
| `--healthz-port int`              | 用于healthz端点的端口. (default 10254) |
| `--http-port int`                 | 用于服务HTTP流量的端口. (default 80) |
| `--https-port int`                | 用于服务HTTPS流量的端口. (default 443) |
| `--status-port int`                | 用于lua HTTP端点配置的端口. (default 10246) |
| `--stream-port int`                | 用于lua TCP/UDP端点配置的端口. (default 10247) |
| `--ingress-class string`          | 该controller满足的ingress class 名称. 使用注释'kubernetes.io/ingress.class'设置Ingress class。 如果将此参数保留为空，则满足所有入口类. |
| `--kubeconfig string`             | 包含授权和API服务器信息的kubeconfig文件的路径. |
| `--log_backtrace_at traceLocation` | 当记录命中行file:N时，发出堆栈跟踪(default :0) |
| `--log_dir string`                | 如果为非空，则在此目录中写入日志文件 |
| `--logtostderr`                   | log写入到标准错误而不是文件 (default true) |
| `--metrics-per-host`              | 启用主机标签用于 prometheus metrics. 您可能要禁用此功能以减少创建的时间序列数. (default true) |
| `--profiling`                     | 开启Web界面启用分析借款 host:port/debug/pprof/(default true) |
| `--publish-service string`        | Ingress controller前的服务. 遵循"namespace/name"格式. 与update-status一起使用时, 控制器将该服务端点的地址镜像到它满足的所有Ingress对象的负载均衡器状态。. |
| `--publish-status-address string` | 定制地址，设置为此控制器满足的Ingress对象的负载均衡器状态。 需要update-status参数. |
| `--report-node-internal-ip-address` | 将Ingress对象的负载均衡器状态设置为内部Node地址，而不是外部。 需要update-status参数。. |
| `--ssl-passthrough-proxy-port int` | 内部用于SSL传递的端口. (default 442) |
| `--stderrthreshold severity`      | 达到或超过此阈值的日志转到stderr (default 2) |
| `--sync-period duration`          | 控制器强制重新填充其本地对象存储区的时间段。 默认禁用 |
| `--sync-rate-limit float32`       | 定义同步频率上限 (default 0.3) |
| `--tcp-services-configmap string` | 包含要公开的TCP服务的定义的ConfigMap的名称. 映射中的键指示要使用的外部端口.  值为service的引用遵循 "namespace/name:port"格式,其中"端口"可以是端口号或名称。 控制器保留TCP端口80和443用于服务HTTP流量. |
| `--udp-services-configmap string` | 包含要公开的UDP服务的定义的ConfigMap的名称. 映射中的键指示要使用的外部端口. 值为service的引用遵循 "namespace/name:port"格式, 其中"端口"可以是端口名称或数字. |
| `--update-status`                 | 更新此控制器满足的Ingress对象的负载均衡器状态。 需要将publish-service参数设置为有效的Service引用 (default true) |
| `--update-status-on-shutdown`     | 控制器关闭时，更新Ingress对象的负载均衡器状态。 需要update-status参数. (default true) |
| `-v`, `--v Level`                 | 日志输出级别 |
| `--version`                       | 显示有关nginx Ingress控制器的版本信息并退出。 |
| `--vmodule moduleSpec`            | 以逗号分隔的pattern=N设置列表，用于文件过滤的日志记录 |
| `--watch-namespace string`        | 控制器的命名空间watch Kubernetes对象的更新. 这包括入口，服务和所有配置资源。 如果将此参数保留为空，则监视所有名称空间 |
|`--validating-webhook`|启动准入控制器的地址|
|`--validating-webhook-certificate`|Webhook用于TLS处理的证书|
|`--validating-webhook-key`|Webhook用于TLS处理的密钥|
