# 【kubernetes系列学习】client-go学习与实践

release author: ningan123

release time: 2022-08-09



## client-go客户端对象

client-go支持RESTClient、ClientSet、DynamicClient、DiscoveryClient四种客户端与Kubernetes Api Server进行交互。

- RESTClient：最基础的客户端，主要是对HTTP请求进行了封装，支持Json和Protobuf格式的数据。

- DiscoveryClient：发现客户端，负责发现APIServer支持的资源组、资源版本和资源信息的(GVR)。
- ClientSet：负责操作Kubernetes内置的资源对象，例如：Pod、Services等。
- DynamicClient：动态客户端，可以对任意的Kubernetes资源对象进行通用操作，包括CRD。





## client-go-demo 工程初始化

```bash
[root@master ~]# mkdir client-go-demo
[root@master ~]#
[root@master ~]#
[root@master ~]# cd client-go-demo/
[root@master client-go-demo]#
[root@master client-go-demo]# ll
总用量 0
[root@master client-go-demo]#
[root@master client-go-demo]#
[root@master client-go-demo]# go mod init ningan.com/client-go-demo
go: creating new go.mod: module ningan.com/client-go-demo
[root@master client-go-demo]#
[root@master client-go-demo]# ll
总用量 4
-rw-r--r-- 1 root root 42 8月   9 04:06 go.mod
[root@master client-go-demo]#
[root@master client-go-demo]# cat go.mod
module ningan.com/client-go-demo

go 1.17



```



## RESTClient

### 

RESTClient是最基础的客户端，其他的ClientSet、DynamicClient即DscoveryClient都是基于RESTClient实现的。

RESTClient对HTTP Client进行了封装，实现了Restful风格的API。

它具有很高的灵活性，数据不依赖于方法和资源，因此RESTClient能够处理多种类型的调用，返回不同的数据格式。



demo示例：通过RESTClient示例列出某一命名空间下的Pod资源对象

```go
package main

import (
	"context"
	"fmt"

	corev1 "k8s.io/api/core/v1"
	metav1 "k8s.io/apimachinery/pkg/apis/meta/v1"
	"k8s.io/client-go/kubernetes/scheme"
	"k8s.io/client-go/rest"
	"k8s.io/client-go/tools/clientcmd"
)

/*
	@Author: ningan
	@Desc: 获取kube-system命名空间下的Pod列表
*/

func main() {
	/*
		1. k8s的配置文件
		2. 保证开发机能通过这个配置文件连接到k8s集群
	*/

	// 1. 加载配置文件，生成config对象
	config, err := clientcmd.BuildConfigFromFlags("", "/root/.kube/config")
	if err != nil {
		panic(err)
		// panic(err.Error())
	}

	// 2. 配置API路径  请求的HTTP路径
	config.APIPath = "api" // pods,  /api/v1/pods
	// config.APIPath = "apis" // deployments,  /apis/apps/v1/namespaces/{namespace}/deployments/{deployment}

	// 3. 配置请求的资源组/资源版本 GV
	config.GroupVersion = &corev1.SchemeGroupVersion // 无名资源组, group: " ", version: "v1"

	// 4. 配置数据的编解码工具  序列化和反序列化
	config.NegotiatedSerializer = scheme.Codecs

	// 5. 通过kubeconfg配置信息实例化RESTClient对象
	restClient, err := rest.RESTClientFor(config)
	if err != nil {
		panic(err)
	}

	// 6. 定义接收返回值的变量
	result := &corev1.PodList{}

	// 跟APIServer交互
	err = restClient.
		Get().                                                                  // RESTClient对象构建HTTP请求参数  请求方法：Get、Post、Put、Delete、Patch
		Namespace("kube-system").                                               // 命名空间
		Resource("pods").                                                       // 资源名
		VersionedParams(&metav1.ListOptions{Limit: 50}, scheme.ParameterCodec). // 将一些查询选项(如limit、TimeoutSeconds)添加到请求参数中 limit参数表示最多检索出多少信息
		Do(context.TODO()).                                                     // 执行该请求
		Into(result)                                                            // 将kube-apiserver返回的结果（Result对象）解析到corev1.PodList对象中
	if err != nil {
		panic(err)
	}

	/*
		1）Get定义请求方式，返回了一个Request结构体对象。这个Request结构体对象，就是构建访问APIServer用的。
		2）依次执行了Namespace、Resource、VersionedParams，构建与APIServer交互的参数。
		3）Do方法通过requets发起请求，然后通过transformResponse解析请求返回，并绑定到对应资源对象的结构体对象上。这里的话，表示的是corev1.PodList对象。
		4）request先是检查了有没有可用的client，在这里开始调用net/http包的功能。
	*/

	/*
		1) RESTClient发送请求的过程对Go语言标准库net/http进行了封装，由Do -> Request函数实现。
		2) 请求发送之前需要根据请求参数生成请求的RESTful URL，由r.URL().String()函数完成。 http://xxxx:x/api/v1/namespaces/kube-system/pods?limit=1
		3) 通过Go语言标准库net/http向RESTful URL(即kube-apiserver)发送请求，请求得到的结果存放在http.Response的Body对象中，fn函数（即transformResponse）将结果转化为资源对象。
	*/

	// 格式化输出结果
	for _, item := range result.Items {
		fmt.Printf("namespace: %v, name: %v, status: %v\n", item.Namespace, item.Name, item.Status.Phase)
	}
}

```



