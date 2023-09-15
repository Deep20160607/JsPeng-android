###Jetpack的成员,以注重生命周期的方式存储和管理界面的相关数据.mvvm框架的重要组件,vm类让数据可以在发生屏幕旋转等配置更改后继续保存.###

- 为什么Activity旋转屏幕后ViewModel可以恢复数据
- ViewModel 的实例缓存到哪儿了
- 什么时候 ViewModel#onCleared() 会被调用

详细可看:https://zhuanlan.zhihu.com/p/458663693

简单分析:

activity创建
```
viewModel创建:ViewModelProvider(this).get(XXViewModel::class.java)

ComponentActivty implement viewModelStoreOwner {
    getViewModelStore(){
        ensumeViewModeStore();
        returen viewModeStore;
    }
}

viewModelStore{
    Map<String, ViewModel> map;
}

//所以ViewModel缓存在ViewModelStore中 !!!

ensumeViewMode() {
    if (mViewModelStore == null) {
        NonConfigurationInstances nc = getLastNonConfigurationInstances();
        if (nc != null) {
            mViewModelStore = nc.viewModelStore;
        }

        if (mViewModelStore == null) {
                mViewModelStore = new ViewModelStore();
        }
    } 
}
//类似onSaveInstanceState()
onRetainNonConfigurationInstances() {
    ViewModelStore viewModelStore = mViewModelStore;
        if (viewModelStore == null) {
            NonConfigurationInstances nc =
                    (NonConfigurationInstances) getLastNonConfigurationInstance();
            if (nc != null) {
                viewModelStore = nc.viewModelStore;
            }
        }
}

Activity >>attach()创建lastNonConfigurationInstances

ComponentActivity() {
    getLifecycle().addObserver(new LifecycleEventObserver() {
        onStateChanged(LifecycleOwner source, Lifecycle.Event event) {
            if (event == Lifecycle.Event.ON_DESTROY) {
                    if (!isChangConfigurations()) {
                        getViewModelStore().clear()
                    }
            }
        }
    })
}

所以activity销毁时,判断如果非配置改变导致的,才会调用clear()

```
- ViewModelProvider(f, ViewModelProvider.NewInstanceFactory()).get(vmClazz)和ViewModelProvider(f).get(vmClazz) 区别:   
使用 ViewModelProvider(f, ViewModelProvider.NewInstanceFactory()) 时，你可以自定义工厂来创建 ViewModel，这允许你在创建 ViewModel 时传递参数，或执行其他自定义逻辑。   
使用 ViewModelProvider(f) 时，将使用默认工厂，它通常适用于没有特殊要求的情况。   
通常情况下，如果你的 ViewModel 类不需要接受额外的参数，那么使用 ViewModelProvider(f) 就足够了。如果你需要传递参数或执行自定义逻辑来创建 ViewModel，那么可以使用 ViewModelProvider(f, customFactory)，其中 customFactory 是你自己定义的工厂类的实例。    




