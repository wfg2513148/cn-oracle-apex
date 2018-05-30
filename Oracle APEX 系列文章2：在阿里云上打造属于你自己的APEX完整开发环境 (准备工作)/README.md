---
title: Oracle APEX 系列文章2：在阿里云上打造属于你自己的APEX完整开发环境 (准备工作)
copyright: true
type: categories
date: 2018-05-03 07:35:24
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


本文是钢哥的Oracle APEX系列文章中的第二篇，完整 Oracle APEX 系列文章如下：
- [Oracle APEX 系列文章1：Oracle APEX, 让你秒变全栈开发的黑科技](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-1/)
- [Oracle APEX 系列文章2：在阿里云上打造属于你自己的APEX完整开发环境 (准备工作)](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-2/)
- [Oracle APEX 系列文章3：在阿里云上打造属于你自己的APEX完整开发环境 (安装CentOS, Tomcat, Nginx)](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-3/)
- [Oracle APEX 系列文章4：在阿里云上打造属于你自己的APEX完整开发环境 (安装XE, ORDS, APEX)](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-4/)
- [Oracle APEX 系列文章5：在阿里云上打造属于你自己的APEX完整开发环境 (进一步优化)](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-5/)
- [Oracle APEX 系列文章6：Oracle APEX 到底适不适合企业环境？](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-6/)
- [Oracle APEX 系列文章7：Oracle APEX 18.1 新特性](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-7/)


## 引言

> 钢哥：“接下来的几篇文章我会详细讲解如何在阿里云上安装部署一套完整的 Oracle APEX 开发环境（如果你想，完全可以直接当生产环境使用）。”
> 
> 不了解什么是 Oracle APEX 的同学，请参考钢哥的上一篇博客：[Oracle APEX 系列文章1：Oracle APEX, 让你秒变全栈开发的黑科技](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-1/) 
> 
> 本文内容主要包括以下软件的介绍：
> - CentOS 7 操作系统（阿里云）；
> - Nginx Web 服务器；
> - Apache Tomcat 应用服务器；
> - Oracle REST Data Services（ORDS）；
> - Oracle XE 免费版数据库（Oracle Database XE）；
> - Oracle Application Express 18.1（Oracle APEX）；

如果你已经事先安装好了上述一些软件，可以跳过其中完成部分，仅安装缺失部分即可。



## 前提假设
本系列文章假设你已经熟悉Oracle系列产品（包括Oracle数据库，APEX，ORDS），至少知道它们是什么，但可能并不了解Linux操作系统的操作，以及如何在CentOS上完整安装APEX。




