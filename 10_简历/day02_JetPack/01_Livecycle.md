Lifecycle可以让某一个类变成Activity、Fragment的生命周期观察者类，监听其生命周期的变化并可以做出响应

主要两个角色:
- LifecycleOwner: 生命周期拥有者，如Activity/Fragment等类都实现了该接口并通过getLifecycle()获得Lifecycle，进而可通过addObserver()添加观察者。

- LifecycleObserver: 生命周期观察者，实现该接口后就可以添加到Lifecycle中，从而在被观察者类生命周期发生改变时能马上收到通知。