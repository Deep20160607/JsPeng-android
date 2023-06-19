## 应用Gradle插件
在Gradle中一般有两种类型的插件，分别叫做脚本插件和对象插件。脚本插件是额外的构建脚本，它会进一步配置构建，可以把它理解为一个普通的build.gradle。对象插件又叫做二进制插件，是实现了Plugin接口的类，下面分别介绍如何使用它们。


### 脚本插件
other.gradle
```groovy
ext{
verson='1.0'
url='http://liuwangshu.cn'
}
```
这实际上不算是一个真正的脚本插件，就是一个简单的脚本，主要是用于演示脚本插件是如何被应用的。我们在build.gradle中来应用这个插件：
build.gradle
```groovy
apply from: 'other.gradle'
task test {
    doLast {
        println "版本为：${verson},地址为：${url}"
    }
}
```

apply是Gradle project中提供的方法，用于配置项目中的插件。


***对象插件***

### 内部插件
如果我们想要应用Java插件可以这么写：
build.gradle
```groovy
 apply plugin: org.gradle.api.plugins.JavaPlugin  
 ```
Gradle默认就导入了org.gradle.api.plugins包，因此我们也可以去掉包名：
```groovy
apply plugin: JavaPlugin  
```
实现了org.gradle.api.plugins接口的插件会有pulginid，使用pulginid是最简洁、最常用的方式：
```groovy
apply plugin: 'java'  
```
Gradle 的发行包中有大量的插件，这些插件有很多类型，比如语言插件、集成插件、软件开发插件等等，如果我们想
向项目添加 c++ 源代码编译功能，可以这么写：
```groovy
apply plugin: 'cpp'  
```

### 第三方插件

第三方的对象插件通常是jar文件，要想让构建脚本知道第三方插件的存在，需要使用buildscrip来设置。
```groovy
buildscript {
  repositories {
    maven {
      url "https://plugins.gradle.org/m2/"
    }
  }
  dependencies {
    classpath "com.jfrog.bintray.gradle:gradle-bintray-plugin:1.8.4"
  }
}
apply plugin: "com.jfrog.bintray"
```
在buildscrip中来定义插件所在的原始仓库和插件的依赖 ，再通过apply方法配置就可以了。Android Gradle插件也属于第三方插件，如果我们想引入Android Gradle插件，可以这么写：
```groovy
buildscript {
    repositories {
        jcenter()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:2.3.2'
    }
}
apply plugin: 'com.android.application'
```
这样我们就可以使用Android Gradle插件，通过apply方法来使用App工程插件，这样项目会编译成为一个apk