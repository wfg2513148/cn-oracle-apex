---
title: Oracle APEX 系列文章4：在阿里云上打造属于你自己的APEX完整开发环境 (安装XE, ORDS, APEX)
copyright: true
type: categories
date: 2018-05-04 16:28:14
categories:
- Oracle
- Oracle APEX

tags:
- Aliyun
- CentOS
- Tomcat
- Nginx
- ORDS
- Oracle
- APEX

---

![](https://ws1.sinaimg.cn/large/006By2pOgy1fqy4n04lv1j30s207oaeu.jpg)


本文是钢哥的Oracle APEX系列文章中的第四篇，完整 Oracle APEX 系列文章如下：
- [Oracle APEX 系列文章1：Oracle APEX, 让你秒变全栈开发的黑科技](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-1/)
- [Oracle APEX 系列文章2：在阿里云上打造属于你自己的APEX完整开发环境 (准备工作)](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-2/)
- [Oracle APEX 系列文章3：在阿里云上打造属于你自己的APEX完整开发环境 (安装CentOS, Tomcat, Nginx)](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-3/)
- [Oracle APEX 系列文章4：在阿里云上打造属于你自己的APEX完整开发环境 (安装XE, ORDS, APEX)](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-4/)
- [Oracle APEX 系列文章5：在阿里云上打造属于你自己的APEX完整开发环境 (进一步优化)](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-5/)
- [Oracle APEX 系列文章6：Oracle APEX 到底适不适合企业环境？](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-6/)
- [Oracle APEX 系列文章7：Oracle APEX 18.1 新特性](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-7/)


## 引言

在这一章节里，我们将一起动手安装Oracle数据库（XE）、APEX以及ORDS，并完成相关的设置。

> 友情提示：由于之前没有绑定阿里云的弹性公网IP，导致阿里云ECS在关机重启后IP地址会变化，这里只要简单把`公网IP`转换成`弹性公网IP`即可。弹性公网IP的好处是IP地址不会变化，需要的时候拿过来绑定到ECS等设备上即可，非常灵活。
> 更多关于阿里云弹性公网IP的介绍请移步[这里](https://help.aliyun.com/document_detail/32321.html?spm=5176.10695662.1996646101.searchclickresult.2ed44e755AM8Ts)。

现在我们的ECS服务器已经转成弹性公网IP了，以后只要不解绑，这个公网IP地址就不会自己变化了。
![](https://ws1.sinaimg.cn/large/006By2pOgy1fqz2m0u2auj31h50acgp0.jpg)

言归正传，接下来我们开始今天的教程。



## 安装前准备工作
### 下载软件包
我们需要到Oracle官网下载如下软件，如果你还没有注册过Oracle账号，请先完成注册（免费的），登录后才可以下载。

- [Oracle Database Express Edition 11g Release 2 for Linux x64（XE）](http://www.oracle.com/technetwork/database/database-technologies/express-edition/downloads/index.html)
- [Oracle Application Express](http://www.oracle.com/technetwork/developer-tools/apex/downloads/index.html) 
- [Oracle REST Data Services](http://www.oracle.com/technetwork/developer-tools/rest-data-services/downloads/index.html)

> 截止到本文写作时间为止，APEX最新的版本是`18.1.0.00.45`，请下载`All Language`多语言版本，ORDS最新版本是`18.1.1.95.1251`。你下载的版本可能比钢哥的高，不过安装步骤是一样的。


<!-- more -->


### 创建安装包目录
现在软件包已经下载到本地了，在上传之前ssh连接到ECS，创建一下上传目录`/u01/media`。
```bash
mkdir /u01
mkdir /u01/media
chmod -Rf 777 /u01
```

### 增加swap空间
```bash
# 检查当前swap文件
swapon -s

# 检查当前磁盘空间
df

# 创建一个2GB的swap文件
dd if=/dev/zero of=/swapfile bs=1024 count=2048k
mkswap /swapfile
swapon /swapfile

# 再次检查swap文件
swapon -s

# 将新的swap文件加入到启用项
echo "/swapfile swap swap defaults 0 0" >> /etc/fstab

# 赋予适当权限
chown root:root /swapfile 
chmod 0600 /swapfile
```


### 上传安装包
接下来需要利用ftp工具上传到你的ECS服务器上。这里钢哥使用的是 [FileZilla](https://filezilla-project.org/) 来上传，连接信息如下图所示：

![](https://ws1.sinaimg.cn/large/006By2pOgy1fqz35f0cb5j30m80cu3zx.jpg)

上传后如图所示：
![](https://ws1.sinaimg.cn/large/006By2pOgy1frpznw2lkdj30wy05sta7.jpg)



## 安装 Oracle Database XE 数据库
### 将安装包解压缩
```bash
cd /u01/media
unzip oracle-xe-11.2.0-1.0.x86_64.rpm.zip
```

### 利用yum本地安装
```bash
yum localinstall Disk1/oracle-xe-11.2.0-1.0.x86_64.rpm -y
```

提示已成功安装完毕。
![](https://ws1.sinaimg.cn/large/006By2pOgy1fqz4b9eb4ej3280146118.jpg)

### 完成数据库初始化
保持在`root`用户下，继续执行以下命令：
```bash
/etc/init.d/oracle-xe configure
```

按照提示完成初始化设置。
![](https://ws1.sinaimg.cn/large/006By2pOgy1fqz4le0203j30zk0qo44c.jpg)


### 创建dba用户组
```bash
groupadd -g 501 dba
```

### 创建oracle用户，并分配到dba用户组
```bash
useradd -u 501 -g dba -G dba oracle
```

### 设置oracle用户的密码
```bash
passwd oracle
```

### 切换到oracle用户
按照提示设置好oracle用户的密码后，可以用`su - oracle`命令完成用户切换。
![](https://ws1.sinaimg.cn/large/006By2pOgy1fqz3oisyfyj30jw05ggmd.jpg)

> 友情提示：`su - oracle`命令中间的 `-` 符号意思是切换同时做初始化，因为oracle的很多软件需要设置环境变量的，所以每次切换用户请保证带上`-`符号。


### 配置环境变量
我们接下来要为oracle账号设置环境变量，以便保证每次切换到oracle用户时，都可以直接使用`sqlplus`等命令。

将执行`oracle_env.sh`的命令写到bash_profile文件中，这样只要每次使用`su - oracle`命令切换后，会自动执行`~/.bash_profile`文件里的脚本，完成环境变量的初始化。
```bash
echo '. /u01/app/oracle/product/11.2.0/xe/bin/oracle_env.sh' >> ~/.bash_profile
```

测试一下环境变量是否已设置成功。
```bash
source ~/.bash_profile
```

执行后就可以使用`sqlplus`等命令了。
```sql
sqlplus /nolog
```

![](https://ws1.sinaimg.cn/large/006By2pOgy1fqz4vs01wwj30rs08wab2.jpg)

至此，Oracle Database XE 安装配置完毕。

### 测试数据库监听
数据库虽然成功安装好了，但还是要验证一下是否可以正产连接比较好（而不是用`sqlplus /nolog`的方式，因为不走监听）。

直接用`sqlplus`连接数据库，用户名输入`system`，密码输入安装时的密码，看是否能够正常连上数据库。
```bash
sqlplus
```

如果可以，证明数据库服务和监听都没问题了。
![](https://ws1.sinaimg.cn/large/006By2pOgy1fqzojuiua9j30u40dk75r.jpg)


## 安装配置 APEX
### 卸载旧版本的APEX
由于Oracle Datebase XE自带了一个旧版本的APEX，在正式安装最新版APEX之前，我们需要将旧版本的卸载掉。
```sql
su - oracle
cd /u01/app/oracle/product/11.2.0/xe/apex

sqlplus /nolog

-- 用数据库超级管理员连接数据库
SQL> connect sys as sysdba

-- 卸载原有的旧版本APEX
SQL> @apxremov.sql

-- 退出当前数据库会话
SQL> exit
```

### 安装最新版本APEX
确保仍然在`oracle`账号下，执行以下命令安装最新版本的APEX。
```bash
cd /u01/media
mkdir -p /u01/apex
unzip apex_18.1.zip -d /u01/
chown -R oracle:dba /u01/apex
```

现在新的APEX安装文件已经放在`/u01/apex/`目录下了，登录数据库执行安装。
```sql
cd /u01/apex

-- 以超级管理员身份登录数据库
sqlplus / as sysdba

-- 安装APEX，指定默认表空间和静态文件别名
SQL> @apexins.sql SYSAUX SYSAUX TEMP /i/
   
-- 安装完毕后数据库会话会自动断开，再次以超级管理员身份登录数据库
sqlplus / as sysdba

-- 创建APEX实例管理员（Instance Administration）及密码，这个密码必须包含特殊符号，否则设置不上。这个密码很重要，是管理APEX平台的账号密码，以后创建新的应用schema、解锁账号等都靠它，第一次登录APEX时也要用到。
SQL> @apxchpwd.sql

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


### 复制APEX静态文件到Tomcat目录
> 钢哥提示：网上很多教程都是直接把APEX静态文件内容放到Web服务器（httpd、Nginx）的目录下，我个人推荐放到Tomcat目录下（`/u01/tomcat/webapps/i/`），这样做的好处是可以先测试APEX在Tomcat上是否能跑起来，如果可以，再将上边的目录映射到Nginx中。

```bash
## 切换到root用户
su - root

## 在Tomcat的webapps目录下新建一个名为`i`的文件夹
mkdir -p /u01/tomcat/webapps/i/

## 将APEX静态文件复制过去
cp -a /u01/apex/images/* /u01/tomcat/webapps/i/

## 重启Tomcat服务
systemctl restart tomcat
```



## 安装配置 ORDS
### 解压缩安装包
```bash
mkdir -p /u01/ords
unzip /u01/media/ords.18.1.1.95.1251.zip -d /u01/ords/
```

### 执行安装脚本
```bash
cd /u01/ords
java -jar ords.war install advanced
```

按照提示完成ORDS的安装配置。

![](https://ws1.sinaimg.cn/large/006By2pOgy1frpy7zw9rkj31p818o7fy.jpg)

> 钢哥友情提示：这里的参数要认真填写，特别是数据库名称`name of the database server`，**一定要跟数据库监听器里 `/u01/app/oracle/product/11.2.0/xe/network/admin/listener.ora` 保持一致**，否则后面会因为ORDS连接不上数据库，导致访问报错。
> 另外这里要设置好几个数据库账号的密码，建议第一次安装时统一设置成一个，并做好记录，避免后面错乱。

![](https://ws1.sinaimg.cn/large/006By2pOgy1fqzqzil4vbj30ue0jajtr.jpg)



### 为tomcat账号授权（需切换到root用户）
确保tomcat账号（安装Tomcat服务器时自动创建的）可以访问`/u01/ords/config`目录。
```bash
su - root
chown -R tomcat:tomcat /u01/ords/config
```


### 将 ords.war 部署到 Tomcat
现在我们可以将刚才生成的`ords.war`文件部署到Tomcat上了。
```bash
cp -a /u01/ords/ords.war /u01/tomcat/webapps/

## 重启Tomcat服务
systemctl restart tomcat
```

## 测试 APEX 和 ORDS
### 测试 APEX + ORDS + Tomcat 的组合是否正常工作
打开浏览器，访问`http://47.100.207.171:8080/ords`，如果一切正常，应该可以访问到APEX的页面了。

![](https://ws1.sinaimg.cn/large/006By2pOgy1fqzpcx6zowj31h816yjwc.jpg)

- workspace: `INTERNAL`
- username: `administrator`
- password: `你刚才设置的instance administration的密码`

成功登录后界面如下图所示：
![](https://ws1.sinaimg.cn/large/006By2pOgy1fqzrpue9cej327c14edps.jpg)


### 配置Nginx，将http请求转发到Tomcat
最后，我们需要配置一下Nginx，让所有的http请求都能自动转发到部署在Tomcat上的ORDS上，完成跟APEX的交互。

Nginx默认配置文件在`/etc/nginx/nginx.conf`，我们需要修改这个文件，主要修改`server`节点下的内容。
```nginx
    server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  _;
        root         /usr/share/nginx/html;

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

        location / {
        }

		# 增加了/i/目录的请求转发规则，/i/目录是APEX默认的静态文件目录别名。
        location ^~ /i/ {
            alias /u01/tomcat/webapps/i/;
        }

		# 增加/ords/目录的请求转发规则，所有形如http://xxx.xxx.xxx.xxx/ords/的请求都会自动转发到http://xxx.xxx.xxx.xxx:8080/ords/上
		# 即APEX请求都会由Tomcat接管
        location ^~ /ords/ {
            proxy_pass http://localhost:8080/ords/;
            proxy_redirect off;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-Proto  $scheme;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            client_max_body_size 20m;
        }

        error_page 404 /404.html;
            location = /40x.html {
        }

        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }
    }
```

最终内容如下：
![](https://ws1.sinaimg.cn/large/006By2pOgy1fqzph215q7j30za14ktg0.jpg)


### 重启Nginx服务
```bash
systemctl restart nginx
```

### 验证 APEX + ORDS + Tomcat + Nginx 的组合是否正常工作
打开浏览器，这次直接访问`http://47.100.207.171/ords`，如果一切顺利，应该就直接打开APEX的页面了。

![](https://ws1.sinaimg.cn/large/006By2pOgy1fqzpk260mlj31ck17cq7r.jpg)



## 总结
至此，Oracle XE数据库 + ORDS + Tomcat + Nginx 的完整开发环境就搭建好了。在接下来的文章里，钢哥将带你做进一步的优化，敬请期待。

