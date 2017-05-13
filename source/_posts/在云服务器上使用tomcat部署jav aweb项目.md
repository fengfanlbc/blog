---
title: 在云服务器上使用tomcat部署java web项目
date: 2016-12-24 20:36:39
tags: JavaWeb
---



我们在本地调试好web项目后，往往需要把项目放到云服务器上以供外网访问并测试，同时通过**git**版本控制器管理项目，使得项目开发更加便捷。以下是部署的详细流程以供参考。

<!-- more -->

初次主要步骤包括：

>* 配置java运行环境
>* 安装MySQL并设置远程登入
>* 安装Tomcat，并配置环境
>* 将web项目部署到Tomcat

我用的是阿里云服务器进行部署的，首先通过**ssh**登入，这是登入后的页面

![pic1](http://odbihfqll.bkt.clouddn.com/Image.png)

## 配置java运行环境

首先安装java，这里我使用了较为简便的安装：
```command
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
sudo apt-get install oracle-java8-installer
```

如果add-apt-repository 报错，则应该是缺少python-software-properties ，运行如下指令：
```command
apt-get install python-software-properties
apt-get install software-properties-common
```
然后再运行刚刚的安装java的指令。运行sudo apt-get install oracle-java8-installer指令后会安装一个installer，它会从Oracle官网下载java二进制文件，然后安装到ubuntu系统，这里需要同意Oracle使用条款：

![pic2](http://odbihfqll.bkt.clouddn.com/Image2.png)

安装完成后，使用如下指令查看java版本和java编译器版本：
```command
java -version
javac -version
```
运行如下指令设置java环境变量：
```command
sudo apt-get install oracle-java8-set-default
```
指令执行完毕后，会在/etc/profile.d目录下生成两个新的文件：jdk.csh和jdk.sh。这两个文件是shell脚本，里面是设置java环境变量的命令（所以不用去认为修改配置文件了），这里一共会设置5个环境变量，我们可以用cat命令输出下：
```command
cat /etc/profile.d/jdk.sh
export J2SDKDIR=/usr/lib/jvm/java-8-oracle
export J2REDIR=/usr/lib/jvm/java-8-oracle/jre
export PATH=$PATH:/usr/lib/jvm/java-8-oracle/bin:/usr/lib/jvm/java-8-oracle/db/bin:/usr/lib/jvm/java-8-oracle/jre/bin
export JAVA_HOME=/usr/lib/jvm/java-8-oracle
export DERBY_HOME=/usr/lib/jvm/java-8-oracle/db
```
然后使用source命令运行/etc/profile：
```command
source /etc/profile
```
然后检查下是否设置成功：
```command
#输出java环境变量
echo $JAVA_HOME
```
![pic3](http://odbihfqll.bkt.clouddn.com/Image3.png)

这样java环境就配置好了
## MySQL安装和配置
项目运行必然要涉及数据库，我想把数据库也部署在该台服务器上，但实际是可以分开部署的。首先我们安装mysql。
直接运行指令：
```command
sudo apt-get install mysql-server mysql-client
```
在安装过程中会让你设置root用户密码（这里是管理mysql的用户非Linux用户）安装好后，使用`mysql -uroot -p`指令进行Mysql登入测试：

![pic4](http://odbihfqll.bkt.clouddn.com/Image11.png)

然后就是根据项目的数据字典建表啦~，这里要注意，刚刚安装的mysql默认是不允许远程访问的，所以进行进一步设置远程访问：
首先，在目录/etc/mysql目录下找到my.cnf文件，使用vim进行编辑

![pic5](http://odbihfqll.bkt.clouddn.com/Image12.png)

如图将bind-address = 127.0.0.1给注释掉实现任意IP均可以访问。
然后用root账户（mysql）登入mysql数据库，然后授权用户进行远程连接：
```command
grant all on *.* to username@'%' identified by 'password';
```
我这里解释下这个语句：
```command
"*.*"：第一个*代表数据库名；第二个*代表表名。这里的意思是所有数据库里的所有表都授权给用户。 
root：授予root账号。   
“%”：表示授权的用户IP可以指定，这里代表任意的IP地址都能访问MySQL数据库。   
“password”：分配账号对应的密码，这里密码自己替换成你的mysql root帐号密码。
```
然后刷新一下，执行：
```
flush privileges;
```
最后重启:
```command
MySQL: sudo /etc/init.d/mysql restart
```
这样应该就可以远程登入了，这里推荐使用**workbench**工具远程登入并进行数据库操作，方便快捷~（最后还要设置一下编码，一般设为utf8，这个自行google~）

## Tomcat配置
我这里安装的是tomcat7，建议在另一台电脑上下好安装包后，使用ftp工具发送给服务器。

![pic6](http://odbihfqll.bkt.clouddn.com/Image4.png)

然后进入压缩包所在目录进行解压:

![pic7](http://odbihfqll.bkt.clouddn.com/Image5.png)

把包解压到自己方便使用的位置，不管解压在哪都可以直接使用的，不用担心~然后进入加压后的目录，以下是目录里的所有内容：

![pic8](http://odbihfqll.bkt.clouddn.com/Image6.png)

如果我们想要修改tomcat服务器启动端口，可以打开conf目录下的server.xml文件（自行google，很简单）
然后我们进行tomcat启动和关闭测试，进入bin目录，执行./start.sh启动tomcat服务器：

![pic9](http://odbihfqll.bkt.clouddn.com/Image7.png)

然后我们在浏览器访问http://serverip:8080/  就可以访问熟悉的tomcat默认主页了。关闭时运行 ./shutdown.sh即可。

![pic10](http://odbihfqll.bkt.clouddn.com/Image8.png)

我们可以进如tomcat服务器的logs目录，打开catalina.out进行查看，这里面记录了tomcat的日志信息，出错后可以看看。

## 项目部署
将写好的java web项目通过eclipse导出成war包，再将war包通过ftp工具导出到tomcat目录下的webapp目录下:

![pic11](http://odbihfqll.bkt.clouddn.com/Image9.png)

tomcat会自动解压webapp下的war包，然后我们开启tomcat，tomcat会自动解压webapp并运行，如图成功访问登入页面：

![pic12](http://odbihfqll.bkt.clouddn.com/Image10.png)

这里要注意，当我同时导入两个war包（也就是一个tomcat下导入两个项目）的情况下会报端口占用错误，此问题还有待解决，所以建议，一次就导入一个包。当想删除项目时，也是比较麻烦的，**注意**，不仅仅要删除webapp下的项目，还要删除work目录下的所有文件，再重新运行。

## 总结
按照这几个步骤搭建好后，基本上就可以完成整个的项目部署工作了，但是为了之后的开发调试方便，我们需要使用git进行版本控制，我会在以后进行说明。






