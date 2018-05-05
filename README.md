![](https://ws1.sinaimg.cn/large/006By2pOgy1fqy4n04lv1j30s207oaeu.jpg)

# Oracle APEX 系列文章：Oracle APEX, 让你秒变全栈开发的黑科技

本文是钢哥的Oracle APEX系列文章中的第一篇，完整 Oracle APEX 系列文章如下：
- [Oracle APEX 系列文章1：Oracle APEX, 让你秒变全栈开发的黑科技](http://140.143.1.225/Oracle/Oracle-APEX/apex-series-1/)
- [Oracle APEX 系列文章2：在阿里云上打造属于你自己的APEX完整开发环境 (准备工作)](http://140.143.1.225/Oracle/Oracle-APEX/apex-series-2/)
- [Oracle APEX 系列文章3：在阿里云上打造属于你自己的APEX完整开发环境 (安装CentOS, Tomcat, Nginx)](http://140.143.1.225/Oracle/Oracle-APEX/apex-series-3/)
- [Oracle APEX 系列文章4：在阿里云上打造属于你自己的APEX完整开发环境 (安装XE, ORDS, APEX)](http://140.143.1.225/Oracle/Oracle-APEX/apex-series-4/)
- [Oracle APEX 系列文章5：在阿里云上打造属于你自己的APEX完整开发环境 (进一步优化)](http://140.143.1.225/Oracle/Oracle-APEX/apex-series-5/)


## 引言

不知不觉在Oracle ERP这个领域已经13个年头了，从刚毕业学习最基本的SQL、PL/SQL、Linux Shell脚本、Oracle Form、Oracle Report、Workflow、Discovery、Middleware (BPEL)、OBIEE、BI (XML) Publisher、OAF、ADF，到后来的环境搭建、克隆等应用DBA的工作，可以说要想成为一名合格的Oracle ERP技术顾问，要掌握的东西太多了，远不是一个普通互联网开发人员的技能可比的。

但随着互联网技术的崛起，新的开发框架和技术层出不穷。成熟的互联网架构的出现、前后端技术的分离、缓存的大量使用、微服务的兴起，动辄就提全栈开发等等，都使得今天的Oracle技术从业者无论从技术方面还是待遇方面，都受到了不小的打击（想想10年前一名Oracle ERP技术顾问的待遇，绝对比相同经验的Java开发人员的薪资高一倍不止）。很多以前的从事Oracle ERP开发的同事现在不是另谋高就，就是在维护一些古董级的项目，偶尔聊起前途也是一片唉声叹气，感叹生不逢时。

是否从事Oracle开发出路就一定越来越窄呢？答案肯定是否定的。Oracle的数据库至今为止还是市场占有率最大的数据库，而Oracle ERP领域也不是互联网技术可以简单比拟的，其复杂的业务逻辑和行业经验才是你最宝贵的资本。如何合理利用自己已有的知识和技能，在当今互联网当道的时代重新闯出一片天地，恐怕是大部分Oracle技术人员目前面临的困惑。

不可否认，Oracle的产品线非常丰富，但带来的问题是技术栈也非常杂。另外在其主打的Oracle ERP产品：Oracle Fusion Application技术选型和设计上有硬伤。当年（2009年）我第一次了解Oracle Fusion Application时，就已经预判到Oracle ERP要走下坡路了。虽然Java很好，但Oracle打开的方式不对，它选择了ADF作为开发框架，性能和扩展性大打折扣，非常差的用户体验使得Oracle在坚持了多年、投入了巨大的资源后不得不放弃继续开发Oracle Fusion Application，转而收购了云ERP厂商（NetSuite）作为现在主推的ERP产品，浪费了大量的时间和人力物力，令人惋惜。

不过Oracle众多的产品，有一个是非常好的。（其实叫产品也不太贴切，因为它既是产品、又是开发工具、同时又可以作为框架来理解）自从我10年前使用，到最近两年Oracle加速发展，使得这一产品迎来了新的春天，它就是今天的主角：`Oracle Application Express`（简称`Oracle APEX`）。



## 本文目的
> 本系列文章力求把我这些年使用 Oracle APEX 的经验积累沉淀下来，方便自己查询。同时也希望能为广大 Oracle 技术从业者带来一点儿灵感和思路，跟大家共同学习、共同进步！！
> 如果你满足以下任意一项：
- `Oracle Developer`
- `Oracle DBA`
- `Oracle ERP技术开发顾问`

> 并且想要秒变全栈开发，一人把前端后端全搞定，just follow me！

<!-- more -->



## Oracle APEX 简史

Oracle APEX 是Oracle公司出品的基于Oracle数据库的一款Web应用快速开发工具，旨在方便Oracle数据库开发人员快速搭建Web应用系统。其历史最早可以追述到2004年，之前叫`HTML DB`，2009年更名为`Oracle APEX`。


![](https://ws1.sinaimg.cn/large/006By2pOgy1fqwtgg770lj30n70d00x4.jpg)

![](https://ws1.sinaimg.cn/large/006By2pOgy1fqwre1l6j0j30n20cvgpa.jpg)

![](https://ws1.sinaimg.cn/large/006By2pOgy1fqwtdnv4zbj30n20cqwhe.jpg)

![](https://ws1.sinaimg.cn/large/006By2pOgy1fqwte8apxrj30n70d141m.jpg)

但 Oracle APEX 真正被人们关注，要从 APEX 4.0 这个版本算起了。从这个版本开始，Oracle大大加强了APEX的开发效率，同时增强了很多新功能，使Web开发变得更加简单。

![](https://ws1.sinaimg.cn/large/006By2pOgy1fqwteo5enpj30n60cy0wp.jpg)

![](https://ws1.sinaimg.cn/large/006By2pOgy1fqwtexnfrrj30n20cwq76.jpg)

而 APEX 5.0 可以说是 Oracle APEX 的一个里程碑。从5.0版本起，Oracle APEX增加了一系列新组件和新特性（特别是`Dynamic Actions`动态行为，允许用户嵌入自定义的javascript代码，也使得Ajax更加容易被应用到开发中），括开发工具的增强，可视化效果的提升，把 Oracle 开发Web应用的速度和质量推向了一个新高度，可以说是非常重要的一个版本。

![](https://ws1.sinaimg.cn/large/006By2pOgy1fqwtf9b9vmj30n10cwtd3.jpg)

而 APEX 5.1 增加了`Interactive Grid`控件，可以零代码实现对数据库表的增删改查，并且有很多非常易用的功能，极大地提高了开发效率。同时整合了Oracle最新的Javascript图标库`Oracle Jet`，使数据可视化更加方便高效。

![](https://ws1.sinaimg.cn/large/006By2pOgy1fqwtfjreilj30n30cwjvh.jpg)

![](https://ws1.sinaimg.cn/large/006By2pOgy1fqwtg0fvkjj30n10czgqe.jpg)



## Oracle APEX 适合做什么
现在互联网概念已经深入人心了，往往出来一门新技术或者工具，大家都喜欢放在互联网的范畴内进行比较。诚然，互联网技术有很多好东西值得借鉴，包括很多思想都可以运用到传统ERP领域。然而，很多时候我们做事还是要看性价比，不能为了“互联网”而“互联网”。对于个人而言，要考虑自身现有技能情况，最大化的利用已有技能，同时要保留技术敏感度，认识到自己现有技能有哪些缺失，有意识的学习。对于企业而言，要综合考虑今后一段时间的发展，目前的技术栈和人员技术水平，不能什么新上什么，而是什么适合自己上什么。做到“适合”也最不容易，这里就不展开讨论了。

> 钢哥感悟：Oracle APEX适合做`to B类`的企业应用（`Enterprise Application`），即 ERP、CRM 类应用产品。这类产品的特点是对UI和并发要求不像 to C 类互联网产品那么高，但同时业务逻辑相对更复杂，更适合企业内部使用。优势也是相当明显的，往往一个有 Oracle 开发经验的初级程序员，通过 Oracle APEX 可以完成`前端 + 后端`的所有工作，而且基本属于零代码开发，无论从开发效率，还是开发质量方面，都不是传统企业开发工具或者技术能够比拟的，这一点在后面使用过程中大家就会深刻体会到。

放几张图大家直观感受下APEX：

**开发者门户**：整体 APEX 开发门户入口，包括：应用程序构建器、SQL工作室、小组开发以及打包的应用程序几大模块。
![](https://ws1.sinaimg.cn/large/006By2pOly1fqxx2twkoaj31hc0qo787.jpg)


**应用开发界面**：大部分时间都是在这里完成的。
![](https://ws1.sinaimg.cn/large/006By2pOly1fqxx6t0kkvj31hc0qo78m.jpg)


**SQL工作室**：又是熟悉的场景，可以替代SQL工具。
![](https://ws1.sinaimg.cn/large/006By2pOly1fqxx90gr58j30ys0koac7.jpg)


**小组开发**：Oracle APEX天然支持云端开发部署，远程协作当然已经考虑到了。
![](https://ws1.sinaimg.cn/large/006By2pOly1fqy0g71yd2j30ys0qojub.jpg)


**打包的应用程序**：自带的Web应用是学习APEX最佳途径。
![](https://ws1.sinaimg.cn/large/006By2pOly1fqy0kc81wuj31gx0w6gs4.jpg)

**Interactive Grid 控件**：是我见过的最NB的控件，零代码实现数据增删改查 + 搜索排序 + 级联等功能，也是APEX 5.1 以后新推出的功能，强烈推荐。
![](https://ws1.sinaimg.cn/large/006By2pOgy1fqy0z0cyiwj31990qeq6k.jpg)


**更多图示**：
![](https://ws1.sinaimg.cn/large/006By2pOly1fqy0pnurclj31490q1tby.jpg)

![](https://ws1.sinaimg.cn/large/006By2pOly1fqy0sdccznj31450mkwic.jpg)

![](https://ws1.sinaimg.cn/large/006By2pOgy1fqy0te7uh4j31480nitdk.jpg)


> 值得一提的是，Oracle APEX 开发工具本身就是用 APEX 开发的，是真正意义上的“`APEX on APEX`”，也是我们学习参考的重要规范。



## Oracle APEX 的优势
- 仅仅使用本机浏览器即可完成Web应用的设计和开发；
- 可以方便地对标准组件、UI等进行扩展、自定义开发；
- 开发完成及部署完成，天然适应云端部署；
- 零代码也可以快速开发出复杂的业务系统；
- 高效便捷的IDE令开发Web应用从未如此简单；
- 自带丰富的控件和可视化图库，让你不愁轮子和素材；
- 自适应UI皮肤，让你一次搞定PC端和移动端；
- 自带多种场景演示实例，让你快速起步；
- Oracle官方支持，可以放心开发企业级应用，而无需担心额外成本；




## Oracle APEX 现状
目前 Oracle APEX 在全球拥有超过40万的开发者，但在中国真正使用 Oracle APEX 的公司或开发者却很少，鉴于目前国内对于 Oracle APEX 最新的技术资料有限，我有必要把自己的所学心得整理出来，以便更多的人了解学习。

- [Oracle APEX 官网](https://apex.oracle.com/)
- [Oracle APEX 社区](http://apex.oracle.com/community)
- [Oracle APEX官方论坛](https://community.oracle.com/community/database/developer-tools/application_express)
- [Oracle APEX活跃博客](http://www.odtug.com/apex)




## Oracle APEX 下载地址
截止到目前，最新版本2017年12月17日发布的 [Oracle APEX 5.1.4.00.08](http://www.oracle.com/technetwork/developer-tools/apex/downloads/index.html) 



## 总结
使用Oracle APEX 的唯一要求，就是要有一个Oracle数据库（因为Oracle APEX必须依赖Oracle Database）。在下一篇文章里，我会带领大家一步一步的安装部署一个Oracle APEX开发环境，让大家更近一步了解Oracle APEX的强大所在。

跟着钢哥的步伐走，包你迅速掌握全栈开发，为了让脚下的路更宽，我们一起努力！！




## 附录：精选Oracle APEX博客网站
亲测可用，部分网站需要翻墙。
- [http://vmorneau.me](http://vmorneau.me)
- [http://rimblas.com/blog](http://rimblas.com/blog)
- [http://apexbyg.blogspot.sg](http://apexbyg.blogspot.sg)
- [http://lschilde.blogspot.sg](http://lschilde.blogspot.sg)
- [https://dickdral.blogspot.jp](https://dickdral.blogspot.jp)
- [http://max-tremblay.blogspot.jp](http://max-tremblay.blogspot.jp)
- [https://blogs.oracle.com/apex](https://blogs.oracle.com/apex)
- [https://thtechnology.com](https://thtechnology.com)
- [http://dgielis.blogspot.sg](http://dgielis.blogspot.sg)
- [https://emoracle.wordpress.com](https://emoracle.wordpress.com)
- [http://www.grassroots-oracle.com](http://www.grassroots-oracle.com)
- [http://hardlikesoftware.com/weblog](http://hardlikesoftware.com/weblog)
- [http://www.explorer.uk.com/blog](http://www.explorer.uk.com/blog)
- [https://ruepprich.wordpress.com](https://ruepprich.wordpress.com)
- [http://davidsgale.com](http://davidsgale.com)
- [http://joelkallman.blogspot.hk](http://joelkallman.blogspot.hk)
- [https://www.talkapex.com](https://www.talkapex.com)
- [http://nuijten.blogspot.nl](http://nuijten.blogspot.nl)
- [https://www.odtug.com/apex](https://www.odtug.com/apex)
- [https://ora-00001.blogspot.sg](https://ora-00001.blogspot.sg)
- [https://apextips.blogspot.sg](https://apextips.blogspot.sg)
- [https://oracle-base.com](https://oracle-base.com)
- [http://spendolini.blogspot.sg](http://spendolini.blogspot.sg)
