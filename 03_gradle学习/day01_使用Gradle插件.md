# 二进制插件
## 一、插件的作用
***将插件应用于项目允许插件扩展项目的功能。它可以做如下事情：***

- 扩展 Gradle 模型（例如添加可配置的新 DSL 元素）

- 根据约定配置项目（例如添加新任务或配置合理的默认值）

- 应用特定配置（例如添加组织存储库或执行标准）

***通过应用插件，而不是向项目构建脚本添加逻辑，我们可以获得许多好处。应用插件：***

- 促进重用并减少跨多个项目维护相似逻辑的开销

- 允许更高程度的模块化，增强可理解性和组织性

- 封装命令式逻辑并允许构建脚本尽可能具有声明性

## 二、约束语法

***其中«plugin id»和«plugin version»必须是常量、文字、字符串，apply带有 a 的语句boolean可用于禁用立即应用插件的默认行为（例如，您只想在 中应用它subprojects）。不允许有其他陈述；它们的存在会导致编译错误。***

settings.gradle:   
```groovy
//该pluginManagement {}块只能出现在文件中settings.gradle，它必须是文件中的第一个块
pluginManagement {
    plugins{
        id 'com.jfrog.bintray' version "${bintrayPluginVersion}"
        id 'com.android.library' version '7.2.2'
        id 'org.jetbrains.kotlin.android' version '1.8.0'
    }
    //插件解析规则允许您修改plugins {}块中的插件请求，例如更改请求的版本或明确指定实现工件坐标。
    resolutionStrategy {
        eachPlugin {
            //拿到plugins 中的id
            println("dupeng-eachPlugin: ${requested.id}")
//            println("dupeng-eachPlugin: ${requested.id.namespace}")
            if (requested.id.namespace == 'com.jspeng.lib') {
                useModule('com.jspeng.lib:android-androidLibA:1.0.1')
            }
        }
    }
    repositories {
        google()
        mavenCentral()
        gradlePluginPortal()
//        maven { url new File(rootProject.projectDir, "tools/repo").toURI() }
        maven { url 'tools/repo' }//要指定自定义插件存储库
    }
}
dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
    repositories {
        google()
        mavenCentral()
//        maven { url new File(rootProject.projectDir, "tools/repo").toURI() }
        maven { url 'tools/repo' }//仓库引用1
    }

}
rootProject.name = "GradleNode"
include ':app'
//include ':libA'
include ':libB'
include ':lib-LeetCode'

```


root-project: build.gradle:   
```groovy
plugins {
    id «plugin id»                                            
    id «plugin id» version «plugin version» [apply «false»]   
}
```

project:build.gradle:  
```groovy
plugins {
    id «plugin id»                                            
}
```

## 三、插件管理
***
pluginManagement {}块只能出现在文件中settings.gradle，它必须是文件中的第一个块，或者出现在Initialization Script中。
***

- 例如:配置每个项目和全局的    
  
root-project:build.gradle   
``` groovy
pluginManagement {
    plugins {
    }
    resolutionStrategy {
    }
    repositories {
    }
}
rootProject.name = 'plugin-management'
```
init.gradle   
```groovy
settingsEvaluated { settings ->
    settings.pluginManagement {
        plugins {
        }
        resolutionStrategy {
        }
        repositories {
        }
    }
}
```
***要指定自定义插件存储库，请使用pluginManagement {}内部的块repositories {}***

## 四、插件版本管理
***
plugins {}内部的块允许pluginManagement {}在单个位置定义构建的所有插件版本。然后可以通过 id 通过块将插件应用于任何构建脚本plugins {}。

以这种方式设置插件版本的一个好处是，它pluginManagement.plugins {}没有与构建脚本块相同的受限语法plugins {}。这允许从中获取插件版本gradle.properties，或通过另一种机制加载。
***
settings.gralde   
```groovy
pluginManagement {
  plugins {
        id 'com.example.hello' version "${helloPluginVersion}"
    }
}
```

build.gradle   
```groovy
plugins {
    id 'com.example.hello'
}
```
gradle.properties   
```groovy
helloPluginVersion=1.0.0
```
***插件版本从设置脚本中加载gradle.properties和配置，允许将插件添加到任何项目而无需指定版本。***

## 五、插件解析规则

***
插件解析规则允许您修改plugins {}块中的插件请求，例如更改请求的版本或明确指定实现工件坐标。

要添加解析规则，请使用块resolutionStrategy {}内部pluginManagement {}：
***
例如:settings.gradle   
```groovy
pluginManagement {
    resolutionStrategy {
        eachPlugin {
            if (requested.id.namespace == 'com.example') {
                useModule('com.example:sample-plugins:1.0.0')
            }
        }
    }
    repositories {
        maven {
            url './maven-repo'
        }
        gradlePluginPortal()
        ivy {
            url './ivy-repo'
        }
    }
}
```
***告诉 Gradle 使用指定的插件实现工件，而不是使用其内置的从插件 ID 到 Maven/Ivy 坐标的默认映***

## 使用 buildscript 块应用插件
***
通过将插件添加到构建脚本类路径然后应用插件，可以将已作为外部 jar 文件发布的二进制插件添加到项目中。可以使用块将外部 jar 添加到构建脚本类路径，如构建脚本的外部依赖项buildscript {}中所述。
***
build.gradle   
```groovy
buildscript {
    repositories {
        gradlePluginPortal()
    }
    dependencies {
        classpath 'com.jfrog.bintray.gradle:gradle-bintray-plugin:1.8.5'
    }
}

apply plugin: 'com.jfrog.bintray'
```

### 
***举例:buildSrc目录应用插件***   
buildSrc模块中:
VersionManager.java   
```groovy
package com.jspeng.plugin;

import org.gradle.api.Action;
import org.gradle.api.Plugin;
import org.gradle.api.Project;
import org.gradle.api.Task;

public class VersionManager implements Plugin<Project> {
    @Override
    public void apply(Project project) {
        System.out.println("VersionManager&apply(): " + project.getName());
        project.task("VersionManagerPluginTask").doLast(task -> {
            System.out.println("VersionManagerPluginTask: " + project.getName());
        });
    }
}
```
buildSrc/build.gradle    
```groovy
plugins {
    id 'java-gradle-plugin'
}

gradlePlugin {
    plugins {
        myPlugins {
             id = 'com.jspeng.plugin'
            implementationClass = 'com.jspeng.plugin.VersionManager'
        }
    }
}
```
需要引用次插件的modle的project:build.gradle   
```groovy
plugins {
    id 'com.jspeng.plugin'
}
```
则可以在此build.gradle中使用MyPlugin,例如:   
apply plugin: VersionManager



# 脚本插件
build.gradle
```
apply from: 'other.gradle'
```
***
脚本插件是自动解析的，可以从本地文件系统或远程位置的脚本应用。文件系统位置是相对于项目目录的，而远程脚本位置是用 HTTP URL 指定的。可以将多个脚本插件（任何一种形式）应用于给定目标。


***
