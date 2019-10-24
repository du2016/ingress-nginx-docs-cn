# 开发 nginx ingress controller

本文档说明了如何开始为nginx Ingress controller进行开发。它包括如何构建，测试和发布  ingress controller。

## 快速开始

### 获取代码

必须将代码检出为k8s.io的子目录，而不是github.com。

```
mkdir -p $GOPATH/src/k8s.io
cd $GOPATH/src/k8s.io
# Replace "$YOUR_GITHUB_USERNAME" below with your github username
git clone https://github.com/$YOUR_GITHUB_USERNAME/ingress-nginx.git
cd ingress-nginx
```

### 初始开发人员环境构建

>**前提**: 必须安装Minikube.
查看 [releases](https://github.com/kubernetes/minikube/releases) 获取安装说明. 

如果您使用 **MacOS** 并部署到 **minikube**, 则以下命令将构建本地nginx控制器容器镜像，
并将  ingress controller部署到在名称空间ingress-nginx中启用RBAC的minikube集群中:

```
$ make dev-env
```

### 升级deployment

可以使用以下命令重建Nginx控制器容器映像:
```
$ ARCH=amd64 TAG=dev REGISTRY=$USER/ingress-controller make build container
```

该镜像仅由重建后创建的Pod使用。删除旧pod将导致新的Pod被拉起:
```
$ kubectl get pods -n ingress-nginx
$ kubectl delete pod -n ingress-nginx nginx-ingress-controller-<unique-pod-id>
```

## 依赖

构建使用`vendor`目录中的依赖项, 必须在构建二进制文件/映像之前安装该依赖项. 有时，您可能需要更新依赖项.

本指南要求您安装 [dep](https://github.com/golang/dep) 依赖管理工具.

检查您使用的`Dep`的版本，并确保它是最新的.

```console
$ dep version
dep:
 version     : devel
 build date  : 
 git hash    : 
 go version  : go1.9
 go compiler : gc
 platform    : linux/amd64
```

如果您使用的是`Dep`的旧版本，则可以按照以下说明进行更新:

```console
$ go get -u github.com/golang/dep
```

这将自动将依赖项保存到`vendor` 目录.

```console
$ cd $GOPATH/src/k8s.io/ingress-nginx
$ dep ensure
$ dep ensure -update
$ dep prune
```

## Building

所有ingress controllers都是通过Makefile构建的。
根据您的要求，您可以构建原始服务器二进制文件，本地容器映像或将映像推送到远程存储库.

为了使用本地Docker，您可能需要设置以下环境变量:

```console
# "gcloud docker" (default) or "docker"
$ export DOCKER=<docker>

# "quay.io/kubernetes-ingress-controller" (default), "index.docker.io", or your own registry
$ export REGISTRY=<your-docker-registry>
```

要查找registry，只需运行: `docker system info | grep Registry`

### 构建e2e测试镜像

e2e测试映像也可以通过Makefile构建.

```console
$ make e2e-test-image
```

然后，您可以通过导出映像并将其加载到minikube docker上下文中，使该映像在您的minikube主机上可用

```console
$ docker save nginx-ingress-controller:e2e |  (eval $(minikube docker-env) && docker load)
```


### nginx Controller

构建原始服务器二进制文件
```console
$ make build
```

[TODO](https://github.com/kubernetes/ingress-nginx/issues/387): 添加原始服务器二进制文件所需的更多特定说明.

构建本地容器映像

```console
$ TAG=<tag> REGISTRY=$USER/ingress-controller make container
```

将容器映像推送到远程镜像仓库

```console
$ TAG=<tag> REGISTRY=$USER/ingress-controller make push
```

## Deploying

有几种将  ingress controller部署到集群上的方法.
请检查 [部署指南](./deploy)

## 测试

要运行单元测试，只需运行

```console
$ cd $GOPATH/src/k8s.io/ingress-nginx
$ make test
```

如果您有权访问Kubernetes集群，则还可以使用ginkgo运行e2e测试

```console
$ cd $GOPATH/src/k8s.io/ingress-nginx
$ make e2e-test
```

NOTE: 如果e2e pod一直挂在ImagePullBackoff中，
      请确保已将e2e nginx-ingress-controller 镜像提供给minikube，如构建e2e测试镜像中所述
      
要在本地运行lua代码的单元测试，请运行:

```console
$ cd $GOPATH/src/k8s.io/ingress-nginx
$ ./rootfs/etc/nginx/lua/test/up.sh
$ make lua-test
```

Lua测试位于 `$GOPATH/src/k8s.io/ingress-nginx/rootfs/etc/nginx/lua/test`.
创建新的测试文件时，它必须遵循命名约定<mytest> _test.lua，否则它将被忽略。

## 发行版本

如上所示，所有Makefile都会产生一个发行二进制文件。要将其发布给更广泛的Kubernetes用户群，
请将映像推送到容器镜像仓库, 例如
[gcr.io](https://cloud.google.com/container-registry/). 所有发行映像均托管在 `gcr.io/google_containers`，
并根据[semver](http://semver.org/) 方案进行标记.

示例发行版可能看起来像:
```
$ make release
```

请遵循以下准则以减少发布:

* 更新 [release](https://help.github.com/articles/creating-releases/)
页面使用给定image标签相对应的主要更改的简短描述.
* 剪切释放分支(如果适用)。发行分支遵循
`controller-release-version`的格式. 通常，预发行版是从HEAD中剪切的。所有主要功能工作都在HEAD中完成。
将特定的错误修复精选到发布分支中。
* 如果您不确定代码的稳定性,
[tag](https://help.github.com/articles/working-with-tags/) 需要设置为alpha或beta.
通常，发布分支应具有稳定的代码。
