
## 编写一个 operator

> 本文来演示如何创建一个operator, 该operator会自动监管应用的pod数量。并且，把这个operator部署在k3s 集群上，让它真正运行起来。

## 安装operator-sdk

&emsp;&emsp; Mac 直接用 `brew` 安装即可。其它平台可以参考https://github.com/operator-framework/operator-sdk/blob/master/doc/user/install-operator-sdk.md

```shell
Mathew : ~  🚀  ==> brew install operator-sdk
Mathew : ~  🚀  ==> operator-sdk version
operator-sdk version: "v0.16.0", commit: "55f1446c5f472e7d8e308dcdf36d0d7fc44fc4fd", go version: "go1.14 darwin/amd64"
```

## 新建一个operator 项目， 比如 operator-mathew

> 工程目录为 $GOPATH/src/github.com/operator-mathew

```shell
Mathew : ~/work/go/src/github.com  🚀  ==> operator-sdk new operator-mathew
INFO[0000] Creating new Go operator 'operator-mathew'.
INFO[0000] Created go.mod
INFO[0000] Created tools.go
INFO[0000] Created cmd/manager/main.go
INFO[0000] Created build/Dockerfile
INFO[0000] Created build/bin/entrypoint
INFO[0000] Created build/bin/user_setup
INFO[0000] Created deploy/service_account.yaml
INFO[0000] Created deploy/role.yaml
INFO[0000] Created deploy/role_binding.yaml
INFO[0000] Created deploy/operator.yaml
INFO[0000] Created pkg/apis/apis.go
INFO[0000] Created pkg/controller/controller.go
INFO[0000] Created version/version.go
INFO[0000] Created .gitignore
INFO[0000] Validating project
INFO[0013] Project validation successful.
INFO[0013] Project creation complete.
```

# 查看目录结构

&emsp;&emsp;可以看到整个工程的框架已经被operator-sdk 创建好了。


```shell
Mathew : ~/work/go/src/github.com  🚀  ==> tree operator-mathew/
operator-mathew/
├── build
│   ├── Dockerfile
│   └── bin
│       ├── entrypoint
│       └── user_setup
├── cmd
│   └── manager
│       └── main.go
├── deploy
│   ├── operator.yaml
│   ├── role.yaml
│   ├── role_binding.yaml
│   └── service_account.yaml
├── go.mod
├── go.sum
├── pkg
│   ├── apis
│   │   └── apis.go
│   └── controller
│       └── controller.go
├── tools.go
└── version
    └── version.go

9 directories, 14 files
```

## 业务逻辑代码只需关心两个方面:

1. 自定义API

```golang
Mathew : ~/work/go/src/github.com/operator-mathew  🚀  ==> cat pkg/apis/apis.go
package apis

import (
	"k8s.io/apimachinery/pkg/runtime"
)

// AddToSchemes may be used to add all resources defined in the project to a Scheme
var AddToSchemes runtime.SchemeBuilder

// AddToScheme adds all Resources to the Scheme
func AddToScheme(s *runtime.Scheme) error {
	return AddToSchemes.AddToScheme(s)
}
```
2. 自定义控制器

```golang
Mathew : ~/work/go/src/github.com/operator-mathew  🚀  ==> cat pkg/controller/controller.go
package controller

import (
	"sigs.k8s.io/controller-runtime/pkg/manager"
)

// AddToManagerFuncs is a list of functions to add all Controllers to the Manager
var AddToManagerFuncs []func(manager.Manager) error

// AddToManager adds all Controllers to the Manager
func AddToManager(m manager.Manager) error {
	for _, f := range AddToManagerFuncs {
		if err := f(m); err != nil {
			return err
		}
	}
	return nil
}
```

## 开始编写逻辑代码

### 使用`add api` 创建新的API资源

使用 `--kind`    来指定新API的名称，这里命名为  `Mathew`

