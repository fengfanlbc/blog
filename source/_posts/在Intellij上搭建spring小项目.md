---
title: 在IntelliJ上搭建spring小项目
date: 2017-01-10 10:40:11
tags: JavaWeb

---



通过学校邮箱进行注册可以免费下载Intellij， 功能多而全，确实十分好用，它和Eclipse就好像是大教堂和集市一样， 它们有各自的优缺点， 但我觉的从开发的快捷性上来讲， intellij还是更加好用的（毕竟是要花钱的东西）。但对于初次上手Intellij的童鞋来说确实有很多蛋疼的地方，所以在这里通过一个完整的示例来详细说明一下，其中最主要的是解决maven下载慢的问题。

<!-- more -->

首先新建一个工程，如图所示，选择Maven，勾选create from archetype，并选择如图所示的archetype再点击next，这里表示创建具有webapp项目骨架的Maven项目

![pic1](http://odbihfqll.bkt.clouddn.com/IntelliJ_1.png)

然后填写项目的GroupId和ArtifactId（随意）点击next：

![pic2](http://odbihfqll.bkt.clouddn.com/IntelliJ_2.png)

然后是maven的设置，这一步要特别注意，Intellij默认使用的是自带的maven，最好选择自己下载的maven，我这里是选择了我自己之前下好的maven，然后我们要去下载一个文件：**archetype-catalog.xml**，因为Intellij总是会到网上下载该文件，虽然该文件很小，但就是下不好，往往会在Maven命令执行到 *Generating Project in Batch mode*卡住，一卡就是很久很久很久（建议不要试验。。。），所以我们可以在浏览器直接下载该文件：*http://repo.maven.apache.org/maven2/archetype-catalog.xml*（直接全选粘贴到你新建的文本里，保存为archetype-catalog.xml ）然后将这个文件存至你的仓库目录下的 *repository\org\apache\maven\archetype\archetype-catalog\2.4*里面，再在现在这个窗口下点击‘+’号添加一个参数如图所示，它表示告诉Maven不要从远程服务器获取catalog，而是从本地获取，这样就不会出现项目生成受到阻塞的问题了。

![pic3](http://odbihfqll.bkt.clouddn.com/IntelliJ_3.png)

![pic4](http://odbihfqll.bkt.clouddn.com/IntelliJ_4.png)
讲到慢的问题，在后面我们写项目依赖的时候，从Apache远程仓库下载jar包也是很恶心的，所以我这里推荐使用阿里提供的镜像文件，下载速度飞起，我曾经1个多小时发呆等着maven下载Apache中央仓库的jar包。。。。所以一定要改啊。。修改conf根目录下的setting.xml文件即可，找到**mirrors**标签，添加如下子标签保存：
```command
 <mirror>  
	<id>alimaven</id>  
	<name>aliyun maven</name>  
    <url>http://maven.aliyun.com/nexus/content/groups/public/</url>  
</mirror>  
```
然后给项目起名字如图，随意，最后finish，项目就基本建好了。

![pic5](http://odbihfqll.bkt.clouddn.com/IntelliJ_5.png)

首先看下目录结构，这里由于我之前就已经搭建好了，所以会多出一些东西，其中java目录和test目录都是后来才建的，刚开始是没有的，分别是存放项目的java源代码和相应的项目测试代码，稍后我会讲述如何创建。External Libraries会存放我们在pom.xml里写的所需的依赖包

![pic6](http://odbihfqll.bkt.clouddn.com/IntelliJ_6.png)

我们点击如图所示的按钮，查看项目结构，然后新建一个java目录，再点击source按钮将其标注为source目录，同理创建一个test目录标注为Test表示测试目录，点击应用，这样一个完整的目录结构就有了

![pic7](http://odbihfqll.bkt.clouddn.com/IntelliJ_7.png)

![pic8](http://odbihfqll.bkt.clouddn.com/IntelliJ_8.png)

然后我们开始写pom.xml文件，添加依赖，如下是我写好的文件（项目比较小，有些属性没有用到），其中**spring-context**是spring的核心包，它会自动导入其它的依赖包：**aop,beans,core,expression,aopalliance,commons-logging**，这里所需要演示的小项目有这些包就够了。

```command
 <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.zoro</groupId>
  <artifactId>springmvc6</artifactId>
  <packaging>war</packaging>
  <version>1.0-SNAPSHOT</version>
  <name>springmvc6 Maven Webapp</name>
  <url>http://maven.apache.org</url>

  <properties>
    <!--Generic properties-->
    <java.version>1.7</java.version>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
    <!--web-->
    <jsp.version>2.2</jsp.version>
    <jstl.version>1.2</jstl.version>
    <servlet.version>3.1.0</servlet.version>
    <!--spring-->
    <spring-framework.version>4.1.5.RELEASE</spring-framework.version>
    <!--Logging-->
    <logback.version>1.0.13</logback.version>
    <slf4j.version>1.7.5</slf4j.version>
  </properties>

  <dependencies>
    <dependency>
      <groupId>javax</groupId>
      <artifactId>javaee-web-api</artifactId>
      <version>7.0</version>
      <scope>provided</scope>
    </dependency>

    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.10</version>
      <scope>test</scope>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>${spring-framework.version}</version>
    </dependency>
  </dependencies>
  <build>
    <finalName>springmvc6</finalName>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <configuration>
          <source>1.6</source>
          <target>1.6</target>
        </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```
可以点击下载[项目源码][1]，最好自己敲一遍，这里要说明下，pom.xml里junit依赖的**scope**是test，这就意味着在java目录下的程序是无法直接使用单元测试的，这里我是建了一个测试目录test实现单元测试的，如果嫌麻烦可以直接把scope属性改为compile，这样就可以直接使用了。这里其实只是一个spring的小项目，并不涉及web，但是为了以后的开发建成了web项目，不影响测试和学习。


[1]: http://pan.baidu.com/s/1o899Nmi