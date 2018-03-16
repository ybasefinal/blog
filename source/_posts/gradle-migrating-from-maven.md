---
title: Maven 迁移
date: 2018-03-15 14:21:03
tags: Gradle
---
原文链接：https://guides.gradle.org/migrating-from-maven

## 从 Maven 迁称到 Gradle
“欢迎入住 Gradle 社区！”... 最起码这是你完成这篇指南后我们所希望的。

转换构建工具可能会让人感到提心吊胆，但是你不是一个人。你可以从 help.gradle.org 搜索文档，论坛，以及 StackOverflow，或者在感觉被卡住的时候在论坛向 Gradle 社区求助。

Apache Maven 是一个被广泛使用的 Java 及其它基于 JVM 的项目构建工具，所以那么想要使用 Gradle 的人经常需要将现有的 Maven 构建工程迁称到 Gradle。这篇指南通过解释两者所采用模型的不同及相似之处来帮助你完成迁移，并提供一些可以简化流程的步骤供你参考。

## 尝试迁移
Gradle 和 Maven 最主要的区别在于灵活性，性能，用户体验，及依赖管理。Maven 与 Gradle 特性比较提供了一份这些层面的可视化概述。

从 Gradle 3.0 开始，Gradle 在如何让 Gradle 构建更快方面做了重大努力，包括这些特性如构建缓存，编译回避，以及一个改良的增量 Java 编译器。现在就算不用构建缓存，Gradle 也要比 Maven 在大多数主流项目上快 2 到 10 倍。可以在这里找到从 Maven 切换到 Gradle 的深入性能比较及商业案例。

## 理解基本区别
Gradle 和 Maven 在如何构建项目上有基本的区别。Gradle 是基于一张任务依赖图，具体的活由任务来干。而 Maven 的工作模型及是阶段性的固定流程，你可以在这条流程上附加目标（也就是干活的人）。尽管如此，从的迁移却出人意料地的简单，因为 Gradle 遵循了 Maven 的大部分惯例，并且二者的依赖管理采用的是相同的方式。

有一个特别有用的功能可以帮你理解新的 Gradle 构建工具，即构建扫描：它们是构建信息的网页版快照，有助于协作调试及性能微调分析。例如，这里是构建 Groovy 的时间性：

