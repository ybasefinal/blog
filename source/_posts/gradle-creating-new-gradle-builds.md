---
title: 生成新的 Gradle 构建
date: 2018-03-14 12:26:08
tags: Gradle
---
跟随这篇指南，你将会生成一个简单的 Gradle 项目，调用一些 Gradle 的基础命令，以及对 Gradle 如果管理项目有一个大致了解。

## 你将需要
* 大概11分钟

* 一个终端或者IDE应用程序

* 一个 Java 开发工具包（JDK），版本 1.7 及以上（只有运行 Gradle 的时候需要用到）

* 一个 Gradle 实例，版本 4.6 及以上

所给出的 Shell 命令都是 Unix 系统的。各个命令在 Windows 平台都拥有类似命令。

## 初始化项目
首先，我们创建一个目录，我们的项目会保存在这里。
{% codeblock %}
❯ mkdir basic-demo
❯ cd basic-demo
{% endcodeblock %}

然后我们就可以使用 Gradle 的 init 命令来生成一个简单的项目。我们会探讨所有生成出来的文件，以便你能清楚的知道到底发生了什么。
{% codeblock %}
❯ gradle init
Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 3s
2 actionable tasks: 2 executed
{% endcodeblock %}

这条命令应该会显示“BUILD SUCCESSFUL”并生成下面的“空”项目。如果没有，请确保 Gradle 被正确安装，然后检查 JAVA_HOME 环境变量配置是否正确。

这些就是 Gradle 为你生成的。
{% codeblock %}
.
├── build.gradle  1
├── gradle
│   └── wrapper
│       ├── gradle-wrapper.jar  2
│       └── gradle-wrapper.properties  3
├── gradlew  4
├── gradlew.bat  5
└── settings.gradle  6
{% endcodeblock %}

1. 当前项目用于配置任务的项目配置脚本
2. Gradle Wrapper 可执行 JAR 包
3. Gradle Wrapper 属性配置文件
4. 用于 Unix 系统的 Gradle Wrapper 脚本
5. 用于 Windows 的 Gradle Wrapper 脚本
6. 配置哪些项目参与到构建中的设置配置脚本

gradle 的 init 可以生成多种不同类型的项目，它甚至还知道如何将简单的 pom.xml 文件转换为 Gradle。

本来我们可以就此结束这篇指南，但是你可能会想要知道如何在这个项目中使用 Gradle。那么我们继续。

## 创建一个任务
Gradle 提供了使用 Groovy 或基于 Kotlin 的 DSL 来配置任务的 API。一个项目包含了大量任务，每个任务都执行一些基本操作。

Gradle 自带了一个可以在你项目中配置的任务库。例如，有一个叫做 Copy 的核心类型，它可以将文件从一个地方复制到另一个地方。Copy 任务非常有用（查看文档了解详情），但是这里我们还是只做简单了解。执行下列步骤：

创建一个 src 目录。

在 src 中添加一个叫做 myfile.txt 的文件。内容可以是任意的（甚至可以是空的），不过为了方便我们还是只放一行 Hello, World! 到里面。

在主构建文件 build.gradle 中定义一个叫做 copy 的 Copy （注意大写）类型任务，该任务会将 src 目录复制到另一个叫做 dest 的目录中。（你不需要创建 dest 目录 -- 任务会帮你完成）
{% codeblock %}
task copy(type: Copy, group: "Custom", description: "Copies sources to the dest directory") {
    from "src"
    into "dest"
}
{% endcodeblock %}

这里的 group 和 description 可以是你想要的任何东西。你也可以省略它们，但是这么做的话，它们在后续被使用的任务报告中也会被省略。

现在执行你的新任务 copy：
{% codeblock %}
❯ ./gradlew copy
:copy

BUILD SUCCESSFUL in 0s
1 actionable task: 1 executed
{% endcodeblock %}

通过检查 dest 目录是否拥有一个叫做 myfile.txt 的文件，以及文件的内容与 src 目录的相同文件匹配来验证任务是否按预期执行。

## 应用一个插件
Gradle 包含了大量插件，更多可用插件可以在 Gradle 插件门户找到。base 插件就是 Gradle 包含的插件之一。它与一个叫做 ZIP 的核心类型联合起来，你可以用配置好的名字和路径为你的项目生成一个 zip 存档。

使用 plugins 语法将 base 插件添加到你的 build.gradle 文件中。确保将 plugins {} 块添加到文件的最上面。
{% codeblock %}
plugins {
    id "base"
}

... rest of the build file ...
{% endcodeblock %}

现在添加一个任务将 src 目录打成 zip 包。
{% codeblock %}
task zip(type: Zip, group: "Archive", description: "Archives sources in a zip file") {
    from "src"
}
{% endcodeblock %}

base 插件联会同配置一起在 build/distributions 目录生成一个叫做 basic-demo-1.0.zip 的存档文件。

在这种情况下，只需要运行这个新的 zip 任务然后查看生成的 zip 文件是不是你所期待的。
{% codeblock %}
❯ ./gradlew zip
:zip

BUILD SUCCESSFUL in 0s
1 actionable task: 1 executed
{% endcodeblock %}

