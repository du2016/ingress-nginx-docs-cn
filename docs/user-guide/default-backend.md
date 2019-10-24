# Default backend

默认的后端是处理所有URL路径并托管Nginx控制器无法理解的服务
(即未与Ingress映射的所有请求)。

基本上，默认后端公开两个URL

- `/healthz` that returns 200
- `/` that returns 404

!!! 例子
    [`/images/custom-error-pages`](https://github.com/kubernetes/ingress-nginx/tree/master/images/custom-error-pages)子目录提供另一项服务，用于自定义通过默认后端提供的错误页面。
