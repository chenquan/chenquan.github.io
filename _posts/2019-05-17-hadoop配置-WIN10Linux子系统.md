---
layout: post
title: hadoop配置-WIN10Linux子系统
categories: [Hadoop,WIN]
description: hadoop配置-WIN10Linux子系统
---

hadoop专属用户创建

```shell
$ sudo useradd -m hadoop -s /bin/bash
```

设置密码：

```shell
$ sudo passwd hadoop
```

需要输入两次密码

```shell
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully
```

提升`hadoop`用户的权限为管理员（虽然增加了管理员权限，但后面有些地方还是需要进入root用户进行操作）

```shell
$ sudo adduser hadoop sudo
```

切换到`hadoop`用户

```shell
$ su hadoop
```

执行之后

```shell
......
Get:28 http://archive.ubuntu.com/ubuntu bionic-backports/universe Translation-en [1604 B]
Fetched 11.9 MB in 5min 13s (37.9 kB/s)
Reading package lists... Done
```

安装SSH并配置SSH无密码登录
网上是说Ubuntu已经自带了SSH client，还需要安装SSH server

```shell
$ sudo apt-get install openssh-server
```
手动启动`sshd`,重启WSL不会启动`sshd`:

```shell
$ sudo service ssh restart
```
得到
```
* Restarting OpenBSD Secure Shell server sshd
```
查看是否重启成功

```shell
$ sudo service ssh status
```

配置`ssh`开启自启

```shell
$ cd /etc/profile.d/
```

创建`ssh.sh`文件

```shell
$ sudo vim ssh.sh
```
写入:

```shell
sudo service ssh restart
```

然后使用`:wq`保存文件（下同）
这样就可以开机自动启动了

SSH设置和密钥生成
SSH设置需要在集群上执行不同的操作，如启动，停止和分布式守护shell操作。进行身份验证不同的Hadoop用户，需要一种用于Hadoop的用户提供的公钥/私钥对，并用不同的用户共享。

```shell
$ su hadoop
```

```shell
$ ssh-keygen -t rsa
$ cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
$ chmod 0600 ~/.ssh/authorized_keys
```

验证ssh

```shell
$ ssh localhost
```

接下来可以再次尝试ssh localhost，如若无需再次输入密码，既成功。

JDK安装

```shell
$ sudo apt-get install openjdk-8-jdk
```

设置JDK环境变量

```shell
$ cd /etc/profile.d/
```

新建`java.sh`文件（需要输入当前密码）

```shell
$ sudo vim java.sh
```

使`source`配置文件生效

```
$ source java.sh
```

输出`JAVA_HOME`路径，查看配置是否生效

```shell
$ echo $JAVA_HOME
```

输出以下内容说明配置已经生效

```
$ /usr/lib/jvm/java-8-openjdk-amd64/
```

查看是否java安装成功

```shell
$ java -version
```

显示以下信息说明安装成功

```
openjdk version "1.8.0_212"
OpenJDK Runtime Environment (build 1.8.0_212-8u212-b03-0ubuntu1.18.04.1-b03)
OpenJDK 64-Bit Server VM (build 25.212-b03, mixed mode)
```

