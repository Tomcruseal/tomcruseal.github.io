---
layout: post
title: Openldap cas SSO配置
categories: [Java]
tags: []
fullview: true
comments: true
---
上周配置CAS和OpenL搭配，直到今天才基本完成，由于各种教程都与实际情况有些许区别，故记录如下  
本记录整理自两位师兄给出的配置文件，结合实际情况和官方文档、各种博客做了修改。  
`系统环境：Ubuntu 12.06 64位，tomcat-8.0.33，JDK 1.8.0_91`  
**openldap的安装和配置**  
OpenLDAP Software is an open source implementation of the **Lightweight Directory Access Protocol**.  
The suite includes:
+ slapd - stand-alone LDAP daemon (server)
+ libraries implementing the LDAP protocol, and
+ utilities, tools, and sample clients.  
首先，`sudo apt-get install slapd ldap-utils`安装软件包，安装完成后编辑配置文件`/etc/ldap/ldap.conf`，修改后的内容如下：  
{% highlight conf %}
#
# LDAP Defaults
#

# See ldap.conf(5) for details
# This file should be world readable but not world writable.

BASE	dc=test,dc=com
URI	ldap://test.com ldap://test.com:666

#SIZELIMIT	12
#TIMELIMIT	15
#DEREF		never

# TLS certificates (needed for GnuTLS)
TLS_CACERT	/etc/ssl/certs/ca-certificates.crt
{% endhighlight %}
  
编辑完成后保存，执行以下命令：
`sudo dpkg-reconfigure slapd`  
该命令是配置生效ldap,而以往的版本有一个配置文件`/etc/openldap/slapd.d/`.接下来会弹出一系列窗口，根据情况选择。  
接下来，创建组和用户--在`/home/kim`下创建`test.com.ldif  
这里各组成部分的意思是：  
{% highlight apacheconf %}
dc:domian component
dn:distinguished name
ou:organizatin unit
cn:common name
sn:surname
uid:userid
{% endhighlight %}  
更多解释[详见](http://www.cnblogs.com/SkyMouse/archive/2010/10/25/2340750.html)  
添加：`dapadd -x -D cn=admin,dc=test,dc=com -W -f test.com.ldif`  
查询：`ldapsearch -h test.com -x -LLL -W -D cn=admin,dc=test,dc=com -b dc=test,dc=com`  
输入`ldapsearch -x`查看添加的用户和组。  
**配置CAS**  
CAS认证中心需要的用户名、密码将存储在LDAP中，为了集成LDAP，首先需要下载jar包：**cas-server-support-ldap-3.5.2.jar**和**spring-ldap-1.3.1.RELEASE-all.jar**，放置于`webapps/cas/WEB-INF/lib`目录下。  
CAS与LDAP集成有FastBindLdapAuthenticationHandler和BindLdapAuthenticationHandler两种接口，这里使用BindLdapAuthenticationHandler接口  
在WEB-INFO/deployerConfigContext.xml添加如下ContextSource：
{% highlight xml  %}
	<bean id="contextSource" class="org.springframework.ldap.core.support.LdapContextSource">
	  <!-- DO NOT enable JNDI pooling for context sources that perform LDAP bind operations. -->
	  <property name="pooled" value="false"/>

	  <!--
	    Although multiple URLs may defined, it's strongly recommended to avoid this configuration
	    since the implementation attempts hosts in sequence and requires a connection timeout
	    prior to attempting the next host, which incurs unacceptable latency on node failure.
	    A proper HA setup for LDAP directories should use a single virtual host that maps to multiple
	    real hosts using a hardware load balancer.
	  -->
	  <property name="url" value="ldap://test.com" />

	  <!--
	    Manager credentials are only required if your directory does not support anonymous searches.
	    Never provide these credentials for FastBindLdapAuthenticationHandler since the user's
	    credentials are used for the bind operation.
	  -->
	  <property name="userDn" value="cn=admin,dc=test,dc=com"/>
	  <property name="password" value="*******"/>

	  <!-- Place JNDI environment properties here. -->
	  <property name="baseEnvironmentProperties">
	    <map>
	      <!-- Three seconds is an eternity to users. -->
	      <entry key="com.sun.jndi.ldap.connect.timeout" value="3000" />
	      <entry key="com.sun.jndi.ldap.read.timeout" value="3000" />

	      <!-- Explained at http://docs.oracle.com/javase/jndi/tutorial/ldap/security/auth.html -->
	      <entry key="java.naming.security.authentication" value="simple" />
	    </map>
	  </property>
	</bean>
{% endhighlight %}
如果LDAP Server开启了SSL，那么需要将`ldap://test.com`修改为`ldaps://test.com`，可以通过运行命令`netstat -ln`查看端口，如果只开启了389端口，就使用`ldap`，如果端口636开启，则使用`ldaps`。经过查看，
本机没有开启636端口，故使用`ldap`.  
替换`deployerConfigContext.xml`中的`SimpleTestUsernamePasswordAuthenticationHandler`为：
{% highlight xml linenos %}
<bean class="org.jasig.cas.adaptors.ldap.BindLdapAuthenticationHandler"
    p:filter="uid=%u"
    p:searchBase="ou=People,dc=test,dc=com"
    p:contextSource-ref="contextSource" />
{% endhighlight %}
到目前为止，我没有替换，于是登陆CAS的时候只需要用户名和密码相同即可通过验证。  
**创建证书**  
使用`JDK`自带的`keytool`工具生成证书：
`keytool -genkey -alias tomcatsso -keyalg RSA -keysize 1024 -keypass password -validity 365 -keystore /home/kim/tomcatsso.keystore  -storepass password`

