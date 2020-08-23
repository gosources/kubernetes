gen
###

使用::

    sharedInformers := informers.NewSharedInformerFactory(clientSet, 0)
    informer := sharedInformers.Core().V1().Pods().Informer()
    informer.AddEventHandler(cache.ResourceEventHandlerFuncs{
      AddFunc: func(obj interface{}) {
      },
      UpdateFunc: func(oldObj, newObj interface{}) {
      },
      DeleteFunc: func(obj interface{}) {
      }
    })
    informer.Run(stopCh)

informers.NewSharedInformerFactory
==================================

.. code-block:: go
   :lineno-start: 79
   :caption: k8s.io/client-go/informers/factory.go

    func NewSharedInformerFactory(client kubernetes.Interface, defaultResync time.Duration) SharedInformerFactory {
      return NewSharedInformerFactoryWithOptions(client, defaultResync)
    }

    func NewSharedInformerFactoryWithOptions(client kubernetes.Interface, defaultResync time.Duration, options ...SharedInformerOption) SharedInformerFactory {
      factory := &sharedInformerFactory{
        client:           client,
        namespace:        v1.NamespaceAll,
        defaultResync:    defaultResync,
        informers:        make(map[reflect.Type]cache.SharedIndexInformer),
        startedInformers: make(map[reflect.Type]bool),
        customResync:     make(map[reflect.Type]time.Duration),
      }

      // Apply all options
      for _, opt := range options {
        factory = opt(factory)
      }

      return factory
    }

coreV1.NewFilteredPodInformer
=============================

.. code-block:: go
   :lineno-start: 57
   :caption: k8s.io/client-go/informers/core/v1/pod.go

    func NewFilteredPodInformer(client kubernetes.Interface, namespace string, resyncPeriod time.Duration, indexers cache.Indexers, tweakListOptions internalinterfaces.TweakListOptionsFunc) cache.SharedIndexInformer {
      return cache.NewSharedIndexInformer(
        &cache.ListWatch{
          ListFunc: func(options metav1.ListOptions) (runtime.Object, error) {
            if tweakListOptions != nil {
              tweakListOptions(&options)
            }
            return client.CoreV1().Pods(namespace).List(options)
          },
          WatchFunc: func(options metav1.ListOptions) (watch.Interface, error) {
            if tweakListOptions != nil {
              tweakListOptions(&options)
            }
            return client.CoreV1().Pods(namespace).Watch(options)
          },
        },
        &corev1.Pod{},
        resyncPeriod,
        indexers,
      )
    }


cache.NewSharedIndexInformer
============================

.. code-block:: go
   :lineno-start: 79
   :caption: k8s.io/client-go/tools/cache/shared_informer.go

    func NewSharedIndexInformer(lw ListerWatcher, objType runtime.Object, defaultEventHandlerResyncPeriod time.Duration, indexers Indexers) SharedIndexInformer {
      realClock := &clock.RealClock{}
      sharedIndexInformer := &sharedIndexInformer{
        processor:                       &sharedProcessor{clock: realClock},
        indexer:                         NewIndexer(DeletionHandlingMetaNamespaceKeyFunc, indexers),
        listerWatcher:                   lw,
        objectType:                      objType,
        resyncCheckPeriod:               defaultEventHandlerResyncPeriod,
        defaultEventHandlerResyncPeriod: defaultEventHandlerResyncPeriod,
        cacheMutationDetector:           NewCacheMutationDetector(fmt.Sprintf("%T", objType)),
        clock:                           realClock,
      }
      return sharedIndexInformer
    }

cache.NewIndexer
================

.. code-block:: go
   :lineno-start: 239
   :caption: k8s.io/client-go/tools/cache/store.go

    func NewIndexer(keyFunc KeyFunc, indexers Indexers) Indexer {
      return &cache{
        cacheStorage: NewThreadSafeStore(indexers, Indices{}),
        keyFunc:      keyFunc,
      }
    }

.. code-block:: go
   :lineno-start: 305
   :caption: k8s.io/client-go/tools/cache/thread_safe_store.go

    func NewThreadSafeStore(indexers Indexers, indices Indices) ThreadSafeStore {
      return &threadSafeMap{
        items:    map[string]interface{}{},
        indexers: indexers,
        indices:  indices,
      }
    }


cache.newProcessListener
========================

.. code-block:: go
   :lineno-start: 491
   :caption: k8s.io/client-go/tools/cache/shared_informer.go

    func newProcessListener(handler ResourceEventHandler, 
            requestedResyncPeriod, resyncPeriod time.Duration, 
            now time.Time, bufferSize int) *processorListener {
      ret := &processorListener{
        nextCh:                make(chan interface{}),
        addCh:                 make(chan interface{}),
        handler:               handler,
        pendingNotifications:  *buffer.NewRingGrowing(bufferSize),
        requestedResyncPeriod: requestedResyncPeriod,
        resyncPeriod:          resyncPeriod,
      }

      ret.determineNextResync(now)

      return ret
    }