![groovy build scan](https://guides.gradle.org/migrating-from-maven/images/groovy-build-scan.png)

你可以使用构建扫描来调试依赖方案，Gradle 任务的执行，及其它构建的各个层面。

一旦你决定了要做迁移，那么从哪里开始？最好的起点是记录 Maven 构建的输入和输出，这样你就可以验证新的 Gradle 构建的是否在功能上是等同的。

## 自动转换
Gradle 的 init 任务不仅仅可以用来创建新的项目轮廓，它还可以自动将现有的 Maven 项目 转换为 Gradle。而你所需要做的仅仅只是在项目根目录运行下面的命令

{% codeblock %}
$ gradle init
{% endcodeblock %}

然后让 Gradle 做剩下的事情。这基本上包括转换现有的 POMs 以及生成对应的 Gradle 构建文件，如果项目是多层次结构的话还会生成一个 settings.gradle 文件。

你会发现新生成的 Gradle 构建里包含了 POM 中的所有自定义仓库、外部和跨项目依赖，相应的插件（任何 maven，java 和 war 之类的），以及其它。用户指南中包含了一份完成的功能自动转换清单。

有一件情要心里有底，那就是程序是不会自动转换的。转换它们的时候不是一定会有问题，但是需要你做一些手动工作。

如果你的项目很幸运，没有多少插件或者各种定制，那你可以在迁移完成的时候直接运行下面的。

{% codeblock %}
$ gradle build
{% endcodeblock %}

它会运行测试然后自动加入所需要的部件而不需要你的介入。

## 物料清单（BOMs）
Maven 可以通过在定义了 <packaging>pom</packaging> 的 POM 文件中定义好依赖管理来分享依赖约束。然后这种特殊类型的 POM（也就是一个物料清单） 就可以补导入到其它 POM 文件中，这样你的项目依赖的代码库版本就可以保持一致了。

Gradle 4.6 及以上支持导入物料清单，导管在 Gradle 5.0 之前你还必需明确的在 settings.gradle 文件中加入下面的代码：

{% codeblock [settings.gradle]%}
enableFeaturePreview("IMPROVED_POM_SUPPORT")
{% endcodeblock %}
Gradle 的 BOM 支持类似于在 Maven 中的使用 <scope>import</scope> 依赖 BOM。但是在 Gradle 中是通过在 BOM 中使用正常的依赖定义来实现的。

{% codeblock [build.gradle]%}
dependencies {
    implementation 'org.springframework.boot:spring-boot-dependencies:1.5.8.RELEASE' 1

    implementation 'com.google.code.gson:gson' 2
    implementation 'dom4j:dom4j'
}
{% endcodeblock %}

1. Spring Boot 的依赖 BOM
2. 无需要版号的依赖

更多信息可以在用户指南的从 Maven BOM 导入版本号建议中找到。

Gradle 4.5 版及以下不直接支持 BOMs，不去你可以通过使用 Netfilx 的 nebula-dependency-recommender-plugin 插件在 Gradle 构建文件中使用该功能。

## Provided scope 和可选依赖
Gradle 在管理依赖的方式上有很大的灵活性，特别是通过配置管理。但是，除非你配置了 war 插件，否则 provided 这个 scope 是不可用的。不过使用可以 Gradle 2.12 加入的 compileOnly 配置来实现相同的功能。

至于 optional 依赖，Gradle 4.6 及以上在消耗 POM 文件的时候会创建依赖约束。对于 Gradle 5.0 之前的版本，你得在 settings.gradle 里加入 enableFeaturePreview('IMPROVED_POM_SUPPORT')。

参考 Gradle 用户指南的传递性依赖管理来了解更多关于依赖约束的信息。

Gradle 4.5 及以下不支持可选依赖，但是你可以通过使用 Netflix 的 optional-base 插件来使用这个功能。

## Maven profiles 及属性
Maven 允许拥有多种参数化构建的方式。有一些是项目模型固定的属性，其它的则是用户在 POM 中自定义的。它甚至允许你将系统属性当做项目属性来对待。

Gradle 有一套类似的项目属性，尽管他们和系统属性有所区分。你可以，举例来说，将属性定义在：

* 构建文件中的

* 项目根目录中的 gradle.properties 文件

* $HOME/.gradle 目录中的 gradle.properties 文件

这些不是所有的选项，所以如果你对找出更多如何以及什么地方定义属性有兴趣，查看用户指南。

一个很重要的地方是你需要意识到当同一个属性同时被定义在构建文件和某个扩展属性文件中时会发生什么：构建文件的优先级更高。永远是这样。幸运的是，你可以效仿 profiles 的观念来提供可覆盖的默认值。

这也就将我们带入到了 Maven profiles 这个地方。有种办法可以基于环境，目标平台，或者其它任何类似因素来启用和禁用不同的配置。从逻辑上来讲，也就是用有限的if语句。因为 Gradle 拥有很多不错的方法来定义条件，所以也不需要正式的去支持 profiles（except in the POMs of dependencies）。你可以很容易的将条件和次要的构建文件绑定在一起来获取同等的功能，下面就会讲到。

我们假设你拥有针对不同环境的配置：本地开发（默认的），测评环境，以及生产。要实现类似 profile 的功能，首先在项目根目录为各种环境创建构建文件：profile-default.gradle，profile-test.gradle，以及 profile-prod.gradle。接下来，将类似下面的条件加入到主构建文件中：

{% codeblock %}
if (!hasProperty('buildProfile')) ext.buildProfile = 'default'
apply from: "profile-${buildProfile}.gradle"
{% endcodeblock %}

然后你所需要做的就只是将特定环境的配置，如项目属性、依赖，等等放入对应的构建文件中。要激活某个特定的 profile，你只需要在命令行传入相应的项目属性即可：

{% codeblock %}
$ gradle -PbuildProfile=test build
{% endcodeblock %}

或者你也可以用另一种方式来设置项目属性。这取决于你自己。而这些条件不仅仅可以检查项目属性。你还可以检查环境变量，JDK 的版本，构建所运行的系统，以及其它所有你能想到的一切。

有一件事情要记在心里是的高级 ‘if’ 条件让构建更难理解和维护，这和它们将面向对象代码复杂化有点相似。同样的事情也发生在 profiles 上。Gradle 为你提供了更好的方法来避免像 Maven 中大量使用 profiles，比如它提供了变体。

这里有一篇很长的关于 Maven profiles 在 Gradle 中应用的讨论，没有比它介绍更详细的了，由 Benjamin Muschko 提供。

## 资源过滤
Maven 有一个阶段叫做 process-resources，它拥有一个 goal 叫做 resources:resources，它默认绑定到该阶段上。这给予了构建作者一个机会去执行各种文件的变量替换，比如 web 资源，打包的属性文件，等等。

Gradle 中的 Java 插件提供了一个叫做 processResources 的任务来做同样的事情。这里是一个配置样例：

{% codeblock [build.gradle]%}
processResources {
    expand(version: version, buildNumber: currentBuildNumber)
}
{% endcodeblock %}

每个冒号左边是节点名称，而右边则是项目属性。变量替换会应用到你的所有资源文件（通常位于 src/main/resources 下）。

Gradle 还有其它强大的方法来做属性处理。你可以通过使用闭包将自定义过滤器挂住，这会让它们一行接着一行的往下执行，或者你也可以加入自己的 FilterReader 实现。想了解更多信息，查看 ContentFilterable 接口的文档，这个接口是所有 copy 和 archive 任务都要实现的。

## 集成测试
尽管单元测试非常有用，它们却无法保证应用程序或代码库整体能够正常工作。对象之间的相互以及环境对它们的影响很容易产生 bug。这就是为什么许多项目会采用一些高级测试，有时也被称为集成、功能性或验收测试。

Maven 通过提供一些额外的阶段来支持这类测试：预集成测试、集成测试、集成后测试、以及验证。它还使用了 Failsafe 插件而不是 Surefire 来保证失败的集成测试不会自动使构建失败（因为你有可能会想要清空资源，比如运行应用服务器）。

另一个需要考虑的因素是你的集成测试类应该放在什么地方。默认的方式是将它们与单元测试混合到一起，但这并不是最理想的办法。一种常用的替代方式是使用 profiles，这样你就可以让两种测试保持分离了。

所以说如何也能在 Gradle 中完成这种设置呢？忘记插件吧：这种情况下源代码集就是你最好的朋友。一个标准的 Java 项目已经拥有了两个源代码码，一个用于主要的类，另一个测用于测试类。所以为什么不可以为集成测试新加一个源代码集呢？更甚至为何不为不同类型的集成测试添加不同的源代码集呢？比如说针对在线数据库的低级测试以及 FitNesse 之类的高级测试。

通过定义一个新的源代码集，Gradle 能自动为你创建相应的配置（[sourceSet]Compile 和 [sourceSet]Runtime），以及编译任务（compile[SourceSet][Lang]）和资源处理任务（process[SourceSet]Resources）。你所需要做的仅仅只是为测试添加一个任务并保证类路径完整。如果你使用到了数据库或者应用服务器，那你可能也需要创建一个任务来启动、停止它们。

现在我们来看一个例子，以便你了解实际使用中都涉及到了什么：

{% codeblock [build.gradle]%}
sourceSets {
    integTest {
        compileClasspath += main.output + test.output
        runtimeClasspath += main.output + test.output
    }
}
configurations {
    integTestCompile.extendsFrom testCompile
    integTestRuntime.extendsFrom testRuntime
}
{% endcodeblock %}

上面这个例子中，我创建了一个叫做 integTest 的源代码集。另外我还保证了在编译集成测试时，应用程序或者代码库的类，以及它们的依赖，都已经包含在了类路径中。

你的集成测试可能会使用到它们自带的一些类库，所以你也会需要将这些类库也加入到编译类路径中。这用正常的 dependencies 块就可以做到：

{% codeblock [build.gradle]%}
dependencies {
    // ...
    integTestCompile 'org.codehaus.groovy:groovy-all:2.4.3'
    integTestCompile 'org.spockframework:spock-core:0.7-groovy-2.0', {
        exclude module: 'groovy-all'
    }
}
{% endcodeblock %}

现在集成测试可以编译了，但是去没有办法来运行它们。这里就要用到自定义任务了：

{% codeblock [build.gradle]%}
task integTest(type: Test) {
    dependsOn startApp
    finalizedBy stopApp
    testClassesDirs = files(sourceSets.integTest.output.classesDirs)
    classpath = sourceSets.integTest.runtimeClasspath
    mustRunAfter test
}
{% endcodeblock %}

在上面的例子中，我假设针对应用服务器的集成测试需要在某个特定的时间点启动和关闭。你可以在 Gradle 的 DSL 参考里找到更多关于测试任务的可配置项以及如何配置它们。

到了这一步，就只剩下将 integTest 任务合并到你的任务图中，例如让构建依赖它。如何将它合并到构建中完全取决于你。如果你想支持其它类型的测试，重复上述步骤就够了。

Common plugins
## 常用插件
Maven 和 Gradle 都用同样的方式来支持扩展，那就是插件。尽管底层的插件系统有大区别，但是它们却能共享很多基于特征的插件，比如：

* Shade/Shadow

* Jetty

* Checkstyle

* JaCoCo

* AntRun (继续浏览查看更多信息)

为什么会这样？因为大部分插件都依赖标准的 Java 惯例，所以迁移要做的仅仅只要将 Maven 插件的配置换成 Gradle 而已。举个例子，这里是 Maven Checkstyle 插件的配置样例：

{% codeblock %}
...
<plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-checkstyle-plugin</artifactId>
  <version>2.17</version>
  <executions>
    <execution>
      <id>validate</id>
      <phase>validate</phase>
      <configuration>
        <configLocation>checkstyle.xml</configLocation>
        <encoding>UTF-8</encoding>
        <consoleOutput>true</consoleOutput>
        <failsOnError>true</failsOnError>
        <linkXRef>false</linkXRef>
      </configuration>
      <goals>
        <goal>check</goal>
      </goals>
    </execution>
  </executions>
</plugin>
...
{% endcodeblock %}

在迁移到 Gradle 里，plugin 块外的所有内容都可以被忽略。这种情况下，对应的 Gradle 配置也就如下面所示：

{% codeblock %}
checkstyle {
    config = resources.text.fromFile('checkstyle.xml', 'UTF-8')
    showViolations = true
    ignoreFailures = false
}
{% endcodeblock %}

Checkstyle 任务是 check 任务的依赖，会自动被加入进来。不过如果你想确保 Checkstyle 在测试之前运行，那么只需要用  mustRunAfter() 方法指定一下顺序：

{% codeblock %}
test.mustRunAfter checkstyleMain, checkstyleTest
{% endcodeblock %}

如果你见，Gradle 的配置通常要比同行类型的 Maven 配置简短的多。另外执行模型也要灵活的多，因为我们不再受 Maven 的固定阶段所限制。

当从 Maven 迁移一个项目时，别忘了源代码集。它们通常可以比 Maven 提供更强大的集成测试或源代码生成功能，所以在酝酿迁移方案时，别忘了它们。

## Ant 目标
很多 Mavan 构建依靠 AntRun 插件来自定义构建，而无需再自制一个 Maven 插件。Gradle 没有与之等价的插件，因为 Ant 在 Gradle 构建中是最顶级成员，通过 ant 对象实现。例如，你可以像这样来使用 Ant 的 Echo 任务：

{% codeblock %}
task sayHello {
    doLast {
        ant.echo message: 'Hello!'
    }
}
{% endcodeblock %}

甚至连 Ant 的属性和文件集都是天然支持的。想要了解更多信息，查看用户指南的 Ant 章节。

## 你不需要的插件
值得记住的是 Gradle 的构建通常比 Maven 更容易定制。就此而言，你可能并不需要用 Gradle 插件来替换 Maven 的。例如，Maven 的 Enforcer 插件允许你控制依赖版本和环境因素，但是在 Gradle 中却可以直接在构建脚本中配置出来。

## 不常用的和自定义插件
你可以偶尔会遇到在 Maven 中有，而 Gradle 中没有的插件，特别是你或者你所在的机构里某人自己编写的插件。这种情况下就要依靠你自己对于 Gradle （也有可能是对 Maven）是如果工作的理解程度了，因为通常你得自己编写这些插件。

以迁移的目的来说，Maven 插件拥有两种类型：

* 使用 Maven 项目对象的。

* 不使用 Maven 项目对象的。

为什么这个很重要？因为如果你使用的是后一种，那么你可以将它重新实现为 Gradle 任务。定义输入及输出以响应魔力参数，并将执行逻辑转换为任务动作。

如果插件依赖于 Maven 项目，那你就得重写它了。不要从 Maven 插件是如果工作的这点开始思考，而是思考它要试图解决的问题是什么。然后尝试在 Gradle 中解决那个问题。你可能会发现两种构建模型区别太大，导致将 Maven 插件代码转变成 Gradle 的效率不高。就有利方面来说，因为 Gradle 拥有的更丰富的构建模型的缘故，编写插件会比最开始在 Maven 里编写来的更容易。

如果你确实需要自定义逻辑，不管是通过构建文件还是插件，那么请确保自己对 Gradle 的 DSL 参考很熟悉，它提供了一份你可能会用到的 API 的完整文档。它对标准的配置块（以及它们背后的对象），系统里的核心类型（项目，任务等等），以及标准的任务集做了详细解释。最主要的入口是 Project 接口，它是构建文件背后的顶级对象。

## 下一步
你做到了！Gradle 社区欢迎你！

一旦你的 Gradle 构建可以正常工作了，那么就值得尝试让构建更快更容易维护。查看 Gradle 的文档来了解如何组织构建逻辑以及如何优化构建性能。你可以选择将构建的设计过程文档化，这会是一个不错的决定，比如你是如何保存和维护依赖版本的。

如果你将你的 Gralde 构建扫描发送给我们，那我们会给你发送一些免费的Gradle 装饰的优惠码。

最后，如果其它用户在 Gradle 构建上遇到了问题，不要害怕，直接向他们索要构建扫描。

## 帮助改进这篇指南
想反馈或者提问？发现了错别字？和其它 Gradle 指南一样，用 Github issue 帮助解决它。请给 gradle-guides/creating-new-gradle-builds 创建一个 issue 或者发起一个 pull request，我们会回复你的。