## APEX工作示意图
这些组件的关系简单如下图所示：
![](https://ws1.sinaimg.cn/large/006By2pOgy1fqy4n04lv1j30s207oaeu.jpg)

> 上述提到的软件都是开源免费的，当然你也可以替换其中任意部分来实现同样的功能。比如：将Nginx替换成Apache http server（甚至直接用Tomcat做http服务器也可以，不过一般不建议这么做），或者用其他应用服务器（Oracle Weblogic，GlassFish）替换Apache Tomcat。操作系统也可以选择其他操作系统，只要可以安装Oracle数据库即可。

<!-- more -->



## 阿里云 - 云提供商
这个搞IT的同学应该都了解的吧，就不多做介绍了，不了解的同学可以把它想象成网络服务器托管商，可以随时随地按需租用网络服务器。本系列文章使用的是[阿里云](https://account.aliyun.com/login/login.htm)的`ECS`云服务器，仅仅是因为在国内用阿里云的人比较多而已。大家完全可以选择其他云提供商的云服务器，比如[腾讯云](https://cloud.tencent.com/login)的`云主机`，[Amazon AWS](https://aws.amazon.com/cn/?nc2=h_lg)的`EC2`，只要操作系统都是CentOS 7 64位的，操作上没有任何区别。




##  CentOS 7（64位）- 操作系统
CentOS操作系统应该是个人用的比较多的操作系统了，我们选择的CentOS 7（64位）也是主流版本，可以适用于多种场景，简单易用，功能强大，不熟悉Linux的同学，推荐学习一下。





## Oracle Database 11g XE (64位) - 数据库
Oracle APEX唯一的依赖就是Oracle数据库，好在Oracle除了商业版数据库以外，有一款体验版Express Edition（缩写为`XE`）数据库，最大内存和硬盘可以上到1GB，对于个人开发测试已经足够了。虽然叫体验版，但我们要用的功能都有，不比商业版差，最重要的是免费，所以在这里采用这个版本。如果有同学要用于生产环境，还是推荐Oracle Database Enterprise版本。对于APEX安装来说没有太大区别。

> 目前的Oracle Database XE还是11g的版本。按照Oracle的惯例，应该很快会推出Oracle Database 18c XE，对于我们研究APEX没有区别。





## Oracle Application Express (APEX)
[Oracle APEX](https://apex.oracle.com/) （全称：Oracle Application Express）是Oracle为了追求“零编码”开发Web应用而推出的一款云开发平台，它允许开发人员在其之上完成从设计、开发到部署的全生命周期管理，可以快速开发出漂亮的响应式Web应用。开发人员在开发时仅仅需要一个能连上网的浏览器即可，本地无需安装任何软件。

Oracle APEX 必须依赖Oracle数据库，因为所有的应用元数据都是保存在Oracle数据库中的，这也就意味着必须有一个web监听器来处理网络请求。Oracle APEX可以通过以下3种方式实现网络监听：
- Oracle数据库内嵌PL/SQL网关（EPG）：这个是Oracle数据库内置的基本功能。通过EPG可以直接将APEX请求。但这种模式只适合简单的开发调试，不推荐用于生产环境；
- 支持mod_plsql模块的Oracle HTTP Server：这种方式已被废弃，不推荐使用；
- Oracle REST Data Services（ORDS）：这种方式是官方推荐的方式，你可以从[这里下载最新的ORDS安装包](http://www.oracle.com/technetwork/developer-tools/rest-data-services/downloads/index.html)（目前ORDS最新版本是18.1.1.95.1251，发布于2018年4月5日）。





## Oracle REST Data Services (ORDS) - Web监听器
[Oracle REST Data Services](http://www.oracle.com/technetwork/developer-tools/rest-data-services/downloads/index.html)是Oracle出品的基于Java EE的web应用，它可以运行在独立模式（使用其内置的Jetty作为web服务器），也可以单独被部署在其他应用服务器（Oracle Weblogic，GlassFish，Apache Tomcat等）上运行。


作为 Oracle APEX 的web监听器，ORDS可以轻松实现基于数据库（不仅仅是Oracle Database，同样适用于Oracle NoSQL Database）的RESTful API接口，可以用来快速集成其他系统或服务。





## Apache Tomcat - 应用服务器
[Apache Tomcat](http://tomcat.apache.org/)是一款主流的开源应用服务器，支持Java Servlet、JavaServer Page、Java Expression Language以及Java WebSocket，普遍用于部署Java应用。经过了多年的发展，技术上非常成熟，而且开源免费，是应用服务器（特别是互联网项目）的首选。

在这里我们采用将ORDS单独部署到Tomcat上运行的方式完成安装，这也是Oracle比较推荐的方式。





## Nginx - Web服务器
接触Web开发的同学应该都听说过[Nginx](https://www.nginx.com/)了吧，它是目前主流的Web服务器，比Apache Http Server或者微软的IIS强大太多，除了基本的Web服务器功能外，还可以实现负载均衡、反向代理等。在这里我们用Nginx作为Web服务器。Nignx处理网络上过来的http请求，通过转发规则，将请求转发给后台的Tomcat服务器或者直接请求静态资源，有关Nginx Web服务器的工作原理请自行[谷歌](https://www.google.com.hk/search?hl=cn&q=nginx&gws_rd=cr)（`不要用百度，不要用百度，不要用百度，重要事情说三遍`）。




## 总结
本文从概念上讲解了Oracle APEX安装部署需要的环境及功能，下一篇文章将主要从实战角度出发，一步一步带领大家完成Oracle APEX的安装部署。如果有遗漏或者不准确的地方，也希望大家批评指正。