```shell
Mathew : ~/work/go/src/github.com/operator-mathew  🚀  ==> operator-sdk add api --api-version=mathew.cloudnative.cool/v1 --kind=Mathew
INFO[0000] Generating api version mathew.cloudnative.cool/v1 for kind Mathew.
INFO[0000] Created pkg/apis/mathew/group.go
INFO[0002] Created pkg/apis/mathew/v1/mathew_types.go
INFO[0002] Created pkg/apis/addtoscheme_mathew_v1.go
INFO[0002] Created pkg/apis/mathew/v1/register.go
INFO[0002] Created pkg/apis/mathew/v1/doc.go
INFO[0002] Created deploy/crds/mathew.cloudnative.cool_v1_mathew_cr.yaml
INFO[0002] Running deepcopy code-generation for Custom Resource group versions: [mathew:[v1], ]
INFO[0009] Code-generation complete.
INFO[0009] Running CRD generator.
INFO[0010] CRD generation complete.
INFO[0010] API generation complete.
```

可以看到，对应的CR(customer resource)已经被operator-sdk 创建。

```yaml
Mathew : ~/work/go/src/github.com/operator-mathew  🚀  ==> cat deploy/crds/mathew.cloudnative.cool_mathews_crd.yaml
apiVersion: apiextensions.k8s.io/v1beta1
kind: CustomResourceDefinition
metadata:
  name: mathews.mathew.cloudnative.cool
spec:
  group: mathew.cloudnative.cool
  names:
    kind: Mathew
    listKind: MathewList
    plural: mathews
    singular: mathew
  scope: Namespaced
  subresources:
    status: {}
  validation:
    openAPIV3Schema:
      description: Mathew is the Schema for the mathews API
      properties:
        apiVersion:
          description: 'APIVersion defines the versioned schema of this representation
            of an object. Servers should convert recognized schemas to the latest
            internal value, and may reject unrecognized values. More info: https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#resources'
          type: string
        kind:
          description: 'Kind is a string value representing the REST resource this
            object represents. Servers may infer this from the endpoint the client
            submits requests to. Cannot be updated. In CamelCase. More info: https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#types-kinds'
          type: string
        metadata:
          type: object
        spec:
          description: MathewSpec defines the desired state of Mathew
          type: object
        status:
          description: MathewStatus defines the observed state of Mathew
          type: object
      type: object
  version: v1
  versions:
  - name: v1
    served: true
    storage: true
```

### 使用`add controller`创建对应的控制器

```shell
Mathew : ~/work/go/src/github.com/operator-mathew  🚀  ==> operator-sdk add controller --api-version=mathew.cloudnative.cool/v1 --kind=Mathew
INFO[0000] Generating controller version mathew.cloudnative.cool/v1 for kind Mathew.
INFO[0000] Created pkg/controller/mathew/mathew_controller.go
INFO[0000] Created pkg/controller/add_mathew.go
INFO[0000] Controller generation complete.
```

### 添加代码

在资源类型文件中定义自己的资源结构。本示例的operator会监控Mathew 资源，并根据Mathew 资源中的size 域来更改对应的pod 数量。MathewStatus 结构会显示实时状态。

```golang
Mathew : ~/work/go/src/github.com/operator-mathew  🚀  ==> less pkg/apis/mathew/v1/mathew_types.go
package v1

import (
        metav1 "k8s.io/apimachinery/pkg/apis/meta/v1"
)

// EDIT THIS FILE!  THIS IS SCAFFOLDING FOR YOU TO OWN!
// NOTE: json tags are required.  Any new fields you add must have json tags for the fields to be serialized.

// MathewSpec defines the desired state of Mathew
type MathewSpec struct {
        // INSERT ADDITIONAL SPEC FIELDS - desired state of cluster
        // Important: Run "operator-sdk generate k8s" to regenerate code after modifying this file
        // Add custom validation using kubebuilder tags: https://book-v1.book.kubebuilder.io/beyond_basics/generating_crd.html
        Size int32  `json:"size"`
}

// MathewStatus defines the observed state of Mathew
type MathewStatus struct {
        // INSERT ADDITIONAL STATUS FIELD - define observed state of cluster
        // Important: Run "operator-sdk generate k8s" to regenerate code after modifying this file
        // Add custom validation using kubebuilder tags: https://book-v1.book.kubebuilder.io/beyond_basics/generating_crd.html
        PodNames []string  `json:"podnames"`
}
```