运行：

```bash
[root@master demo1]# go run main.go
namespace: kube-system, name: calico-kube-controllers-67878b879f-2x5xg, status: Running
namespace: kube-system, name: calico-node-4xwrz, status: Running
namespace: kube-system, name: metrics-server-68955dc6f-xvv9t, status: Running
```



## ClientSet

RESTClient是一张最基础的客户端，使用时需要指定Resource和Version等信息，编写代码需要提前知道Resource所在的Group和对应的Version信息。

相比RESTClient，ClientSet使用起来更加便捷，一般情况下，开发者对Kubernetes进行二次开发时通常使用ClientSet。



ClientSet在RESTClient的基础上封装了对Resource和Version的管理方法。每一个Resource可以理解为一个客户端，而ClientSet则是多个客户端的几核，每一个Resource和Version都以函数的方法暴露给开发者，例如ClientSet提供的RbacV1、CoreV1、NetworkingV1等接口参数。





demo示例：通过ClientSet示例列出某一命名空间下的Pod资源对象

```go
package main

import (
	"context"
	"fmt"

	metav1 "k8s.io/apimachinery/pkg/apis/meta/v1"
	"k8s.io/client-go/kubernetes"
	"k8s.io/client-go/tools/clientcmd"
)

/*
	@Author: ningan
	@Desc: 获取kube-system命名空间下的Pod列表
*/

func main() {
	// 1. 加载配置文件，生成config对象
	config, err := clientcmd.BuildConfigFromFlags("", "/root/.kube/config")
	if err != nil {
		panic(err.Error())
	}

	// 2. 通过kubeconfig配置信息实例化ClientSet 该对象用于管理所有Resource的客户端
	clientset, err := kubernetes.NewForConfig(config)
	if err != nil {
		panic(err.Error())
	}

	// 3. 查询
	result, err := clientset.
		CoreV1().            // 返回CoreV1Client实例
		Pods("kube-system"). // 指定查询的资源预计指定资源的namespace，namespace如果为空，表示查询所有namespace
		// Namespaces().     // 查询namespace
		List(context.TODO(), metav1.ListOptions{Limit: 50})
	if err != nil {
		panic(err.Error())
	}
	/*
		1) CoreV1返回CoreV1Client实例对象
		2) Pods 调用了newPods函数，该函数返回的试PodInterface对象，PodInterface对象实现了Pods资源相关的全部方法，同时再newPods里面还将RESTClient实例对象赋值给了对应的Client属性
		3) List内部使用RestClient与k8s APIServer进行了交互
	*/

	/*
		1) clientset.CoreV1().Pods表示请求core核心资源组的v1资源版本下的Pod资源对象，其内部设置了APIPath请求的HTTP路径，GroupVersion请求的资源组、资源版本，NegotiatedSerializer数据的编解码器。
		2) Pods函数是一个资源对象接口，用于Pod资源对象的管理。例如，对Pod资源执行Create、Update、Delete、Get、List、Watch、Patch等操作，这些操作实际上是对RESTClient进行了封装，可以设置选项（如Limit、TimeoutSeconds等）。
	*/

	// 4. 格式化输出结果
	for _, item := range result.Items {
		fmt.Printf("namespace: %v, name: %v, status: %v\n", item.Namespace, item.Name, item.Status.Phase)
	}

}

```







## DynamicClient



DynamicClient是一种动态客户端，它可以对任意Kubernetes资源进行RESTFul操作，包括CRD自定义资源。

DynamicClient与ClientSet操作类似，同样封装了RESTClient，同样提供了Create、Update、Delete、Get、List、Watch、Patch等方法。



