.. _example_out-of-cluster-client-configuration:

实例out-of-cluster-client-configuration
#######################################



.. code-block:: go
   :lineno-start: 52
   :caption: k8s.io/client-go/examples/out-of-cluster-client-configuration/main.go

    config, err := clientcmd.BuildConfigFromFlags("", *kubeconfig)
    ...
    clientset, err := kubernetes.NewForConfig(config)
    ...
    podClient := clientset.CoreV1().Pods("")
    pods, err := podClient.List(metav1.ListOptions{})
    fmt.Printf("There are %d pods in the cluster\n", len(pods.Items))

获取 ``*k8s.io/client-go/rest.Config`` 对象::

    即把~/.kube/config中的凭证信息通过如下函数转化为rest.Config对象
    clientcmd.BuildConfigFromFlags("", *kubeconfig)

获取 ``*k8s.io/client-go/kubernetes/Clientset`` 对象::

    clientset看名字需要知道，它是一个client集合，里面存放着多种client
    它的本质是对restClient的封装(这块后面会详解)
    通过如下命令
    kubernetes.NewForConfig(config)
    获取kubernetes.Clientset对象
    client对象获取后就可以请求api-server(操作k8s本质是请求api-server)

获取 ``k8s.io/client-go/kubernetes/typed/core/v1/PodList`` 对象::

    先获取Pod类型的client
    podClient := clientset.CoreV1().Pods("")
    前面说了clientset是client集合，里面有pod, deploy, service...类型的client

    podClient实现了 k8s.io/client-go/kubernetes/typed/core/v1/PodInterface 接口
    里面有List方法获取v1.PodList列表
    podClient.List(metav1.ListOptions{})








