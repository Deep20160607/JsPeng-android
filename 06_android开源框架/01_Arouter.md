- ARouter的原理？
- ARouter怎么实现页面拦截？
- 组件化中使用ARouter需要注意什么，或者说怎么正确应用到组件化中呢？

Arouter是阿里巴巴开源的用来解决组件、模块间页面跳转的框架
```java
@Route(path = "/test/activity")
public class YourActivity extend Activity {
    ...
}

//跳转
ARouter.getInstance().build("/test/activity").navigation();
```
- 代码里加入的@Route注解，会在编译时期通过apt生成一些存储path和activity.class映射关系的类文件
- app进程启动的时候会加载这些类文件，把保存这些映射关系的数据读到内存里(保存在map里)
- 进行路由跳转的时候，通过build()方法传入要到达页面的路由地址，ARouter会通过它自己存储的路由表找到路由地址对应的Activity.class
- 然后new Intent方法，如果有调用ARouter的withString()方法，就会调用intent.putExtra(String name, String value)方法添加参数
- 最后调用navigation()方法，它的内部会调用startActivity(intent)进行跳转


具体原理:
https://www.jianshu.com/p/6021f3f61fa6  
https://www.jianshu.com/p/5b35309e9bb2

延伸知识点

注解   
RetentionPolicy三种:
- Source:注解仅在源代码中保留
- Class:编译时会保留在字节码中
- Runtime:编译时会保留在字节码中,且运行时会被jvm加载和使用