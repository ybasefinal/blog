---
title: 命令行接口
date: 2018-03-16 19:56:12
tags: Gradle
---
原文链接：https://docs.gradle.org/4.6/userguide/command_line_interface.html

命令行接口是与 Gradle 交互的其中一种主要方式。下面的内容可以做为一份使用命令行执行及定制 Gradle，或者是编写脚本，再或者是配置持续集成的参考。

强烈推荐使用 Gradle Wrapper。下面所有的例子在用到 Wrapper 的时候，你都应该用 ./gradlew 或 gradlew.bat 来替代 gradle。

按照下面的结构在命令行调用 Gradle。任务名之前或之后都可以使用选项。

{% codeblock  %}
gradle [taskName...] [--option-name...]
{% endcodeblock  %}

如果指定了多个任务，那么就用空格区分它们。

选项和参数之前可以使用也可以忽略 = 号；但是还是推荐使用 = 号。

{% codeblock  %}
--console=plain
{% endcodeblock  %}

那些开关选项拥有与之对立的选项，通过 --no- 指定。下面就是彼此对立的。

{% codeblock  %}
--build-cache
--no-build-cache
{% endcodeblock  %}

许多长格式选项都拥有对应的短格式。下面二者作用相同：

{% codeblock  %}
--help
-h
{% endcodeblock  %}

许多命令行开关可以通过在 gradle.properties 中指定以避免需要手动输入。查看配置构建环境指南来了解详情。

下面的部分描述了 Gradle 命令行接口的作用，概略地以用户目标分组。某些插件也添加了它们自己的命令行选项，比如 --tests 用于 Java 测试过滤。想了解如何为你自己的任务暴露命令行选项，查看这个位置“定义及使用命令行选项”。

## 执行任务
你可以运行某个任务及它的所有依赖。

{% codeblock  %}
❯ gradle myTask
{% endcodeblock  %}

你可以从项目报告这里了解到那些项目和任务是可用的。

### 在多层级项目构建工程中执行任务
在多层级项目构建工程中，子项目任务可以以 ":" 子项目名及任务那么的格式来执行。当在项目根目录执行时，下面二者作用相同。

{% codeblock  %}
❯ gradle :mySubproject:taskName
❯ gradle mySubproject:taskName
{% endcodeblock  %}

你也可以只使用任务名来为所有子项目运行任务。例如，这里在项目根目录为所有子项目运行了 "test" 任务。

{% codeblock  %}
❯ gradle test
{% endcodeblock  %}

当在子项目中调用 Gradle 的时候，要忽略项目名：

{% codeblock  %}
❯ cd mySubproject
❯ gradle taskName
{% endcodeblock  %}

当在子项目中执行 Gradle Wrapper 的时候，必需要使用 gradlew 的相对路径。例如：../gradlew 任务名。社区里的 gdub 项目的目标就是让这件事情变得更方便。

### 执行多个任务
你还可以指定多个任务。例如，下面会根据 test 和 deploy 任务在命令行上列出的顺序来执行它们以及它们各自的依赖。

{% codeblock  %}
❯ gradle test deploy
{% endcodeblock  %}

### 将任务从执行中排除
你可以使用 -x 或者 --exclude-task 命令行选项再加上任务任务名将某个任务从执行中排除。

