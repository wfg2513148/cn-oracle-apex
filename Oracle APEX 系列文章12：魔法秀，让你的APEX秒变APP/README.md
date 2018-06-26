# Oracle APEX 系列文章12：魔法秀，让你的APEX秒变APP


![](https://ws1.sinaimg.cn/large/006By2pOgy1fsn9x2qfdnj30vq0b0wic.jpg)


## 引言
很多时候我们也想要有自己的移动端APP，怎奈还要兼容 iOS、Android 不同平台，开发成本太高。昨天刷 twitter，无意中发现一个好玩的网站 [https://gonative.io/](https://gonative.io/)，输入任意网址，就可以快速帮你创建好可以部署在 iOS 和 Android 平台上的代码（当然不是免费的，价目表在[这里](https://gonative.io/pricing)），用来测试移动端效果还不错，有类似需求的同学可以关注一下。

<!-- more -->

## 配置步骤
整个配置过程非常简单，只需要填你要生成的网址和你的邮箱，点击生成按钮，稍等几分钟，你的邮箱会收到一封邮件，打开里面的地址，就可以在线体验了。

![](https://ws1.sinaimg.cn/large/006By2pOgy1fsn8vq6n3pj30vu0pymzd.jpg)

## 演示效果
我用 [apex.oracle.com](https://apex.oracle.com/) 上自带的 demo 应用简单尝试了一下，效果很好，感兴趣的小伙伴也可以直接打开这个网址来体验：[https://gonative.io/share/jezbmd](https://gonative.io/share/jezbmd) 
username：`demo`
password：`demo`

以下是一些截图：
### iOS 平台
![](https://ws1.sinaimg.cn/large/006By2pOgy1fsn98dmiqxj30rd0h6wl3.jpg)

### Android 平台
![](https://ws1.sinaimg.cn/large/006By2pOgy1fsn9alvlblj30sq0gsq8x.jpg)

## 结尾
原理虽然跟微信小程序 WebView 内嵌 H5 一样，但却能真实地解决客户现实存在的问题，这让那些暂时没有能力开发 APP 的公司也可以快速上线自己的移动端应用了。另外，即使不花钱，作为一个快速验证原型的测试平台也是值得一试的。
