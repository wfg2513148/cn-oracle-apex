---
title: Oracle APEX 系列文章8：如何从 APEX 5.1.4 升级到最新的 APEX 18.1
copyright: true
type: categories
date: 2018-05-29 21:10:18
categories:
- Oracle
- Oracle APEX

tags:
- Oracle
- APEX
- CentOS
- Tomcat
- Nginx
- ORDS

---


![](https://ws1.sinaimg.cn/large/006By2pOgy1frsj7crfv1j30mu0dw75b.jpg)

本文是钢哥的 Oracle APEX 系列文章的第8篇，完整文章目录如下：
- [Oracle APEX 系列文章1：Oracle APEX, 让你秒变全栈开发的黑科技](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-1/)
- [Oracle APEX 系列文章2：在阿里云上打造属于你自己的APEX完整开发环境 (安装 CentOS)](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-2/)
- [Oracle APEX 系列文章3：在阿里云上打造属于你自己的APEX完整开发环境 (安装 Tomcat, Nginx)](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-3/)
- [Oracle APEX 系列文章4：在阿里云上打造属于你自己的APEX完整开发环境 (安装XE, ORDS, APEX)](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-4/)
- [Oracle APEX 系列文章5：在阿里云上打造属于你自己的APEX完整开发环境 (进一步优化)](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-5/)
- [Oracle APEX 系列文章6：Oracle APEX 到底适不适合企业环境？](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-6/)
- [Oracle APEX 系列文章7：Oracle APEX 18.1 新特性](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-7/)
- [Oracle APEX 系列文章8：如何从 APEX 5.1.4 升级到 最新的 APEX 18.1](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-8/)

## 引言
Oracle APEX 18.1 发布已经有几天了，相信很多搞APEX的同学都尝过鲜了，接下来需要面临的问题就是如何从早期的 APEX 版本升级到最新的 APEX 18.1。

> 简单看了一下 APEX 18.1 的文档，并没有提到有现成的升级脚本文件可以跑（虽然安装目录下也有个叫`apxpatch.sql`的鬼）。由于新版本的 APEX 的 DB Schema 已经变成了 `APEX_180100`，猜测跟之前 APEX 4 升到 5 一样，没办法直接升级。
> 以下升级过程是跑在 CentOS 6 上的，数据库用的是 **Oracle Database 11gR2**，APEX 版本是 5.1.4。
> 另外如果不更换数据库，已有的 APEX Application 应用、Workspace 及 Schema 都不需要改，升级后还可以使用，这一点要为 APEX 研发部门点赞！！
> 闲话少说，以下就是涉及到的组件：
* `APEX`：这个自不必说，不管之前用的是 3、4 还是 5 版本的，都要升级到最新的 `18.1`；
* `ORDS`：同步升级到最新的 18.1 版本，并重新配置部署到 Tomcat 8.5 上；
* `JAVA`：ORDS 18.1 要求 JDK/JRE 最低版本 `1.8` 以上；
* `Tomcat`：ORDS 18.1 要求 Tomcat 最低版本 `8.5` 以上；
* `Nignx`：需要重新配置参数（SSL证书、静态文件路径等）；

下面就让钢哥带你开始今天的 APEX 升级（踩坑）之旅！

<!-- more -->

---

## 准备工作
### 上传安装包
首先将升级需要用到的安装包上传到服务器上，比如：`/u02/Media`。
![](https://ws1.sinaimg.cn/large/006By2pOgy1frs13zjztij30t005sdgx.jpg)

停止当前 nginx，tomcat 服务。
```bash
## 停止 nginx 服务
service nginx stop

## 停止 tomcat 服务
service tomcat stop
```

### 升级 JDK / JRE
利用`java -version`查看当前 JDK 版本，从下图可知，当前系统 JDK 版本是 1.7 的，不满足 ORDS 的需要，必须升级 Java 版本。

![](https://ws1.sinaimg.cn/large/006By2pOgy1frs2ohvrgkj30pi03uq3o.jpg)

安装 JDK 1.8
```bash
yum install -y java-1.8.0-openjdk.x86_64 java-1.8.0-openjdk-devel.x86_64
```

安装完 JDK，将环境变量添加到 `~/.bash_profile` 文件中；



```bash
# .bash_profile

# Get the aliases and functions
if [ -f ~/.bashrc ]; then
        . ~/.bashrc
fi

# User specific environment and startup programs
export NLS_LANG=American_America.AL32UTF8
export JAVA_HOME=/u01/java/jdk1.8.0_162
export JRE_HOME=/u01/java/jdk1.8.0_162/jre
export ORACLE_SID=XE

PATH=/bin:/sbin:/usr/bin:$JAVA_HOME/bin:$JRE_HOME/bin:$PATH:$HOME/bin
export PATH

CLASSPATH=$JAVA_HOME/lib/tools.jar:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib:$JRE_HOME/lib:$CLASSPATH
export CLASSPATH

```



手动初始化一下环境变量。
```bash
source ~/.bash_profile

## 再次查看JDK版本
java -version
```

JDK 版本应该已经变成1.8了。
![](https://ws1.sinaimg.cn/large/006By2pOgy1frs5ctbevrj30m404sgmk.jpg)


### 升级 Tomcat
由于我当前用的 tomcat 版本是 7 的，ORDS 18.1 要求 tomcat 8.5 以上版本，所以需要更新一下 tomcat。

下载 Tomcat 8.5
```bash
## 切换到安装包目录
cd /u02/Media

## 下载tomcat 8.5以上版本
wget http://mirrors.shu.edu.cn/apache/tomcat/tomcat-8/v8.5.31/bin/apache-tomcat-8.5.31.zip

## 解压缩安装包
unzip apache-tomcat-8.5.31.zip

## 将解压完的文件移动到 /u01/tomcat8 目录下
mv apache-tomcat-8.5.31/* /u01/tomcat8

## 授权
chown -Rf tomcat:tomcat /u01/tomcat8
chmod -Rf 755 /u01/tomcat8/bin/*
```


由于我用的是 CentOS 6，用的脚本启动 Tomcat，所以需要修改下`/etc/init.d/tomcat`文件。
```bash
#!/bin/bash
# description: Tomcat Start Stop Restart
# processname: tomcat
# chkconfig: 234 20 80
JAVA_HOME=/u01/java/jdk1.8.0_162
export JAVA_HOME
PATH=$JAVA_HOME/bin:$PATH
export PATH
CATALINA_HOME=/u01/tomcat8

case $1 in
start)
/bin/su tomcat $CATALINA_HOME/bin/startup.sh
;;
stop)
/bin/su tomcat $CATALINA_HOME/bin/shutdown.sh
;;
restart)
/bin/su tomcat $CATALINA_HOME/bin/shutdown.sh
/bin/su tomcat $CATALINA_HOME/bin/startup.sh
;;
esac

. /root/firewall.sh

exit 0
```

验证 tomcat 是否安装成功
```bash
service tomcat start
```

可以看到 tomcat 8 服务已经启动了，并且使用的是 jdk 1.8。
![](https://ws1.sinaimg.cn/large/006By2pOgy1frs857xbcfj30ym07mmyy.jpg)


用浏览器访问 http://xxx.xxx.xxx.xxx:8080，也可以看到 tomcat 页面。
![](https://ws1.sinaimg.cn/large/006By2pOgy1frs88lo5wnj30sa0q7qah.jpg)


---

## 升级 APEX 18.1
### 解压缩安装包
```bash
## 切换到安装包目录
cd /u02/Media/

## 将安装包解压缩
unzip apex_18.1.zip
```

![](https://ws1.sinaimg.cn/large/006By2pOgy1frs1kjzlhrj30vu04yt9t.jpg)

### 执行脚本安装 APEX 18.1
```bash
## 切换到 oracle 用户
su - oracle

cd /u02/Media/apex
```

以超级管理员身份登录数据库，这里以 Oracle Database 11g 数据库举例，12c 安装步骤类似。
```sql
sqlplus / as sysdba

-- 创建单独的 tablespace（不建议用系统默认的表空间）
SQL> create tablespace APEX181 DATAFILE '/u03/oradata/apex181.dbf' SIZE 1024m REUSE AUTOEXTEND ON NEXT 100M MAXSIZE UNLIMITED;

-- 安装 APEX 18.1，安装过程可能会持续5-10分钟
-- 这里的 APEX181 是刚刚创建的 tablespace
SQL> @apexins APEX181 APEX181 temp /i/

-- 重置 APEX 管理控制台账号密码
SQL> @apxchpwd

-- 配置RESTful Services服务
SQL> @apex_rest_config.sql

-- 禁用数据库内置的PL/SQL网关
SQL> exec dbms_xdb.sethttpport(0);
SQL> exec dbms_xdb.setftpport(0);

-- 解锁ORDS用户账号
SQL> alter user apex_public_user account unlock;
SQL> alter user apex_public_user identified by "your password";

-- 断开数据库会话
SQL> exit
```

![](https://ws1.sinaimg.cn/large/006By2pOgy1frs2ev4a38j30wo0ayq4k.jpg)

### 将静态文件部署到 tomcat
```bash
## 切换到root用户
su - root

## 在 Tomcat 的 webapps 目录下新建一个名为`i`的文件夹
mkdir -p /u01/tomcat8/webapps/i/

## 将APEX静态文件部署到tomcat目录下
cp -a /u02/Media/apex/images/* /u01/tomcat8/webapps/i/

## 授予相应权限
chown -Rf tomcat:tomcat /u01/tomcat8/webapps/
```

---

## 升级 ORDS 18.1
### 解压缩安装包
```bash
mkdir -p /u01/ords
unzip /u02/Media/ords.18.1.1.95.1251.zip -d /u01/ords/
```

### 执行安装脚本
```bash
cd /u01/ords

java -jar ords.war install advanced
```

![](https://ws1.sinaimg.cn/large/006By2pOgy1frsd0gp3suj31p40x2wn0.jpg)

为 tomcat 账号授权，确保 tomcat 账号可以读取`/u01/ords/conf`目录内文件。
```bash
chown -R tomcat:tomcat /u01/ords/conf
```

### 将 ords.war 部署到 Tomcat
```bash
cp -a /u01/ords/ords.war /u01/tomcat8/webapps/

chown -Rf tomcat:tomcat /u01/tomcat8/webapps/

## 重启 tomcat 服务
service tomcat restart
```

### 验证 ORDS 已部署成功
打开浏览器，访问 http://xxx.xxx.xxx.xxx:8080/ords，如果部署成功，APEX 应该就可以访问了。
![](https://ws1.sinaimg.cn/large/006By2pOgy1frsdevwzfij30py0ljq42.jpg)

> 钢哥注：如果想把 url 里的`ords`替换成别的，比如`qingxi`，需要在先将`ords.war`重命名为`qingxi.war`，然后再跑`java -jar qingxi.war install advanced`命令完成安装和部署动作。

> 如果想重装 ORDS，可以执行`java -jar ords.war uninstall`命令，卸载成功后在删除安装目录的所有文件即可。

---

## 配置 Nginx (可选)
检查 nginx.conf 里是否有需要修改的地方，比如更新`i`目录
![](https://ws1.sinaimg.cn/large/006By2pOgy1frsdq5ch53j30ey03i3yl.jpg)

## 测试升级后的环境
打开浏览器，访问 http://xxx.xxx.xxx.xxx/ords，如果部署成功，APEX 应该就可以访问了。
![](https://ws1.sinaimg.cn/large/006By2pOgy1frsdevwzfij30py0ljq42.jpg)

登录到管理控制台，查看现有的工作空间（Existed Workspace），发现老铁都还在。
![](https://ws1.sinaimg.cn/large/006By2pOgy1frshxpz2k3j328013stf2.jpg)

输入对应的账号后，检查之前的应用也都能正常运行，完美！！

![](https://ws1.sinaimg.cn/large/006By2pOgy1frsi1heqdcj328013s489.jpg)


---

## 结语
以上就是如何从之前的 APEX 升级到最新的 APEX 18.1 版本的实操，希望老铁们喜欢。