### 图：任务图示例
![任务图示例](https://docs.gradle.org/4.6/userguide/img/commandLineTutorialTasks.png)

救命：排除任务

**gradle dist --exclude-task test** 的输出结果

{% codeblock  %}
> gradle dist --exclude-task test
:compile
compiling source
:dist
building the distribution

BUILD SUCCESSFUL in 0s
2 actionable tasks: 2 executed
{% endcodeblock  %}

你可以看到，尽管 test 任务是被 dist 任务依赖的，它却没有被执行。同样 test 任务的依赖 compileTest 也没有执行。但是 test 的依赖中那些被其它任务所要求的，比如编译，依然会执行。

### 强制执行任务
你可以使用 --rerun-tasks 选项让 Gradle 强制执行所有任务而忽略过时检查。

{% codeblock  %}
❯ gradle test --rerun-tasks
{% endcodeblock  %}

这里会让 test 及其所有依赖强制执行。这有点类似运行 gradle clean test，但是不会清空构建所生成的输出结果。

### 当错误发现时继续构建
默认情况下，Gradle 会在任何任务失败时马上终止执行。这会让构建过程完成的更快，但是也会隐藏那么可能花生的错误。想要在单次构建中发现尽可能多的错误，你可以使用 --continue 选项。

{% codeblock  %}
❯ gradle test --continue
{% endcodeblock  %}

当使用 --continue 执行任务时，Gradle 会执行所有在不发生错误的情况下会执行到的任务，而不是在第一个错误发生的时候就马上停止。不过遇到的每一个错误都会构建结束时体现出来。

If a task fails, any subsequent tasks that were depending on it will not be executed. For example, tests will not run if there is a compilation failure in the code under test; because the test task will depend on the compilation task (either directly or indirectly).
如果某个任务失败，那么任何依赖它的任务都不会被执行。例如，如果 test 下的代码编译失败，那么不会运行测试；因为 test 任务要依赖于编译任务（不管是直接的还是间接的）。

### 任务名缩写
当你在命令行指定任务时，可以不用提供任务的全名。你所提供的只需要让任务能够被识别出来就够了。例如，gradle che 看起来就足够用来识别 check 任务了。

你还可以用任务名各个单词的缩写组成一个驼峰形式。例如，你可以运行 gradle compTest 或者 gradle cT 来执行 compileTest 任务。

示例：任务名缩写驼峰化

**gradle cT** 的输出结果

{% codeblock  %}
> gradle cT
:compile
compiling source
:compileTest
compiling unit tests

BUILD SUCCESSFUL in 0s
2 actionable tasks: 2 executed
{% endcodeblock  %}

你还可以用这些缩写连同 -x 命令行选项一起使用。

## 常用任务
下面按照惯例内置的以及最主要的 Gradle 插件所添加的任务。

### 计算所有结果
通常在 Gradle 构建工程中使用 build 任务来指定装配所有输出并运行所有检查。

{% codeblock  %}
❯ gradle build
{% endcodeblock  %}

### 运行应用程序
通常使用 run 任务来运行应用程序，它会装配好程序然后执行一些配置或者类库。

{% codeblock  %}
❯ gradle run
{% endcodeblock  %}

### 运行所有检查
通常使用 check 任务执行所有验证，包含测试和分析。

{% codeblock  %}
❯ gradle check
{% endcodeblock  %}

### 清理输出
你可以使用 clean 任务清理 build 目录，但是这么做会导致之前计算出的输出会丢失掉，从而导致接下来的任务执行需要额外花费大量构建时间。

{% codeblock  %}
❯ gradle clean
{% endcodeblock  %}

## 项目报告
Gradle 提供一些内置任务可以用来查看构建的相关详情。这有助于理解你的构建工程的结构和依赖关系，以及调试错误。

你可以使用 gradle help 来查看可用报告选项的简要帮助。

### 列举项目
运行 gradle projects 会层级显示出所选项目的子项目。

{% codeblock %}
❯ gradle projects
{% endcodeblock %}

另外你还会得到一份构建扫描的报告。查看创建构建扫描了解更多。

### 列举任务
运行 gradle tasks 会显示所选项目的主要任务列表。这份报告会显示项目的默认任务，另外如果某个任务有描述的话也会被显示出来。

{% codeblock %}
❯ gradle tasks
{% endcodeblock %}

默认情况下，这份报告只显示被分配到某个任务组中的任务。你可以使用 -all 选项来获取更多关于任务列表的信息。

{% codeblock %}
❯ gradle tasks --all
{% endcodeblock %}

### 显示任务用法详情
运行 gradle help --task someTask 会显示关于某个待定任务的详细信息。

示例：获取任务帮助详情

gradle -q help --task libs 的输出结果

{% codeblock %}
> gradle -q help --task libs
Detailed task information for libs

Paths
     :api:libs
     :webapp:libs

Type
     Task (org.gradle.api.Task)

Description
     Builds the JAR

Group
     build
{% endcodeblock %}

这份信息包含待定任务的完整路径，任务类型，任务的命令行选项以及描述。

### 依赖关系报告
构建扫描给出了一份在不同配置，传递依赖及依赖版本选择情况下的完整的可视化依赖关系报告。

{% codeblock %}
❯ gradle myTask --scan
{% endcodeblock %}

上面的命令会给你一条一份网页版报告的链接，你可以在报告中发现如下的依赖信息。

![Build Scan dependencies report](https://docs.gradle.org/4.6/userguide/img/gradle-core-test-build-scan-dependencies.png)

查看查验依赖关系了解更多。

### 列举项目依赖关系
运行 gradle dependencies 会给你一个所选项目的依赖关系列表，按配置做分解。对于每个配置，直接或间接的依赖关系在同一个树中展示。下面是这种报告的一个例子：

{% codeblock  %}
❯ gradle dependencies
{% endcodeblock  %}

Inspecting Dependencies 有一个详细的构建脚本及输出例子。

运行 gradle buildEnvironment 将所选项目的脚本依赖关系可视化，与 gradle dependencies 如何将工程的依赖工程可视化类似。

{% codeblock  %}
❯ gradle buildEnvironment
{% endcodeblock  %}

运行 gradle dependencyInsight 可以让你探查指定项的依赖关系。

{% codeblock  %}
❯ gradle dependencyInsight
{% endcodeblock  %}

由于依赖关系报告可能会非常大，所以可以通过指定一个配置可限制报告大小。这通过 --configuration 这个可靠参数来实现：

### 列举项目属性
运行 gradle properties 会给你一个所选项目的属性列表。

示例：属性的相关信息

**gradle -q api:properties** 的输出结果

{% codeblock  %}
> gradle -q api:properties

------------------------------------------------------------
Project :api - The shared API for the application
------------------------------------------------------------

allprojects: [project ':api']
ant: org.gradle.api.internal.project.DefaultAntBuilder@12345
antBuilderFactory: org.gradle.api.internal.project.DefaultAntBuilderFactory@12345
artifacts: org.gradle.api.internal.artifacts.dsl.DefaultArtifactHandler_Decorated@12345
asDynamicObject: DynamicObject for project ':api'
baseClassLoaderScope: org.gradle.api.internal.initialization.DefaultClassLoaderScope@12345
buildDir: /home/user/gradle/samples/userguide/tutorial/projectReports/api/build
buildFile: /home/user/gradle/samples/userguide/tutorial/projectReports/api/build.gradle
{% endcodeblock  %}

### 软件模型报告
你可以使用 model 任务获取一份软件模型项目的分层视图。

{% codeblock  %}
❯ gradle model
{% endcodeblock  %}

查看软件模型文档了解更多模型报告的信息。

## 命令行自动完成
Gradle 通过 gradle-completion 提供了在 bash 及 zsh 环境下的 tab 键自动完成功能，包括任务、选项、以及 Gradle 的属性，它与 Gradle 分开安装。

图： Gralde 自动完成

![Gradle Completion](https://docs.gradle.org/4.6/userguide/img/gradle-completion-4.0.gif)

## 调试选项
**-?, -h, --help**
显示帮助信息及所有有效的命令行选项。

**-v, --version**
输出 Gradle, Groovy, Ant, JVM 及操作系统的版本信息。

**-S, --full-stacktrace**
输出任意异常的完整堆栈信息（非常长）。参见日志选项。

**-s, --stacktrace**
输出用户异常堆栈信息（例如编译错误）。参见日志选项。

**--scan**
创建一份构建扫描，包含构建工程各个层面的详细信息。

**-Dorg.gradle.debug=true**
调试 Gradle 客户端（非守护）进程。默认 Gradle 会在 localhost:5005 上等待你附加一个调试器。

**-Dorg.gradle.daemon.debug=true**
调试 Gradle 守护进程。

## 性能选项
在做性能调优的时候试试这些选项。到这里了解如何提升 Gradle 构建的性能。

这里许多选项都可以在 gradle.properties 中进行指定而无需在命令行用设置开关。查看配置构建环境指南。

**--build-cache, --no-build-cache**
打开 Gradle 构建缓存。Gradle 会试图复用上一次构建的输出结果。默认关闭。

**--configure-on-demand, --no-configure-on-demand**
打开 Configure-on-demand。运行本次构建中只有关联的项目被配置。默认关闭。

**--max-workers**
设置 Gradle 可能会用到的 workers 的最大个数。默认值为处理器的数量。

**--parallel, --no-parallel**
并行构建项目。查看 “Parallel project execution” 了解这个选项的限制。默认关闭。

**--profile**
在 $buildDir/reports/profile 目录生成一份高级性能报告。优先使用 --scan。

**--scan**
生成一份构建扫描，包含了详细的性能诊断信息。

![Build Scan performance report](https://docs.gradle.org/4.6/userguide/img/gradle-core-test-build-scan-performance.png)

### Gradle 守护进程选项
你可以使用下列命令行选项管理 Gradle 守护进程。

**--daemon, --no-daemon**
使用 Gradle 守护进程运行构建。如果守护进程没有运行或者繁忙就启动一个新的守护进程。默认打开。

**--foreground**
在一个前台进程中启动 Gradle 守护进程。

**--status (Standalone command)**
运行 gradle --status 列举运行中及近期停止的 Gradle 守护进程。只显示同一 Gradle 版本的守护进程。

**--stop (Standalone command)**
运行 gradle --stop 来停止所有同一 Grade 版本的守护进程。

**-Dorg.gradle.daemon.idletimeout=(number of milliseconds)**
Gradle 守护进程会在 number 秒空闲后停止。默认值为 10800000 （3小时）。

## 日志选项
### 设置日志级别
你可以用下列选项定制 Gradle 日志的冗余程度，按照最小冗余到最大冗余排序。查看日志文档了解更多。

**-Dorg.gradle.logging.level=(quiet,warn,lifecycle,info,debug)**
通过 Gradle 属性设置日志级别。

**-q, --quiet**
只记录错误。

**-w, --warn**
设置日志级别为警告。

**-i, --info**
设置日志级别为信息。

**-d, --debug**
设置日志级别为调试（包含正常堆栈信息）。

默认的日志级别是生命周期。

### 定制日志格式
你可以使用下列方式指定控制台模式来控制输出（颜色和字体）：

**-Dorg.gradle.console=(auto,plain,rich,verbose)**
通过 Gradle 属性指定控制台模式。下面会立即介绍各种模式。

**--console=(auto,plain,rich,verbose)**
指定生成何种控制台输出。

设置为 plain 只生成普通文本。这个选项禁用了控制台输出中的所有颜色和其它高级输出。当 Gradle 没有被附加到终端上时这是默认选项。

设置为 auto （默认选项）的情况下，当构建进程附加到控制台时在控制台输出中启用颜色及其它高级输出，而没有附加到控制台的时候还是只生成普通文本。当 Gradle 被附加到终端上时这里默认选项。

设置为 rich 的情况下，在控制台输出中启用颜色及其它高级输出，不管构建进程是否附加到了控制台上。当没有附加时，构建的输出结果会使用 ANSI 的控制字符来生成高级输出。

设置为 verbose 的情况下，颜色及其它高级输出类似 rich 选项，但是任务名及产出以及生命周期的日志级别输出，这在 Gradle 3.5 及以下中是默认的。

### 显示或隐藏警告
默认情况下，Gradle 不会显示各种警告（例如过时警告）。反之，Gradle 会将它们收集起来并构建结束时呈现一个像下面这样的总结：

Deprecated Gradle features were used in this build, making it incompatible with Gradle 5.0.

你可以用下列选项控制控制台里的警告冗余程度。

**-Dorg.gradle.warning.mode=(all,none,summary)**
通过 Gradle 属性来指定警告模式。下面会立即介绍各种模式。

**--warning-mode=(all,none,summary)**
指定如果记录警告。默认是 summary。

设置为 all 记录所有警告。

设置为 summary 抑制所有警告并在构建构建时呈现一个总结。

设置为 none 抑制所有警告，包括构建结束时总结。

### 高级输出
Gradle 的高级输出可以在构建运行时显示额外信息。

![Gradle Rich Console](https://docs.gradle.org/4.6/userguide/img/rich-cli.png)

特性：

上面的日志以生成它们的任务来分组

进度条和计时器形象化地的描述了全部状态

下面的几行并行的 work-in-progress 描述了正在发生的情事

## 执行选项
下列选项会影响构建是如何执行的，通过更改构建内容或依赖关系的解析方式。

**--include-build**
将构建以混合体运行，包含被指定的构建。查看混合构建。

**--offline**
指定构建应该在不访问网络资源的情况下运行。了解更多覆盖依赖缓存的选项。

**--refresh-dependencies**
刷新依赖状态。查看依赖管理文档来了解如果使用它。

**--dry-run**
运行 Gradle 但不执行所有任务的动作。用这个来显示哪些任务被执行了。

## 环境选项
你可以通过下列选项来定制很多关于构建脚本，配置文件，缓存以及其它位于何处的内容。了解更多如何定制你的构建环境的内容。

**-b, --build-file**
指定构建文件。例如： gradle --build-file=foo.gradle。默认是 build.gralde，然后是 build.gradle.kts，再然后是 myProjectName.gradle。

**-c, --settings-file**
Specifies the settings file. For example: gradle --settings-file=somewhere/else/settings.gradle

**-g, --gradle-user-home**
指定 Gradle 用户主目录。默认是 用户主目录里的 .gradle 目录。

**-p, --project-dir**
指定 Gradle 的启动目录。默认是当前目录。

**--project-cache-dir**
指定待定项目的缓存目录。默认值是项目根目录的 .gradle。

**-u, --no-search-upward (deprecated)**
不在父目录中搜索 settings.gradle 文件。

**-D, --system-prop**
为 JVM 设置一个系统属性，例如 -Dmyprop=myvalue。查看“系统属性”部分。

**-I, --init-script**
指定一个初始化脚本。查看初始化脚本。

**-P, --project-prop**
为主项目设置一个项目属性，例如 -Pmyprop=myvalue。查看“项目属性”部分。

**-Dorg.gradle.jvmargs**
设置 JVM 参数。

**-Dorg.gradle.java.home**
设置 JDK 主目录。

## 启动新项目
### 创建新的 Gradle 构建工程
使用内置 gradle init 任务来创建一个新的 Gradle 构建工程，可以使用新的或者已存在的项目。

{% codeblock %}
❯ gradle init
{% endcodeblock %}

很多时候你会想要指定一个项目类型。可用的项目类型包括 basic （默认），java-library, java-application 以及其它。查看初始化插件文档了解更多。

{% codeblock %}
❯ gradle init --type java-library
{% endcodeblock %}

### 规范和规定 Gradle
内置的 gradle wrapper 任务会生成一个脚本，gradlew，它会调用一个定义好版本的 Gradle，并在需要时预先下载它。

{% codeblock %}
❯ gradle wrapper --gradle-version=4.4
{% endcodeblock %}

除了 --gradle-version 之外你也可以指定 --distribution-type=(bin|all), --gradle-distribution-url, --gradle-distribution-sha256-sum。更多关于如何使用这些选项的信息记录在 Gradle wrapper 文档中。

## 持续构建
持续构建让你可以在输入发生变更时自动重新执行所请求的任务。

例如，你可以持续运行测试任务及所有它的依赖任务，通过运行：

{% codeblock %}
❯ gradle test --continuous
{% endcodeblock %}

Gradle 会表现为就好像你在源代码或测试代码发生修改后运行 gradle test 一样。这也表示那些没有关联的修改（比如构建脚本的修改）是不会触发重新构建的。想要合并构建逻辑变更，持续构建需要手动重启。

### 终止持续构建
如果 Gradle 被附加到一个可交互输入源，比如一个终端，那么持续构建可以通过按 CTRL-D （在 Windows 上，还需要按 ENTER 或 RETURN）来退出。如果 Gradle 没有附加到可交互输入源（例如作为脚本的一部分运行），那么构建进程必需要被终止（例如使用 kill 或者类似的命令）。如果构建是通过 Tooling API 来执行的，那么构建可以使用 Tooling API 的取消机制来取消。

### 限制和怪异
持续构建是一个孵化功能。

当前版本的持续构建实现还有很多问题需要注意。这些问题会在未来的 Gradle 版本中被处理。

### 构建循环
Gradle 会在一个任务开始执行的时候启动对修改的监控。如果某个任务在执行过程中修改了它的输入，那么 Gradle 会检测到这个修改并触发一个新的构建。如果每次任务执行时，输入都再次被修改，那么构建也会再次被触发。这种情况不只在持续构建中存在。在非持续构建时，一个会修改自身输入源的任务永远不会被视为最新的。

如果你的构建进入了这种构建循环，你可以通过可看 Gradle 报告的文件列表来跟踪任务。在找到每次构建中都发生修改的文件后，你应该找出以它作为输入源的任务。某些时候，它可能是明显的（例如用 compileJava 编译的一个 Java 文件）。其它情况下，你可以使用 --info 记录日志来找出由于那个文件而过时的任务。

### Java 9 的限制
由于 Java 9 的类访问限制，Gradle 无法设置某些操作系统待定的选项，这就表示：

在 macOS 上，Gradle 会每 10 秒轮询文件更改而不是每 2 秒。

在 Windows 上，Gradle 必需使用独立文件监视（类似在 Linux/Mac OS 上），这可能会导致持续构建在非常大的项目上不可用。

### 性能及稳定性
JDK 文件监视要依赖 macOS 上的低效文件系统轮询（查看 JDK-7133447）。这会极大的延迟拥有很多源文件的大型项目的修改通知。

另外，这个监视机制在 macOS 负载过重里可能会发生死锁（查看： JDK-8079620）。这将表现为 Gradle 显得不通知文件更改。如果你猜测可能发生了这种情况，那就退出持续构建并重新启动。

在 Linux上，OpenJDK 的文件监视服务的实现有时会丢失文件系统事件（查看： JDK-8145981）。

### 符号链接更改
* 创建或删除文件的符号链接会启动一个构建。

* 修改符号链接的目标不会导致重新构建。

* 创建或删除目录的符号链接不会导致重新构建。

* 在符号链接的目标目录中创建新文件不会导致重新构建。

* 删除目标目录不会导致重新构建。

### 构建逻辑的修改不被考虑
当前实现不会在后续构建时重新计算构建模型。这表示任务配置的修改，或者任何构建模型的修改，实际上被忽略了。