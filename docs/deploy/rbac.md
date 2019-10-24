# 基于角色的访问控制(rbac)

## 总览

此示例适用于在启用了RBAC的环境中部署的Nginx-ingress-controllers

基于角色的访问控制由四层组成:

1. `ClusterRole` - 分配给适用于整个集群的角色的权限
2. `ClusterRoleBinding` - 将ClusterRole绑定到特定帐户
3. `Role` - 分配给适用于特定名称空间的角色的权限
4. `RoleBinding` - 将角色绑定到特定帐户

为了将RBAC应用于`nginx-ingress-controller`，应将该控制器分配给`ServiceAccount`。
该`ServiceAccount`应该绑定到为`nginx-ingress-controller`定义的`Roles`和`ClusterRoles`

## 在此示例中创建serviceaccount

在此示例中，创建了一个ServiceAccount，即`nginx-ingress-serviceaccount`。

## 在此示例中授予的权限

在此示例中定义了两组权限。由名为`nginx-ingress-clusterrole`的`ClusterRole`定义的集群范围权限，
以及由名为`nginx-ingress-role`的`role`定义的特定于命名空间的权限

### 集群权限

授予这些权限是为了使nginx-ingress-controller能够充当跨集群的入口。
这些权限被授予名为`nginx-ingress-clusterrole`的ClusterRole

* `configmaps`, `endpoints`, `nodes`, `pods`, `secrets`: list, watch
* `nodes`: get
* `services`, `ingresses`: get, list, watch
* `events`: create, patch
* `ingresses/status`: update

### 命名空间权限

这些权限是特定于nginx-ingress名称空间授予的。这些权限被授予名为`nginx-ingress-role`的角色

* `configmaps`, `pods`, `secrets`: get
* `endpoints`: get

此外，为了支持领导者选举，nginx-ingress-controller需要使用resourceName `ingress-controller-leader-nginx`来访问configmap

>请注意，resourceNames不能用于使用"create"动作请求限制，因为授权者只能访问可以从请求URL，
方法和标头获得的信息("create"请求中的资源名称是请求body的一部分)。

* `configmaps`: get, update (资源名为`ingress-controller-leader-nginx`)
* `configmaps`: create

此resourceName是由  ingress controller定义的`election-id`和`ingress-class`的拼接，默认为

* `election-id`: `ingress-controller-leader`
* `ingress-class`: `nginx`
* `resourceName` : `<election-id>-<ingress-class>`

如果在启动Nginx-ingress-controller时覆盖了两个参数，请进行相应调整

### Bindings

ServiceAccount `nginx-ingress-serviceaccount`绑定到角色`nginx-ingress-role`和ClusterRole `nginx-ingress-clusterrole`。

与部署中的容器关联的serviceAccountName必须与serviceAccount匹配。
部署元数据，容器参数和POD_NAMESPACE中的名称空间引用应位于nginx-ingress名称空间中