DynamicClient与ClientSet最大的不同之处在于：

- ClientSet仅能访问Kubernetes自带的资源（即客户端几核内的资源），不能直接访问CRD自定义资源。ClientSet需要预先实现每种Resource和Version的操作，其内部的数据都是结构化数据（即已知数据结构）

- DynamicClient内部实现了Unstructured，用于处理非结构化数据结构（即无法提前预知数据结构），这也是DynamicClient能够处理CRD自定义资源的关键。



DynamicClient的处理过程将Resource（例如PodList）转化成Unstructured结构类型，Kubernetes的所有Resource都可以转化为该结构类型。处理完成后，再将Unstructured转换成PodList。整个过程类似于Go语言的interface{}断言转换过程。另外，Unstructured结构类型是通过map[string]interface{}转换的。





两个重要的知识点：

- Object.runtime：Kubernetes中的所有资源对象都实现了这个接口，其中包含DeepCopyObject和GetObjectKind的方法，分别用于对象深拷贝和获取对象的具体资源类型。
- Unstructured：包含map[string]interface{}类型字段，在处理无法预知结构的数据时，将数据值存入interface{}中，待运行时利用反射判断。该结构体提供了大量的工具方法，便于处理非结构化的数据。



demo示例：通过DynamicClient示例列出某一命名空间下的Pod资源对象

```go
package main

import (
	"context"
	"fmt"

	corev1 "k8s.io/api/core/v1"
	metav1 "k8s.io/apimachinery/pkg/apis/meta/v1"
	"k8s.io/apimachinery/pkg/runtime"
	"k8s.io/apimachinery/pkg/runtime/schema"
	"k8s.io/client-go/dynamic"
	"k8s.io/client-go/tools/clientcmd"
)

/*
	@Author: ningan
	@Desc: 获取kube-system命名空间下的Pod列表
*/

func main() {
	// 1. 加载配置文件，生成config对象
	config, err := clientcmd.BuildConfigFromFlags("", "/root/.kube/config")
	if err != nil {
		panic(err.Error())
	}

	// 2. 通过kubeconfig配置信息实例化客户端对象，这里是实例化动态客户端对象。该对象用于管理Kubernetes的所有Resource的客户端，例如对Resource执行Create、Update、Delete、Get、List、Watch、Patch等操作。
	dynamicClient, err := dynamic.NewForConfig(config)
	if err != nil {
		panic(err.Error())
	}

	// 3. 配置需要调用的GVR
	gvr := schema.GroupVersionResource{
		Group:    "",
		Version:  "v1",
		Resource: "pods",
	}

	// 4. 发送请求，并得到返回结果
	unstructedObj, err := dynamicClient.
		Resource(gvr).
		Namespace("kube-system").
		List(context.TODO(), metav1.ListOptions{})
	if err != nil {
		panic(err.Error())
	}

	// 5. unstructedObj转化为结构化数据
	podList := &corev1.PodList{}
	err = runtime.DefaultUnstructuredConverter.FromUnstructured(
		unstructedObj.UnstructuredContent(),
		podList,
	)
	if err != nil {
		panic(err.Error())
	}
	/*
		1) Resource：基于gvr生成了一个针对于资源的客户端，也可以称之为动态资源客户端，dunamicResourceClient
		2) Namespace：指定一个可操作的命名空间，同时它是dynamicResourceClient的方法
		3) List：首先通过RESTClient调用k8s APIServer的接口返回了Pod的数据，返回的数据格式是二进制的Json格式，然后通过一些列的解析方法，转换成unstructured.UnstructuredList
	*/

	/*
		1) dynamicClient.Resource()函数用于设置请求的资源组、资源版本、资源名称。
		2) Namespace函数用于设置请求的命名空间。
		3) List函数用户获取Pod列表，得到的Pod列表为unstructured.UnstructuredList指针类型
		4) 然后通过runtime.DefaultUnstructuredConverter.FromUnstructured函数将unstructured.UnstructuredList转换为PodList类型。
	*/

	// 6. 格式化输出结果
	for _, item := range podList.Items {
		fmt.Printf("namespace: %v, name: %v, status: %v\n", item.Namespace, item.Name, item.Status.Phase)
	}
}

```





## DiscoveryClient

DiscoveryClient是发现客户端，它主要用于发现Kubernetes API Server所支持的资源组、资源版本、资源信息。

Kubernetes API Server支持很多资源组、资源版本、资源信息，开发者可以通过DiscoveryClient进行查看。



