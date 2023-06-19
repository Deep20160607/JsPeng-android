
## 一、Gradle 构建具有三个不同的阶段。
### 初始化
Gradle 支持单项目和多项目构建。在初始化阶段，Gradle 确定哪些项目将参与构建，并为每个项目创建一个Project实例。

### 配置
在此阶段配置项目对象。执行作为构建一部分的所有项目的构建脚本。

### 执行
Gradle 确定要执行的任务子集，这些任务是在配置阶段创建和配置的。该子集由传递给命令的任务名称参数gradle和当前目录确定。Gradle 然后执行每个选定的任务。

***
例如:
***
settings.gradle
```groovy
rootProject.name = 'basic'
println 'This is executed during the initialization phase.
```

builid.gradle
```groovy
println 'This is executed during the configuration phase.'

tasks.register('configured') {
    println 'This is also executed during the configuration phase, because :configured is used in the build.'
}

tasks.register('test') {
    doLast {
        println 'This is executed during the execution phase.'
    }
}

tasks.register('testBoth') {
	doFirst {
	  println 'This is executed first during the execution phase.'
	}
	doLast {
	  println 'This is executed last during the execution phase.'
	}
	println 'This is executed during the configuration phase as well, because :testBoth is used in the build.'
}
```

***输入 gradle test testBoth***
```
> gradle test testBoth
This is executed during the initialization phase.

> Configure project :
This is executed during the configuration phase.
This is executed during the configuration phase as well, because :testBoth is used in the build.

> Task :test
This is executed during the execution phase.

> Task :testBoth
This is executed first during the execution phase.
This is executed last during the execution phase.

BUILD SUCCESSFUL in 0s
2 actionable tasks: 2 executed
```


## 响应构建脚本中的生命周期
***当构建在其生命周期中进行时，您的构建脚本可以接收通知。这些通知通常采用两种形式：您可以实现特定的侦听器接口，或者您可以提供一个闭包以在触发通知时执行***

#### 1 项目构建前后收到通知 projectEvaluate (aterEvaluate  beforEvaluate )

root-project: build.gradle
```groovy
allprojects {
    afterEvaluate { project ->
        if (project.hasTests) {
            println "Adding test task to $project"
            project.task('test') {
                doLast {
                    println "Running tests for $project"
                }
            }
        }
    }
}
```
model-a: build.gradle   
```groovy
hasTests = true
```
***
输入 ,gradlew -q test 
***

***还可以在评估任何项目时收到通知。此示例执行一些项目评估的自定义日志记录。请注意，afterProject无论项目评估成功还是异常失败，都会收到通知。***
```
gradle.afterProject { project ->
    if (project.state.failure) {
        println "Evaluation of $project FAILED"
    } else {
        println "Evaluation of $project succeeded"
    }
}
```
还可以将ProjectEvaluationListener添加到Gradle以接收这些事件。
#### 2 任务创建
***将任务添加到项目后，您可以立即收到通知。这可用于在任务在构建文件中可用之前设置一些默认值或添加行为。***

build.gradle
```groovy
tasks.whenTaskAdded { task ->
    task.ext.srcDir = 'src/main/java'
}

tasks.register('a')

println "source dir is $a.srcDir"
```
*输出:./gradlew -q a*

还可以将Action添加到TaskContainer以接收这些事件

#### 3 任务执行图就绪
***
填充任务执行图后，可以立即收到通知。

还可以将TaskExecutionGraphListener添加到TaskExecutionGraph以接收这些事件。
***

#### 4 任务执行
***可以在执行任何任务之前和之后立即收到通知。
以下示例记录每个任务执行的开始和结束。请注意，afterTask无论任务是成功完成还是因异常而失败，都会收到通知。***

build.gradle
```groovy
tasks.register('ok')

tasks.register('broken') {
    dependsOn ok
    doLast {
        throw new RuntimeException('broken')
    }
}

gradle.taskGraph.beforeTask { Task task ->
    println "executing $task ..."
}

gradle.taskGraph.afterTask { Task task, TaskState state ->
    if (state.failure) {
        println "FAILED"
    }
    else {
        println "done"
    }
}
```
> gradle -q broken

```
executing task ':ok' ...
done
executing task ':broken' ...
FAILED

FAILURE: Build failed with an exception.
```
还可以对TaskExecutionGraph使用TaskExecutionListener来接收这些事件。