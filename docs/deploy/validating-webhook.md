# Validating webhook (准入控制器)

## 总览

nginx ingress controller提供了在入口进入集群之前对其进行验证的选项，以确保控制器将生成有效的配置

每当启用 [ValidatingAdmissionWebhook][1] 时，每次新的入口进入集群时，Kubernetes API服务器都会调用此控制器，并拒绝生成无法通过其验证的对象的Nginx配置
                                     
此功能需要对群集进行一些进一步的配置，因此它是一项可选功能，本节说明如何为您的群集启用它。

## 配置Webhook

### 生成webhook证书


#### 自签名证书

Validating webhook必须使用TLS进行服务，您需要生成一个证书。
请注意，kube API服务器正在检查证书的主机名，证书的通用名称将需要与服务名称匹配

!!! 例子
    要使用命名空间`ingress-nginx`中名为`ingress-validation-webhook`的服务运行验证webhook，请运行

    ```bash
    openssl req -x509 -newkey rsa:2048 -keyout certificate.pem -out key.pem -days 365 -nodes -subj "/CN=ingress-validation-webhook.ingress-nginx.svc"
    ```

##### 使用KUBERNETES CA

Kubernetes还提供了用于签署证书请求的原函数。这是有关如何使用它的示例

!!! example
    ```
    #!/bin/bash

    SERVICE_NAME=ingress-nginx
    NAMESPACE=ingress-nginx

    TEMP_DIRECTORY=$(mktemp -d)
    echo "creating certs in directory ${TEMP_DIRECTORY}"

    cat <<EOF >> ${TEMP_DIRECTORY}/csr.conf
    [req]
    req_extensions = v3_req
    distinguished_name = req_distinguished_name
    [req_distinguished_name]
    [ v3_req ]
    basicConstraints = CA:FALSE
    keyUsage = nonRepudiation, digitalSignature, keyEncipherment
    extendedKeyUsage = serverAuth
    subjectAltName = @alt_names
    [alt_names]
    DNS.1 = ${SERVICE_NAME}
    DNS.2 = ${SERVICE_NAME}.${NAMESPACE}
    DNS.3 = ${SERVICE_NAME}.${NAMESPACE}.svc
    EOF

    openssl genrsa -out ${TEMP_DIRECTORY}/server-key.pem 2048
    openssl req -new -key ${TEMP_DIRECTORY}/server-key.pem \
        -subj "/CN=${SERVICE_NAME}.${NAMESPACE}.svc" \
        -out ${TEMP_DIRECTORY}/server.csr \
        -config ${TEMP_DIRECTORY}/csr.conf

    cat <<EOF | kubectl create -f -
    apiVersion: certificates.k8s.io/v1beta1
    kind: CertificateSigningRequest
    metadata:
      name: ${SERVICE_NAME}.${NAMESPACE}.svc
    spec:
      request: $(cat ${TEMP_DIRECTORY}/server.csr | base64 | tr -d '\n')
      usages:
      - digital signature
      - key encipherment
      - server auth
    EOF

    kubectl certificate approve ${SERVICE_NAME}.${NAMESPACE}.svc

    for x in $(seq 10); do
        SERVER_CERT=$(kubectl get csr ${SERVICE_NAME}.${NAMESPACE}.svc -o jsonpath='{.status.certificate}')
        if [[ ${SERVER_CERT} != '' ]]; then
            break
        fi
        sleep 1
    done
    if [[ ${SERVER_CERT} == '' ]]; then
        echo "ERROR: After approving csr ${SERVICE_NAME}.${NAMESPACE}.svc, the signed certificate did not appear on the resource. Giving up after 10 attempts." >&2
        exit 1
    fi
    echo ${SERVER_CERT} | openssl base64 -d -A -out ${TEMP_DIRECTORY}/server-cert.pem

    kubectl create secret generic ingress-nginx.svc \
        --from-file=key.pem=${TEMP_DIRECTORY}/server-key.pem \
        --from-file=cert.pem=${TEMP_DIRECTORY}/server-cert.pem \
        -n ${NAMESPACE}
    ```

#### 使用 helm

要使用helm生成证书，可以使用以下代码段

!!! 例子
    ```
    {{- $cn := printf "%s.%s.svc" ( include "nginx-ingress.validatingWebhook.fullname" . ) .Release.Namespace }}
    {{- $ca := genCA (printf "%s-ca" ( include "nginx-ingress.validatingWebhook.fullname" . )) .Values.validatingWebhook.certificateValidity -}}
    {{- $cert := genSignedCert $cn nil nil .Values.validatingWebhook.certificateValidity $ca -}}
    ```

### Ingress controller 参数

要在  ingress controller中启用此功能，您_需要_在命令行中提供3个标志

|flag|description|example usage|
|-|-|-|
|`--validating-webhook`|The address to start an admission controller on|`:8080`|
|`--validating-webhook-certificate`|The certificate the webhook is using for its TLS handling|`/usr/local/certificates/validating-webhook.pem`|
|`--validating-webhook-key`|The key the webhook is using for its TLS handling|`/usr/local/certificates/validating-webhook-key.pem`|

### kube API server参数

Validating webhook功能需要在kube API服务器端进行特定设置。根据您的kubernetes版本，默认情况下可以启用或禁用该标志。
要检查您的kube API服务器是否以必需的标志运行，请参阅kubernetes文档。

### 其他Kubernetes对象

一旦将  ingress controller和kube API服务器都配置为服务Webhook，添加即可使用以下对象配置Webhook:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: ingress-validation-webhook
  namespace: ingress-nginx
spec:
  ports:
  - name: admission
    port: 443
    protocol: TCP
    targetPort: 8080
  selector:
    app: nginx-ingress
    component: controller
---
apiVersion: admissionregistration.k8s.io/v1beta1
kind: ValidatingWebhookConfiguration
metadata:
  name: check-ingress
webhooks:
- name: validate.nginx.ingress.kubernetes.io
  rules:
  - apiGroups:
    - extensions
    apiVersions:
    - v1beta1
    operations:
    - CREATE
    - UPDATE
    resources:
    - ingresses
  failurePolicy: Fail
  clientConfig:
    service:
      namespace: ingress-nginx
      name: ingress-validation-webhook
      path: /extensions/v1beta1/ingress
    caBundle: <pem encoded ca cert that signs the server cert used by the webhook>
```

[1]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/#validatingadmissionwebhook