Hadoop
最新版:[http://hadoop.apache.org/releases.html](http://hadoop.apache.org/releases.html)

```shell
$ cd /usr/local/
 
$ wget https://www.apache.org/dyn/closer.cgi/hadoop/common/hadoop-3.1.2/hadoop-3.1.2.tar.gz
```
 
```shell
$ tar -vzxf hadoop-3.1.2.tar.gz # 解压

$ mv hadoop-3.1.0  hadoop # 重命名
 
$ cd hadoop
 
$ ./bin/hadoop version
```

你将会看到类似内容：

```
Hadoop 3.1.2
Source code repository https://github.com/apache/hadoop.git -r 1019dde65bcf12e05ef48ac71e84550d589e5d9a
Compiled by sunilg on 2019-01-29T01:39Z
Compiled with protoc 2.5.0
From source with checksum 64b8bdd4ca6e77cce75a93eb09ab2a9
This command was run using /usr/local/hadoop/share/hadoop/common/hadoop-common-3.1.2.jar
```

```shell
$ cd /etc/profile.d/
$ sudo vim hadoop.sh
```

输入以下内容:

```shell
export HADOOP_HOME=/usr/local/hadoop
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export YARN_HOME=$HADOOP_HOME
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin
export HADOOP_INSTALL=$HADOOP_HOME
```

应用所有更改到当前正在运行的系统

```shell
$ source /etc/profile
```


配置core-site.xml

core-site.xml文件中包含，如：用于Hadoop实例的端口号，分配给文件系统，存储器限制用于存储数据存储器和读/写缓冲器的大小的信息。

```shell
$ sudo vim /usr/local/hadoop/etc/hadoop/core-site.xml
```

写入以下内容：

```xml
<configuration>
    <property>
        <name>hadoop.tmp.dir</name>
        <value>file:/usr/local/hadoop/tmp</value>
        <description>Abase for other temporary directories.</description>
    </property>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://localhost:9000</value>
    </property>
</configuration>
```

配置hadoop-env.sh
需要添加JAVA_HOME的路径
```shell
$ echo $JAVA_HOME
```

```shell
$ sudo vim /usr/local/hadoop/etc/hadoop/hadoop-env.sh
```

找到被注释掉的JAVA_HOME,填写上面输出的JAVA_HOME地址

```shell
export JAVA_HOME= /usr/lib/jvm/java-8-openjdk-amd64/
```
配置hdfs-site.xml

hdfs-site.xml文件中包含，如：复制数据的值，NameNode的路径，本地文件系统，要存储Hadoop基础架构的Datanode路径的信息。

```shell
$ sudo vim /usr/local/hadoop/etc/hadoop/hdfs-site.xml
```
写入以下内容：
```xml
<configuration>
    <property>
        <name>dfs.replication</name>
        <value>1</value>
    </property>
    <property>
        <name>dfs.namenode.name.dir</name>
        <value>file:/usr/local/hadoop/tmp/dfs/name</value>
    </property>
    <property>
        <name>dfs.datanode.data.dir</name>
        <value>file:/usr/local/hadoop/tmp/dfs/data</value>
    </property>
</configuration>
```
上面的文件，所有的属性值是用户定义的，可以根据自己的Hadoop的基础架构进行更改。

配置完成后，执行NameNode的格式化

```shell
$ /usr/local/hadoop/bin/hdfs namenode -format
```

出现类似输出，说明名称节点设置成功

```shell
2019-05-17 16:34:23,646 INFO namenode.FSImageFormatProtobuf: Image file /usr/local/hadoop/tmp/dfs/name/current/fsimage.ckpt_0000000000000000000 of size 393 bytes saved in 0 seconds .
2019-05-17 16:34:23,685 INFO namenode.NNStorageRetentionManager: Going to retain 1 images with txid >= 0
2019-05-17 16:34:23,700 INFO namenode.NameNode: SHUTDOWN_MSG:
/************************************************************
SHUTDOWN_MSG: Shutting down NameNode at ZERO.localdomain/127.0.1.1
************************************************************/
```

下面的命令用来启动DFS。执行这个命令将启动Hadoop文件系统。

这里不能再root下运行，切换到 hadoop用户下运行

```shell 
$ sudo /usr/local/hadoop/sbin/start-dfs.sh
```

出现权限问题

```shell
Starting namenodes on [localhost]
ERROR: Attempting to operate on hdfs namenode as root
ERROR: but there is no HDFS_NAMENODE_USER defined. Aborting operation.
Starting datanodes
ERROR: Attempting to operate on hdfs datanode as root
ERROR: but there is no HDFS_DATANODE_USER defined. Aborting operation.
Starting secondary namenodes [account.jetbrains.com]
ERROR: Attempting to operate on hdfs secondarynamenode as root
ERROR: but there is no HDFS_SECONDARYNAMENODE_USER defined. Aborting operation.
```
运行 `sudo chown -R hadoop:root /usr/local/hadoop`命令，把hadoop目录所有用户改到hadoop上。

再次运行
```shell
# 注意：不能加sudo
$ /usr/local/hadoop/sbin/start-dfs.sh
```

出现类似输出，说明安装成功：

```shell
Starting namenodes on [localhost]
localhost: namenode is running as process 16525.  Stop it first.
Starting datanodes
Starting secondary namenodes [account.jetbrains.com]
account.jetbrains.com: secondarynamenode is running as process 16988.  Stop it first.
2019-05-17 17:04:20,232 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
```

至此，完成配置，可通过jps查看是否启动成功。

```shell
$ jps
```

结果：

```shell
16988 SecondaryNameNode
16525 NameNode
18269 Jps
```

访问Hadoop上的浏览器
访问Hadoop的默认端口号为9870。使用以下网址，以获取Hadoop服务在浏览器中。

http://localhost:9870

![](/img/picture/hadoop-1.png)


如果发现无法访问,手动修改hdfs-site.xml，
修改hdfs-site.xml

```shell
$ sudo vim /usr/local/hadoop/etc/hadoop/hdfs-site.xml
```
 添加如下：

```xml
<property>
  <name>dfs.http.address</name>
  <value>127.0.0.1:9870</value>
</property>

```




```shell
$ sudo vi /usr/local/hadoop/etc/hadoop/yarn-site.xml
```
修改如下内容：

```shell
<configuration>
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>
</configuration>
```

mapred-site.xml

此文件用于指定MapReduce框架以使用。默认情况下Hadoop包含yarn-site.xml模板。首先，它需要从mapred-site.xml复制模板到mapred-site.xml文件，使用下面的命令来。

```shell
$ sudo vim /usr/local/hadoop/etc/hadoop/mapred-site.xml
```
提供一个默认的`mapred-site.xml`配置文件:[mapred-default.xml](/file/mapred-default.xml)
将默认的文件复制到`mapred-site.xml`中，并添加`<configuration> `和` </configuration>`标签之间添加以下属性

```xml
<configuration> <!--复制时不包含configuration标签-->
<property>
 <name>mapreduce.framework.name</name>
 <value>yarn</value>
 </property>
</configuration>
```
获取启动，执行此命令将启动yarn守护进程。
```shell
$ /usr/local/hadoop/sbin/start-yarn.sh
```

[http://localhost:8088/](http://localhost:8088/)

![](/img/picture/hadoop-2.png)

之后需要要启动Hadoop直接使用以下命令：
```shell
$ /usr/local/hadoop/sbin/start-all.sh
```