>详细代码参见：https://github.com/Mathew857/operator-mathew/blob/master/pkg/apis/mathew/v1/mathew_types.go#L15

逻辑控制代码：
```golang
// The Controller will requeue the Request to be processed again if the returned error is non-nil or
// Result.Requeue is true, otherwise upon completion it will remove the work from the queue.
func (r *ReconcileMathew) Reconcile(request reconcile.Request) (reconcile.Result, error) {
	reqLogger := log.WithValues("Request.Namespace", request.Namespace, "Request.Name", request.Name)
	reqLogger.Info("Reconciling Mathew")

	// Fetch the Mathew instance
	mathew := &mathewv1.Mathew{}
	err := r.client.Get(context.TODO(), request.NamespacedName, mathew)
	if err != nil {
		if errors.IsNotFound(err) {
			// Request object not found, could have been deleted after reconcile request.
			// Owned objects are automatically garbage collected. For additional cleanup logic use finalizers.
			// Return and don't requeue
			return reconcile.Result{}, nil
		}
		// Error reading the object - requeue the request.
		return reconcile.Result{}, err
	}


	// Check if this Deployment already exists, if not create a new one
	found := &appsv1.Deployment{}
	err = r.client.Get(context.TODO(), types.NamespacedName{Name: mathew.Name, Namespace: mathew.Namespace}, found)
	if err != nil && errors.IsNotFound(err) {
		// Define a new deployment
		dep := r.deploymentforMathew(mathew)
		reqLogger.Info("Creating a new Deployment", "Deployment.Namespace", dep.Namespace, "Deployment.Name", dep.Name)
		err = r.client.Create(context.TODO(), dep)
		if err != nil {
			reqLogger.Error(err, "Failed to create new Deployment", "Deployment.Namespace", dep.Namespace, "Deployment.Name", dep.Name)
			return reconcile.Result{}, err
		}

		// Deployment created successfully - don't requeue
		return reconcile.Result{}, nil
	} else if err != nil {
		reqLogger.Error(err, "Failed to get Deployment")
		return reconcile.Result{}, err
	}

	// Ensure the deployment size is the same as the spec
	size := mathew.Spec.Size
	if *found.Spec.Replicas != size {
		found.Spec.Replicas = &size
		err = r.client.Update(context.TODO(), found)
		if err != nil {
			reqLogger.Error(err, "Failed to update deployment", "Deployment.Namespaces", found.Namespace, "Deployment.Name", found.Name)
			return reconcile.Result{}, err
		}
		// Spec updated - return and requeue
		return reconcile.Result{Requeue: true}, nil
	}


	// Update the Mathew status with pod names
	// List the pods for this mathew's deployment
	podList := &corev1.PodList{}
	listOpts := []client.ListOption{
		client.InNamespace(mathew.Namespace),
		client.MatchingLabels(labelsForMathew(mathew.Name)),
	}
	if err = r.client.List(context.TODO(), podList, listOpts...); err != nil {
		reqLogger.Error(err, "Failed to list pods", "Mathew.Namespaces", mathew.Namespace, "Mathew.Name", mathew.Name)
		return reconcile.Result{}, err
	}
	podNames := getPodNames(podList.Items)

	// Update status.PodNames if needed
	if !reflect.DeepEqual(podNames, mathew.Status.PodNames) {
		mathew.Status.PodNames = podNames
		err := r.client.Status().Update(context.TODO(), mathew)
		if err != nil {
			reqLogger.Error(err, "Failed to update Mathew status")
			return reconcile.Result{}, err
		}
	}
	return reconcile.Result{}, nil
}
```
> 详细代码参见： https://github.com/Mathew857/operator-mathew/blob/master/pkg/controller/mathew/mathew_controller.go#L92-L162


