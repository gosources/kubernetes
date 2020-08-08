前言
####


大家好， 我们这一节来讲一讲 ``examples`` 目录下的几个小例子，先看下目录::

     tree -L 1
    .
    ├── create-update-delete-deployment
    ├── fake-client
    ├── in-cluster-client-configuration
    ├── out-of-cluster-client-configuration
    └── workqueue

好了，这5个项目已经列出来了，每个项目都是干嘛的呢？我先在这儿给大家做一个简单的介绍:

* ``out-of-cluster-client-configuration``::

    大家可能注意到了，使用client-go写的程序是操作Kubernetes的。
    我们之前已经使用kubectl操作过Kubernetes了，这样的操作基本步骤是:
    1. 从文件~/.kube/config获取连接Kubernetes凭证
    2. 连接api-server，发送http请求获取结果
    本实例就是实现了类似kubectl get po的功能。

* ``in-cluster-client-configuration``::

    上面的例子是从文件~/.kube/config获取连接Kubernetes凭证
    这就有一个问题，这个程序可不可以(以及如何)在Kubernetes内部运行(以pod的形式)呢？
    答案是肯定的，如何获取凭证后面介绍。
    实例 in-cluster-client-configuration 就是介绍了如何实现此目的。


* ``create-update-delete-deployment``::
    
    上面的2个实例比较简单，里面重点是实现了in-cluster和out-of-cluster获取client实例。
    之后用获取pod列表和指定pod的信息来证明client创建成功。
    本实例实现了创建deployment的过程，类似你用 kubectl create deploy.yaml 创建过程。

* ``fake-client``::
  
    这么大的一个项目，单元测试的重要性不言而喻，但我们不能弄一个真实的Kubernetes集群用于测试。
    因为即使单元测试失败，你不知道是集群出问题，还是功能有问题。
    所以 fake-client 项目教你如何使用 k8s.io/client-go/kubernetes/fake 构造一个假的集群来做单元测试。
    我们先暂不讲，等后面讲单元测试时再专门拿出来讲。

* ``workqueue``::

    是client-go项目实现的一个队列，属于内容结构的用法，我们暂不讲，等我们后面讲到时再回来看这个实例。


上面把五个例子简单介绍了一下，下面讲解这五个例子的源码。讲解的目的是对每一行代码的作用有个了解，不作更深入的源码剖析，更深入的剖析留给后面章节。






