---
title: Oracle APEX 系列文章3：在阿里云上打造属于你自己的APEX完整开发环境 (安装CentOS, Tomcat, Nginx)
copyright: true
type: categories
date: 2018-05-03 14:39:56
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


本文是钢哥的Oracle APEX系列文章中的第三篇，完整 Oracle APEX 系列文章如下：
- [Oracle APEX 系列文章1：Oracle APEX, 让你秒变全栈开发的黑科技](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-1/)
- [Oracle APEX 系列文章2：在阿里云上打造属于你自己的APEX完整开发环境 (准备工作)](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-2/)
- [Oracle APEX 系列文章3：在阿里云上打造属于你自己的APEX完整开发环境 (安装CentOS, Tomcat, Nginx)](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-3/)
- [Oracle APEX 系列文章4：在阿里云上打造属于你自己的APEX完整开发环境 (安装XE, ORDS, APEX)](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-4/)
- [Oracle APEX 系列文章5：在阿里云上打造属于你自己的APEX完整开发环境 (进一步优化)](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-5/)
- [Oracle APEX 系列文章6：Oracle APEX 到底适不适合企业环境？](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-6/)
- [Oracle APEX 系列文章7：Oracle APEX 18.1 新特性](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-7/)


## 引言

在这一章节里，我们将一起动手安装 Tomcat 以及 Nginx，并为后面的安装做一些初始化设置。