## 构建对应的operator image

现在，代码已经写好了。我们要让它运行起来。在云平台中，组件是容器化运行，那首先我们需要创建一个image. 使用build 参数可以快速把代码打包到一个image. 当然你可以修改Dockerfile 来定制特别的需求，这里选择默认配置。构建过程如下：

```shell
Mathew : ~/work/go/src/github.com/operator-mathew  🚀  ==> operator-sdk build registry.cn-beijing.aliyuncs.com/mathew-cloud/operator-mathew
INFO[0000] Building OCI image registry.cn-beijing.aliyuncs.com/mathew-cloud/operator-mathew
Sending build context to Docker daemon  43.13MB
Step 1/7 : FROM registry.access.redhat.com/ubi8/ubi-minimal:latest
 ---> c103a05423dd
Step 2/7 : ENV OPERATOR=/usr/local/bin/operator-mathew     USER_UID=1001     USER_NAME=operator-mathew
 ---> Running in 0f1159ccb078
Removing intermediate container 0f1159ccb078
 ---> 2a7b094782f2
Step 3/7 : COPY build/_output/bin/operator-mathew ${OPERATOR}
 ---> 7a1b7b4dc762
Step 4/7 : COPY build/bin /usr/local/bin
 ---> 08c7034b5164
Step 5/7 : RUN  /usr/local/bin/user_setup
 ---> Running in 4a258c9533aa
+ echo 'operator-mathew:x:1001:0:operator-mathew user:/root:/sbin/nologin'
+ mkdir -p /root
+ chown 1001:0 /root
+ chmod ug+rwx /root
+ rm /usr/local/bin/user_setup
Removing intermediate container 4a258c9533aa
 ---> c4ef02136051
Step 6/7 : ENTRYPOINT ["/usr/local/bin/entrypoint"]
 ---> Running in 359fd39076a3
Removing intermediate container 359fd39076a3
 ---> 987581b9f3b4
Step 7/7 : USER ${USER_UID}
 ---> Running in 18e1301504a0
Removing intermediate container 18e1301504a0
 ---> e3453eb87d6e
Successfully built e3453eb87d6e
Successfully tagged registry.cn-beijing.aliyuncs.com/mathew-cloud/operator-mathew:latest
INFO[0004] Operator build complete.
```

把该镜像推送到一个image 仓库，这里选择阿里云。

```shell
Mathew : ~/work/go/src/github.com/operator-mathew  🚀  ==> docker push registry.cn-beijing.aliyuncs.com/mathew-cloud/operator-mathew:latest
The push refers to repository [registry.cn-beijing.aliyuncs.com/mathew-cloud/operator-mathew]
005f0a80dddf: Pushed
d1b6429f25ef: Pushed
da27e6dfb600: Pushed
37330a2a1954: Pushed
d6ec160dc60f: Pushed
latest: digest: sha256:ebd813b0b546ee31d86a04f60a0fc8a115c3ebf5855e97e2cb41ce2afc70da43 size: 1363
```

### 部署Operator

我们使用YAML文件来部署这个operator到云平台，当然你也可以使用Helm. Operator-SDK 已经自动生成了所有相关的部署文件，我们只需在部署文件中配置上面这个image 即可.

```shell
root@k3s:~/operator-mathew# sed -i  's@REPLACE_IMAGE@registry.cn-beijing.aliyuncs.com/mathew-cloud/operator-mathew:latest@g' deploy/operator.yaml
```

可以看到，在部署之前，当前集群并没有`kind`资源：
```shell
root@k3s:~/operator-mathew# kubectl get mathew
error: the server doesn't have a resource type "mathew"
```

开始部署：