* `keytool`将密钥`(key)`和证书`(certificates)`保存在一个称为`keystore`的文件
* `-alias`：产生别名，每个`keystore`都关联这个独一无二的`alias`，通常不区分大小写
* `-keyalg`：指定密钥的算法
* `-keysize`：指定密钥的长度
* `-keypass`：指定别名条目的密码(私钥的密码)
* `-validity`：指定创建的证书有效期多少天(默认`90`)
* `-keystore`：指定密钥库的名称(产生的各类信息将不在`.keystore`文件中)
* `-storepass`：指定密钥库的密码(获取`keystore`信息所需的密码)

**导出证书**  
`keytool -export -alias tomcatsso -keystore /home/kimtomcatsso.keystore -file /home/kim/tomcatsso.crt -storepass tomcatsso`
生成的证书文件为`tomcatsso.crt`，位于运行该命令所在的当前目录下。  
**客户端导入证书**  
将目录切换到`%JAVA_HOME%/jre/lib/security`下，执行命令：
`keytool -import -keystore cacerts -file /home/kim/tomcatsso.crt -alias tomcatsso`（这里前后别名应保持一致）  
在本机测试时，本机上一共有3个tomcat server同时运行（1个cas server,2 个客户端）,为了使它们互相不产生冲突，可依次改变`catalina.sh`中的变量`CATALINA_HOME`,如`CATALINA_HOME1`等等。  
**部署CAS-Server相关的Tomcat**  
解压`apache-tomcat-8.0.33.zip`并重命名后的路径为`/home/kim/tomcat-cas`，在文件`conf/server.xml`中找到：
{% highlight xml  %}
    <!--
    <Connector port="8443" protocol="org.apache.coyote.http11.Http11Protocol"
       maxThreads="150" SSLEnabled="true" scheme="https" secure="true"
       clientAuth="false" sslProtocol="TLS" />
    -->
{% endhighlight %}

去掉注释，修改为如下：
{% highlight xml  %}
     <Connector port="8443" protocol="org.apache.coyote.http11.Http11Protocol"
       maxThreads="150" SSLEnabled="true" scheme="https" secure="true"
       keystoreFile="/home/kim/tomcatsso.keystore" keystorePass="password"
       clientAuth="false" sslProtocol="TLS" />
{% endhighlight %}
解压提取`cas-server-3.5.2-release/cas-server-3.5.2/modules/cas-server-webapp-3.5.2.war`文件，把该文件拷贝到`/home/kim/tomcat-cas/tomcat-cas/webapps/` 目下，并重命名为：`cas.war`。  
启动`tomcat-cas`，在浏览器地址栏输入：`https://localhost:8443/cas/login` 。输入相同的用户名和密码，即可登陆成功。  
**部署CAS-Client相关的Tomcat**  

**安装配置 myapp1**  
解压`cas-client-3.2.1-release.zip`，提取`cas-client-3.2.1-release/cas-client-3.2.1/modules/cas-client-core-3.2.1.jar`文件，以`tomcat`自带的`examples`作为测试样例。

