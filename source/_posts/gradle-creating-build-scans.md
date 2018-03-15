---
title: 创建构建扫描
date: 2018-03-15 00:15:48
tags: Gradle
---
原文链接： https://guides.gradle.org/creating-build-scans

构建扫描是一个可共享的中央化构建记录，通过它可以洞察发生了什么以及为什么。通过在你的项目中应用构建扫描插件，你可以将构建扫描免费发布到 https://scans.gradle.com 上。

## 会生成什么
这篇指南告诉你如何在不修改任何构建脚本的情况下发布构建扫描。你还会学到如何修改构建脚本以实现为给定项目的所有构建启用构建扫描。或者，你也可以选择修改一个初始化脚本以实现为你所有的项目都启用构建扫描。

## 需要哪些东西
* 可以是你自己的示例项目，也可以使用 Gradle 提供的示例项目

* 可以访问互联网

* 可以访问电子邮件

* 大概7分钟

## 选择一个示例项目
Gradle 提供了一个简单的 Java 项目来展示构建扫描的相关功能。如果你希望使用它，那么可以从 https://github.com/gradle/gradle-build-scan-quickstart 克隆或者下载它。但如果你想用自己的项目，可以跳过这一步。

## 自动应用构建扫描插件
从 Gradle 4.3 开始，你可以在不修改任何配置的情况下启用构建扫描。当使用命令行参数 --scan 来发布构建扫描的时候，所需求的构建扫描插件会自动应用。在构建结束之前，命令行会需要你接受一份许可协议。下面的控制台输出演示了该动作。

{% codeblock %}
$ ./gradlew build --scan

BUILD SUCCESSFUL in 6s

Do you accept the Gradle Cloud Services license agreement (https://gradle.com/terms-of-service)? [yes, no]
yes
Gradle Cloud Services license agreement accepted.

Publishing build scan...
https://gradle.com/s/czajmbyg73t62
{% endcodeblock %}

这种机制可以很容易生成构建扫描而不需要修改你的构建的配置。但如果你想要对配置做微调，你可以像下面描述的这样来配置构建扫描插件。

## 在你所有的项目中应用构建扫描
Add a plugins block to the build.gradle file with the following contents:
在 build.gradle 文件中添加一个 plugins 块，内容如下：

{% codeblock %}
plugins {
    id 'com.gradle.build-scan' version '1.12.1' 
}
{% endcodeblock %}

在 Gradle 插件门户查找可使用的最新插件版本。

如果你已经配置了 plugins 块，那就确保把构建扫描插件放在最前面。将它添加到其它插件的后面也行，但是会丢失一些有用的信息。

## 接受许可协议
为了能将构建扫描发布到 https://scans.gradle.com 上，你需要接受一份许可协议。这步操作可以在执行命令行发布的时候执行，但也可以在你的 Gradle 构建脚本中指定，加入下面的部分：

{% codeblock %}
buildScan {
    licenseAgreementUrl = 'https://gradle.com/terms-of-service'
    licenseAgree = 'yes'
}
{% endcodeblock %}

buildScan 块让你能够配置该插件。这里你配置了接受许可协议所需要的2个属性。还有可以可以配置的属性。查看构建扫描用户手册了解更多。

## 发布一个构建扫描
构建扫描通过使用命令行标志 --scan 来发布。

使用 --scan 选项运行一个构建任务。当构建完成，构建数据被发布到 scans.gradle.com 之后，你会看到一个可以查看该构建扫描的链接。

{% codeblock %}
$ ./gradlew build --scan

BUILD SUCCESSFUL in 5s

Publishing build scan...
https://gradle.com/s/47i5oe7dhgz2c
{% endcodeblock %}

## 在线访问构建扫描
在第一次该链接时，你会被要求激活这个构建扫描。

你会收到一份用于激活这个构建扫描的电子邮件，类似如下：

![build scan email](https://guides.gradle.org/creating-build-scans/images/build_scan_email.png)

点击邮件中的链接后，你就会可以查看之前创建的构建扫描了。

![build scan page](https://guides.gradle.org/creating-build-scans/images/build_scan_page.png)

现在你可以浏览构建扫描中包含的所有信息了，包括执行任务所花的时间，构建各阶段所需的时间，各步测试的结果，所使用的插件及其它依赖，各种启用的命令行开关，等等。

## 为所有构建启用构建扫描（可选）
你可以通过配置一个 Gradle 的初始化脚本来避免手动添加插件或者接受许可协议。在 ~/.gradle/init.d （~表示你的用户目录）目录创建一个叫做 buildScan.gradle 的文件，内容如下：

{% codeblock %}
initscript {
    repositories {
        maven { url 'https://plugins.gradle.org/m2' }
    }

    dependencies {
        classpath 'com.gradle:build-scan-plugin:1.12.1'
    }
}

rootProject {
    apply plugin: com.gradle.scan.plugin.BuildScanPlugin

    buildScan {
        licenseAgreementUrl = 'https://gradle.com/terms-of-service'
        licenseAgree = 'yes'
    }
}
{% endcodeblock %}

初始化脚本会在需要的时候下载构建扫描插件并应用到各个项目中，并会接受许可协议。现在你的系统里所有的构建都可以使用 --scan 开关了。

还有一个额外的功能可以添加到这个脚本里，比如在什么情况下发布扫描信息。想要了解更多，查看构建扫描用户指南。

## 总结
这篇指南中，你学会了如何：

* 生成一份构建扫描

* 在线查看构建扫描信息

* 创建一个初始化脚本为所有构建启用构建扫描

## 下一步
更多信息可以在构建扫描用户指南中找到。

## 帮助改进这篇指南
想反馈或者提问？发现了错别字？和其它 Gradle 指南一样，用 Github issue 帮助解决它。请给 gradle-guides/creating-new-gradle-builds 创建一个 issue 或者发起一个 pull request，我们会回复你的。