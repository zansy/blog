title: Spring实战笔记(2019.07.06 C2)
author: zansy
tags: []
categories:
  - 读薄专业书计划
date: 2019-07-08 23:32:03
---
Spring实战（第4版）：Spring 是一种轻量级的、开源的 Java EE应用软件框架，主要优势之一就是其分层架构。
<!--more-->

# 第1部分：Spring框架的核心知识

## 第一章

概要地介绍Spring，包括DI和AOP的一些基本样例。

- Spring的bean容器
- 介绍Spring的核心模块
- 更为强大的Spring生态系统
- Spring的新功能

### 1.1 简化 Java 开发

- 为了降低Java开发的复杂性，Spring采取了以下4种关键策略：
  - 基于POJO的轻量级和最小侵入性编程；
  - 通过依赖注入和面向接口实现松耦合；
  - 基于切面和惯例进行声明式编程；
  - 通过切面和模板减少样板式代码。

#### 基于POJO的最小侵入性编程

- Spring不会强迫你实现Spring规范的接口或继承Spring规范的类，相反，在基于Spring构建的应用中，它的类通常没有任何痕迹表明你使用了Spring。

- Spring的非侵入编程模型意味着**这个类在Spring应用和非Spring应用中都可以发挥同样的作用**。

#### 依赖注入

- *背景*：任何一个有实际意义的应用都会由两个或者更多的类组成，这些类相互之间进行协作来完成特定的业务逻辑。按照传统的做法，每个对象负责管理与自己相互协作的对象（即它所依赖的对象）的引用，这将会导致高度耦合和难以测试的代码。
- *改进*：通过DI，对象的依赖关系将由系统中负责协调各对象的第三方组件在创建对象的时候进行设定。**对象无需自行创建或管理它们的依赖关系，依赖关系将被自动注入到需要它们的对象当中去**。
- 依赖注入分为两个注入方式：

1. **构造器注入依赖关系**：

```java
public static void main(String[] args) {
    ClassB classB = new ClassB();
    ClassA classA = new ClassA(classB);
    classA.a();
}
```

2. **Setter方法注入依赖**：

```java
public static void main(String[] args) {
    ClassB classB = new ClassB();
    ClassA classA = new ClassA();
    classA.setClassB(classB);
    classA.a();
}
```

- DI所带来的最大收益——**松耦合**
- 该如何将`SlayDragonQuest`交给`BraveKnight`呢？又如何将`PrintStream`交给`SlayDragonQuest`呢？创建应用组件之间协作的行为（多个类之间的关联）通常称为**装配（wiring）**

```Java
package sia.knights;
import java.io.PrintStream;

public class SlayDragonQuest implements Quest {
  private PrintStream stream;
  public SlayDragonQuest(PrintStream stream) {
      this.stream = stream;
  }
  public void embark() {
      stream.println("Embarking on quest to slay the dragon!");
  }
}
```

```Java
package sia.knights;
  
public class BraveKnight implements Knight {
  private Quest quest;
  public BraveKnight(Quest quest) {
      this.quest = quest;
  }
  public void embarkOnQuest() {
      quest.embark();
  }
}
```

- 配置Spring容器最常见的三种方法：
  - 在XML中进行显式配置。
  - 在Java中进行显式配置。
  - 隐式的bean发现机制和自动装配。
- 通过简单的Spring配置文件：knights.xml，该配置文件将`BraveKnight`、`SlayDragonQuest`和`PrintStream`装配到了一起。

```XML
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://www.springframework.org/schema/beans 
      http://www.springframework.org/schema/beans/spring-beans.xsd">

  <bean id="knight" class="sia.knights.BraveKnight">
    <constructor-arg ref="quest" />
  </bean>

  <bean id="quest" class="sia.knights.SlayDragonQuest">
    <constructor-arg value="#{T(System).out}" />
  </bean>

</beans>
```

#### 应用切面

