
> *官方安装文档：https://gradle.org/install/*

> *手动安装，在[link](https://gradle.org/releases/)中下载你想要Gradle版本的binary-only*

gradle命令
一般是./gradlew +参数， gradlew代表 gradle wrapper，意思是gradle的一层包装，大家可以理解为在这个项目本地就封装了gradle，即gradle wrapper， 在gradle/wrapper/gralde-wrapper.properties文件中声明了它指向的目录和版本。只要下载成功即可用grdlew wrapper的命令代替全局的gradle命令。


gradle的解析顺序：rootproject 的setting.gradle,然后是rootproject的build.gradle,然后是各个subproject。所以project下的build.gradle会先于app下的build.gradle。

在解析setting.gradle之后，开始解析build.gradle之前，这里如果要干些事情（更改build.gradle校本内容），可以写在beforeEvaluate 

在所有build.gradle解析完成后，开始执行task之前，此时所有的脚本已经解析完成，task，plugins等所有信息可以获取，task的依赖关系也已经生成，如果此时需要做一些事情，可以写在afterEvaluate 
project.afterEvaluate {}

每个task都可以定义doFirst，doLast，用于定义在此task执行之前或之后执行的代码
project.assemble.doLast {
                    println "assemble finish"
                }
project.assemble.doFirst {
                    println "assemble start"
                }


                https://docs.gradle.org/7.3.3/userguide/userguide.html



## 查看依赖树: ./gradlew :app:dependencies > log.txt

## gradle常用属性
<img src=assets/images/gradle常用属性.png width=400 height=500 />
