# Oracle APEX 系列文章11：全站启用 HTTPS，让你的 APEX 更安全


![](https://ws1.sinaimg.cn/large/006By2pOgy1fsl3vvmyrhj318g0s4npd.jpg)

## 引言
目前主流的网站都要求 HTTPS 安全访问，Google Chrome 浏览器、微信内置浏览器打开非 HTTPS 的网页，都会提示不安全。如果做微信端开发，也是必须要 HTTPS 的网址才可以，可见 HTTPS 越来越重要了。

还不了解什么是 Oracle APEX，请阅读我的另一篇文章：[Oracle APEX 系列文章1：Oracle APEX, 让你秒变全栈开发的黑科技](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-1/)

- [Oracle APEX 系列文章2：在阿里云上打造属于你自己的APEX完整开发环境 (安装 CentOS)](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-2/)
- [Oracle APEX 系列文章3：在阿里云上打造属于你自己的APEX完整开发环境 (安装 Tomcat, Nginx)](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-3/)
- [Oracle APEX 系列文章4：在阿里云上打造属于你自己的APEX完整开发环境 (安装XE, ORDS, APEX)](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-4/)
- [Oracle APEX 系列文章5：在阿里云上打造属于你自己的APEX完整开发环境 (进一步优化)](https://wangfanggang.com/Oracle/Oracle-APEX/apex-series-5/)

如果你按照钢哥之前的文章已经搭建好了 Oracle APEX 环境，那么你的应用架构应该如下图所示：

![](https://ws1.sinaimg.cn/large/006By2pOgy1fqy4n04lv1j30s207oaeu.jpg)

这里简单回顾一下各部分组件的作用：
* 用户在浏览器地址栏里输入URL，例如：https://apex.wangfanggang.com/ords/ (不要尝试打开这个网址了，我瞎写的)
* Nginx监听 HTTP (`80`) 端口和 HTTPS (`443`) 端口，如果请求的是静态文件（如：image, js 或者 css），则直接获取`/i/`目录中的内容，对于其他动态请求（如：APEX请求），转发至后端 Tomcat 服务器做进一步处理。
* Tomcat 服务器接收到请求后，会查找部署在它上面的应用，就是我们之前部署的`ORDS`应用；
* 如果是 APEX 请求，ORDS 进一步将请求转发给 APEX (Oracle 数据库) 进行处理；如果是 ORDS 请求，自身进行处理；

原理比较简单，而我们要做的就是在 Nginx 层面将 HTTP 请求转发到 HTTPS 上，进而实现全站启用 HTTPS 访问。




## 申请 SSL 证书
这里以在阿里云上购买免费 SSL 证书为例，首先登录阿里云控制台，进入`安全（云盾）->  SSL证书(应用安全)`，点击`购买证书`。

![](https://ws1.sinaimg.cn/large/006By2pOgy1fsl1bwvrdzj328013s4eq.jpg)

进入到选择购买页面，提示1年需要五千多大洋，土豪直接点击付款即可。

![](https://ws1.sinaimg.cn/large/006By2pOgy1fsl1h0s044j328013swse.jpg)

好吧，我是穷人，只能看看有没有免费证书。其实是有的，依次点击`Symantec` -> `1个域名` -> `免费型DV SSL`，成功激活30人:)

![](https://ws1.sinaimg.cn/large/006By2pOgy1fsl1ofu9cqj328013sgxe.jpg)

接下来回到控制台，补全刚刚申请的证书信息。

![](https://ws1.sinaimg.cn/large/006By2pOgy1fsl1px0uwvj32800la465.jpg)

按照提示补全信息。
![](https://ws1.sinaimg.cn/large/006By2pOgy1fsl1t3m20sj328013s7e6.jpg)

> **钢哥提示：**由于我们申请的是阿里云的免费证书，只能作用于一个固定域名，一般我们都不会把主域名用来放置APEX应用，所以这里可以填写诸如：apex.xxx.com 的二级域名。如果你想要免费通配符域名，可以移步这里：[使用Let’s Encrypt给网站加上免费HTTPS证书](https://kyle.ai/blog/6333.html)

> 另外需要注意的是，第二步的验证环节，如果你选择的是文件验证，请一定按照提示把对应的验证文件放到你的服务器上，`正常文件验证一般不会超过5分钟`，如果长时间没验证通过，一定是你操作有问题了。

当你的证书申请通过后，就可以点击`下载`链接了。



## 配置 Nginx
将 SSL 证书添加进`nginx.conf`，按照`下载证书`页面的提示配置 Nginx：
![](https://ws1.sinaimg.cn/large/006By2pOgy1fsl2959htdj31xo23swyn.jpg)

我的`nginx.conf`文件内容如下：
```nginx
worker_processes  auto;
worker_rlimit_nofile 10000;

error_log  logs/error.log;

events
{  
   worker_connections  2048;
   #==告诉nginx收到一个新链接通知后接受尽可能多的链接
   multi_accept on;
   #==设置用于复用客户端线程的轮训方法
   use epoll;
}

http
{  
   include       mime.types;
   default_type  application/octet-stream;
   charset UTF-8;

   log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

   access_log   /etc/nginx/logs/access_log.log main;


   server_tokens off;
   sendfile      on;
   tcp_nopush    on;

   keepalive_timeout  65;
   proxy_connect_timeout 600;
   proxy_send_timeout 600;
   proxy_read_timeout 600;
   send_timeout 600;

    #==设置nginx采用gzip压缩的形式发送数据，减少发送数据量，但会增加请求处理时间及CPU处理时间，需要权衡
  gzip  on;
    #==加vary给代理服务器使用，针对有的浏览器支持压缩，有个不支持，根据客户端的HTTP头来判断是否需要压缩
    gzip_vary on;
    gzip_http_version 1.0;
    gzip_types text/plain application/javascript application/x-javascript text/css;
    gzip_min_length  1024;
    gzip_comp_level 3;

    server
    {  listen      443 default_server;
       server_name apex.wangfanggang.com;

       ssl on;
       ssl_certificate      cert/214412416080589.pem;
       ssl_certificate_key  cert/214412416080589.key;
       ssl_session_timeout  5m;
       ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
        ssl_prefer_server_ciphers on;

        location = /
        {  
           # 默认打开某个APEX应用
           rewrite ^/(.*) https://apex.wangfanggang.com/ords/f?p=102 redirect;
        }

        location ~* \.(eot|ttf|woff|woff2)$
        {  
          add_header Access-Control-Allow-Origin *;
        }

        location ^~ /i/
        {  
          alias /u01/tomcat/webapps/i/;
        }

        location ^~ /ords/
        {  
           # 将请求转发到tomcat上
           proxy_pass http://localhost:8080/ords/;
           proxy_redirect off;
           proxy_set_header Host $host;
           proxy_set_header X-Real-IP $remote_addr;
           proxy_set_header X-Forwarded-Proto  $scheme;
           proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
           client_max_body_size 20m;
        }

    }

    server
    {  
      listen       80 default_server;
      server_name  apex.wangfanggang.com;

      include /etc/nginx/default.d/*.conf;

      location = /
      {  
          # 所有http请求统一重定向到https上
          rewrite ^/(.*) https://apex.wangfanggang.com/ords/f?p=102 redirect;
      }

      location ~* \.(eot|ttf|woff|woff2)$
      {
          add_header Access-Control-Allow-Origin *;
      }

      location ^~ /i/
      {
           alias /u01/tomcat/webapps/i/;
      }

      location ^~ /ords/
      {  
         # 所有http请求统一重定向到https上
         proxy_pass https://apex.wangfanggang.com/ords/;
         proxy_redirect off;
         proxy_set_header Host $host;
         proxy_set_header X-Real-IP $remote_addr;
         proxy_set_header X-Forwarded-Proto  $scheme;
         proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
         client_max_body_size 20m;
      }


      error_page 404 /404.html;
      location = /40x.html
      {
      }

      error_page 500 502 503 504 /50x.html;
      location = /50x.html
      {
      }
    }
}
```

配置完 nginx，别忘了重启令配置生效。再次在浏览器中访问 APEX 页面，如果能正常访问，恭喜你，你的 SSL 证书生效了！！

## 配置 Tomcat
钢哥在配置的时候 SSL 证书时遇到了一个奇怪的问题，就是启用 SSL 证书后，访问 APEX 页面时会发生重定向错误（`302 error：too_many_redirects`），导致无法正常访问。

![](https://ws1.sinaimg.cn/large/006By2pOgy1fsl3084vxuj328013sqg7.jpg)


经过跟同事几天的研究，发现除了要在 Nginx 上启用 SSL 证书以外，还必须在 Tomcat 上也启用。还是回到阿里云控制台证书下载页面，找到 Tomcat 配置证书部分。

> 钢哥提示：特别要注意的是，这里要选择`JKS`格式证书进行安装，否则会有问题。

![](https://ws1.sinaimg.cn/large/006By2pOgy1fsl36ceb09j31vu2ic1kx.jpg)


在 Tomcat 的`server.xml`文件中添加如下内容：
```xml
<Valve
   className = "org.apache.catalina.valves.RemoteIpValve"
   remoteIpHeader = "X-Forwarded-For"
   protocolHeader = "X-Forwarded-Proto"
/>
```

我的`server.xml`文件内容：
```xml
<?xml version="1.0" encoding="UTF-8"?>

<!--
  Licensed to the Apache Software Foundation (ASF) under one or more
  contributor license agreements.  See the NOTICE file distributed with
  this work for additional information regarding copyright ownership.
  The ASF licenses this file to You under the Apache License, Version 2.0
  (the "License"); you may not use this file except in compliance with
  the License.  You may obtain a copy of the License at

      http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License.
-->

<!--
  Note:  A "Server" is not itself a "Container", so you may not
     define subcomponents such as "Valves" at this level.
     Documentation at /docs/config/server.html
 -->

<Server port="8005" shutdown="SHUTDOWN">
  <Listener className="org.apache.catalina.startup.VersionLoggerListener"
  />

  <!--
    Security listener. Documentation at /docs/config/listeners.html
    <Listener className="org.apache.catalina.security.SecurityListener"
    />
  -->

  <!--
    APR library loader. Documentation at /docs/apr.html
  -->

  <Listener className="org.apache.catalina.core.AprLifecycleListener" SSLEngine="on"
  />

  <!--
    Prevent memory leaks due to use of particular java/javax APIs
  -->
  <Listener className="org.apache.catalina.core.JreMemoryLeakPreventionListener"
  />
  <Listener className="org.apache.catalina.mbeans.GlobalResourcesLifecycleListener"
  />
  <Listener className="org.apache.catalina.core.ThreadLocalLeakPreventionListener"
  />

  <!--
    Global JNDI resources
     Documentation at /docs/jndi-resources-howto.html
  -->

  <GlobalNamingResources>
    <!--
      Editable user database that can also be used by
      UserDatabaseRealm to authenticate users
    -->

    <Resource
      name        = "UserDatabase" auth="Container"
      type        = "org.apache.catalina.UserDatabase"
      description = "User database that can be updated and saved"
      factory     = "org.apache.catalina.users.MemoryUserDatabaseFactory"
      pathname    = "conf/tomcat-users.xml"
    />
  </GlobalNamingResources>

  <!--
    A "Service" is a collection of one or more "Connectors" that share
       a single "Container" Note:  A "Service" is not itself a "Container",
       so you may not define subcomponents such as "Valves" at this level.
       Documentation at /docs/config/service.html
   -->


  <Service
    name = "Catalina">
    <!--
      The connectors can use a shared executor, you can define one or more named thread pools
    -->

    <!--
      <Executor
        name            = "tomcatThreadPool"
        namePrefix      = "catalina-exec-"
        maxThreads      = "150"
        minSpareThreads = "4"
      />
    -->

    <!--
      A "Connector" represents an endpoint by which requests are received
      and responses are returned. Documentation at :
      Java HTTP Connector: /docs/config/http.html
      Java AJP  Connector: /docs/config/ajp.html
      APR (HTTP/AJP) Connector: /docs/apr.html
      Define a non-SSL/TLS HTTP/1.1 Connector on port 8080
    -->

    <Connector
      port              = "8080"
      protocol          = "HTTP/1.1"
      connectionTimeout = "20000"
      redirectPort      = "8443"
    />

    <!--
      A "Connector" using the shared thread pool
    -->

    <!--
      <Connector
        executor             = "tomcatThreadPool"
        port="8080" protocol = "HTTP/1.1"
        connectionTimeout    = "20000"
        redirectPort         = "8443"
      />
    -->

    <!--
      Define a SSL/TLS HTTP/1.1 Connector on port 8443
      This connector uses the NIO implementation. The default
      SSLImplementation will depend on the presence of the APR/native
      library and the useOpenSSL attribute of the
      AprLifecycleListener.
      Either JSSE or OpenSSL style configuration may be used regardless of
      the SSLImplementation selected. JSSE style configuration is used below.
    -->

    <!--
      <Connector
        port       = "8443"
        protocol   = "org.apache.coyote.http11.Http11NioProtocol"
        maxThreads = "150"
        SSLEnabled = "true">

        <Certificate
          certificateKeystoreFile = "conf/localhost-rsa.jks"
          type                    = "RSA"
        />
      </Connector>
    -->


    <Connector
      port="8443"
      protocol     = "HTTP/1.1"
      SSLEnabled   = "true"
      scheme       = "https"
      secure       = "true"
      keystoreFile = "cert/214412416080589.jks"
      keystorePass = "abc123"
      clientAuth   = "false"
      SSLProtocol  = "TLSv1+TLSv1.1+TLSv1.2"
      ciphers      = "TLS_RSA_WITH_AES_128_CBC_SHA,TLS_RSA_WITH_AES_256_CBC_SHA,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256,TLS_RSA_WITH_AES_128_CBC_SHA256,TLS_RSA_WITH_AES_256_CBC_SHA256"
    />

    <!--
      Define a SSL/TLS HTTP/1.1 Connector on port 8443 with HTTP/2
      This connector uses the APR/native implementation which always uses
      OpenSSL for TLS.
      Either JSSE or OpenSSL style configuration may be used. OpenSSL style
      configuration is used below.
    -->

    <!--
      <Connector
        port       = "8443"
        protocol   = "org.apache.coyote.http11.Http11AprProtocol"
        maxThreads = "150"
        SSLEnabled = "true" >
        <UpgradeProtocol
          className="org.apache.coyote.http2.Http2Protocol"
        />
        <SSLHostConfig>
          <Certificate
            certificateKeyFile   = "conf/localhost-rsa-key.pem"
            certificateFile      = "conf/localhost-rsa-cert.pem"
            certificateChainFile = "conf/localhost-rsa-chain.pem"
            type                 = "RSA"
          />
        </SSLHostConfig>
      </Connector>
    -->


    <!--
      Define an AJP 1.3 Connector on port 8009
    -->

    <Connector
      port         = "8009"
      protocol     = "AJP/1.3"
      redirectPort = "8443"
    />

    <!--
      An Engine represents the entry point (within Catalina) that processes
      every request.  The Engine implementation for Tomcat stand alone
      analyzes the HTTP headers included with the request, and passes them
      on to the appropriate Host (virtual host).
      Documentation at /docs/config/engine.html
    -->

    <!--
      You should set jvmRoute to support load-balancing via AJP ie :
      <Engine
        name        = "Catalina"
        defaultHost = "localhost"
        jvmRoute    = "jvm1">
    -->

    <Engine
      name        = "Catalina"
      defaultHost = "localhost">

      <!--
        For clustering, please take a look at documentation at:
        /docs/cluster-howto.html  (simple how to)
        /docs/config/cluster.html (reference documentation)
      -->

      <!--
        <Cluster className="org.apache.catalina.ha.tcp.SimpleTcpCluster"
        />
      -->



      <!--
        Use the LockOutRealm to prevent attempts to guess user passwords
        via a brute-force attack
      -->

      <Realm className="org.apache.catalina.realm.LockOutRealm">
        <!--
          This Realm uses the UserDatabase configured in the global JNDI
          resources under the key "UserDatabase".  Any edits
          that are performed against this UserDatabase are immediately
          available for use by the Realm.
        -->
        <Realm
          className    = "org.apache.catalina.realm.UserDatabaseRealm"
          resourceName = "UserDatabase"
        />
      </Realm>

      <Host
        name       = "localhost"
        appBase    = "webapps"
        unpackWARs = "true"
        autoDeploy = "true">

        <Valve
           className      = "org.apache.catalina.valves.RemoteIpValve"
           remoteIpHeader = "X-Forwarded-For"
           protocolHeader = "X-Forwarded-Proto"
        />
      
      </Host>
    </Engine>
  </Service>
</Server>
```

重启 Tomcat 服务器，再次访问 APEX，烦人的重定向问题终于得以解决。

![](https://ws1.sinaimg.cn/large/006By2pOgy1fsl2siybinj31ja12c43y.jpg)

## 结语
用 HTTPS 协议来安全地访问你的 APEX 应用，这一点特别是对企业应用特别重要，相信你现在已经掌握了如何在 APEX 上全站启用 SSL 证书。本文如有遗漏或不足的地方也请随时跟钢哥交流，让我们共同学习，共同进步！