- 面向切面编程（aspect-oriented programming，AOP）允许把遍布应用各处的功能分离出来形成可重用的组件。
- *背景：*系统由许多不同的组件组成，每一个组件各负责一块特定功能。诸如日志、事务管理和安全这样的系统服务经常融入到自身具有核心业务逻辑的组件中去，这些系统服务通常被称为横切关注点，因为它们会跨越系统的多个组件。<u>AOP能够使这些服务模块化，并以声明的方式将它们应用到它们需要影响的组件中去。</u>
- 可以把切面想象为覆盖在很多组件之上的一个外壳。应用是由那些实现各自业务功能的模块组成的。借助AOP，可以使用各种功能层去包裹核心业务层。这些层以声明的方式灵活地应用到系统中，你的核心应用甚至根本不知道它们的存在。
- 设置一个Minstrel（吟游诗人）类，该类有两个方法，一个在Knight启动embark方法前打印文字，另一个在启动embark方法后打印文字，如果要实现该类，如下代码可实现预期效果：

```Java
public class BraveKnight implements Knight {
  private Minstrel minstrel;
  private Quest quest;

  public BraveKnight(Quest quest, Minstrel minstrel) {
    this.quest = quest;
    this.minstrel = minstrel;
  }
  public void embarkOnQuest() {
    minstrel.singBeforeQuest();
    quest.embark();
    minstrel.singAfterQuest();
  }
}
```

但耦合过高，应该将Minstrel声明为一个切面：

```XML
<bean id="knight" class="sia.knights.BraveKnight">
  <constructor-arg ref="quest" />
</bean>

<bean id="quest" class="sia.knights.SlayDragonQuest">
  <constructor-arg value="#{T(System).out}" />
</bean>

<bean id="minstrel" class="sia.knights.Minstrel">
  <constructor-arg value="#{T(System).out}" />
</bean>

<aop:config>
  <aop:aspect ref="minstrel">
    <aop:pointcut id="embark" expression="execution(* *.embarkOnQuest(..))"/> <!--声明切点-->
      
    <aop:before pointcut-ref="embark" method="singBeforeQuest"/> <!--声明前置通知-->

    <aop:after pointcut-ref="embark" method="singAfterQuest"/> <!--声明后置通知-->
  </aop:aspect>
</aop:config>
```

首先，`Minstrel`仍然是一个POJO，没有任何代码表明它要被作为一个切面使用。当按照上面那样进行配置后，在Spring的上下文中，`Minstrel`实际上已经变成一个切面了。其次，也是最重要的，`Minstrel`可以被应用到`BraveKnight`中，而`BraveKnight`不需要显式地调用它。

#### 使用模板消除样板式代码

- Spring的**JdbcTemplate**使得执行数据库操作时，避免传统的JDBC样板代码成为了可能。

### 1.2 容纳Bean

- Spring容器负责创建对象，装配它们，配置它们并管理它们的整个生命周期，从生存到死亡（在这里，可能就是`new`到`finalize()`）。
- Spring容器并不是只有一个。Spring自带了多个容器实现，可以归为两种不同的类型。**bean工厂**（由`org.springframework.beans.factory.BeanFactory`接口定义）是最简单的容器，<u>提供基本的DI支持</u>。**应用上下文**（由`org.springframework.context.ApplicationContext`接口定义）基于BeanFactory构建，并<u>提供应用框架级别的服务</u>，例如从属性文件解析文本信息以及发布应用事件给感兴趣的事件监听者。

#### 使用应用上下文

- `AnnotationConfigApplicationContext：`从一个或多个基于Java的配置类中加载Spring应用上下文。
- `AnnotationConfigWebApplicationContext：`从一个或多个基于Java的配置类中加载Spring Web应用上下文。
- `ClassPathXmlApplicationContext：`从类路径下的一个或多个XML配置文件中加载上下文定义，把应用上下文的定义文件作为类资源。
- `FileSystemXmlapplicationcontext：`从文件系统下的一个或多个XML配置文件中加载上下文定义。
- `XmlWebApplicationContext：`从Web应用下的一个或多个XML配置文件中加载上下文定义。

