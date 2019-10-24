<!--
-----------------NOTICE------------------------
This file is referenced in code as
https://github.com/kubernetes/ingress-nginx/blob/master/docs/kubectl-plugin.md
Do not move it without providing redirects.
-----------------------------------------------
-->

# The ingress-nginx kubectl 插件

## 安装

安装 [krew](https://github.com/GoogleContainerTools/krew), 运行

```console
kubectl krew install ingress-nginx
```

安装插件然后运行

```console
kubectl ingress-nginx --help
```

确保插件已正确安装并获取命令列表:

```console
kubectl ingress-nginx --help
A kubectl plugin for inspecting your ingress-nginx deployments

Usage:
  ingress-nginx [command]

Available Commands:
  backends    Inspect the dynamic backend information of an ingress-nginx instance
  certs       Output the certificate data stored in an ingress-nginx pod
  conf        Inspect the generated nginx.conf
  exec        Execute a command inside an ingress-nginx pod
  general     Inspect the other dynamic ingress-nginx information
  help        Help about any command
  info        Show information about the ingress-nginx service
  ingresses   Provide a short summary of all of the ingress definitions
  lint        Inspect kubernetes resources for possible issues
  logs        Get the kubernetes logs for an ingress-nginx pod
  ssh         ssh into a running ingress-nginx pod

Flags:
      --as string                      Username to impersonate for the operation
      --as-group stringArray           Group to impersonate for the operation, this flag can be repeated to specify multiple groups.
      --cache-dir string               Default HTTP cache directory (default "/Users/alexkursell/.kube/http-cache")
      --certificate-authority string   Path to a cert file for the certificate authority
      --client-certificate string      Path to a client certificate file for TLS
      --client-key string              Path to a client key file for TLS
      --cluster string                 The name of the kubeconfig cluster to use
      --context string                 The name of the kubeconfig context to use
  -h, --help                           help for ingress-nginx
      --insecure-skip-tls-verify       If true, the server's certificate will not be checked for validity. This will make your HTTPS connections insecure
      --kubeconfig string              Path to the kubeconfig file to use for CLI requests.
  -n, --namespace string               If present, the namespace scope for this CLI request
      --request-timeout string         The length of time to wait before giving up on a single server request. Non-zero values should contain a corresponding time unit (e.g. 1s, 2m, 3h). A value of zero means don't timeout requests. (default "0")
  -s, --server string                  The address and port of the Kubernetes API server
      --token string                   Bearer token for authentication to the API server
      --user string                    The name of the kubeconfig user to use

Use "ingress-nginx [command] --help" for more information about a command.
```

如果刚刚发布了新的`ingress-nginx`版本，则该插件可能尚未在存储库中更新。在这种情况下，您可以通过运行以下命令安装最新版本的插件:

```console
(
    set -x; cd "$(mktemp -d)" &&
    curl -fsSLO "https://github.com/kubernetes/ingress-nginx/releases/download/nginx-0.24.0/{ingress-nginx.yaml,kubectl-ingress_nginx-$(uname | tr '[:upper:]' '[:lower:]')-amd64.tar.gz}" &&
    kubectl krew install \
    --manifest=ingress-nginx.yaml --archive=kubectl-ingress_nginx-$(uname | tr '[:upper:]' '[:lower:]')-amd64.tar.gz
)
```

用最新发布的版本替换`0.24.0`。

## 常用参数

- 每个子命令都支持基本的 `kubectl` 配置参数，像 `--namespace`, `--context`, `--client-key` 等.
- 对特定的Ingress-nginx Pod(`backend`,`certs`，`conf`，`exec`，`exec`，`general`，`logs`，`ssh`)起作用的子命令支持`--deployment <deployment>`和`--pod <pod>`参数，以从deployment中选择一个Pod使用给定名称，或使用给定名称的pod。 --deployment参数默认为`nginx-ingress-controller`
- 检查资源的子命令 (`ingresses`, `lint`) 支持 `--all-namespaces` 标志，这使它们可以检查每个命名空间中的资源。

## 子命令

请注意 `backends`, `general`, `certs`, 和 `conf` 依赖 `ingress-nginx` `0.23.0`版本及以上.

### backends

运行 `kubectl ingress-nginx backends` 以获取ingress-nginx controller当前已知的后端的JSON数组:

```console
$ kubectl ingress-nginx backends -n ingress-nginx
[
  {
    "name": "default-apple-service-5678",
    "service": {
      "metadata": {
        "creationTimestamp": null
      },
      "spec": {
        "ports": [
          {
            "protocol": "TCP",
            "port": 5678,
            "targetPort": 5678
          }
        ],
        "selector": {
          "app": "apple"
        },
        "clusterIP": "10.97.230.121",
        "type": "ClusterIP",
        "sessionAffinity": "None"
      },
      "status": {
        "loadBalancer": {}
      }
    },
    "port": 0,
    "secureCACert": {
      "secret": "",
      "caFilename": "",
      "caSha": ""
    },
    "sslPassthrough": false,
    "endpoints": [
      {
        "address": "10.1.3.86",
        "port": "5678"
      }
    ],
    "sessionAffinityConfig": {
      "name": "",
      "cookieSessionAffinity": {
        "name": ""
      }
    },
    "upstreamHashByConfig": {
      "upstream-hash-by-subset-size": 3
    },
    "noServer": false,
    "trafficShapingPolicy": {
      "weight": 0,
      "header": "",
      "headerValue": "",
      "cookie": ""
    }
  },
  {
    "name": "default-echo-service-8080",
    ...
  },
  {
    "name": "upstream-default-backend",
    ...
  }
]
```

添加`--list`选项以仅显示后端名称。添加`--backend <backend>`选项以仅显示具有给定名称的后端

### certs

使用`kubectl ingress-nginx certs --host <主机名>`转储给定位置的SSL证书/密钥信息.
要求`--enable-dynamic-certificates`为true(这是`0.24.0`版的默认值)

**WARNING:** 此命令将转储敏感的私钥信息。不要盲目分享输出，当然也不要在任何地方记录它

```console
$ kubectl ingress-nginx certs -n ingress-nginx --host testaddr.local
-----BEGIN CERTIFICATE-----
...
-----END CERTIFICATE-----
-----BEGIN CERTIFICATE-----
...
-----END CERTIFICATE-----

-----BEGIN RSA PRIVATE KEY-----
<REDACTED! DO NOT SHARE THIS!>
-----END RSA PRIVATE KEY-----
```

### conf

使用`kubectl ingress-nginx conf`转储生成的`nginx.conf`文件。添加`--host <hostname>`选项以仅查看该主机的server块:

```console
kubectl ingress-nginx conf -n ingress-nginx --host testaddr.local

	server {
		server_name testaddr.local ;

		listen 80;

		set $proxy_upstream_name "-";
		set $pass_access_scheme $scheme;
		set $pass_server_port $server_port;
		set $best_http_host $http_host;
		set $pass_port $pass_server_port;

		location /{

			set $namespace      "";
			set $ingress_name   "";
			set $service_name   "";
			set $service_port   "0";
			set $location_path  "/";

...
```

### exec

`kubectl ingress-nginx exec` 与 `kubectl exec`完全相同，具有相同的命令标志。它将自动选择一个ingress-nginx pod来运行命令

```console
$ kubectl ingress-nginx exec -i -n ingress-nginx -- ls /etc/nginx
fastcgi_params
geoip
lua
mime.types
modsecurity
modules
nginx.conf
opentracing.json
owasp-modsecurity-crs
template
```

### general

`kubectl ingress-nginx general`将其他控制器状态转储为JSON对象。当前，它仅显示特定控制器容器已知的控制器容器的数量。

```console
$ kubectl ingress-nginx general -n ingress-nginx
{
  "controllerPodsCount": 1
}
```

### info

显示Ingress-nginx服务的内部和外部`IP/CNAMES`

```console
$ kubectl ingress-nginx info -n ingress-nginx
Service cluster IP address: 10.187.253.31
LoadBalancer IP|CNAME: 35.123.123.123
```

如果您的`ingress-nginx` `LoadBalancer`服务未命名为`ingress-nginx`，请使用`--service <service>`参数

### ingresses

`kubectl ingress-nginx ingresses`，或者`kubectl ingress-nginx ing`，显示了名称空间中入口定义的更详细视图。相比

```console
$ kubectl get ingresses --all-namespaces
NAMESPACE   NAME               HOSTS                            ADDRESS     PORTS   AGE
default     example-ingress1   testaddr.local,testaddr2.local   localhost   80      5d
default     test-ingress-2     *                                localhost   80      5d
```

vs

```console
$ kubectl ingress-nginx ingresses --all-namespaces
NAMESPACE   INGRESS NAME       HOST+PATH                        ADDRESSES   TLS   SERVICE         SERVICE PORT   ENDPOINTS
default     example-ingress1   testaddr.local/etameta           localhost   NO    pear-service    5678           5
default     example-ingress1   testaddr2.local/otherpath        localhost   NO    apple-service   5678           1
default     example-ingress1   testaddr2.local/otherotherpath   localhost   NO    pear-service    5678           5
default     test-ingress-2     *                                localhost   NO    echo-service    8080           2
```

### lint

`kubectl ingress-nginx lint`可以检查名称空间或整个集群是否存在潜在的配置问题。在Ingress-nginx版本之间升级时，此命令特别有用。

```console
$ kubectl ingress-nginx lint --all-namespaces --verbose
Checking ingresses...
✗ anamespace/this-nginx
  - Contains the removed session-cookie-hash annotation.
       Lint added for version 0.24.0
       https://github.com/kubernetes/ingress-nginx/issues/3743
✗ othernamespace/ingress-definition-blah
  - The rewrite-target annotation value does not reference a capture group
      Lint added for version 0.22.0
      https://github.com/kubernetes/ingress-nginx/issues/3174

Checking deployments...
✗ namespace2/nginx-ingress-controller
  - Uses removed config flag --sort-backends
      Lint added for version 0.22.0
      https://github.com/kubernetes/ingress-nginx/issues/3655
  - Uses removed config flag --enable-dynamic-certificates
      Lint added for version 0.24.0
      https://github.com/kubernetes/ingress-nginx/issues/3808
```

要显示仅针对特定`ingress-nginx`版本添加的棉绒，请使用`--from-version`和`--to-version`标志

```console
$ kubectl ingress-nginx lint --all-namespaces --verbose --from-version 0.24.0 --to-version 0.24.0
Checking ingresses...
✗ anamespace/this-nginx
  - Contains the removed session-cookie-hash annotation.
       Lint added for version 0.24.0
       https://github.com/kubernetes/ingress-nginx/issues/3743

Checking deployments...
✗ namespace2/nginx-ingress-controller
  - Uses removed config flag --enable-dynamic-certificates
      Lint added for version 0.24.0
      https://github.com/kubernetes/ingress-nginx/issues/3808
```

### logs

`kubectl ingress-nginx logs` 几乎与 `kubectl logs`相似，与更少的标志。它将自动选择一个`ingress-nginx` Pod来读取日志。

```console
$ kubectl ingress-nginx logs -n ingress-nginx
-------------------------------------------------------------------------------
nginx Ingress controller
  Release:    dev
  Build:      git-48dc3a867
  Repository: git@github.com:kubernetes/ingress-nginx.git
-------------------------------------------------------------------------------

W0405 16:53:46.061589       7 flags.go:214] SSL certificate chain completion is disabled (--enable-ssl-chain-completion=false)
nginx version: nginx/1.15.9
W0405 16:53:46.070093       7 client_config.go:549] Neither --kubeconfig nor --master was specified.  Using the inClusterConfig.  This might not work.
I0405 16:53:46.070499       7 main.go:205] Creating API client for https://10.96.0.1:443
I0405 16:53:46.077784       7 main.go:249] Running in Kubernetes cluster version v1.10 (v1.10.11) - git (clean) commit 637c7e288581ee40ab4ca210618a89a555b6e7e9 - platform linux/amd64
I0405 16:53:46.183359       7 nginx.go:265] Starting nginx Ingress controller
I0405 16:53:46.193913       7 event.go:209] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"ingress-nginx", Name:"udp-services", UID:"82258915-563e-11e9-9c52-025000000001", APIVersion:"v1", ResourceVersion:"494", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap ingress-nginx/udp-services
...
```

### ssh

`kubectl ingress-nginx ssh`与`kubectl ingress-nginx exec -it --/bin/bash`完全相同。
如果您想快速放入正在运行的`i`ngress-nginx`容器内的shell中，请使用它

```console
$ kubectl ingress-nginx ssh -n ingress-nginx
www-data@nginx-ingress-controller-7cbf77c976-wx5pn:/etc/nginx$
```
