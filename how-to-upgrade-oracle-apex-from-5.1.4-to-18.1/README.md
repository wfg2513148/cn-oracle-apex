# How to upgrade Oracle APEX from 5.1.4 to 18.1

<!-- TOC depthFrom:2 insertAnchor:true -->

- [Introduction](#Introduction)
- [Preparation](#Preparation)
  - [Generate Configuration](#Upload-installation-media)
  - [Upgrade JDK/JRE 1.8](#Upgrade-JDK/JRE-1.8)
  - [Upgrade Tomcat Server](#Upgrade-Tomcat-Server)
- [Upgrade APEX 18.1](#Upgrade-APEX-18.1)
  - [Execute installation of APEX 18.1](#Execute-installation-of-APEX-18.1)
  - [Copy apex static files to tomcat folder](#copy-apex-static-files-to-tomcat-folder)
- [Upgrade ORDS 18.1](#Upgrade-ORDS-18.1)
  - [Execute installation of ORDS](#Execute-installation-of-ORDS)
  - [Deploy ords.war to Tomcat](#deploy-ords.war-to-Tomcat)
  - [Validate ORDS deployment](#Validate-ORDS-deployment)
- [Nginx Configiration (optional)](#Nginx-Configiration-(optional))
- [Test your environment](#Test-your-environment)
- [Conclution](#Conclution)



<!-- /TOC -->

![](https://ws1.sinaimg.cn/large/006By2pOgy1frsj7crfv1j30mu0dw75b.jpg)

Original blog: https://wangfanggang.com/Oracle/Oracle-APEX/how-to-upgrade-apex-from-5-1-4-to-18-1/

## Introduction
Oracle APEX 18.1 has already been published several days, I believe most of apexers had tried it. The next thing we need to do is how to upgrade Oracle APEX from earlier version (such as 5.1.4) to the latest 18.1. 

> I quickly go through Oracle APEX 18.1 installation guide but not find a upgrade script I could use (even if there is also a sql named `apxpatch.sql`, I didn't try it yet). Since the new APEX (db) schema is `APEX_180100`, I guess I have to reinstall APEX 18.1 this time. 
> The following upgrade steps are based on CentOS 6, Oracle database is 11gR2 and APEX 5.1.4. 
> The wonderful part I find out is I don't have to reinstall my existed APEX applications after I upgrade APEX 18.1. All my data is still there (workspace, application and so on) and I could continue to use them painlessly. Thanks to what Oracle APEX team did!!
> Here is a component list we could upgrade: 
* `APEX`：we will upgrade from earlier version to the latest 18.1；
* `ORDS`：we will also upgrade it to the latest 18.1, and re-config and deploy to tomcat;
* `JAVA`：ORDS 18.1 requires the lowest version of JDK/JRE is 1.8 or above；
* `Tomcat`：ORDS 18.1 requires the lowest version of tomcat server is 8.5 or above；
* `Nignx`：we need to adjust some configurations in nginx.conf if necessary；

OK, let's begin the upgrade process. 


## Preparation
### Upload installation media
I uploaded all installation media files to `/u02/Media`.
![](https://ws1.sinaimg.cn/large/006By2pOgy1frs13zjztij30t005sdgx.jpg)

Shut down nginx and tomcat services.
```bash
## shutdown nginx service
service nginx stop

## shutdown tomcat service
service tomcat stop
```

### Upgrade JDK/JRE 1.8
Use command `java -version` to check the current version of JDK. Per below picture, we can see the current version of JDK is 1.7. We have to upgrade to 1.8 or above to meet the requirement of ORDS 18.1. 

![](https://ws1.sinaimg.cn/large/006By2pOgy1frs2ohvrgkj30pi03uq3o.jpg)

JDK 1.8 Installation
```bash
yum install -y java-1.8.0-openjdk.x86_64 java-1.8.0-openjdk-devel.x86_64
```

We can update some values in `~/.bash_profile`. 


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



Manually initialize environment variables. 
```bash
source ~/.bash_profile

## Check java version again
java -version
```

We find out the version of JDK is 1.8 now. 
![](https://ws1.sinaimg.cn/large/006By2pOgy1frs5ctbevrj30m404sgmk.jpg)


### Upgrade Tomcat Server
I checked my tomcat server and found out it was version 7. I have to upgrade it to version 8.5 or above to meet requirements of ORDS 18.1. 

Download installation media of Tomcat server 8.5
```bash
cd /u02/Media

wget http://mirrors.shu.edu.cn/apache/tomcat/tomcat-8/v8.5.31/bin/apache-tomcat-8.5.31.zip

unzip apache-tomcat-8.5.31.zip
mv apache-tomcat-8.5.31/* /u01/tomcat8

chown -Rf tomcat:tomcat /u01/tomcat8
chmod -Rf 755 /u01/tomcat8/bin/*
```


Since I use CentOS 6, I have to adjust some values of `/etc/init.d/tomcat`.
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

start tomcat service and validate it.
```bash
service tomcat start
```

We can see the tomcat 8.5 is started and the JDK we used is 1.8 now.
![](https://ws1.sinaimg.cn/large/006By2pOgy1frs857xbcfj30ym07mmyy.jpg)


Open your web explorer and type http://xxx.xxx.xxx.xxx:8080，you should also see the wellcome page of tomcat. 
![](https://ws1.sinaimg.cn/large/006By2pOgy1frs88lo5wnj30sa0q7qah.jpg)



## Upgrade APEX 18.1
```bash
cd /u02/Media/

unzip apex_18.1.zip
```

![](https://ws1.sinaimg.cn/large/006By2pOgy1frs1kjzlhrj30vu04yt9t.jpg)

### Execute installation of APEX 18.1
```bash
su - oracle

cd /u02/Media/apex
```

```sql
sqlplus / as sysdba

-- recommend to create new tablespace for APEX
SQL> create tablespace APEX181 DATAFILE '/u03/oradata/apex181.dbf' SIZE 1024m REUSE AUTOEXTEND ON NEXT 100M MAXSIZE UNLIMITED;

-- the whole installation of APEX 18.1 might last 5-10 minutes.
-- here APEX181 is the new tablespace we created just now
SQL> @apexins APEX181 APEX181 temp /i/

-- reset password of apex administration
SQL> @apxchpwd

-- config RESTful Services
SQL> @apex_rest_config.sql

-- disable embeded PL/SQL gateway since we will use ORDS later
SQL> exec dbms_xdb.sethttpport(0);
SQL> exec dbms_xdb.setftpport(0);

-- unlock ORDS user account
SQL> alter user apex_public_user account unlock;
SQL> alter user apex_public_user identified by "your password";

SQL> exit
```

![](https://ws1.sinaimg.cn/large/006By2pOgy1frs2ev4a38j30wo0ayq4k.jpg)

### Copy apex static files to tomcat folder
```bash
su - root

mkdir -p /u01/tomcat8/webapps/i/
cp -a /u02/Media/apex/images/* /u01/tomcat8/webapps/i/

chown -Rf tomcat:tomcat /u01/tomcat8/webapps/
```


## Upgrade ORDS 18.1
```bash
mkdir -p /u01/ords
unzip /u02/Media/ords.18.1.1.95.1251.zip -d /u01/ords/
```

### Execute installation of ORDS
```bash
cd /u01/ords

java -jar ords.war install advanced
```

![](https://ws1.sinaimg.cn/large/006By2pOgy1frsd0gp3suj31p40x2wn0.jpg)

grant read privilege to make sure tomcat user could read files in folder `/u01/ords/conf`
```bash
chown -R tomcat:tomcat /u01/ords/conf
```

### Deploy ords.war to Tomcat
```bash
cp -a /u01/ords/ords.war /u01/tomcat8/webapps/

chown -Rf tomcat:tomcat /u01/tomcat8/webapps/

## restart tomcat service
service tomcat restart
```

### Validate ORDS deployment
open your web explorer, navigate to http://xxx.xxx.xxx.xxx:8080/ords . If you could see apex page, congratulations!!
![](https://ws1.sinaimg.cn/large/006By2pOgy1frsdevwzfij30py0ljq42.jpg)



## Nginx Configiration (optional)
Review your nginx.conf and check if there are something need to be changed，such as path of static file folder `i`
![](https://ws1.sinaimg.cn/large/006By2pOgy1frsdq5ch53j30ey03i3yl.jpg)

## Test your environment
Open your web explorer again, navigate to http://xxx.xxx.xxx.xxx/ords . If you could see apex page, congratulations again!!
![](https://ws1.sinaimg.cn/large/006By2pOgy1frsdevwzfij30py0ljq42.jpg)

Login APEX Administration and switch to`Existed Workspace`，you will find all existed workspaces.
![](https://ws1.sinaimg.cn/large/006By2pOgy1frshxpz2k3j328013stf2.jpg)

Login APEX with your workspace, all applications are there. Perfect!!

![](https://ws1.sinaimg.cn/large/006By2pOgy1frsi1heqdcj328013s489.jpg)



## Conclution
Now you have successfully upgraded your earlier version of APEX to the latest version 18.1. 