```shell
root@k3s:~/operator-mathew/deploy# kubectl create -f .
deployment.apps/operator-mathew created
role.rbac.authorization.k8s.io/operator-mathew created
rolebinding.rbac.authorization.k8s.io/operator-mathew created
serviceaccount/operator-mathew created
root@k3s:~/operator-mathew/deploy# cd crds/
root@k3s:~/operator-mathew/deploy/crds# kubectl create -f mathew.cloudnative.cool_
mathew.cloudnative.cool_mathews_crd.yaml   mathew.cloudnative.cool_v1_mathew_cr.yaml
root@k3s:~/operator-mathew/deploy/crds# kubectl create -f mathew.cloudnative.cool_
mathew.cloudnative.cool_mathews_crd.yaml   mathew.cloudnative.cool_v1_mathew_cr.yaml
root@k3s:~/operator-mathew/deploy/crds# kubectl create -f mathew.cloudnative.cool_mathews_crd.yaml
Warning: apiextensions.k8s.io/v1beta1 CustomResourceDefinition is deprecated in v1.16+, unavailable in v1.22+; use apiextensions.k8s.io/v1 CustomResourceDefinition
customresourcedefinition.apiextensions.k8s.io/mathews.mathew.cloudnative.cool created
```

可以看到该operator已经运行起来，并且该集群已经有了`mathew`资源了

```shell
root@k3s:~/operator-mathew/deploy/crds# kubectl get mathew
NAME             AGE
example-mathew   10s
root@k3s:~# kubectl get pod
NAME                              READY   STATUS    RESTARTS   AGE
operator-mathew-684bf8b5f-9jgz7   1/1     Running   0          15s
root@k3s:~# kubectl get crd
NAME                              CREATED AT
addons.k3s.cattle.io              2020-12-06T11:09:04Z
helmcharts.helm.cattle.io         2020-12-06T11:09:04Z
helmchartconfigs.helm.cattle.io   2020-12-06T11:09:04Z
mathews.mathew.cloudnative.cool   2020-12-06T11:21:50Z
```

好了，那我们就开始使用这个`mathew` 资源吧！

```shell
root@k3s:~/operator-mathew/deploy/crds# cat mathew.cloudnative.cool_v1_mathew_cr.yaml
apiVersion: mathew.cloudnative.cool/v1
kind: Mathew
metadata:
  name: example-mathew
spec:
  # Add fields here
  size: 2

root@k3s:~/operator-mathew/deploy/crds# kubectl get mathew
NAME             AGE
example-mathew   107s
root@k3s:~/operator-mathew/deploy/crds# kubectl get pod -o wide
NAME                              READY   STATUS    RESTARTS   AGE     IP           NODE   NOMINATED NODE   READINESS GATES
operator-mathew-684bf8b5f-9jgz7   1/1     Running   0          9m10s   10.42.0.10   k3s    <none>           <none>
example-mathew-767469bf84-wdb2x   1/1     Running   0          6m12s   10.42.0.11   k3s    <none>           <none>
example-mathew-767469bf84-8lk5n   1/1     Running   0          6m12s   10.42.0.12   k3s    <none>           <none>
```

那把size 改为 3 试试？可以看到pod数量增长到了3个！

```shell
root@k3s:~/operator-mathew/deploy/crds# kubectl edit mathew example-mathew
mathew.mathew.cloudnative.cool/example-mathew edited
root@k3s:~/operator-mathew/deploy/crds# kubectl get pod -o wide
NAME                              READY   STATUS              RESTARTS   AGE     IP           NODE   NOMINATED NODE   READINESS GATES
operator-mathew-684bf8b5f-9jgz7   1/1     Running             0          10m     10.42.0.10   k3s    <none>           <none>
example-mathew-767469bf84-wdb2x   1/1     Running             0          7m49s   10.42.0.11   k3s    <none>           <none>
example-mathew-767469bf84-8lk5n   1/1     Running             0          7m49s   10.42.0.12   k3s    <none>           <none>
example-mathew-767469bf84-q74nz   0/1     ContainerCreating   0          3s      <none>       k3s    <none>           <none>
```

至此，一个operator项目已经完成，关于这个operator的所有代码可以在这里找到
> https://github.com/Mathew857/operator-mathew.git
