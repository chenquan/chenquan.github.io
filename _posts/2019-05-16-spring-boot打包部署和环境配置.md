---
layout: post
title: Spring Boot打包部署和环境配置
categories: [Spring Boot]
description: Spring Boot打包部署和环境配置

---



[TOC]

## Java安装



执行命令`yum -y list java*`查看可安装java版本。

```shell
Failed to set locale, defaulting to C
Loaded plugins: fastestmirror
base                                                                                             | 3.6 kB  00:00:00
extras                                                                                           | 3.4 kB  00:00:00
updates                                                                                          | 3.4 kB  00:00:00
(1/4): base/7/x86_64/group_gz                                                                    | 166 kB  00:00:00
(2/4): extras/7/x86_64/primary_db                                                                | 201 kB  00:00:00
(3/4): updates/7/x86_64/primary_db                                                               | 5.0 MB  00:00:00
(4/4): base/7/x86_64/primary_db                                                                  | 6.0 MB  00:00:00
Determining fastest mirrors
 * base: ftpmirror.your.org
 * extras: mirror.fileplanet.com
 * updates: centos.mirror.constant.com
Available Packages
java-1.6.0-openjdk.x86_64                                         1:1.6.0.41-1.13.13.1.el7_3                     base
java-1.6.0-openjdk-demo.x86_64                                    1:1.6.0.41-1.13.13.1.el7_3                     base
java-1.6.0-openjdk-devel.x86_64                                   1:1.6.0.41-1.13.13.1.el7_3                     base
java-1.6.0-openjdk-javadoc.x86_64                                 1:1.6.0.41-1.13.13.1.el7_3                     base
java-1.6.0-openjdk-src.x86_64                                     1:1.6.0.41-1.13.13.1.el7_3                     base
java-1.7.0-openjdk.x86_64                                         1:1.7.0.221-2.6.18.0.el7_6                     updates
java-1.7.0-openjdk-accessibility.x86_64                           1:1.7.0.221-2.6.18.0.el7_6                     updates
java-1.7.0-openjdk-demo.x86_64                                    1:1.7.0.221-2.6.18.0.el7_6                     updates
java-1.7.0-openjdk-devel.x86_64                                   1:1.7.0.221-2.6.18.0.el7_6                     updates
java-1.7.0-openjdk-headless.x86_64                                1:1.7.0.221-2.6.18.0.el7_6                     updates
java-1.7.0-openjdk-javadoc.noarch                                 1:1.7.0.221-2.6.18.0.el7_6                     updates
java-1.7.0-openjdk-src.x86_64                                     1:1.7.0.221-2.6.18.0.el7_6                     updates
java-1.8.0-openjdk.i686                                           1:1.8.0.212.b04-0.el7_6                        updates
java-1.8.0-openjdk.x86_64                                         1:1.8.0.212.b04-0.el7_6                        updates
java-1.8.0-openjdk-accessibility.i686                             1:1.8.0.212.b04-0.el7_6                        updates
java-1.8.0-openjdk-accessibility.x86_64                           1:1.8.0.212.b04-0.el7_6                        updates
java-1.8.0-openjdk-accessibility-debug.i686                       1:1.8.0.212.b04-0.el7_6                        updates
java-1.8.0-openjdk-accessibility-debug.x86_64                     1:1.8.0.212.b04-0.el7_6                        updates
java-1.8.0-openjdk-debug.i686                                     1:1.8.0.212.b04-0.el7_6                        updates
java-1.8.0-openjdk-debug.x86_64                                   1:1.8.0.212.b04-0.el7_6                        updates
java-1.8.0-openjdk-demo.i686                                      1:1.8.0.212.b04-0.el7_6                        updates
java-1.8.0-openjdk-demo.x86_64                                    1:1.8.0.212.b04-0.el7_6                        updates
java-1.8.0-openjdk-demo-debug.i686                                1:1.8.0.212.b04-0.el7_6                        updates
java-1.8.0-openjdk-demo-debug.x86_64                              1:1.8.0.212.b04-0.el7_6                        updates
java-1.8.0-openjdk-devel.i686                                     1:1.8.0.212.b04-0.el7_6                        updates
java-1.8.0-openjdk-devel.x86_64                                   1:1.8.0.212.b04-0.el7_6                        updates
java-1.8.0-openjdk-devel-debug.i686                               1:1.8.0.212.b04-0.el7_6                        updates
java-1.8.0-openjdk-devel-debug.x86_64                             1:1.8.0.212.b04-0.el7_6                        updates
java-1.8.0-openjdk-headless.i686                                  1:1.8.0.212.b04-0.el7_6                        updates
java-1.8.0-openjdk-headless.x86_64                                1:1.8.0.212.b04-0.el7_6                        updates
java-1.8.0-openjdk-headless-debug.i686                            1:1.8.0.212.b04-0.el7_6                        updates
java-1.8.0-openjdk-headless-debug.x86_64                          1:1.8.0.212.b04-0.el7_6                        updates
java-1.8.0-openjdk-javadoc.noarch                                 1:1.8.0.212.b04-0.el7_6                        updates
java-1.8.0-openjdk-javadoc-debug.noarch                           1:1.8.0.212.b04-0.el7_6                        updates
java-1.8.0-openjdk-javadoc-zip.noarch                             1:1.8.0.212.b04-0.el7_6                        updates
java-1.8.0-openjdk-javadoc-zip-debug.noarch                       1:1.8.0.212.b04-0.el7_6                        updates
java-1.8.0-openjdk-src.i686                                       1:1.8.0.212.b04-0.el7_6                        updates
java-1.8.0-openjdk-src.x86_64                                     1:1.8.0.212.b04-0.el7_6                        updates
java-1.8.0-openjdk-src-debug.i686                                 1:1.8.0.212.b04-0.el7_6                        updates
java-1.8.0-openjdk-src-debug.x86_64                               1:1.8.0.212.b04-0.el7_6                        updates
java-11-openjdk.i686                                              1:11.0.3.7-0.el7_6                             updates
java-11-openjdk.x86_64                                            1:11.0.3.7-0.el7_6                             updates
java-11-openjdk-debug.i686                                        1:11.0.3.7-0.el7_6                             updates
java-11-openjdk-debug.x86_64                                      1:11.0.3.7-0.el7_6                             updates
java-11-openjdk-demo.i686                                         1:11.0.3.7-0.el7_6                             updates
java-11-openjdk-demo.x86_64                                       1:11.0.3.7-0.el7_6                             updates
java-11-openjdk-demo-debug.i686                                   1:11.0.3.7-0.el7_6                             updates
java-11-openjdk-demo-debug.x86_64                                 1:11.0.3.7-0.el7_6                             updates
java-11-openjdk-devel.i686                                        1:11.0.3.7-0.el7_6                             updates
java-11-openjdk-devel.x86_64                                      1:11.0.3.7-0.el7_6                             updates
java-11-openjdk-devel-debug.i686                                  1:11.0.3.7-0.el7_6                             updates
java-11-openjdk-devel-debug.x86_64                                1:11.0.3.7-0.el7_6                             updates
java-11-openjdk-headless.i686                                     1:11.0.3.7-0.el7_6                             updates
java-11-openjdk-headless.x86_64                                   1:11.0.3.7-0.el7_6                             updates
java-11-openjdk-headless-debug.i686                               1:11.0.3.7-0.el7_6                             updates
java-11-openjdk-headless-debug.x86_64                             1:11.0.3.7-0.el7_6                             updates
java-11-openjdk-javadoc.i686                                      1:11.0.3.7-0.el7_6                             updates
java-11-openjdk-javadoc.x86_64                                    1:11.0.3.7-0.el7_6                             updates
java-11-openjdk-javadoc-debug.i686                                1:11.0.3.7-0.el7_6                             updates
java-11-openjdk-javadoc-debug.x86_64                              1:11.0.3.7-0.el7_6                             updates
java-11-openjdk-javadoc-zip.i686                                  1:11.0.3.7-0.el7_6                             updates
java-11-openjdk-javadoc-zip.x86_64                                1:11.0.3.7-0.el7_6                             updates
java-11-openjdk-javadoc-zip-debug.i686                            1:11.0.3.7-0.el7_6                             updates
java-11-openjdk-javadoc-zip-debug.x86_64                          1:11.0.3.7-0.el7_6                             updates
java-11-openjdk-jmods.i686                                        1:11.0.3.7-0.el7_6                             updates
java-11-openjdk-jmods.x86_64                                      1:11.0.3.7-0.el7_6                             updates
java-11-openjdk-jmods-debug.i686                                  1:11.0.3.7-0.el7_6                             updates
java-11-openjdk-jmods-debug.x86_64                                1:11.0.3.7-0.el7_6                             updates
java-11-openjdk-src.i686                                          1:11.0.3.7-0.el7_6                             updates
java-11-openjdk-src.x86_64                                        1:11.0.3.7-0.el7_6                             updates
java-11-openjdk-src-debug.i686                                    1:11.0.3.7-0.el7_6                             updates
java-11-openjdk-src-debug.x86_64                                  1:11.0.3.7-0.el7_6                             updates
java-atk-wrapper.i686                                             0.30.4-5.el7                                   base
java-atk-wrapper.x86_64                                           0.30.4-5.el7                                   base
java_cup.noarch                                                   1:0.11a-16.el7                                 base
java_cup-javadoc.noarch                                           1:0.11a-16.el7                                 base
java_cup-manual.noarch                                            1:0.11a-16.el7                                 base
javacc.noarch                                                     5.0-10.el7                                     base
javacc-demo.noarch                                                5.0-10.el7                                     base
javacc-javadoc.noarch                                             5.0-10.el7                                     base
javacc-manual.noarch                                              5.0-10.el7                                     base
javacc-maven-plugin.noarch                                        2.6-17.el7                                     base
javacc-maven-plugin-javadoc.noarch                                2.6-17.el7                                     base
javamail.noarch                                                   1.4.6-8.el7                                    base
javamail-javadoc.noarch                                           1.4.6-8.el7                                    base
javapackages-tools.noarch                                         3.4.1-11.el7                                   base
javassist.noarch                                                  3.16.1-10.el7                                  base
javassist-javadoc.noarch                                          3.16.1-10.el7                                  base
```