解压`apache-tomcat-8.0.33.zip`并重命名后的路径为`/home/kim/download/myapp1`，修改`conf/server.xml`文件的`http`访问端口，`shutdown`端口，以及`JVM`启动端口：
{% highlight xml  %}
    <Server port="18005" shutdown="SHUTDOWN">
    ...
    <Connector port="18080" protocol="HTTP/1.1"
                   connectionTimeout="20000"
                   redirectPort="18443" />
    ...
    <Connector port="18009" protocol="AJP/1.3" redirectPort="18443" />
{% endhighlight %}
把`cas-client-core-3.2.1.jar`和`commons-logging-1.1.1.jar`复制到`/home/kim/download/myapp1/webapps/examples/WEB-INF/lib`目录下。
修改`web.xml`，在`web.xml`末尾添加以下内容：  
{% highlight xml  %}
    <!-- ======================== 单点登录开始 ======================== -->
        <filter>
          <filter-name>CAS Filter</filter-name>
          <filter-class>org.jasig.cas.client.authentication.AuthenticationFilter</filter-class>
          <init-param>
            <param-name>casServerLoginUrl</param-name>
            <param-value>https://demo.test.com:8443/cas/login</param-value>
          </init-param>
          <init-param>
            <param-name>serverName</param-name>
            <param-value>http://myapp1.test.com:18080</param-value>
          </init-param>
        </filter>
        <filter-mapping>
          <filter-name>CAS Filter</filter-name>
          <url-pattern>/*</url-pattern>
        </filter-mapping>
        <!-- 该过滤器负责对Ticket的校验工作，必须启用它 -->
        <filter>
          <filter-name>CAS Validation Filter</filter-name>
          <filter-class>
            org.jasig.cas.client.validation.Cas20ProxyReceivingTicketValidationFilter</filter-class>
          <init-param>
            <param-name>casServerUrlPrefix</param-name>
            <param-value>https://demo.test.com:8443/cas</param-value>
          </init-param>
          <init-param>
            <param-name>serverName</param-name>
            <param-value>http://myapp1.test.com:18080</param-value>
          </init-param>
        </filter>
        <filter-mapping>
          <filter-name>CAS Validation Filter</filter-name>
          <url-pattern>/*</url-pattern>
        </filter-mapping>
     
    <!-- ======================== 单点登录结束 ======================== -->  
{% endhighlight %}
由于目前只测试单点登录功能，未测试单点登出，所以省略掉单点登出的相关配置，特别重要的是，根据官网文档的提示，如果没有`authentication`的过滤器，跳转将会报错另外还需。
要特别注意的是`<param-value>`里`url`的写法.  
类似地，配置myapp2,不过tomcat-server应使用不同的端口号以避免冲突。  
**测试**    
先后启动CAS-server,myapp1,myapp2,再在浏览器中输入`http://myapp1.test.com:18080/examples/servlets/servlet/HelloWorldExample ` 浏览器跳转到cas服务器，输入与之前相同的
用户名和密码后，立刻出现"Hello World!".再在浏览器中输入` http://myapp2.test.com:28080/examples/servlets/servlet/HelloWorldExample `，此时没出现跳转即可看到"Hello World!"  


参考：  
1. [web.xml的配置说明](https://wiki.jasig.org/display/CASC/Configuring+the+Jasig+CAS+Client+for+Java+in+the+web.xml)  
2. [cas-client](https://github.com/Jasig/java-cas-client)  
3. [a blog about tomcat cas configurations](https://www.howtoforge.com/how-to-set-up-apache-tomcat-mod_jk-sso-cas-mod_auth_cas)  
4. [java拦截器、过滤器的介绍](http://blog.csdn.net/lzwjavaphp/article/details/13771109)  
5. [tomcat cas 的配置](http://www.ibm.com/developerworks/cn/opensource/os-cn-cas/index.html)虽然有些老了，不过挺详细  
6. [ldif文件的详细说明](http://www.cnblogs.com/SkyMouse/archive/2010/10/25/2340750.html)  
7. [多个tomcat同时运行](http://blog.163.com/hegonghe@126/blog/static/50004810200871483335100/)  
8. [intellij的使用](http://jingyan.baidu.com/article/54b6b9c0f79b0c2d583b47d6.html)  