应用上下文准备就绪之后，我们就可以调用上下文的`getBean()`方法从Spring容器中获取bean。

#### bean的生命周期

- 在传统的Java应用中：使用Java关键字new进行bean实例化，然后该bean就可以使用了。一旦该bean不再被使用，则由Java自动进行垃圾回收。

- 在Spring容器中：
  1. Spring对bean进行实例化；

  2. Spring将值和bean的引用注入到bean对应的属性中；

  3. 如果bean实现了`BeanNameAware`接口，Spring将bean的ID传递给`setBean-Name()`方法；

  4. 如果bean实现了`BeanFactoryAware`接口，Spring将调用`setBeanFactory()`方法，将BeanFactory容器实例传入；

  5. 如果bean实现了`ApplicationContextAware`接口，Spring将调用`setApplicationContext()`方法，将bean所在的应用上下文的引用传入进来；

  6. 如果bean实现了`BeanPostProcessor`接口，Spring将调用它们的`post-ProcessBeforeInitialization()`方法；

  7. 如果bean实现了`InitializingBean`接口，Spring将调用它们的`after-PropertiesSet()`方法。类似地，如果bean使用`init-method`声明了初始化方法，该方法也会被调用；

  8. 如果bean实现了`BeanPostProcessor`接口，Spring将调用它们的`post-ProcessAfterInitialization()`方法；

  9. 此时，bean已经准备就绪，可以被应用程序使用了，它们将一直驻留在应用上下文中，直到该应用上下文被销毁；

  10. 如果bean实现了`DisposableBean`接口，Spring将调用它的`destroy()`接口方法。同样，如果bean使用`destroy-method`声明了销毁方法，该方法也会被调用。

![bean在Spring容器中从创建到销毁经历了若干阶段](/images/image-20190706100646318.png)

### 1.3 俯瞰Spring风景线

![Spring框架由6个定义良好的模块分类组成](/images/image-20190706101127122.png)

## 第二章 装配Bean

更为详细地介绍DI，展现应用程序中的各个组件（bean）如何装配在一起。这包括基于XML装配、基于Java装配以及自动装配。

- <u>创建应用对象之间协作关系的行为</u>通常称为**装配（wiring）**，这也是依赖注入（DI）的本质。

### 2.2 自动化装配bean

Spring从两个角度来实现自动化装配：

- **组件扫描（component scanning）**：Spring会自动发现应用上下文中所创建的bean。
- **自动装配（autowiring）**：Spring自动满足bean之间的依赖。

#### 1. 创建可被发现的bean

`@Component`注解表明该类会作为组件类，并告知Spring要为这个类创建bean。

> 不过，组件扫描默认是不启用的。还需要显式配置一下Spring，从而命令它去寻找带有`@Component`注解的类。

`@ComponentScan`注解能够在Spring中启用组件扫描。

如果没有其他配置的话，`@ComponentScan`默认会扫描与配置类相同的包。Spring将会扫描这个包以及这个包下的所有子包，查找带有`@Component`注解的类，并且会在Spring中自动为其创建一个bean。

CompactDisc接口在Java中定义了CD的概念
```JAVA
package soundsystem;
public interface CompactDisc {
  void play();
}
```
带有@Component注解的CompactDisc实现类SgtPeppers
```Java
package soundsystem;
import org.springframework.stereotype.Component;
@Component
public class SgtPeppers implements CompactDisc {
  private String title = "Sgt. Pepper's Lonely Hearts Club Band";
  private String artist = "The Beatles";
  public void play() {
    System.out.println("Playing " + title + " by " + artist);
  }
}
```
@ComponentScan注解启用了组件扫描
```Java
package soundsystem;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
@Configuration
@ComponentScan
public class CDPlayerConfig {
}
```
测试组件扫描能够发现CompactDisc

