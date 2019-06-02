title: Java EE 笔记
author: zansy
tags: []
categories:
  - 读薄专业书计划
date: 2018-04-03 03:16:00
---
学习笔记，未完待续。
<!--more-->
## 关于Java EE
Java 平台企业版。

Java EE 应用程序的模型框架分为：
- Domain Object（领域对象）层：由系列的普通Java对象组成，往往包含了各自所需要的逻辑方法
- DAO（Data Access Object，数据访问对象）层：由系列的DAO组件组成，实现了对数据库的查询、更新、删除和创建等原子操作。
- 业务逻辑层：实现了系统所需的业务逻辑方法（JavaBean/EJB）
- 控制层：拦截用户请求，并调用业务逻辑组件的业务逻辑方法，处理用户请求，并根据处理结果转发到不同的表现层组件（Servlet、监听器、拦截器）
- 表现层：主要负责收集用户请求，并显示处理结果。（HTML/JS/CSS/JSP）

### Java EE 环境配置
1. [下载JDK程序包](http://www.oracle.com/technetwork/java/javase/downloads/index.html)（Java 开发工具包，包含了运行环境和开发工具及基础类库）并安装
2. 设置JDK环境变量
    - 我的电脑-属性-系统属性-高级-环境变量；在“系统变量”中找到JAVA_HOME变量，没有则新建并设置变量值为JDK安装路径`C:\Program Files\Java\jdk1.8.0_162`；
    - 设置“系统变量”中的Path值，添加`;%JAVA_HOME%\bin;%JAVA_HOME%\lib;`
    - 添加classPath变量，设置其值`%JAVA_HOME%\lib;`
3. 进入cmd命令行洁面，输入javac查看是否安装并设置成功
4. [下载安装Tomcat](http://tomcat.apache.org)（支持Web程序运行的服务器，Tomcat是一个Servlet容器）
5. 配置Tomcat
    - 计算机→属性→高级系统设置→高级→环境变量；系统变量中添加TOMCAT_HOME变量，变量值`C:\apache-tomcat-8.5.29`
    - 新建CATALINA_HOME变量，变量值`C:\apache-tomcat-8.5.29`
    - 修改变量Path，变量值后添加`;%CATALINA_HOME%\bin;%CATALINA_HOME%\lib`
6. 在cmd窗口中输入`startup`回车，在浏览器地址栏中输入http://localhost:8080，如果打开Tomcat的默认主页，表示安装成功
7. 设置工作编码方式
    - Window->Preferences->General->Workspace->Text file encoding 选择UTF-8
    - Window->Preferences->Web->JSP Files 选择UTF-8
8. 新建Server：打开右下方的Servers视图，右击添加新Server
9. 新建工程->新建文件编写代码
10. 启动服务器：Servers视图右上角运行按钮
11. 添加并发布项目：在Servers窗口下右击选择Add and Remove。选择Publish命令可发布项目；从服务器移走已部署发布的项目：右击选择Clean命令

## Java EE Web应用开发基础技术

### 表示层技术
HTML/CSS/JavaScript/JSP/EL表达式

### 控制层技术

#### Servlet
Servlet可以用来处理客户端传来的HTTP请求，并返回响应。

#### 过滤器
过滤器Fliter是一种特殊的Servlet技术，对用户的请求信息和响应信息进行过滤，常被用于权限检查和参数编码统一设置等。

#### 监听器
监听器Listener可以监听Web应用的上下文信息、Servlet请求信息和Servlet回话信息，并自动根据不同情况，在后台调用相应的处理程序。
根据监听对象的不同，监听器可以划分为3种：
- ServletContext事件监听器：用于监听应用程序环境对象。
- HttpSession事件监听器：用于监听用户会话对象。
- ServletRequest事件监听器：用于监听请求消息对象。

### 业务逻辑层技术
由JavaBean实现。JavaBean是JavaWeb程序的重要组件，它是一些封装了数据和操作的功能类，供JSP或Servlet调用，完成数据封装和数据处理等功能。
设计JavaBean就是编写Java类，通常遵守以下规则：
- 是一个公共类
- 具有一个公共的无参的构造方法
- 所有的属性均定义为私有
- 需要对每个属性提供两个公共方法：getter/setter

### 持久层技术
由JDBC技术实现。在Java Web 应用程序中，数据库访问是通过Java数据库连接实现的

### MVC和DAO设计模式
MVC设计模式：用JSP设计页面，用Servlet实现控制逻辑，用JavaBean实现业务逻辑处理，把一个系统的功能分为三种不同类型的组件。
DAO设计模式：把对数据库表的操作转化为对Java类的操作。

## Strut 2 框架基础
Struts 2是基于MVC模式的Web框架，主要包括：
1. 模型组件：实现业务逻辑的模块，主要由JavaBean组成
2. 视图组件：主要有HTML/JSP/Struts 2标签 等视图技术
3. 控制器组件：主要由核心控制器和业务控制器Action组成

### 搭建 Struts 2开发环境
- [下载Struts 2(2.3.24-all-zip)文件](https://struts.apache.org/download.cgi#struts2516)
- 将其中 apps/struts2-blank/WEB_INF/lib 下所有jar包放到 当前Eclipse（Version: Oxygen.3 Release (4.7.3)）工程中->WebContent->WEB-INF->lib 中。
- 复制 apps/struts2-blank/WEB_INF/web.xml 文件中的过滤器配置到 当前Eclipse工程中->WebContent->WEB-INF->web.xml 中。
```
    <filter>
        <filter-name>struts2</filter-name>
        <filter-class>org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter</filter-class>
    </filter>

    <filter-mapping>
        <filter-name>struts2</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
```
- 复制  apps/struts2-blank/WEB_INF/classes 下的struts.xml文件到 当前Eclipse工程中->JavaResources->src 中。
```
<struts>
    <constant name="struts.enable.DynamicMethodInvocation" value="false" />
    <constant name="struts.devMode" value="true" />

    <package name="default" namespace="/" extends="struts-default">
		<action name="optriangular" class="com.action.IsTriangular" method="executeIs">
			<result name="yes">positive.jsp</result>
			<result name="no">input.jsp</result>
		</action>
    </package>

    <include file="example.xml"/>
    <!-- Add packages here -->
</struts>
```