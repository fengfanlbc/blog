---
title: IntelliJ-Maven-Spark-Scala
date: 2017-05-11 10:52:38
tags: Spark
---

在学习 *Spark* 的时候，如果有一个好的简便的开发工具和固定的流程，会使得学习更加简单， 这里要介绍的就是在查找和尝试各种开发流程后总结出来的经验。

首先我假定已经安装好了 Intellij，以及*Scala* 插件。第一步需要下载 Spark（我下的是**spark-2.1.1-bin-hadoop2.7**）以及对应的 Scala（2.11.0），这两个的安装过程比较简单就不介绍了，安装后配置环境变量并运行 *Spark-shell* 进行简单的测试。这里重点讲的是环境搭建好后如何在Intellij 平台上高效开发。

<!--more -->

首先打开 Intellij 后新建 maven 项目，这里要注意，需要 **Add Archetype**，这里要添加maven-scala-plugin，Intellij 默认的总是出各种问题。这个信息可以在 maven repository 中查看到。

![pic01](http://odbihfqll.bkt.clouddn.com/image/spark-01/pic01.png)



```xml
<dependency>
    <groupId>net.alchim31.maven</groupId>
    <artifactId>scala-archetype-simple</artifactId>
    <version>1.6</version>
</dependency>

```

之后就是填写项目名字，和选择 maven 仓库，这里要注意的是最好修改成自己下的 maven，并且添加镜像，这里我添加的是阿里云镜像，这个可以参考之前的文章。默认生成的POM 文需要修改一些信息，添加一些依赖，同时更改 compiler 版本。

最后 POM 文件如图：

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.zoro</groupId>
  <artifactId>Spark</artifactId>
  <version>1.0-SNAPSHOT</version>
  <name>${project.artifactId}</name>
  <description>My wonderfull scala app</description>
  <inceptionYear>2015</inceptionYear>
  <licenses>
    <license>
      <name>My License</name>
      <url>http://....</url>
      <distribution>repo</distribution>
    </license>
  </licenses>

  <properties>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
    <encoding>UTF-8</encoding>
    <scala.version>2.11.5</scala.version>
    <scala.compat.version>2.11</scala.compat.version>
  </properties>

  <dependencies>
    <dependency>
      <groupId>org.scala-lang</groupId>
      <artifactId>scala-library</artifactId>
      <version>${scala.version}</version>
    </dependency>

    <!-- Test -->
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>org.specs2</groupId>
      <artifactId>specs2-core_${scala.compat.version}</artifactId>
      <version>3.7.2</version>
      <scope>test</scope>
    </dependency>

    <dependency>
      <groupId>org.apache.spark</groupId>
      <artifactId>spark-core_2.11</artifactId>
      <version>2.1.1</version>
    </dependency>

    <dependency>
      <groupId>org.specs2</groupId>
      <artifactId>specs2-junit_${scala.compat.version}</artifactId>
      <version>3.7.2</version>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>org.scalatest</groupId>
      <artifactId>scalatest_${scala.compat.version}</artifactId>
      <version>2.2.4</version>
      <scope>test</scope>
    </dependency>
  </dependencies>

  <build>
    <sourceDirectory>src/main/scala</sourceDirectory>
    <testSourceDirectory>src/test/scala</testSourceDirectory>
    <plugins>
      <plugin>
        <!-- see http://davidb.github.com/scala-maven-plugin -->
        <groupId>net.alchim31.maven</groupId>
        <artifactId>scala-maven-plugin</artifactId>
        <version>3.2.0</version>
        <executions>
          <execution>
            <goals>
              <goal>compile</goal>
              <goal>testCompile</goal>
            </goals>
            <configuration>
              <args>
                <!--<arg>-make:transitive</arg>-->
                <arg>-dependencyfile</arg>
                <arg>${project.build.directory}/.scala_dependencies</arg>
              </args>
            </configuration>
          </execution>
        </executions>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-surefire-plugin</artifactId>
        <version>2.18.1</version>
        <configuration>
          <useFile>false</useFile>
          <disableXmlReport>true</disableXmlReport>
          <!-- If you have classpath issue like NoDefClassError,... -->
          <!-- useManifestOnlyJar>false</useManifestOnlyJar -->
          <includes>
            <include>**/*Test.*</include>
            <include>**/*Suite.*</include>
          </includes>
        </configuration>
      </plugin>
    </plugins>
  </build>
</project>

```

等待一段时间下载依赖包，下载好后，整个目录结构如图，运行下默认示例。

![pic02](http://odbihfqll.bkt.clouddn.com/image/spark-01/pic04.png)

基本框架搭好后，首先用 **Scala**写一个词频统计的经典实例，代码如下:

```scala
package com.zoro

import org.apache.spark.{SparkConf, SparkContext}
/**
 * @Feng
 */
object SparkCount {  
  def foo(x : Array[String]) = x.foldLeft("")((a,b) => a + b)  
  def main(args : Array[String]) {
    println( "Hello World!" )
    println("concat arguments = " + foo(args))
    //val conf = new SparkConf().setAppName("App").setMaster("local[2]").set("spark.executor.memory","1g")
    val conf = new SparkConf().setAppName("App")
    val sc = new SparkContext(conf)
    val input = sc.textFile(args(0))
    val words = input.flatMap(line => line.split(" "))
    val counts = words.map(word => (word,1)).reduceByKey{case(x,y) => x+y}
    counts.saveAsTextFile(args(1))
  }
  com.zoro.SparkCount
}
```

然后我们尝试单机运行，也就是不提交任务到集群。将上面的代码注释行去注释，同时将下一行注释掉（上面的代码主要用于后面测试将任务提交给伪分布式集群）。这里在Main 下创建 input 文件夹，并添加一个示例文件，内容随意。

然后编辑运行参数，添加输入文件路径和输出文件路径。

![](http://odbihfqll.bkt.clouddn.com/image/spark-01/pic06.png)

之后就可以直接运行程序了，可以查看 output 文件夹，符合预期。

最后一步也是需要理解的一步，就是将将项目打成 jar 包，同时提交到 Spark。这里我是提交到了伪分布式集群上。

首先进行打包设置，进入项目组织，点击 Artifacts，添加 jar，然后选择 from modules with dependency,填写 Main class

![](http://odbihfqll.bkt.clouddn.com/image/spark-01/pic08.png)

点击 OK 后会进入下图，这是依赖配置，因为是提交给 Spark，很多依赖都是有的，所以除了最后一个 compile out 都可以去掉。

![](http://odbihfqll.bkt.clouddn.com/image/spark-01/pic09.png)

去掉后如图所示:

![](http://odbihfqll.bkt.clouddn.com/image/spark-01/pic10.png)



最后点击 build 中的 build artifacts，会生成一个 out 目录，里面有所需的 jar 包。

jar 包生成后就需要提交了，提交之前打开 spark，在 spark 根目录下的 sbin 目录里运行 start-all指令，运行 jps，会发现有 master 和 worker 进程。

然后在 Intellij 里进入终端，进入 jar 包所在的目录,同时将上面的程序复原。

然后就是执行提交命令了，这里由几项设置需要了解，建议参考[Spark 任务提交](http://uohzoaix.github.io/studies/2014/09/17/submitApplication/)

在 jar 包所在目录放置 input 目录，然后我执行的命命令是：

```shell
spark-submit --class com.zoro.SparkCount --master spark://fengfandeMacBook-Pro.local:7077 sparkDemo05.jar input output

```

得到预期结果，整个流程结束。