选择一个java版本进行安装，这里我们希望安装java1.8，因为我们的机器是64位的，所以选择安装`java-1.8.0-openjdk-devel.x86_64`。

执行命令

```
$ yum install -y java-1.8.0-openjdk-devel.x86_64
```

执行完后会看见控制台刷出很多输出。

```shell
Dependency Updated:
  chkconfig.x86_64 0:1.7.4-1.el7        glib2.x86_64 0:2.56.1-2.el7         nspr.x86_64 0:4.19.0-1.el7_5
  nss.x86_64 0:3.36.0-7.1.el7_6         nss-softokn.x86_64 0:3.36.0-5.el7_5 nss-softokn-freebl.x86_64 0:3.36.0-5.el7_5
  nss-sysinit.x86_64 0:3.36.0-7.1.el7_6 nss-tools.x86_64 0:3.36.0-7.1.el7_6 nss-util.x86_64 0:3.36.0-1.1.el7_6
  ntsysv.x86_64 0:1.7.4-1.el7

Complete!
```

看到上面的提示，就表示已经安装完成了。

检查时候安装成功,查看Java版本`(Java被安装在/usr/lib/jvm目录下面)`

```
java -version
```



## MySql数据库

安装前，我们可以检测系统是否自带安装 MySQL:

```shell
rpm -qa | grep mysql
```