## 安装配置CentOS 7
### 在阿里云控制台购买并启动CentOS
之前说过，我们选择阿里云作为云提供商。注册步骤就不展开了，大家可以到[阿里云官网](https://account.aliyun.com/)进行注册。注册完账号并成功登陆**控制台**，点击左侧导航菜单`云服务器 ECS`，再点击`创建实例`按钮，如下图所示：

![](https://ws1.sinaimg.cn/large/006By2pOgy1fqyec6pcb4j32801e0av0.jpg)

![](https://ws1.sinaimg.cn/large/006By2pOgy1fqyeo45l4sj32761z6kcm.jpg)

<!-- more -->

![](https://ws1.sinaimg.cn/large/006By2pOgy1fqyestr10wj32761gsgzl.jpg)

![](https://ws1.sinaimg.cn/large/006By2pOgy1fqyewtmpe1j32761727f0.jpg)

![](https://ws1.sinaimg.cn/large/006By2pOgy1fqyeyunjqmj32761es4b3.jpg)

![](https://ws1.sinaimg.cn/large/006By2pOgy1fqyezqv2tlj30ru0ki0vh.jpg)

系统提示创建成功，返回`管理控制台`稍等片刻，等待系统状态变为`运行中`即表示系统已创建完毕。

![](https://ws1.sinaimg.cn/large/006By2pOgy1fqyf5f0yumj31xa0hyah2.jpg)

用你习惯的ssh工具（如iTerm2、PuTTY等）登录测试一下，第一次会询问是否将该IP地址加到可信任站点名单，直接`yes`后输入刚才设置的`root`密码，如果看到下面的结果，就表示CentOS系统安装完毕。

![](https://ws1.sinaimg.cn/large/006By2pOgy1fqyfaeujvoj30wo09wac0.jpg)





### 升级CentOS系统包
```bash
yum upgrade -y
```
![](https://ws1.sinaimg.cn/large/006By2pOgy1fqyfg0qdqoj328016mdu8.jpg)

### 安装必要的工具包
```bash
yum install java-1.8.0-openjdk.x86_64 java-1.8.0-openjdk-devel.x86_64 libaio flex bc mc net-tools.x86_64 htop iotop iftop unzip wget epel-release vim rlwrap -y
```

安装完成后，执行`java -version`验证jdk是否安装成功。
![](https://ws1.sinaimg.cn/large/006By2pOgy1fqyfickthwj30mu05i3zg.jpg)

### 同步网络时间
```bash
systemctl start chronyd
systemctl enable chronyd
```

### 配置 /etc/hosts 文件
修改`/etc/hosts`文件，增加对本机的解析
![](https://ws1.sinaimg.cn/large/006By2pOgy1frpytz7xmyj30v4030t8z.jpg)



## 安装配置Tomcat服务器

> 钢哥提示：Tomcat安装这部分是个大坑。由于最新的ORDS 18需要Tomcat 8.5以上版本，且jdk 1.8以上，而目前网上的教程都是用yum直接安装Tomcat（版本是7的），导致版本不兼容，后面配置ORDS会发生错误，所以这里不要采用yum安装Tomcat，而是手动安装。


### 添加tomcat用户和用户组
> 如果之前安装过tomcat，对应的用户和组已经存在的话，会提示个警告，忽略即可。
> 可以用 `yum -y remove tomcat*`来卸载之前yum安装的tomcat。

```bash
## 创建tomcat安装目录
mkdir -p /u01/tomcat

groupadd tomcat
useradd -s /bin/false -g tomcat -d /u01/tomcat tomcat
```


### 下载 Tomcat 8.5 以上安装包
到 [Apache Tomcat 官网](http://tomcat.apache.org/)下载Tomcat 8.5以上安装包，本次下载的Tomcat版本是8.5.31的，你可以自行选择，只要高于8.5版本即可。
```bash
cd /u01/media

wget http://mirrors.shu.edu.cn/apache/tomcat/tomcat-8/v8.5.31/bin/apache-tomcat-8.5.31.zip
```

### 安装 Tomcat 8.5
```bash
## 先将下载的zip文件解压缩
unzip apache-tomcat-8.5.31.zip

## 将解压缩后的文件挪到安装目录中
mv apache-tomcat-8.5.31/* /u01/tomcat

## 授权
chmod -Rf 755 /u01/tomcat/bin/
chown -hR tomcat:tomcat /u01/tomcat
```

### 新增一个`tomcat.service`文件
你可以像我一样直接用`vim`命令新增文件，然后添加内容。不熟悉vim命令的请移步这里：[有关vi(vim)的常用命令](https://mp.weixin.qq.com/s/zZAWpZbDtSFK6EROxaBRKw)。
或者在本地编辑好，再用ftp工具上传到服务器`/etc/systemd/system/`目录中。
```bash
vim /etc/systemd/system/tomcat.service
```

`tomcat.service`文件的内容如下：
```bash
[Unit]
Description=Apache Tomcat 8 Servlet Container
After=syslog.target network.target

[Service]
User=tomcat
Group=tomcat
Type=forking
Environment=CATALINA_PID=/u01/tomcat/tomcat.pid
Environment=CATALINA_HOME=/u01/tomcat
Environment=CATALINA_BASE=/u01/tomcat
ExecStart=/u01/tomcat/bin/startup.sh
ExecStop=/u01/tomcat/bin/shutdown.sh
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

### 设置 tomcat 启动目录权限
```bash
chmod 755 /u01/tomcat/bin
```


### 将 tomcat 服务设置成自启动。
```bash
systemctl daemon-reload
systemctl start tomcat
systemctl enable tomcat
```

### 检查 tomcat 服务是否正常启动
```bash
systemctl status tomcat
```

如果如下图所示，证明已正常启动。
![](https://ws1.sinaimg.cn/large/006By2pOgy1fr0ax0cmu6j315y0c8tbx.jpg)


> 如果想重启Tomcat，可以执行`systemctl restart tomcat`
> 更多有关在CentOS 7上安装 tomcat 8.5 的信息可以参考这里：[How to Install Apache Tomcat 8.5 on CentOS 7.3](https://www.howtoforge.com/tutorial/how-to-install-tomcat-on-centos/)

### 开启8080端口
阿里云的服务器安全组类似CentOS的防火墙，可以配置到多台ECS服务器上，管理上更加灵活。

![](https://ws1.sinaimg.cn/large/006By2pOgy1fqyg6vsoq8j31xa0hy0yz.jpg)

![](https://ws1.sinaimg.cn/large/006By2pOgy1fqyg8rmtwpj327o0hu44c.jpg)

目前我们的ECS启用了如下端口：
- 80：http端口；
- 443：https端口；
- 22：ssh端口；
- 3389：Windows远程桌面端口（我们用不上）；

点击`快速创建规则`按钮新增一条安全组规则：
![](https://ws1.sinaimg.cn/large/006By2pOgy1fqygakup0jj32720ueqed.jpg)

> 这里我们要新增一条安全组规则，把`8080`端口开放出来，方便我们测试Tomcat是否安装成功。后面设置好 Nginx 以后，所有http请求会通过Nginx转发到Tomcat的8080端口上，这时再把8080端口关闭掉，让系统更加安全。

![](https://ws1.sinaimg.cn/large/006By2pOgy1fqygh0qhnwj30xa12k42c.jpg)

### 测试Tomcat
打开浏览器，访问`http://47.100.207.171:8080/` (把IP地址换成你自己的)，如果能看到下面的页面，证明Tomcat安装完毕。
![](https://ws1.sinaimg.cn/large/006By2pOgy1fr0ba38nq2j31km10eqj6.jpg)


### Tomcat重要目录及文件
- `/u01/tomcat` Tomcat默认的安装目录；
- `/u01/tomcat/conf/server.xml` Tomcat的主配置文件，包含service, connectors, engine, realm, valve, hosts等组件；
- `/u01/tomcat/conf/web.xml` 遵循Servlet规范标准的配置文件，用于配置servlet，并为所有的Web应用程序提供包括MIME映射等默认配置信息；
- `/u01/tomcat/conf/tomcat-user.xml` Realm认证时用到的相关角色、用户和密码等信息；Tomcat自带的manager默认情况下会用到此文件；在Tomcat中添加/删除用户，为用户指定角色等将通过编辑此文件实现；
- `/u01/tomcat/conf/catalina.policy` java相关的安全策略配置文件，在系统资源级别上提供访问控制的能力；
- `/u01/tomcat/conf/catalina.properties` Tomcat内部package的定义及访问相关的控制，也包括对通过类装载器装载的内容的控制；Tomcat在启动时会事先读取此文件的相关设置；
- `/u01/tomcat/conf/logging.properties` Tomcat通过自己内部实现的JAVA日志记录器来记录操作相关的日志，此文件即为日志记录器相关的配置信息，可以用来定义日志记录的组件级别以及日志文件的存在位置等；
- `/u01/tomcat/conf/context.xml` 所有host的默认配置信息；



## 安装配置Nginx
### 安装Nginx
```bash
yum install nginx -y
```

### 设置Nginx以便下次自启动
```bash
systemctl start nginx
systemctl enable nginx
```

### 检查Nginx是否已启动
```bash
systemctl status nginx
```

![](https://ws1.sinaimg.cn/large/006By2pOgy1fqyiireudmj313w0ee0wm.jpg)

### 测试Nginx
打开浏览器，访问`http://106.14.172.85` (把IP地址换成你自己的)，如果能看到下面的页面，证明Nginx安装完毕。

![](https://ws1.sinaimg.cn/large/006By2pOgy1fqyikeh72nj31ey0ocq6y.jpg)

### Nginx重要目录及文件
- `/etc/nginx` Nginx默认的安装目录；
- `/etc/nginx/nginx.conf` Nginx默认全局配置文件；
- `/etc/nginx/conf.d/` Nginx默认子配置文件目录；
- `/usr/share/nginx/html/` Nginx默认html根目录；



## 总结
本文详细介绍了如何在阿里云上购买并启动一个CentOS 7的新实例，以及如何安装、配置和测试Tomcat和Nginx。下一篇文章将着重讲解如何安装配置Oracle数据库（XE）、Oracle APEX以及ORDS，如有遗漏或不准确的地方还请大家指正，谢谢！
