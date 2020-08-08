.. _example_in-cluster-client-configuration:

实例in-cluster-client-configuration
###################################

.. code-block:: go
   :lineno-start: 51
   :caption: k8s.io/client-go/examples/in-cluster-client-configuration/main.go

    config, err := rest.InClusterConfig()
    ...
    clientset, err := kubernetes.NewForConfig(config)
    ...
    pods, err := clientset.CoreV1().Pods("").List(metav1.ListOptions{})
    fmt.Printf("There are %d pods in the cluster\n", len(pods.Items))

看上面代码，和上一节看的代码很相似，只有获取 ``*k8s.io/client-go/rest.Config`` 对象的方法不一样::

    在集群内部凭证在目录/var/run/secrets/kubernetes.io/serviceaccount中
    使用方法rest.InClusterConfig()来获取rest.Config对象