kuectlde api-versions和api-resources命令输出也是通过DiscoveryClient实现的。另外，DiscoveryClient也是子啊RESTClient的基础上进行了封装。



DiscoveryClient除了可以发现Kubernetes API Server所支持的资源组、资源版本、资源信息，还可以将这些信息存储到本地，用于本地缓存（cache），以减轻对Kubernetes API Server访问的压力。在运行Kubernetes组件的机器上，缓存信息默认存储与~/.kube/cache和~/.kube/http-cache下。



demo示例：通过DynamicClient列出Kubernetes API Server所支持的资源组、资源版本、资源信息

```go
package main

import (
	"fmt"

	"k8s.io/apimachinery/pkg/runtime/schema"
	"k8s.io/client-go/discovery"
	"k8s.io/client-go/tools/clientcmd"
)

/*
	@Author: ningan
	@Desc: 通过DynamicClient列出Kubernetes API Server所支持的资源组、资源版本、资源信息
*/

func main() {
	// 1. 加载配置文件，生成config对象
	config, err := clientcmd.BuildConfigFromFlags("", "/root/.kube/config")
	if err != nil {
		panic(err.Error())
	}

	// 2. 通过kubeconfig配置信息实例化discoveryClient对象，这个对象是用于发现Kubernetes API Server所支持的资源组、资源版本、资源信息的客户端。
	discoveryClient, err := discovery.NewDiscoveryClientForConfig(config)
	if err != nil {
		panic(err.Error())
	}

	// 3. 发送请求，获取GVR数据
	_, apiResourcesList, err := discoveryClient.ServerGroupsAndResources()
	if err != nil {
		panic(err.Error())
	}
	/*
		1) ServerGroups负责获取gv数据
		2) 然后调用fetchGroupVersionResources，且给这个方法传递gv参数，通过调用ServerResourcesForGroupVersion方法获取gv对应的Resource数据，同时返回一个map[gv]resourceList的数据格式
		3) 处理map->slice，然后返回GCR slice
	*/

	/*
		Kubernetes API Server暴露出/api和/apis接口。DiscoveryClient通过RESTClient分别请求/api和/apis接口，从而获取Kubernetes API Server所支持的资源组、资源版本、资源信息。核心实现位于：ServerGroupsAndResources->ServerGroups中

	*/

	for _, list := range apiResourcesList {
		gv, err := schema.ParseGroupVersion(list.GroupVersion)
		if err != nil {
			panic(err.Error())
		}

		for _, resource := range list.APIResources {
			fmt.Printf("name: %v, group: %v, version: %v\n", resource.Name, gv.Group, gv.Version)
		}
	}

}

```





