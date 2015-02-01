---
layout: post
title: "Spring+Log4j在项目目录中生成log"
date: 2014-04-08 11:29:24 +0800
comments: true
categories: J2EE
---
>Log4j由三个重要的组件构成：Loggers，Appenders和Layouts，分别表示：日志信息的优先级，日志信息的输出目的地，日志信息的输出格式。支持key=value格式设置或xml格式设置。

* 日志信息的优先级从高到低有FATAL、ERROR、WARN、INFO、DEBUG，分别用来指定这条日志信息的重要程度；

* 日志信息的输出目的地指定了日志将打印到控制台还是文件中；

*  而输出格式则控制了日志信息的显示内容。


>log4j.properties配置文件示例  注意这句 :log4j.appender.file.File=${webApp.root}/WEB-INF/logs/app.log

<pre><code>
log4j.rootCategory=INFO, stdout,file 

###. 定义名为 stdout 的输出端的类型 
log4j.appender.stdout=org.apache.log4j.ConsoleAppender 
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout 
log4j.appender.stdout.layout.ConversionPattern=[QC] %p [%t] %C.%M(%L) | %m%n 

###. 定义名为 file 的输出端的类型为每天产生一个日志文件。 
log4j.appender.file =org.apache.log4j.DailyRollingFileAppender 
log4j.appender.file.File=${webApp.root}/WEB-INF/logs/app.log
log4j.appender.file.layout=org.apache.log4j.PatternLayout 
log4j.appender.file.layout.ConversionPattern=%d-[TS] %p %t %c - %m%n 
###. 指定 com.neusoft 包下的所有类的等级为 DEBUG 。可以把 com.neusoft 改为自己项目所用的包名。 
log4j.logger.com.neusoft=DEBUG 

###. 如果项目中没有配置 EHCache ，则配置以下两句为 ERROR 。 
log4j.logger.com.opensymphony.oscache=ERROR 
log4j.logger.net.sf.navigator=ERROR 

### struts 配置 
log4j.logger.org.apache.commons=ERROR 
log4j.logger.org.apache.struts=WARN 
###也可以统一写成如下，当然了，如果想细分的话，当然上面比较好点
#log4j.logger.org.apache=ERROR

### displaytag 配置 
log4j.logger.org.displaytag=ERROR 

### .spring 配置 
### log4j.logger.org.springframework=DEBUG 

### . ibatis 配置 
log4j.logger.com.ibatis.db=WARN
 
### . hibernate 配置 
log4j.logger.org.hibernate=DEBUG 
</code></pre>

>下面是在web.xml中配置Spring的监听器

 <code>
 
	<context-param>
		<param-name>webAppRootKey</param-name>
		<param-value>webApp.root</param-value>
	</context-param>
	<context-param>
		<param-name>log4jConfigLocation</param-name>
		<param-value>classpath:log4j.properties</param-value>
	</context-param>
	<listener>
		<listener-class>org.springframework.web.util.Log4jConfigListener</listener-class>
	</listener>
</code> 

>把监听器放在加载applicationContext.xml的前面 ok.Have fun with Spring and Log4j!