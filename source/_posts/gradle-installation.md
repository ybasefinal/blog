---
title: Gradle 安装
date: 2018-03-14 11:28:18
tags: Gradle
---
你可以在 Linux, MacOS 或 Windows 上安装 Gradle 构建工具。这篇文档涵盖了使用如 SKDMAN!, Homebrew, 或 Scoop 等包管理器来安装 Gradle，以及手动安装 Gradle 的过程。

如果想要升级 Gradle，推荐使用 Gradle Wrapper。

你可以在版本页面找到所有的版本以及它们的校验码。

## 必备条件
Gradle 可以运行在所有主流的操作系统上，需要仅仅只是 Java JDK 7 或者更高版本。运行 java --version 来检查你的 jdk 版本。你应该会看到如下信息：
{% codeblock %}
❯ java -version
java version "1.8.0_151"
Java(TM) SE Runtime Environment (build 1.8.0_151-b12)
Java HotSpot(TM) 64-Bit Server VM (build 25.151-b12, mixed mode)
{% endcodeblock %}

Gradle 自带了 Groovy 库，所以不需要额外安装 Groovy。其它的 Groovy 安装会被 Gradle 忽略。

Gradle 会使用你的 path 中可以找到的任意 JDK。或者，你可以设置 JAVA_HOME 环境变量以指向预期的 JDK 版本。

## 使用包管理器安装
SDKMAN! 是一个在大多数 Unix 系统上管理各种软件开发包版本并行的工具。
{% codeblock %}
❯ sdk install gradle 4.6
{% endcodeblock %}

Homebrew 是 “macOS 上缺失的包管理器”。
{% codeblock %}
❯ brew install gradle
{% endcodeblock %}

Scoop 是受 Homebrew 启发的 Windows 平台命令行安装工具。
{% codeblock %}
❯ scoop install gradle
{% endcodeblock %}

Chocolatey 是 “Windows 上缺失的包管理器”。
{% codeblock %}
❯ choco install gradle
{% endcodeblock %}

MacPorts 是 macOS 上管理工具的一套系统：
{% codeblock %}
❯ sudo port install gradle
{% endcodeblock %}

↓ 继续下一步

## 手动安装
**第一步 下载最新版 Gradle**
发布的 ZIP 文件有2种

只包含二进制的（可执行程序）

包含文档和源码的完成体（所有）

需要更老的版本？查看版本页面。

**第二步 解压 Gradle**
**Linux 和 MacOS 用户**
将 Gradle 解压到你选择的目录中，例如：
{% codeblock %}
❯ mkdir /opt/gradle
❯ unzip -d /opt/gradle gradle-4.6-bin.zip
❯ ls /opt/gradle/gradle-4.6
LICENSE  NOTICE  bin  getting-started.html  init.d  lib  media
{% endcodeblock %}

**Microsoft Windows 用户**
在**文件管理器**中创建一个新的目录 C:\Gradle。

打开一个新的**文件管理器**并定位到 Gradle 下载后保存的位置。双击打开 ZIP 存档显示内容。拖动 gradle-4.6 目录到你新创建的 C:\Gradle 目录。

另外你可以使用解压工具将 Gradle ZIP 包解压。

**第三步 配置你的系统环境变量**
想要运行 Gradle，首先添加 GRADLE_HOME 环境变量。它需要指向解压后的文件目录。然后将 GRADLE_HOME/bin 添加到 PATH 环境变量中。通常这些就足以运行 Gradle 了。

**Linux & MacOS 用户**
配置你的 PATH 环境以包含解压后的 bin 目录，例如：
{% codeblock %}
❯ export PATH=$PATH:/opt/gradle/gradle-4.6/bin
{% endcodeblock %}

**Microsoft Windows 用户**
在**文件管理器**的这台电脑（或者电脑）上右键，然后依次点击属性 → 高级系统设置 → 环境变量。

在系统变量中选中 Path，然后点击编辑。将 C:\Gradle\gradle-4.6\bin 添加为一个新项。然后点击确认来保存设置。

↓ 继续下一步

## 验证安装
打开一个控制台（或者一个 Windows 命令提示符）然后运行 gradle -v 来启动 gradle 并显示版本信息，例如：
{% codeblock %}
❯ gradle -v

------------------------------------------------------------
Gradle 4.6
------------------------------------------------------------

Build time:   2018-02-21 15:28:42 UTC
Revision:     819e0059da49f469d3e9b2896dc4e72537c4847d

Groovy:       2.4.12
Ant:          Apache Ant(TM) version 1.9.9 compiled on February 2 2017
JVM:          1.8.0_151 (Oracle Corporation 25.151-b12)
OS:           Mac OS X 10.13.3 x86_64
{% endcodeblock %}

如果你遇到任何问题，查看关于安装故障排除章节。

你可以下载 SHA-256 文件并跟随这个验证指示来验证 Gradle 的完整性。

## 下一步
现在你已经安装了 Gradle，使用这些资源来入门：

* 跟随 Creating New Gradle Builds 教程生成你的第一个 Gradle 项目。

* 注册一个核心开发者的在线 Gradle 引导培训。

* 学习如何使用命令行接口来达成日常任务。

* 配置 Gradle 任务，如使用 HTTP 代码下载依赖。

* 订阅 Gradle 时事通讯以获知每月发布及社区动态。