## 浏览和调试你的构建
我们来看看项目中还有什么事情是 Gradle 可以做的。这里还有一份完成的命令行接口参考。

**找出可用任务**
tasks 命令可以列出你可以调用的 Gradle 任务，包括那么通过 base 插件添加进来的，以及你刚刚自定义添加的任务。
{% codeblock %}
❯ ./gradlew tasks

> Task :tasks

------------------------------------------------------------
All tasks runnable from root project
------------------------------------------------------------

Archive tasks
-------------
zip - Archives sources in a zip file

Build tasks
-----------
assemble - Assembles the outputs of this project.
build - Assembles and tests this project.
clean - Deletes the build directory.

Build Setup tasks
-----------------
init - Initializes a new Gradle build.
wrapper - Generates Gradle wrapper files.

Custom tasks
------------
copy - Simply copies sources to a the build directory

Help tasks
----------
buildEnvironment - Displays all buildscript dependencies declared in root project 'basic-demo'.
components - Displays the components produced by root project 'basic-demo'. [incubating]
dependencies - Displays all dependencies declared in root project 'basic-demo'.
dependencyInsight - Displays the insight into a specific dependency in root project 'basic-demo'.
dependentComponents - Displays the dependent components of components in root project 'basic-demo'. [incubating]
help - Displays a help message.
model - Displays the configuration model of root project 'basic-demo'. [incubating]
projects - Displays the sub-projects of root project 'basic-demo'.
properties - Displays the properties of root project 'basic-demo'.
tasks - Displays the tasks runnable from root project 'basic-demo'.

Verification tasks
------------------
check - Runs all checks.

Rules
-----
Pattern: clean<TaskName>: Cleans the output files of a task.
Pattern: build<ConfigurationName>: Assembles the artifacts of a configuration.
Pattern: upload<ConfigurationName>: Assembles and uploads the artifacts belonging to a configuration.

To see all tasks and more detail, run gradlew tasks --all

To see more detail about a task, run gradlew help --task <task>

BUILD SUCCESSFUL in 0s
1 actionable task: 1 executed
{% endcodeblock %}

**分析和调试你的构建**
Gradle 还提供了一个功能强大的网页版视图，称为一个 build scan。

![basic demo build scan](https://guides.gradle.org/creating-new-gradle-builds/images/basic-demo-build-scan.png)

通过使用 --scan 选项或者在项目中明确地应用 build scan 插件，你可以在 scans.gradle.com 创建一个 build scan。在 scans.gradle.com 上发布 build scan 会将这些数据传输给 Gradle 的服务器。想要在你自己的服务器上保存数据的话，查看 Gradle 企业服务。

试试在执行任务的时候使用 --scan 来创建一个 build scan
{% codeblock %}
❯ ./gradlew zip --scan

BUILD SUCCESSFUL in 0s
1 actionable task: 1 up-to-date

Publishing a build scan to scans.gradle.com requires accepting the Terms of Service defined at https://scans.gradle.com/terms-of-service. Do you accept these terms? [yes, no]
Gradle Cloud Services license agreement accepted.

Publishing build scan...
https://gradle.com/s/repnge6srr5qs
If you browse around your build scan, you should be able to easily find out what tasks where executed and how long they took, which plugins were applied, and much more. Consider sharing a build scan the next time you are debugging something on StackOverflow.

Learn more about how to configure and use build scans in the Build Scan Plugin User Manual.

Discover available properties
The properties command tells you about a project’s attributes.

❯ ./gradlew properties
The output is extensive. Here are just a few of the available properties:

> Task :properties

------------------------------------------------------------
Root project
------------------------------------------------------------

buildDir: /Users/.../basic-demo/build
buildFile: /Users/.../basic-demo/build.gradle
description: null
group:
name: basic-demo
projectDir: /Users/.../basic-demo
version: unspecified

BUILD SUCCESSFUL
{% endcodeblock %}

项目的名称默认与文件夹的名称匹配。你也可以指定 group 和 version 属性，当前的话它们正采用它们的默认值，就如 description 一样。

The buildFile property is fully-qualified path name to build.gradle, which resides in the buildDir — by default, the build subdirectory of the projectDir, the directory containing the build.gradle file.

很多属性你都可以修改。例如，你可能会尝试在你的 build.gradle 文件中加入下面几行，然后重新执行 gradles properties.
{% codeblock %}
description = "A trivial Gradle build"
version = "1.0"
{% endcodeblock %}

## 下一步
祝贺！你已经生成了一个新的 Gradle 构建，并学会了如果探查 Gradle 构建！

有可能你还想为某个平台创建一个代码库或者应用程序，所以这里为你准备了一些可供你选择的如何为你的平台生成构建的教程：

创建 Android 应用

创建 C++ 可执行程序

创建 Groovy 代码库

创建 Java 代码库

创建 Lotlin JVM 代码库

创建 Scala 代码库

你还可以在 Github 上查看很多 Gradle 构建的救命。

## 帮助改进这篇指南
想反馈或者提问？发现了错别字？和其它 Gradle 指南一样，用 Github issue 帮助解决它。请给 gradle-guides/creating-new-gradle-builds 创建一个 issue 或者发起一个 pull request，我们会回复你的。