```Java
package soundsystem;
import static org.junit.Assert.*;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes=CDPlayerConfig.class)
public class CDPlayerTest {
  @Autowired
  private CompactDisc cd;
  @Test
  public void cdShouldNotBeNull() {
    assertNotNull(cd);
  }
}
```

使用了Spring的`SpringJUnit4ClassRunner`，以便在测试开始的时候自动创建Spring的应用上下文。注解`@ContextConfiguration`会告诉它需要在`CDPlayerConfig`中加载配置。因为`CDPlayerConfig`类中包含了`@ComponentScan`，因此最终的应用上下文中应该包含`CompactDisc`bean。

为了证明这一点，在测试代码中有一个`CompactDisc`类型的属性，并且这个属性带有`@Autowired`注解，以便于将`CompactDisc`bean注入到测试代码之中。最后，会有一个简单的测试方法断言cd属性不为null。如果它不为null的话，就意味着Spring能够发现`CompactDisc`类，自动在Spring上下文中将其创建为bean并将其注入到测试代码之中。

#### 2. 为组件扫描的bean命名

Spring应用上下文中所有的bean都会根据类名为其指定一个ID。也就是将类名的第一个字母变为小写。

如果想为这个bean设置不同的ID，将期望的ID作为值传递给`@Component`注解。

```Java
@Component("lonelyHeartsClub")
public class SgtPeppers implements CompactDisc {
  ...
}
```

#### 3. 设置组件扫描的基础包

没有为@ComponentScan设置任何属性意味着，按照默认规则，它会以配置类所在的包作为基础包（base package）来扫描组件。如果想要将配置类放在单独的包中，使其与其他的应用代码区分开来，那默认的基础包就不能满足要求了。

为了指定不同的基础包，需要在`@ComponentScan`的value属性中指明包的名称：

```Java
@Configuration
@ComponentScan("soundsystem")//@ComponentScan(basePackages="soundsystem")
public class CDPlayerConfig {}
```

设置多个基础包

```java
@Configuration
@ComponentScan(basePackages={"soundsystem", "video"})
public class CDPlayerConfig {}
```

除了将包设置为简单的String类型之外，`@ComponentScan`还提供了另外一种方法，将其指定为包中所包含的类或接口：

```Java
@Configuration
@ComponentScan(basePackageClasses={CDPlayer.class, DVDPlayer.class})
public class CDPlayerConfig {}
```

#### 4. 通过为bean添加注解实现自动装配

**自动装配**就是让Spring自动满足bean依赖的一种方法，在满足依赖的过程中，会在Spring应用上下文中寻找匹配某个bean需求的其他bean。为了声明要进行自动装配，我们可以借助Spring的`@Autowired`注解。

`CDPlayer`类的构造器上添加了`@Autowired`注解，这表明当Spring创建`CDPlayer`bean的时候，会通过这个构造器来进行实例化并且会传入一个可设置给`CompactDisc`类型的bean。

通过自动装配，将一个CompactDisc注入到CDPlayer之中

```Java
package soundsystem;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
@Component
public class CDPlayer implements MediaPlayer {
  private CompactDisc cd;
  @Autowired
  public CDPlayer(CompactDisc cd) {
    this.cd = cd;
  }
  public void play() {
    cd.play();
  }
}
```

`@Autowired`注解不仅能够用在构造器上，还能用在属性的Setter方法上。比如说，如果`CDPlayer`有一个`setCompactDisc`()方法，那么可以采用如下的注解形式进行自动装配：

```Java
@Autowired
public void setCompactDisc(CompactDisc cd) {
  this.cd = cd;
}
```

`@Autowired`注解可以用在类的任何方法上。

如果没有匹配的bean，那么在应用上下文创建的时候，Spring会抛出一个异常。<u>为了避免异常的出现，可以将`@Autowired`的`required`属性设置为`false`。</u>将`required`属性设置为`false`时，如果没有匹配的bean的话，Spring将会让这个bean处于未装配的状态。

