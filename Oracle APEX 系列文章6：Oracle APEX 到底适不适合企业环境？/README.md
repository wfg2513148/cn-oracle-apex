---
title: Oracle APEX 系列文章6：Oracle APEX 到底适不适合企业环境？
copyright: true
type: categories
date: 2018-05-13 13:28:25
categories:
- Oracle
- Oracle APEX

tags:
- Oracle
- APEX
---


![](https://ws1.sinaimg.cn/large/006By2pOly1fr9nu8eldxj31r115owkv.jpg)


本文是钢哥的Oracle APEX系列文章中的第六篇，完整 Oracle APEX 系列文章如下：
- [Oracle APEX 系列文章1：Oracle APEX, 让你秒变全栈开发的黑科技](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-1/)
- [Oracle APEX 系列文章2：在阿里云上打造属于你自己的APEX完整开发环境 (准备工作)](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-2/)
- [Oracle APEX 系列文章3：在阿里云上打造属于你自己的APEX完整开发环境 (安装CentOS, Tomcat, Nginx)](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-3/)
- [Oracle APEX 系列文章4：在阿里云上打造属于你自己的APEX完整开发环境 (安装XE, ORDS, APEX)](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-4/)
- [Oracle APEX 系列文章5：在阿里云上打造属于你自己的APEX完整开发环境 (进一步优化)](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-5/)
- [Oracle APEX 系列文章6：Oracle APEX 到底适不适合企业环境？](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-6/)
- [Oracle APEX 系列文章7：Oracle APEX 18.1 新特性](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-7/)


> 钢哥注：本文是一篇翻译文章，原文作者：[Joel R. Kallman](https://www.blogger.com/profile/01915290758512999160)（Oracle APEX 研发总监），原文请移步这里：“[Is APEX Suitable for an Enterprise Setting?](http://joelkallman.blogspot.com/2018/05/is-apex-suitable-for-enterprise-setting.html?m=1&from=timeline&isappinstalled=0)”。

> 很多人对 Oracle APEX 是否真的适合企业环境还心存顾虑，所以我觉得有必要做个解释。就我个人的理解，IT 行业从有狗那年起就没有银弹。不管是从前的 SOA、企业服务总线，还是现在的微服务架构、容器技术、无服务等。即使 BAT 这些一线互联网大厂，公司内部也存在很多不同的应用框架和技术栈。别人家的架构永远也只是别人家的，能借鉴的也就是个思路，而现在国内每天都在进行的各种“技术分享会”，也只能靠 “XX公司的技术架构演进之路”之类的话题来吸引人气，因为没有一个架构或技术适合所有的公司。**`架构或技术本身并没有绝对的好坏之分，只有适不适合。`**（想争论 PHP 是最好的编程语言的同学请无视我，谢谢）

> 言归正传，下面是主要译文。



`Oracle APEX 18.1` 最显著的新特性就是有能力`消费多种远端数据源`，从普通的 REST 数据源乃至基于 [ORDS](http://www.oracle.com/technetwork/developer-tools/rest-data-services/overview/index.html)（Oracle REST Data Services）的远程 SQL。直到 Oracle APEX 18.1 之前，数据库连接（DB Link）还一直是访问远端 Oracle 数据库的最普遍方式。当然，这种数据库连接在云端环境是不存在的，而针对这方面的（功能）提升已然变成 Oracle APEX 18.1 的一个核心关注点。


<!-- more -->


一位具有多年经验的 Oracle 顾问最近发表了一篇关于 Oracle APEX 的负面评论，他在博客中声称：
> “在 Oracle 众多的产品中，APEX 已然是（一种）过时的，单层的，与 Oracle Forms 类似古董（工具）。现在许多应用架构都基于 REST 服务了，并且其他的 Oracle 工具，如：[Oracle Jet](http://oraclejet.org/), [VBCS](https://cloud.oracle.com/visual-builder) 和 [ADF](http://www.oracle.com/technetwork/developer-tools/adf/overview/index.html) 长久以来就具备生成和（或）消费 REST 服务的能力。”

在我继续（下面的话题）之前，我要纠正（他的）几个观点。首先，**Oracle APEX 很久以前就已具备生产和消费 REST 以及 SOAP 服务的能力了**。我（之所以）知道，是因为我早在2002年就授权了 APEX 针对 SOAP 服务的第一个支持。并且，您也不可能在 Oracle Jet 上生产 REST 服务，因为 Oracle Jet 是一个工具集，本身并不具备后端数据存储（的功能），更没有能力用来"支撑"一个 REST 服务。包括 Oracle 自己的 [Oracle Jet](http://oraclejet.org/) 的产品经理们现在都在使用来自 [Oracle APEX](apex.oracle.com) 上的 REST 服务来演示 Oracle Jet！最后，Oracle Jet 是2015年10月才发布的，而 ABCS (现在叫“[VBCS](https://cloud.oracle.com/visual-builder)”) 也仅仅是2015年6月才发布的第一版。如果这就是这位顾问所谓的“长久以来就具备”的能力，那么好吧。

回到（博主）提到的"过时的，单层的，不够现代化"的问题。在回应 [Morten Braten](https://twitter.com/mortenbraten) (Oracle APEX 论坛社区的资深人士)的问询时，该顾问声称“单层（应用）对于企业环境来说很少是好的选择”，在回应我关于什么是"企业环境"的定义时，他仅仅援引了另一篇讲述“单层工具对企业是坏的”的网络博文。

他质疑 Oracle APEX 架构的观点之一是："数据在被其他人看到之前必须先提交到数据库"。我觉得这是个很奇怪的观点。上一次我关注（这类问题）的时候，大部分的业务应用系统都是用来处理数据的。从现在（往前）倒推30年，我们处理数据的界面和方法变更了十几次了。然而，（企业应用系统）处理的仍然只是 - **数据**。Billy Verreynne（一位资深 Oracle APEX 专家）曾在2005年[评论](https://community.oracle.com/message/886570)道：`“企业应用系统到底应该关注什么？是数据！（数据）才是最核心的，（数据）才是驱动业务的关键。铁打的数据，流水的应用。数据保存在哪里？数据库！数据库才是核心所在。数据库自从上世纪80年代就出现了，而现在仍然在那里。将关注点聚焦在数据上，为了数据来设计，并有效利用好数据才是企业应用设计的关键所在。”`

我经常告诉人们，Oracle APEX 跟许多其他技术的交叉点就是 Oracle 数据库。**Oracle APEX 是一个功能非常强大的应用开发环境，它同时也是一个带有交互界面的设计开发引擎**，跟这位顾问提到的许多企业应用框架是一样的。`并发性、事务完整性、持久性` - 这些问题 Oracle 数据库早在许多年前就已经解决了。并且作为奖励，您同时还免费获得了`零延迟的数据访问`能力。所以，**“在任何人看到数据之前提交到数据库”从来不应该被认为是缺陷，反而应该被考虑成是一个特性。**

反观“`企业设置`”这一术语，对于每一家企业而言，从简单的应用到完整的关键业务应用，对应着不同的需求。如果把这些需求画成一个图，类似下面这种：

![](https://ws1.sinaimg.cn/large/006By2pOgy1fr91nx7r55j304c08wdfo.jpg)

**最底部代表最简单的应用需求。**这类应用应该是非常`简单`就可以实现的，`复杂度非常低`，基本`一到两个人`就可以开发完成，并且只有`短暂`的可预见的`生命周期`，这类应用往往都是 "机会主义" 类型的应用。

**而图的最上面则对应的是真正的企业关键应用需求。**这类需求往往需要`更大的团队`（通常10到20人，甚至更多的开发人员）、并且配备`专职的项目经理`，拥有`专门的预算`，系统`复杂度和成本都非常高`，开发的则是企业真正的关键核心应用系统。

那么，Oracle APEX 能够解决的需求落在哪个区间范围内呢？我相信这也是我跟上面那位顾问最大的分歧所在。我相信 `Oracle APEX 绝对可以处理这里面从下至上 90% 的需求。` 虽然Oracle APEX 可以被企业客户用来开发大型 ERP、HR 和 CRM 系统，并支持数以千计的终端用户的，但 Oracle APEX 真正的强项还是在处理从下至上这 90% 的需求。

![](https://ws1.sinaimg.cn/large/006By2pOly1fr9h46t01bj304408waa0.jpg)

每家公司自己的应用系统（与真正的需要之间）都有差距。连作为信息管理公司的 [Oracle 公司](https://www.oracle.com/) 自己也会存在这种差距。没有一个企业或应用系统可以完美地解决所有的业务需要。问题是，我们该如何来解决（这些问题）？还是仅仅放任自流，根本不去解决？企业架构师优先选择的肯定都是主流的、广受支持的技术栈，但往往这些技术栈对大部分现有开发人员不是那么容易可以使用的，这也是为什么至今为止 Excel 式的“系统”仍然在企业里广泛使用的原因。

上面那位顾问所信奉的企业架构都应该选择最理想的技术来开发。但问题是，在上面的图中，这类"理想"的技术对于开发数量众多的简单应用而言，在应用架构和相关技术栈层面，是否带来了更多不必要的复杂度或成本开支呢？一家企业现存的应用系统中，能有多少可以被称为真正的关键应用系统？10个？20个？30个？与之相对的却是数以百计、千计乃至万计的“非关键”应用系统。我很高兴看到 Oracle APEX 可以被用来快速解决掉这其中 90% 的需求，即使对于大型企业也依然如此。

在我们 Oracle 内部，我每天也都能看到 Oracle APEX 正在解决这 90% 的需求，所覆盖的范围从`跟踪硬件资产分配 & 管理` 到 `旨在管理与区块链案例相关的抵押品应用系统`，再到`可以给财务部门提交薪资问题的应用系统` - 这类“90%”的需求的覆盖面是非常广的。问题是，被认为是理想中的技术真正解决了这其中的多少需求？最终是用纸，还是用一个电子表格来解决的？您是否更愿意选择`一个基于 Oracle 数据库的、久经考验的、可扩展的低代码开发框架`，让它来帮您完成 Web 应用开发中涉及到的所有方方面面，而您则可以将注意力更专注于解决业务问题呢？是的，我的朋友，我说的`这个框架就是 Oracle APEX`！
