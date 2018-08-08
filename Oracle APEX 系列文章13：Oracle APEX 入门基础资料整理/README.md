# Oracle APEX 系列文章13：Oracle APEX 入门基础资料整理


![](https://ws1.sinaimg.cn/large/006By2pOgy1fu1li2szerj31j00ugkjm.jpg)

## 引言
经常有刚刚接触 Oracle APEX 的同学，想自己动手试一试，但找不到比较好的入门教程。其实网上有很多，比如 [Oracle Learning Library](https://apexapps.oracle.com/pls/apex/f?p=44785:OLL_HOME:5347733649657:::::)，或者 Youtube 上。问的人多了，索性整理一下，节省大家时间，方便大家快速入门。

本文是钢哥的 Oracle APEX 系列文章的第13篇，完整文章目录如下：
- [Oracle APEX 系列文章01：Oracle APEX, 让你秒变全栈开发的黑科技](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-1/)
- [Oracle APEX 系列文章02：在阿里云上打造属于你自己的APEX完整开发环境 (安装 CentOS)](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-2/)
- [Oracle APEX 系列文章03：在阿里云上打造属于你自己的APEX完整开发环境 (安装 Tomcat, Nginx)](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-3/)
- [Oracle APEX 系列文章04：在阿里云上打造属于你自己的APEX完整开发环境 (安装XE, ORDS, APEX)](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-4/)
- [Oracle APEX 系列文章05：在阿里云上打造属于你自己的APEX完整开发环境 (进一步优化)](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-5/)
- [Oracle APEX 系列文章06：Oracle APEX 到底适不适合企业环境？](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-6/)
- [Oracle APEX 系列文章07：Oracle APEX 18.1 新特性](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-7/)
- [Oracle APEX 系列文章08：如何从 APEX 5.1.4 升级到 最新的 APEX 18.1](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-8/)
- [Oracle APEX 系列文章09：为什么选择使用 Oracle APEX](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-9/)
- [Oracle APEX 系列文章10：Oracle APEX Evangelion（EVA 补完计划）](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-10/)
- [Oracle APEX 系列文章11：全站启用 HTTPS，让你的 APEX 更安全](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-11/)
- [Oracle APEX 系列文章12：魔法秀，让你的 H5 页面秒变 APP](https://wangfanggang.com/Mobile/native-apex/)
- [Oracle APEX 系列文章13：Oracle APEX 入门基础资料整理](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-13/)



## Oracle Learning Library (推荐)
[Oracle Learning Library](https://apexapps.oracle.com/pls/apex/f?p=44785:OLL_HOME:5347733649657:::::) 是学习 Oracle 产品最佳途径，上面有Oracle各产品官网的教程。有关 APEX 的请移步这里：[Oracle Application Express Learning Library](https://apexapps.oracle.com/pls/apex/f?p=44785:141:5347733649657::NO:::)，以下简单整理 APEX 官方教程列表。

<!-- more -->


> **注**：`APEX 5.1`版本跟最新的`18.1`没有太大的区别，对于初学者来说，把 5.1 的教程先走一遍，对于掌握 APEX 基础而言已经足够了。

- [Adding Charts to your Existing Database Application in Oracle Application Express 5.1](http://www.oracle.com/webfolder/technetwork/tutorials/obe/db/apex/r51/charts/charts.html)
- [Installing and Running a Packaged Application in Oracle Application Express 5.1](http://www.oracle.com/webfolder/technetwork/tutorials/obe/db/apex/r51/packagedapp/index.html)
- [Creating an Application Express Database Application from a Spreadsheet](http://www.oracle.com/webfolder/technetwork/tutorials/obe/db/apex/r51/appfromcsv/index.html)
- [Adding Security to your Database Application Using Oracle Application Express 5.1](http://www.oracle.com/webfolder/technetwork/tutorials/obe/db/apex/r51/addingsecurity/index.html)
- [Using Interactive Grid Regions in Oracle Application Express 5.1](http://www.oracle.com/webfolder/technetwork/tutorials/obe/db/apex/r51/using_interactive_grid/using_interactive_grid.html)
- [Translating an Application in Oracle Application Express 5.1](http://www.oracle.com/webfolder/technetwork/tutorials/obe/db/apex/r51/translating_appln/translating_appln.html)
- [Transitioning to Oracle Application Express 5.1](http://www.oracle.com/webfolder/technetwork/tutorials/obe/db/apex/r51/transtoapex5.1/index.html)
- [Building Dynamic Actions in Oracle Application Express 5.1](http://www.oracle.com/webfolder/technetwork/tutorials/obe/db/apex/r51/dynamic_actions/dynamic_actions.html)
- [Building a Mobile Web Application Using Oracle Application Express 5.1](http://www.oracle.com/webfolder/technetwork/tutorials/obe/db/apex/r51/mobile_web_app/mobile_web_app.html)
- [Building a Websheet Application in Application Express 5.1](http://www.oracle.com/webfolder/technetwork/tutorials/obe/db/apex/r51/websheet_app/websheet_app.html)
- [Building and Customizing an Interactive Grid in Oracle Application Express 5.1](http://www.oracle.com/webfolder/technetwork/tutorials/obe/db/apex/r51/building_and_customizing_interactive_grid/interactive_grids_part1.html)
- [Creating PDF Reports with Oracle Application Express 5.1 and Oracle REST Data Services](http://www.oracle.com/webfolder/technetwork/tutorials/obe/db/apex/r51/pdf_printing/pdf_printing.html)
- [Creating a Data Load Wizard for Your Application with Oracle Application Express 5.1](http://www.oracle.com/webfolder/technetwork/tutorials/obe/db/apex/r51/dataload/dataload.html)
- [Creating and Using a RESTful Web Service in Application Express 5.1](http://www.oracle.com/webfolder/technetwork/tutorials/obe/db/apex/r51/restful_web_services/restful_web_services.html)
- [Modifying the Universal Theme by using Theme Roller in Application Express 5.1](http://www.oracle.com/webfolder/technetwork/tutorials/obe/db/apex/r51/themeroller/themeroller.html)




## Oracle APEX 插件 (推荐)
- [apex.world](https://apex.world/) 是另一个钢哥常去的网站，特别是上面有很多好用的 [APEX 插件](https://apex.world/ords/f?p=100:700:::NO:700::&cs=3nJKWbr-PCmZ3KR-A4fNp608r8dg)，更新也很及时，强烈推荐有进阶需求的同学。
- [http://www.apex-plugin.com/](http://www.apex-plugin.com/)，这个网站的时间比较早，上面有很多比较老的插件，算是对 apex.world 的补充吧。
- [Pretius plugins for Oracle APEX](http://apex.pretius.com/apex/f?p=PLUGINS:HOME:::NO:::) 一个第三方的 Oracle APEX 插件公司。




## Youtube 视频教程 (推荐)
Youtube 上也有很多网友自行录制的 Oracle APEX 视频教程，不喜欢看文字的同学可以直接看视频学习。不过 Youtube 需要翻墙访问，请自备梯子。有关如何翻墙，可以参考钢哥之前的文章《[10分钟用 Docker 搭建自己的 Shadowsocks VPN](https://wangfanggang.com/Docker/shadowsocks-vpn/)》，或者用[蓝灯](https://getlantern.org/zh_CN/)之类的VPN工具，相信这点儿困难挡不住爱学习的你。

- [Oracle APEX 18.1 Getting Started](https://www.youtube.com/watch?v=q_GfZFio9qg&list=PLUo-NIMouZ_vKrZL3CyRIXH6adADcGp9K)
- [Oracle Application Express 订阅频道](https://www.youtube.com/channel/UCEpIXFjcQIztReQNLymvYrQ)
- [SkillBuilders for APEX 订阅频道](https://www.youtube.com/channel/UCyhuNcrGetIRY9SdD92Uigw)


## Oracle Technology Network
[OTN](http://www.oracle.com/technetwork/index.html) 也是 Oracle 官网的一部分，上面有 Oracle 最前沿的技术动态，也可以在上面找到有关 Oracle APEX 的信息。
- [Oracle Application Express Getting Started](http://www.oracle.com/technetwork/developer-tools/apex/application-express/apex-getting-started-1863613.html)

这里是另一个[入口](https://www.oracle.com/database/technologies/appdev/apex.html)。



## Oracle Application Express Discussion Forum
[Oracle Application Express Discussion Forum](https://community.oracle.com/community/database/developer-tools/application_express) 是 Oracle APEX 的官方论坛，任何有关 APEX 的问题都可以直接在上面提，经常有 Oracle 原厂的同事帮忙解答。


## Oracle Application Express Community
[Oracle APEX 社区](https://apex.oracle.com/pls/apex/f?p=411:1)也是 Oracle 官方为 APEX 专门打造的产品社区，里面有关于 Oracle APEX 方方面面的信息。
- [Oracle APEX 咨询公司](https://apex.oracle.com/pls/apex/f?p=411:2:::NO:::)
- [Oracle APEX 托管公司](https://apex.oracle.com/pls/apex/f?p=411:4:::NO:::)
- [Oracle APEX 商业产品](https://apex.oracle.com/pls/apex/f?p=411:5:::NO:::)
- [Oracle APEX 互联网应用产品](https://apex.oracle.com/pls/apex/f?p=411:9:::NO:::)
- [Oracle APEX 相关图书](https://apex.oracle.com/pls/apex/f?p=411:13:::NO:::)
- [Oracle APEX 成功案例](https://apex.oracle.com/pls/apex/f?p=411:11:::NO:::)
- [Oracle APEX 客户评价](https://apex.oracle.com/pls/apex/f?p=411:15:::NO:::)
- [Oracle APEX 杂志文章](https://apex.oracle.com/pls/apex/f?p=411:19:::NO:::)
- [Oracle APEX 相关链接](https://apex.oracle.com/pls/apex/f?p=411:17:::NO:::)
- [Oracle APEX 开发的真实网站](https://www.builtwithapex.com/ords/f?p=BWA:LIST)

这里有另一个 [APEX 社区](https://www.odtug.com/apex)，主要聚合了很多 Oracle APEX 的博客文章。


## Oracle Application Express Documentation Release 18.1
[Oracle APEX 18.1 官方文档库](https://docs.oracle.com/database/apex-18.1/index.htm)，主要内容如下：
- [Release Notes](https://docs.oracle.com/database/apex-18.1/HTMRN/toc.htm)
- [Installation Guide](https://docs.oracle.com/database/apex-18.1/HTMIG/toc.htm)
- [App Builder User's Guide](https://docs.oracle.com/database/apex-18.1/HTMDB/toc.htm)
- [Application Migration Guide](https://docs.oracle.com/database/apex-18.1/AEMIG/toc.htm)
- [SQL Workshop Guide](https://docs.oracle.com/database/apex-18.1/AEUTL/toc.htm)
- [API Reference](https://docs.oracle.com/database/apex-18.1/AEAPI/toc.htm)
- [JavaScript API Reference](https://docs.oracle.com/database/apex-18.1/AEXJS/index.html)
- [Administration Guide](https://docs.oracle.com/database/apex-18.1/AEADM/toc.htm)
- [End User's Guide](https://docs.oracle.com/database/apex-18.1/AEEUG/toc.htm)
- [Accessibility Guide](https://docs.oracle.com/database/apex-18.1/AEACC/toc.htm)




## 其他 Oracle APEX 资源
- [Oracle APEX Tutorials](https://o7planning.org/en/11003/oracle-apex)
- [Oracle APEX Hands-On Labs](http://www.oracle.com/technetwork/developer-tools/apex/learnmore/apex-hols-2578401.html) 附带官方出品的虚拟机。
- [Oracle Database & APEX Developer Docker Image](https://github.com/Dani3lSun/docker-db-apex-dev) APEX 大神自己搭的 docker 镜像。
- [Oracle University](http://education.oracle.com/pls/web_prod-plq-dad/db_pages.getpage?page_id=609&get_params=dc:D79653,clang:EN) 大名鼎鼎的甲骨文大学，里面有专业的讲师和培训课程，有钱的公司可以直接请他们上门培训。
- [Translate APEX](http://translate-apex.com/apex/f?p=800:START::::::) 有多语言需求的同学可以关注下。


## 结尾
总结了这么多有关 Oracle APEX 的资源，如果再来问我要基础学习资料就说不过去了，把上面这些都过一遍，绝对不止入门级别了。想进阶的同学请关注钢哥整理的博文列表 [Oracle APEX Evangelion（EVA 补完计划）](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-10/)。学习知识贵在持之以恒，主要还是得自己多学多练，在实践中积累，并不断总结反思，这样进步才快。钢哥也会不定期更新有关 Oracle APEX 的最新文章和动态，希望与你共同进步！

可以扫描下面的二维码关注`APEX中文社区`订阅号：
![APEX中文社区](https://ws1.sinaimg.cn/mw690/006By2pOly1ft7hvy8x9ij309k09kwef.jpg)

另外，`APEX中文社区微信群`里每天都在讨论有关 Oracle APEX 的各种话题和最新动态，群里甚至有 APEX 美国原厂的大咖，帮助大家答疑解惑，所以关注 APEX 的同学一定不要错过。由于目前人数已经超过100人了，需要邀请才能加入，想加入的同学请加钢哥微信（添加钢哥时请注明：希望加入`APEX中文社区微信群`），钢哥来拉大家入群。

![APEX中文社区微信群](https://ws1.sinaimg.cn/mw690/006By2pOly1ft7yzed8dhj30ce0fatdk.jpg)