```Java
@Autowired(required=false)
public CDPlayer(CompactDisc cd) {
  this.cd = cd;
}
```

#### 5. 验证自动装配

```Java
package soundsystem;
import static org.junit.Assert.*;
import org.junit.Rule;
import org.junit.Test;
import org.junit.contrib.java.lang.system.StandardOutputStreamLog;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes=CDPlayerConfig.class)
public class CDPlayerTest {

  @Rule
  public final StandardOutputStreamLog log =
                          new StandardOutputStreamLog();

  @Autowired
  private MediaPlayer player;

  @Autowired
  private CompactDisc cd;

  @Test
  public void cdShouldNotBeNull() {
    assertNotNull(cd);
  }

  @Test
  public void play() {
    player.play();
    assertEquals(
        "Playing Sgt. Pepper's Lonely Hearts Club Band" +
        " by The Beatles\n",
        log.getLog());
  }
}
```



## 第三章

在掌握了基本的bean装配后，会介绍几种高级装配技术，了解如何发挥Spring容器最强大的威力

## 第四章

介绍如何使用Spring的AOP来为对象解耦那些对其提供服务的横切性关注点。这一章也为后面各章提供基础，在后面读者将会使用AOP来提供声明式服务，如事务、安全和缓存。

# 第2部分：使用Spring构建Web应用程序

## 第五章

介绍使用Spring MVC的基础知识，这是Spring中的基础Web框架。将会看到如何编写控制器来处理请求，并使用模型数据产生响应。

## 第六章

当控制器的工作完成后，模型数据必须要使用一个视图来进行渲染。第6章将会探讨在Spring中可以使用的各种视图技术，包括JSP、Apache Tiles以及Thymeleaf

## 第七章

如何自定义Spring MVC配置、处理multipart类型的文件上传、处理在控制器中可能会出现的异常并且会通过flash属性在请求之间传递数据。

## 第八章

介绍Spring Web Flow，这是Spring MVC的一个扩展，能够开发会话式的Web应用程序。学习如何构建引导用户完成特定流程的Web应用程序。

## 第九章

如何使用Spring Security为自己的应用程序Web层实现安全性。

# 第3部分：在应用程序的后端使用Spring

如何处理和持久化数据。

## 第十章

如何使用Spring对JDBC的抽象实现关系型数据库中的数据持久化。

## 第十一章

从另外一个角度介绍数据持久化，也就是使用Java持久化API（JPA）存储关系型数据库中的数据。

## 第十二章

如何将Spring与非关系型数据库结合使用，如MongoDB和Neo4j。

## 第十三章

介绍Spring对声明式缓存的支持。不管数据存储在什么地方，缓存都有助于性能的提升，这是通过只有在必要的时候才去查询数据库实现的。

## 第十四章

重新回到Spring Security，将会介绍如何通过AOP将安全性应用到方法级别。

# 第4部分：使用Spring与其他进行集成

## 第十五章

如何创建与使用远程服务，包括RMI、Hessian、Burlap以及基于SOAP的服务。

## 第十六章

再次回到Spring MVC，我们将会看到如何创建RESTful服务，在这个过程中所使用的编程模型与之前在第5章中所描述的是一致的。

## 第十七章

探讨Spring对异步消息的支持，将会包括Java消息服务（Java Message Service，JMS）以及高级消息队列协议（Advanced Message Queuing Protocol，AMQP）。

## 第十八章

如何将Spring与WebSocket和STOMP结合起来，实现服务端与客户端之间的异步通信。

## 第十九章

介绍如何使用Spring发送E-mail。

## 第二十章

关注于Spring对Java管理扩展（Java Management Extensions，JMX）功能的支持，借助这项功能可以对Spring应用程序进行监控和修改运行时配置。

## 第二十一章

介绍Spring Boot如何将Spring应用中样板式的配置移除掉，这样就能让读者更加专注于业务功能。

