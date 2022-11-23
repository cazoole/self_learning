# Gradle 内容整理

2012年谷歌推出的基于Groovy语言的项目构建语言，集合了Ant和Maven的优势且速度优于Maven。但学习成本较高。

## Gradle 基础

### Gradle 命令

需要在含有 build.gradle 文件的目录下执行对应的目录。

- gradle clean      清空build目录

- gradle classes    编译业务代码和配置文件

- gradle test       编译测试代码，生成测试报告

- gradle build      构建项目

- gradle build -x test  跳过测试构建项目

- gradle init       生成一个gradle项目骨架

### Gradle 修改下载源

Gradle 默认的Maven源地址是国外的，访问速度很慢，一般会修改成国内的仓库地址。首先进入到 Gradle 安装目录下的 inti.d 目录下，创建以 .gradle 结尾的文件，该文件会在 build 之前执行，你可以在此配置一些预置配置，例如Maven源地址。

~~~gradle
allprojects {
    repositories {
        mavenLocal()
        maven { name "Alibaba"; url "https://maven.aliyun.com/repository/public"}
        maven {name "bstek"; url "https://nexus.bsdn.org/content/groups/public/"}
        mavenCentral()
    }

    buildscript{
        repositories {
            maven { name "Alibaba"; url "https://maven.aliyun.com/repository/public"}
            maven {name "bstek"; url "https://nexus.bsdn.org/content/groups/public/"}
            maven {name "M2"; url "https://plugins.gradle.org/m2/"}
        }
    }
}
~~~

mavenLocal() 指定使用的 Maven 本地仓库，就是 Maven 配置的 setting.xml 中配置时指定的仓库位置，gradle 查找 jar 包的顺序如下：

1. ${USER_HOME}/.m2/setting.xml
2. ${M2_HOME}/conf/setting.xml
3. ${USER_HOME}/.m2/repository

~~~gradle
maven {url 地址} 指定 maven 仓库，一般使用私有仓库或者三方仓库
mavenCentral() maven 中央仓库，无需配置，声明即可使用。
mavenLocal() maven 本地仓库
~~~

Gradle 可以通过指定仓库地址为本地maven仓库地址和远程仓库地址相结合的方式避免每次都下载以来包，但是下载的jar并不是存储在本地maven仓库的，二十放在自己的缓存目录中，默认 ${USER_HOME}/gradle/caches 目录。 Gradle 缓存的方式和 Maven 并不相同，不能直接将 gradle caches 指向 maven repository 直接使用的。

### Gradle 启用 init.gradle 文件的方式

以下四种方式按顺序执行，如果同一目录下有多个 init 脚本，会按照 a-z 的方式顺序执行。每个 init 脚本都存在一个对应的 gradle 实例，在这个文件中调用的所有方法和属性都会委托给这个 gradle 实例，每个 init 脚本都实现了 Script 接口

1. 命令行指定文件， gradle --init-script ${path}/init.gradle -q taskName 
2. 把 init.gradle 文件放到 ${USER_HOME}/.gradle 目录下
3. 把 .gradle 结尾的文件放到 ${USER_HOME}/.gradle/init.d/ 目录下
4. 把 .gradle 结尾的文件放到 ${GRADLE_HOME}/init.d/ 目录下

## Gradle wrapper包装器

是对 Gradle 的一层包装，用于解决实际开发中可能会遇到的不同项目需要不同版本的 Gradle 问题。可以使用 wrapper,避免不同版本和没有 Gradle 的问题（分享对象没有安装 Gradle 或者安装版本过低）。项目中使用 gradlew, gradlew.cmd 脚本用的就是 wrapper 中规定的 gradle 版本。gradlew ,gradlew.cmd 的使用方式和 gradle 完全一致，只不过是把 gradle 指令换成了 gradlew 指令。在终端执行 gradlew 的时候，需要一些参数来控制 wrapper 的生成，如版本号和下载地址等：

- \--gradle-version  用于指定使用的 Gradle 版本
- \--gradle-distribution-url 用于指定下载 Gradle 发行版的 url 地址

一般项目根目录中会有 gradle 目录，该目录下有 wrapper 目录，该目录下有 gradle-wrapper.jar 和 gradle-wrapper.properties。项目执行时使用的就是该目录下的 gradle

下载会在执行 gradle 命令时触发。