```bash
[root@master demo1]# go run main.go
name: bindings, group: , version: v1
name: componentstatuses, group: , version: v1
name: configmaps, group: , version: v1
name: endpoints, group: , version: v1
name: events, group: , version: v1
name: limitranges, group: , version: v1
name: namespaces, group: , version: v1
name: namespaces/finalize, group: , version: v1
name: namespaces/status, group: , version: v1
name: nodes, group: , version: v1
name: nodes/proxy, group: , version: v1
name: nodes/status, group: , version: v1
name: persistentvolumeclaims, group: , version: v1
name: persistentvolumeclaims/status, group: , version: v1
name: persistentvolumes, group: , version: v1
name: persistentvolumes/status, group: , version: v1
name: pods, group: , version: v1
name: pods/attach, group: , version: v1
name: pods/binding, group: , version: v1
name: pods/eviction, group: , version: v1
name: pods/exec, group: , version: v1
name: pods/log, group: , version: v1
name: pods/portforward, group: , version: v1
name: pods/proxy, group: , version: v1
name: pods/status, group: , version: v1
name: podtemplates, group: , version: v1
name: replicationcontrollers, group: , version: v1
name: replicationcontrollers/scale, group: , version: v1
name: replicationcontrollers/status, group: , version: v1
name: resourcequotas, group: , version: v1
name: resourcequotas/status, group: , version: v1
name: secrets, group: , version: v1
name: serviceaccounts, group: , version: v1
name: services, group: , version: v1
name: services/proxy, group: , version: v1
name: services/status, group: , version: v1
name: apiservices, group: apiregistration.k8s.io, version: v1
name: apiservices/status, group: apiregistration.k8s.io, version: v1
name: apiservices, group: apiregistration.k8s.io, version: v1beta1
name: apiservices/status, group: apiregistration.k8s.io, version: v1beta1
name: ingresses, group: extensions, version: v1beta1
name: ingresses/status, group: extensions, version: v1beta1
name: controllerrevisions, group: apps, version: v1
name: daemonsets, group: apps, version: v1
name: daemonsets/status, group: apps, version: v1
name: deployments, group: apps, version: v1
name: deployments/scale, group: apps, version: v1
name: deployments/status, group: apps, version: v1
name: replicasets, group: apps, version: v1
name: replicasets/scale, group: apps, version: v1
name: replicasets/status, group: apps, version: v1
name: statefulsets, group: apps, version: v1
name: statefulsets/scale, group: apps, version: v1
name: statefulsets/status, group: apps, version: v1
name: events, group: events.k8s.io, version: v1
name: events, group: events.k8s.io, version: v1beta1
name: tokenreviews, group: authentication.k8s.io, version: v1
name: tokenreviews, group: authentication.k8s.io, version: v1beta1
name: localsubjectaccessreviews, group: authorization.k8s.io, version: v1
name: selfsubjectaccessreviews, group: authorization.k8s.io, version: v1
name: selfsubjectrulesreviews, group: authorization.k8s.io, version: v1
name: subjectaccessreviews, group: authorization.k8s.io, version: v1
name: localsubjectaccessreviews, group: authorization.k8s.io, version: v1beta1
name: selfsubjectaccessreviews, group: authorization.k8s.io, version: v1beta1
name: selfsubjectrulesreviews, group: authorization.k8s.io, version: v1beta1
name: subjectaccessreviews, group: authorization.k8s.io, version: v1beta1
name: horizontalpodautoscalers, group: autoscaling, version: v1
name: horizontalpodautoscalers/status, group: autoscaling, version: v1
name: horizontalpodautoscalers, group: autoscaling, version: v2beta1
name: horizontalpodautoscalers/status, group: autoscaling, version: v2beta1
name: horizontalpodautoscalers, group: autoscaling, version: v2beta2
name: horizontalpodautoscalers/status, group: autoscaling, version: v2beta2
name: jobs, group: batch, version: v1
name: jobs/status, group: batch, version: v1
name: cronjobs, group: batch, version: v1beta1
name: cronjobs/status, group: batch, version: v1beta1
name: certificatesigningrequests, group: certificates.k8s.io, version: v1
name: certificatesigningrequests/approval, group: certificates.k8s.io, version: v1
name: certificatesigningrequests/status, group: certificates.k8s.io, version: v1
name: certificatesigningrequests, group: certificates.k8s.io, version: v1beta1
name: certificatesigningrequests/approval, group: certificates.k8s.io, version: v1beta1
name: certificatesigningrequests/status, group: certificates.k8s.io, version: v1beta1
name: ingressclasses, group: networking.k8s.io, version: v1
name: ingresses, group: networking.k8s.io, version: v1
name: ingresses/status, group: networking.k8s.io, version: v1
name: networkpolicies, group: networking.k8s.io, version: v1
name: ingressclasses, group: networking.k8s.io, version: v1beta1
name: ingresses, group: networking.k8s.io, version: v1beta1
name: ingresses/status, group: networking.k8s.io, version: v1beta1
name: poddisruptionbudgets, group: policy, version: v1beta1
name: poddisruptionbudgets/status, group: policy, version: v1beta1
name: podsecuritypolicies, group: policy, version: v1beta1
name: clusterrolebindings, group: rbac.authorization.k8s.io, version: v1
name: clusterroles, group: rbac.authorization.k8s.io, version: v1
name: rolebindings, group: rbac.authorization.k8s.io, version: v1
name: roles, group: rbac.authorization.k8s.io, version: v1
name: clusterrolebindings, group: rbac.authorization.k8s.io, version: v1beta1
name: clusterroles, group: rbac.authorization.k8s.io, version: v1beta1
name: rolebindings, group: rbac.authorization.k8s.io, version: v1beta1
name: roles, group: rbac.authorization.k8s.io, version: v1beta1
name: csidrivers, group: storage.k8s.io, version: v1
name: csinodes, group: storage.k8s.io, version: v1
name: storageclasses, group: storage.k8s.io, version: v1
name: volumeattachments, group: storage.k8s.io, version: v1
name: volumeattachments/status, group: storage.k8s.io, version: v1
name: csidrivers, group: storage.k8s.io, version: v1beta1
name: csinodes, group: storage.k8s.io, version: v1beta1
name: storageclasses, group: storage.k8s.io, version: v1beta1
name: volumeattachments, group: storage.k8s.io, version: v1beta1
name: mutatingwebhookconfigurations, group: admissionregistration.k8s.io, version: v1
name: validatingwebhookconfigurations, group: admissionregistration.k8s.io, version: v1
name: mutatingwebhookconfigurations, group: admissionregistration.k8s.io, version: v1beta1
name: validatingwebhookconfigurations, group: admissionregistration.k8s.io, version: v1beta1
name: customresourcedefinitions, group: apiextensions.k8s.io, version: v1
name: customresourcedefinitions/status, group: apiextensions.k8s.io, version: v1
name: customresourcedefinitions, group: apiextensions.k8s.io, version: v1beta1
name: customresourcedefinitions/status, group: apiextensions.k8s.io, version: v1beta1
name: priorityclasses, group: scheduling.k8s.io, version: v1
name: priorityclasses, group: scheduling.k8s.io, version: v1beta1
name: leases, group: coordination.k8s.io, version: v1
name: leases, group: coordination.k8s.io, version: v1beta1
name: runtimeclasses, group: node.k8s.io, version: v1beta1
name: endpointslices, group: discovery.k8s.io, version: v1beta1
name: cukclusters, group: dubhe.welkin, version: v1alpha1
name: cukclusters/status, group: dubhe.welkin, version: v1alpha1
name: cuknodes, group: dubhe.welkin, version: v1alpha1
name: cuknodes/status, group: dubhe.welkin, version: v1alpha1
name: cuksets, group: dubhe.welkin, version: v1alpha1
name: cuksets/status, group: dubhe.welkin, version: v1alpha1
name: nodes, group: metrics.k8s.io, version: v1beta1
name: pods, group: metrics.k8s.io, version: v1beta1

```





