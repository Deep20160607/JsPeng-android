- Database: 访问底层数据库的入口
- Entity: 代表数据库中的表（table），一般用注解
- Data Access Object (DAO): 数据库访问者


通过Database获取DAO，然后通过DAO查询并获取entities，最终通过entities对数据库table中数据进行读写

``` groovy
dependencies {
  implementation "androidx.room:room-runtime:2.2.5"
  kapt "androidx.room:room-compiler:2.2.5"
}
```

使用:https://juejin.cn/post/6923787841352761351