如果你系统有安装，那可以选择进行卸载:

```shell
rpm -e mysql　　// 普通删除模式
rpm -e --nodeps mysql　　// 强力删除模式，如果使用上面命令删除时，提示有依赖的其它文件，则用该命令可以对其进行强力删除
```



**安装 MySQL：**

接下来我们在 Centos7 系统下使用 yum 命令安装 MySQL，需要注意的是 CentOS 7 版本中 MySQL数据库已从默认的程序列表中移除，所以在安装前我们需要先去官网下载 Yum 资源包，下载地址为：[https://dev.mysql.com/downloads/repo/yum/](https://dev.mysql.com/downloads/repo/yum/)

![linux-mysql-yum-install](/img/picture/linux-mysql-yum-install.png)

```
$ wget http://repo.mysql.com/mysql80-community-release-el7-3.noarch.rpm
$ rpm -ivh mysql80-community-release-el7-3.noarch.rpm
$ yum update
$ yum install mysql-server
```

安装完成！

```shell
Installed:
  mysql-community-server.x86_64 0:8.0.16-2.el7

Dependency Installed:
  mysql-community-client.x86_64 0:8.0.16-2.el7                net-tools.x86_64 0:2.0-0.24.20131004git.el7
  numactl-libs.x86_64 0:2.0.9-7.el7

Complete!
```





权限设置：

```shell
$ chown mysql:mysql -R /var/lib/mysql
```

初始化 MySQL：

```shell
$ mysqld --initialize
```

启动 MySQL：

```shell
$ systemctl start mysqld
```

查看 MySQL 运行状态：

```shell
$ systemctl status mysqld
```

### 验证 MySQL 安装

在成功安装 MySQL 后，一些基础表会表初始化，在服务器启动后，你可以通过简单的测试来验证 MySQL 是否工作正常。

使用 mysqladmin 工具来获取服务器状态：

使用 mysqladmin 命令俩检查服务器的版本, 在 linux 上该二进制文件位于 /usr/bin 目录，在 Windows 上该二进制文件位于C:\mysql\bin 。

```shell
$ mysqladmin --version
```

linux上该命令将输出以下结果，该结果基于你的系统信息：

```shell
$ mysqladmin  Ver 8.23 Distrib 5.0.9-0, for redhat-linux-gnu on i386
```

如果以上命令执行后未输出任何信息，说明你的MySql未安装成功。



Mysql安装成功后，默认的root用户密码为空，你可以使用以下命令来创建root用户的密码：

```shell
$ mysqladmin -u root password 'new_password';
```

### 登录错误的处理

在root权限下` mysqladmin -u root password <new_possword>;`以及`mysql`出现

```shell
Access denied for user 'root'@'localhost' (using password: NO)
```

解决办法：

查询初始密码：

```shell
$ cat /var/log/mysqld.log  | grep password
```

出现：

```shell
2019-05-16T04:59:55.675274Z 5 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: BT?lm5c+Q+hw
```

其中`BT?lm5c+Q+hw`就是初始密码

使用初始密码登录

```shell
$ mysql -u root -p 
```

修改密码：

```sql
ALTER USER 'root'@'localhost' IDENTIFIED BY 'new_password';
```



现在你可以修改之后的密码通过以下命令来连接到Mysql服务器：

```shell
$ mysql -u root -p
Enter password:*******
```

### 远程访问

以root权限登录数据库

```shell
$ mysql -u root -p
```

切换到mysql的系统数据库

````mysql
mysql> use mysql;
mysql> update user set host='%' where user='root';
````
查询修改情况
```mysql

mysql> select host,user from user;
```

### 使用授权的方式

赋予任何主机访问数据的权限

```mysql
mysql> FLUSH PRIVILEGES # 刷新权限
mysql> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%'WITH GRANT OPTION;
mysql> FLUSH PRIVILEGES
```


如果想myuser用户使用mypassword密码从任何主机连接到mysql服务器的话。

```mysql
mysql> GRANT ALL PRIVILEGES ON . TO 'myuser'@'%'IDENTIFIED BY 'mypassword' WITH GRANT OPTION;
```

如果你想允许用户`myuser`从ip为`192.168.1.6`的主机连接到mysql服务器，并使用`mypassword`作为密码

```mysql
mysql> GRANT ALL PRIVILEGES ON . TO 'myuser'@'192.168.1.3'IDENTIFIED BY 'mypassword' WITH GRANT OPTION;

```
### mysql服务命令
```shell
#登录mysql
mysql -u username -p

#退出mysql 
quit

#启动mysql
systemctl start mysqld.service

#结束
systemctl stop mysqld.service

#重启
systemctl restart mysqld.service

#开机自启
systemctl enable mysqld.service

#查看mysql版本
select version();
```


## Spring Boot 打包

使用IDEA和maven一键打包生成jar包

![spring-boot-to-jar](/img/picture/spring-boot-to-jar.png)

## Linux部署

使用`nohup`不挂断地运行命令。使用`&`让程序在后台运行

```shell
$ nohup java -jar spring-boot-api-project-seed-1.0.jar &
```

## 停止springboot运行

```shell
$ ps -ef 
```

![kill-process](/img/picture/kill-process.png)

根据运行的目录，找到PID，例如图中的`734`。然后使用

```shell
$ kill -9 734 
```

杀死该进程。