### 将GVR缓存到本地

可以发现，GVR数据其实是很少变动的，因此我们可以将GVR的数据缓存到本地，来减少Client与Apiserver交互。

在discovery/cached中，有另外两个客户端是来实现将GVR数据缓存到本地文件中和内存中的，分别是CachedDiscoveryClient和memCacheClient。

平时管理集群的kubectl命令也是使用这种方式来使用GVR与APIServer交互的。



DiscoveryClient除了可以发现Kubernetes API Server所支持的资源组、资源版本、资源信息，还可以将这些信息存储到本地，用于本地缓存（cache），以减轻对Kubernetes API Server访问的压力。在运行Kubernetes组件的机器上，缓存信息默认存储与~/.kube/cache和~/.kube/http-cache下。



```go
package main

import (
	"fmt"
	"time"

	"k8s.io/apimachinery/pkg/runtime/schema"
	"k8s.io/client-go/discovery/cached/disk"
	"k8s.io/client-go/tools/clientcmd"
)

/*
	@Author: ningan
	@Desc: 将GVR数据缓存到本地
*/

func main() {
	// 1. 加载配置文件，生成config对象
	config, err := clientcmd.BuildConfigFromFlags("", "/root/.kube/config")
	if err != nil {
		panic(err.Error())
	}

	// 2. 实例化客户端，本客户端负责将GVR数据缓存到本地文件中
	cachedDiscoveryClient, err := disk.NewCachedDiscoveryClientForConfig(config, "./cache/discovery", "./cache/http", time.Minute*60)
	if err != nil {
		panic(err.Error())
	}

	_, apiResourcesList, err := cachedDiscoveryClient.ServerGroupsAndResources()
	if err != nil {
		panic(err.Error())
	}

	/*
		1) 先从缓存文件中找GVR数据，有则直接返回，没有则调用APIServer
		2) 嗲用APIServer获取GVR数据
		3) 将获取到的GVR数据缓存到本地，然后返回给客户端

	*/

	for _, list := range apiResourcesList {
		gv, err := schema.ParseGroupVersion(list.GroupVersion)
		if err != nil {
			panic(err.Error())
		}

		for _, resource := range list.APIResources {
			fmt.Printf("name: %v, group: %v, version: %v\n", resource.Name, gv.Group, gv.Version)
		}
	}
}

```



执行go run main.go之后，确实生成了cache目录



```bash
[root@master demo2]# tree -L 2
.
├── cache
│   ├── discovery
│   └── http
└── main.go

3 directories, 1 file

```





# 参考

Kubernetes源码剖析 5.2节 Client客户端对象

b站视频：[Client-go实战教程](https://www.bilibili.com/video/BV11e4y197Ft/?spm_id_from=pageDriver&vd_source=504b7dbe5398664dd37d144b1bdfc0a1)