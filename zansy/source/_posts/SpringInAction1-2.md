title: 'Spring 实战（第4版）笔记及补充[1-2]'
author: zansy
tags: []
categories:
  - 读薄专业书计划
date: 2019-03-04 17:14:00
---
Spring 是一种轻量级的、开源的 Java EE 应用软件框架，主要优势之一就是其分层架构。
<!--more-->
## 概述
- 两个核心特性，也就是依赖注入（dependency injection，DI）和面向切面编程（aspect-oriented programming，AOP）
- 
![spring框架的组成结构图](/images/pasted-5.png)
	- `核心容器`：包含四个组建：Beans、Core、Context和SpEl。Spring的核心容器也称 IoC容器，提供了 Spring 框架的基本功能。主要组件是 BeanFactory，是工厂模式的具体实现。
容器是Spring框架最核心的部分，它管理着Spring应用中bean的创建、配置和管理。在该模块中，包括了Spring bean工厂，它为Spring提供了DI的功能。基于bean工厂，我们还会发现有多种Spring应用上下文的实现，每一种都提供了配置Spring的不同方式。
	- `Spring 上下文`：是一个配置文件，用于给Spring框架提供上下文信息
	- `数据访问与集成`：使用JDBC编写代码通常会导致大量的样板式代码，例如获得数据库连接、创建语句、处理结果集到最后关闭数据库连接。Spring的JDBC和DAO（Data Access Object）模块抽象了这些样板式代码，使我们的数据库代码变得简单明了，还可以避免因为关闭数据库资源失败而引发的问题。该模块在多种数据库服务的错误信息之上构建了一个语义丰富的异常层，以后我们再也不需要解释那些隐晦专有的SQL错误信息了。
	- ` Web与远程调用`：MVC（Model-View-Controller）模式是一种普遍被接受的构建Web应用的方法，它可以帮助用户将界面逻辑与应用逻辑分离。虽然Spring能够与多种流行的MVC框架进行集成，但它的Web和远程调用模块自带了一个强大的MVC框架，有助于在Web层提升应用的松耦合水平。
	- `Instrumentation`：Spring的Instrumentation模块提供了为JVM添加代理（agent）的功能。具体来讲，它为Tomcat提供了一个织入代理，能够为Tomcat传递类文件，就像这些文件是被类加载器加载的一样。
	- `Spring 的面向方面编程（Spring AOP）`：通过配置管理，Spring AOP 为基于Spring框架的应用程序中的对象提供了事务管理服务。
在AOP模块中，Spring对面向切面编程提供了丰富的支持。这个模块是Spring应用系统中开发切面的基础。与DI一样，AOP可以帮助应用对象解耦。借助于AOP，可以将遍布系统的关注点（例如事务和安全）从它们所应用的对象中解耦出来。
- 为了降低Java开发的复杂性，Spring采取了以下4种关键策略：
	- 基于POJO（简单老式Java对象 Plain Old Java object ）的轻量级和最小侵入性编程；
	- 通过依赖注入和面向接口实现松耦合；
	- 基于切面和惯例进行声明式编程；
	- 通过切面和模板减少样板式代码。
- 通过DI，对象的依赖关系将由系统中负责协调各对象的第三方组件在创建对象的时候进行设定。对象无需自行创建或管理它们的依赖关系，依赖关系将被自动注入到需要它们的对象当中去。

### 简化 Java 开发

#### 依赖注入
- 对象和对象之间的关系除了又继承和组合之外，还有一种关系称为依赖关系。在传统的代码实现中，这种依赖关系是在程序代码中直接定义实现的。而在 IoC 模式中，对象依赖关系是由容器来控制的，程序只负责接口的控制。这种控制权从代码到外部容器的转移，称为`控制反转（IoC）`，也称`依赖注入`。
- Spring框架的控制反转容器就像一个大的对象工程，某个对象的实例化有这个容器控制，而不是以new的方式去实例化对象。换句话说，实例化对象的控制权反转给了容器。同时，在容器实例化对象时，对象之间的相互依赖关系也构建好了，即把所谓的依赖关系“注入”进去。这种管理组件的思想，使得当更改组件依赖关系时，可以通过需改相关的XML配置文件或注释实现，而无须修改代码。

##### 例
类B
```
public class ClassB {
	public ClassB() {
		System.out.println("B类默认构造方法被执行！");
	}
	public void b() {
		System.out.println("exec ClassB.b()!");
	}
}
```
类A
```
public class ClassA {
	private ClassB classB;//类A的依赖于类B，因为类A使用类B的一个实例classB
	public ClassA() {
		classB = new ClassB();
		System.out.println("A类默认构造方法被执行！");
	}
	public ClassA(ClassB classB) {
		System.out.println("A类带参构造方法开始被执行");
		this.classB = classB;
		System.out.println("A类带参构造方法结束被执行");
	}
	public void a() {
		System.out.println("exec ClassA.a()");
		classB.b();
	}
	public ClassB getClassB() {
		return classB;
	}
	public void setClassB(ClassB classB) {
		this.classB = classB;
	}
}
```
<b>强耦合依赖关系</b>
```
public class Main {
	public static void main(String[] args) {
		ClassA classA = new ClassA();
		classA.a();
	}
}
```
<b>弱耦合关系（构造器注入依赖关系）</b>
```
public static void main(String[] args) {
		ClassB classB = new ClassB();
		ClassA classA = new ClassA(classB);
		classA.a();
}
```
<b>弱耦合关系（Setter方法注入依赖）</b>
```
public static void main(String[] args) {
		ClassB classB = new ClassB();
		ClassA classA = new ClassA();
		classA.setClassB(classB);
		classA.a();
}
```
- 创建应用组件之间协作的行为通常称为装配（wiring）。Spring有多种装配bean的方式，采用XML是很常见的一种装配方式。Spring还支持使用Java来描述配置。【具体参见 1.1.2 依赖注入一章】
- Spring通过应用上下文（Application Context）装载bean的定义并把它们组装起来。Spring应用上下文全权负责对象的创建和组装。
#### 应用切面
- DI 能够让相互协作的软件组件保持松散耦合，而面向切面编程（aspect-oriented programming，AOP）允许你把遍布应用各处的功能分离出来形成可重用的组件。
- 系统由许多不同的组件组成，每一个组件各负责一块特定功能。除了实现自身核心的功能之外，这些组件还经常承担着额外的职责。诸如日志、事务管理和安全这样的系统服务经常融入到自身具有核心业务逻辑的组件中去，这些系统服务通常被称为横切关注点，因为它们会跨越系统的多个组件。AOP能够使这些服务模块化，并以声明的方式将它们应用到它们需要影响的组件中去。所造成的结果就是这些组件会具有更高的内聚性并且会更加关注自身的业务，完全不需要了解涉及系统服务所带来复杂性。
- AOP 是对 OOP(面向对象编程)地补充和完善，它利用一种被称为“横切”的技术，剖解封装的对象内部，并将哪些影响了多个类的公共行为封装到一个可重用模块，并将其命名为 Aspect，即“方面/切面”。
- AOP 的核心思想就是“将应用程序中的业务逻辑和对其提供支持的通用服务进行分离，而业务逻辑与通用服务功能之间通过配置信息，实现整合”。
- 实现 AOP 的技术主要分为两大类：
	- 采用`动态代理`技术，利用拦截器截取消息的方式，对该消息进行“插装”，以取代原有对象行为的执行
	- 采用`静态织入`的方式，引入特定的语法创建“方面”，从而使编译器可以在编译期间织入有关“方面”的代码。
- Spring AOP 是实现 AOP 的一种技术，是采用“动态代理技术”实现的。
- AOP 的基本思想：动态地根据需要插入“某方法”运行，完成所要求地独家功能。
- AOP 技术相关的概念和术语
	- `Aspect（切面/方面）`：就是对系统中横切关注点逻辑进行模块化封装的 AOP 概念实体（简单讲就是定义一个类），它可以包含多个 Pointcut（切入点）及相关的 Advice（通知）定义。
	- `JointPoint（连接点）`：系统在运行之前，AOP 的功能模块需要织入到 OOP 的功能模块中。这些将要在其上进行织入操作的系统功能点称为 JointPoint。在 Spring AOP 中，一个连接点总是表示一个方法的执行。
	- `Pointcut（切入点）` ：是对 JointPoint 的形式描述，用于指出要对哪些 JointPoint 进行拦截的定义。
	- `Advice（通知）`：一个 Advice 对应一个横切关注点逻辑的载体，它代表将会织入到 JointPoint的横切逻辑：在切面的某个特定的连接点上执行的逻辑。分为前置通知和后置通知。
- 进行 Spring AOP 编程，有两种实现方式，一种是基于 XML 配置的 Spring AOP 编程，另一种是基于注释配置的 Spring AOP 编程。
#### 模板技术
- 使用Spring的JdbcTemplate（利用了 Java 5特性的JdbcTemplate实现）重写的方法仅仅关注核心逻辑，而不需要迎合JDBC API的需求
- Spring通过面向POJO编程、DI、切面和模板技术来简化Java开发中的复杂性。

### Spring 容器
- 在基于Spring的应用中，应用对象生存于Spring容器（container）中。Spring容器负责创建对象，装配它们，配置它们并管理它们的整个生命周期，从生存到死亡（在这里，可能就是new到finalize()）。
- 容器是Spring框架的核心。Spring容器使用DI管理构成应用的组件，它会创建相互协作的组件之间的关联。毫无疑问，这些对象更简单干净，更易于理解，更易于重用并且更易于进行单元测试。
- Spring自带了多个容器实现，可以归为两种不同的类型。`bean工厂`（由org.springframework.beans.factory.BeanFactory接口定义）是最简单的容器，提供基本的DI支持。`应用上下文`（由org.springframework.context.ApplicationContext接口定义）基于BeanFactory构建，并提供应用框架级别的服务，例如从属性文件解析文本信息以及发布应用事件给感兴趣的事件监听者。

#### 使用应用上下文
- Spring自带了多种类型的应用上下文：
	- AnnotationConfigApplicationContext：从一个或多个基于Java的配置类中加载Spring应用上下文。
	- AnnotationConfigWebApplicationContext：从一个或多个基于Java的配置类中加载Spring Web应用上下文。
	- ClassPathXmlApplicationContext：从类路径下的一个或多个XML配置文件中加载上下文定义，把应用上下文的定义文件作为类资源。
	- FileSystemXmlapplicationcontext：从文件系统下的一个或多个XML配置文件中加载上下文定义。
	- XmlWebApplicationContext：从Web应用下的一个或多个XML配置文件中加载上下文定义。
- 无论从哪装载应用上下文，将bean加载到bean工厂的过程都是相似的。例：`ApplicationContext context = new FileSystemXmlApplicationContext("c:/knight.xml");`
- 应用上下文准备就绪之后，我们就可以调用上下文的getBean()方法从Spring容器中获取bean。

#### bean的生命周期
- 在传统的Java应用中，bean的生命周期很简单。使用Java关键字new进行bean实例化，然后该bean就可以使用了。一旦该bean不再被使用，则由Java自动进行垃圾回收。
- 
![Spring 中 bean 的生命周期](/images/pasted-6.png)
- 在bean准备就绪之前，bean工厂执行了若干启动步骤：
	1．Spring对bean进行实例化；
	2．Spring将值和bean的引用注入到bean对应的属性中；
	3．如果bean实现了BeanNameAware接口，Spring将bean的ID传递给setBean-Name()方法；
	4．如果bean实现了BeanFactoryAware接口，Spring将调用setBeanFactory()方法，将BeanFactory容器实例传入；
	5．如果bean实现了ApplicationContextAware接口，Spring将调用setApplicationContext()方法，将bean所在的应用上下文的引用传入进来；
	6．如果bean实现了BeanPostProcessor接口，Spring将调用它们的post-ProcessBeforeInitialization()方法；
	7．如果bean实现了InitializingBean接口，Spring将调用它们的after-PropertiesSet()方法。类似地，如果bean使用init-method声明了初始化方法，该方法也会被调用；
	8．如果bean实现了BeanPostProcessor接口，Spring将调用它们的post-ProcessAfterInitialization()方法；
	9．此时，bean已经准备就绪，可以被应用程序使用了，它们将一直驻留在应用上下文中，直到该应用上下文被销毁；
	10．如果bean实现了DisposableBean接口，Spring将调用它的destroy()接口方法。同样，如果bean使用destroy-method声明了销毁方法，该方法也会被调用。

### 俯瞰Spring

- Spring Web Flow：Spring Web Flow建立于Spring MVC框架之上，它为基于流程的会话式Web应用（可以想一下购物车或者向导功能）提供了支持。
- Spring Web Service：虽然核心的Spring框架提供了将Spring bean以声明的方式发布为Web Service的功能，但是这些服务是基于一个具有争议性的架构（拙劣的契约后置模型）之上而构建的。这些服务的契约由bean的接口来决定。 Spring Web Service提供了契约优先的Web Service模型，服务的实现都是为了满足服务的契约而编写的。
- Spring Security：安全对于许多应用都是一个非常关键的切面。利用Spring AOP，Spring Security为Spring应用提供了声明式的安全机制。
- Spring Integration：许多企业级应用都需要与其他应用进行交互。Spring Integration提供了多种通用应用集成模式的Spring声明式风格实现。
- Spring Batch：当我们需要对数据进行大量操作时，没有任何技术可以比批处理更胜任这种场景。如果需要开发一个批处理应用，你可以通过Spring Batch，使用Spring强大的面向POJO的编程模型。
- Spring Data：Spring Data使得在Spring中使用任何数据库都变得非常容易。尽管关系型数据库统治企业级应用多年，但是现代化的应用正在认识到并不是所有的数据都适合放在一张表中的行和列中。一种新的数据库种类，通常被称之为NoSQL数据库[2]，提供了使用数据的新方法，这些方法会比传统的关系型数据库更为合适。不管你使用文档数据库，如MongoDB，图数据库，如Neo4j，还是传统的关系型数据库，Spring Data都为持久化提供了一种简单的编程模型。这包括为多种数据库类型提供了一种自动化的Repository机制，它负责为你创建Repository的实现。
- Spring Social：社交网络是互联网领域中新兴的一种潮流，越来越多的应用正在融入社交网络网站，例如Facebook或者Twitter。如果对此感兴趣，你可以了解一下Spring Social，这是Spring的一个社交网络扩展模块。不过，Spring Social并不仅仅是tweet和好友。尽管名字是这样，但Spring Social更多的是关注连接（connect），而不是社交（social）。它能够帮助你通过REST API连接Spring应用，其中有些Spring应用可能原本并没有任何社交方面的功能目标。
- Spring Mobile：移动应用是另一个引人瞩目的软件开发领域。智能手机和平板设备已成为许多用户首选的客户端。Spring Mobile是Spring MVC新的扩展模块，用于支持移动Web应用开发。
- Spring for Android：旨在通过Spring框架为开发基于Android设备的本地应用提供某些简单的支持。
- Spring Boot：Spring极大地简化了众多的编程任务，减少甚至消除了很多样板式代码，如果没有Spring的话，在日常工作中你不得不编写这样的样板代码。Spring Boot是一个崭新的令人兴奋的项目，它以Spring的视角，致力于简化Spring本身。Spring Boot大量依赖于自动配置技术，它能够消除大部分（在很多场景中，甚至是全部）Spring配置。它还提供了多个Starter项目，不管你使用Maven还是Gradle，这都能减少Spring工程构建文件的大小。

## 装配Bean
- 在Spring中，对象无需自己查找或创建与其所关联的其他对象。相反，容器负责把需要相互协作的对象引用赋予各个对象。
- 创建应用对象之间协作关系的行为通常称为装配（wiring），这也是依赖注入（DI）的本质。
- 配置Spring容器最常见的三种方法：
	- 在XML中进行显式配置。
	- 在Java中进行显式配置。
	- 隐式的bean发现机制和自动装配。

### 自动化装配bean
- Spring从两个角度来实现自动化装配：
	- 组件扫描（component scanning）：Spring会自动发现应用上下文中所创建的bean。
	- 自动装配（autowiring）：Spring自动满足bean之间的依赖。

#### 一、  创建可被发现的bean
1. CompactDisc接口在Java中定义了CD的概念。
```
package soundsystem;
public interface CompactDisc {
  void play();
}
```
作为接口，它定义了CD播放器对一盘CD所能进行的操作。它将CD播放器的任意实现与CD本身的耦合降低到了最小的程度。

2. 带有@Component注解的CompactDisc实现类SgtPeppers
```
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
SgtPeppers类上使用了@Component注解。这个简单的注解表明该类会作为组件类，并告知Spring要为这个类创建bean。没有必要显式配置SgtPeppersbean，因为这个类使用了@Component注解，所以Spring会把事情处理妥当。

3. 组件扫描默认是不启用的。还需要显式配置一下Spring，从而命令它去寻找带有@Component注解的类，并为其创建bean。
	- **@ComponentScan注解启用了组件扫描**。类CDPlayerConfig通过Java代码定义了Spring的装配规则。CDPlayerConfig类并没有显式地声明任何bean，只不过它使用了@ComponentScan注解，这个注解能够在Spring中启用组件扫描。如果没有其他配置的话，@ComponentScan默认会扫描与配置类相同的包。因为CDPlayerConfig类位于soundsystem包中，因此Spring将会扫描这个包以及这个包下的所有子包，查找带有@Component注解的类。这样的话，就能发现CompactDisc，并且会在Spring中自动为其创建一个bean。
`package soundsystem;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
@Configuration
@ComponentScan
public class CDPlayerConfig {
}
`
 
 - **通过XML启用组件扫描**。
`<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:context="http://www.springframework.org/schema/context"
  xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context.xsd">
  <context:component-scan base-package="soundsystem" />
</beans>
`
4. 测试组件扫描能够发现CompactDisc

```
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
CDPlayerTest使用了Spring的SpringJUnit4ClassRunner，以便在测试开始的时候自动创建Spring的应用上下文。注解@ContextConfiguration会告诉它需要在CDPlayerConfig中加载配置。因为CDPlayerConfig类中包含了@ComponentScan，因此最终的应用上下文中应该包含CompactDiscbean。

#### 二、为组件扫描的bean命名
注：Spring会根据类名为其自动指定一个ID。

1. 将期望的ID作为值传递给@Component注解
```
@Component("lonelyHeartsClub")
public class SgtPeppers implements CompactDisc {
  ...
}
```
2. 另外一种为bean命名的方式，这种方式不使用@Component注解，而是使用Java依赖注入规范（Java Dependency Injection）中所提供的@Named注解来为bean设置ID

```
package soundsystem;
import javax.inject.Named;

@Named("lonelyHeartsClub")
public class SgtPeppers implements CompactDisc {
  ...
}
```


#### 三、设置组件扫描的基础包
注：如果没有为@ComponentScan设置任何属性，按照默认规则，它会以配置类所在的包作为基础包（base package）来扫描组件。
- 为了指定不同的基础包，需要在@ComponentScan的value属性中指明包的名称：
```
@Configuration
@ComponentScan("soundsystem")
public class CDPlayerConfig {}
```
- 想更加清晰地表明所设置的是基础包，可以通过basePackages属性进行配置：
```
@Configuration
@ComponentScan(basePackages="soundsystem")
public class CDPlayerConfig {}
```
- 可以设置多个基础包，只需要将basePackages属性设置为要扫描包的一个数组即可：
```
@Configuration
@ComponentScan(basePackages={"soundsystem", "video"})
public class CDPlayerConfig {}
```
- 除了将包设置为简单的String类型之外，@ComponentScan还提供了另外一种方法，那就是将其指定为包中所包含的类或接口：
```
@Configuration
@ComponentScan(basePackageClasses={CDPlayer.class, DVDPlayer.class})
public class CDPlayerConfig {}
```

#### 四、通过为bean添加注解实现自动装配
- 需要有一种方法能够将组件扫描得到的bean和它们的依赖装配在一起，即自动装配。
- 自动装配就是让Spring自动满足bean依赖的一种方法，在满足依赖的过程中，会在Spring应用上下文中寻找匹配某个bean需求的其他bean。
- 为了声明要进行自动装配，可以借助Spring的@Autowired注解。
- 通过自动装配，将一个CompactDisc注入到CDPlayer之中：

```
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
>CDPlayer类的构造器上添加了@Autowired注解，这表明当Spring创建CDPlayerbean的时候，会通过这个构造器来进行实例化并且会传入一个可设置给CompactDisc类型的bean。

- @Autowired注解不仅能够用在构造器上，还能用在属性的Setter方法上：
```
@Autowired
public void setCompactDisc(CompactDisc cd) {
  this.cd = cd;
}
```
>在Spring初始化bean之后，它会尽可能得去满足bean的依赖，在本例中，依赖是通过带有@Autowired注解的方法进行声明的，也就是setCompactDisc()。Setter方法并没有什么特殊之处。@Autowired注解可以用在类的任何方法上。

- 不管是构造器、Setter方法还是其他的方法，Spring都会尝试满足方法参数上所声明的依赖。假如有且只有一个bean匹配依赖需求的话，那么这个bean将会被装配进来。如果没有匹配的bean，那么在应用上下文创建的时候，Spring会抛出一个异常。为了避免异常的出现，你可以将@Autowired的required属性设置为false：
```
@Autowired(required=false)
public CDPlayer(CompactDisc cd) {
  this.cd = cd;
}
```
>将required属性设置为false时，Spring会尝试执行自动装配，但是如果没有匹配的bean的话，Spring将会让这个bean处于未装配的状态。但是，把required属性设置为false时，你需要谨慎对待。如果在你的代码中没有进行null检查的话，这个处于未装配状态的属性有可能会出现NullPointerException。如果有多个bean都能满足依赖关系的话，Spring将会抛出一个异常，表明没有明确指定要选择哪个bean进行自动装配。

- @Autowired是Spring特有的注解。如果你不愿意在代码中到处使用Spring的特定注解来完成自动装配任务的话，那么你可以考虑将其替换为@Inject：

```
package soundsystem;
import javax.inject.Inject;
import javax.inject.Named;

@Named
public class CDPlayer {
  ...

  @Inject
  public CDPlayer(CompactDisc cd) {
    this.cd = cd;
  }

  ...
}
```
>@Inject注解来源于Java依赖注入规范，该规范同时还为我们定义了@Named注解。在自动装配中，Spring同时支持@Inject和@Autowired。尽管@Inject和@Autowired之间有着一些细微的差别，但是在大多数场景下，它们都是可以互相替换的。

#### 五、验证自动装配
- 已经在CDPlayer的构造器中添加了@Autowired注解，Spring将把一个可分配给CompactDisc类型的bean自动注入进来。为了验证这一点，修改一下CDPlayerTest，使其能够借助CDPlayer bean播放CD：

```
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
>现在，除了注入CompactDisc，我们还将CDPlayerbean注入到测试代码的player成员变量之中（它是更为通用的MediaPlayer类型）。在play()测试方法中，我们可以调用CDPlayer的play()方法，并断言它的行为与你的预期一致。

### 通过Java代码装配bean
- 有时候自动化配置的方案行不通，因此需要明确配置Spring。比如说，想要将第三方库中的组件装配到你的应用中，在这种情况下，是没有办法在它的类上添加@Component和@Autowired注解的，因此就不能使用自动化装配的方案了。在这种情况下，你必须要采用显式装配的方式。在进行显式配置的时候，有两种可选方案：Java和XML。
- 在进行显式配置时，JavaConfig是更好的方案，因为它更为强大、类型安全并且对重构友好。因为它就是Java代码，就像应用程序中的其他Java代码一样。尽管不是必须的，但通常会将JavaConfig放到单独的包中，使它与其他的应用程序逻辑分离开来，这样对于它的意图就不会产生困惑了。

#### 一、创建配置类
- 现在样例中的CDPlayerConfig：
```
package soundsystem;
import org.springframework.context.annotation.Configuration;
@Configuration
public class CDPlayerConfig {
}
```
>移除了@ComponentScan注解，此时的CDPlayerConfig类就没有任何作用了。测试期望被注入CDPlayer和CompactDisc，但是这些bean根本就没有创建，因为组件扫描不会发现它们。

#### 二、声明简单的bean
注：要在JavaConfig中声明bean，我们需要编写一个方法，这个方法会创建所需类型的实例，然后给这个方法添加@Bean注解。
- 下面的代码声明了CompactDisc bean：
```
@Bean
public CompactDisc sgtPeppers() {
  return new SgtPeppers();
}
```
>@Bean注解会告诉Spring这个方法将会返回一个对象，该对象要注册为Spring应用上下文中的bean。

- 默认情况下，bean的ID与带有@Bean注解的方法名是一样的。如果想为其设置成一个不同的名字的话，那么可以重命名该方法，也可以通过name属性指定一个不同的名字：
```
@Bean(name="lonelyHeartsClubBand")
public CompactDisc sgtPeppers() {
  return new SgtPeppers();
}
```
>不管采用什么方法来为bean命名，bean声明都是非常简单的。方法体返回了一个新的SgtPeppers实例。这里是使用Java来进行描述的，因此我们可以发挥Java提供的所有功能，只要最终生成一个CompactDisc实例即可。

- 在一组CD中随机选择一个CompactDisc来播放：
```
@Bean
public CompactDisc randomBeatlesCD() {
  int choice = (int) Math.floor(Math.random() * 4);
  if (choice == 0) {
    return new SgtPeppers();
  } else if (choice == 1) {
    return new WhiteAlbum();
  } else if (choice == 2) {
    return new HardDaysNight();
  } else {
    return new Revolver();
  }
}
```

#### 三、借助JavaConfig实现注入
- 在JavaConfig中装配bean的最简单方式就是引用创建bean的方法。例如，下面就是一种声明CDPlayer的可行方案：

```
@Bean
public CDPlayer cdPlayer() {
  return new CDPlayer(sgtPeppers());
}
```
>cdPlayer()方法像sgtPeppers()方法一样，同样使用了@Bean注解，这表明这个方法会创建一个bean实例并将其注册到Spring应用上下文中。所创建的bean ID为cdPlayer，与方法的名字相同。
>cdPlayer()的方法体与sgtPeppers()稍微有些区别。在这里并没有使用默认的构造器构建实例，而是调用了需要传入CompactDisc对象的构造器来创建CDPlayer实例。
>看起来，CompactDisc是通过调用sgtPeppers()得到的，但情况并非完全如此。因为sgtPeppers()方法上添加了@Bean注解，Spring将会拦截所有对它的调用，并确保直接返回该方法所创建的bean，而不是每次都对其进行实际的调用。

比如说，假设你引入了一个其他的CDPlayerbean，它和之前的那个bean完全一样：
```
@Bean
public CDPlayer cdPlayer() {
  return new CDPlayer(sgtPeppers());
}

@Bean
public CDPlayer anotherCDPlayer() {
  return new CDPlayer(sgtPeppers());
}
```
假如对sgtPeppers()的调用就像其他的Java方法调用一样的话，那么每个CDPlayer实例都会有一个自己特有的SgtPeppers实例。如果我们讨论的是实际的CD播放器和CD光盘的话，这么做是有意义的。如果你有两台CD播放器，在物理上并没有办法将同一张CD光盘放到两个CD播放器中。
但是，在软件领域中，我们完全可以将同一个SgtPeppers实例注入到任意数量的其他bean之中。默认情况下，Spring中的bean都是单例的，我们并没有必要为第二个CDPlayer bean创建完全相同的SgtPeppers实例。所以，Spring会拦截对sgtPeppers()的调用并确保返回的是Spring所创建的bean，也就是Spring本身在调用sgtPeppers()时所创建的CompactDiscbean。因此，两个CDPlayer bean会得到相同的SgtPeppers实例。
可以看到，通过调用方法来引用bean的方式有点令人困惑。其实还有一种理解起来更为简单的方式：
```
@Bean
public CDPlayer cdPlayer(CompactDisc compactDisc) {
  return new CDPlayer(compactDisc);
}
```
在这里，cdPlayer()方法请求一个CompactDisc作为参数。当Spring调用cdPlayer()创建CDPlayerbean的时候，它会自动装配一个CompactDisc到配置方法之中。然后，方法体就可以按照合适的方式来使用它。借助这种技术，cdPlayer()方法也能够将CompactDisc注入到CDPlayer的构造器中，而且不用明确引用CompactDisc的@Bean方法。
通过这种方式引用其他的bean通常是最佳的选择，因为它不会要求将CompactDisc声明到同一个配置类之中。在这里甚至没有要求CompactDisc必须要在JavaConfig中声明，实际上它可以通过组件扫描功能自动发现或者通过XML来进行配置。你可以将配置分散到多个配置类、XML文件以及自动扫描和装配bean之中，只要功能完整健全即可。不管CompactDisc是采用什么方式创建出来的，Spring都会将其传入到配置方法中，并用来创建CDPlayer bean。

注意：在这里使用CDPlayer的构造器实现了DI功能，但是完全可以采用其他风格的DI配置。比如说，如果想通过Setter方法注入CompactDisc的话，那么代码看起来应该是这样的：
```
@Bean
public CDPlayer cdPlayer(CompactDisc compactDisc) {
  CDPlayer cdPlayer = new CDPlayer(compactDisc);
  cdPlayer.setCompactDisc(compactDisc);
  return cdPlayer;
}
```
再次强调一遍，带有@Bean注解的方法可以采用任何必要的Java功能来产生bean实例。构造器和Setter方法只是@Bean方法的两个简单样例。这里所存在的可能性仅仅受到Java语言的限制。

### 通过XML装配bean
#### 一、创建XML配置规范
- 在使用XML为Spring装配bean之前，需要创建一个新的配置规范。在使用JavaConfig的时候，这意味着要创建一个带有@Configuration注解的类，而在XML配置中，这意味着要创建一个XML文件，并且要以<beans>元素为根。
- 最为简单的Spring XML配置如下所示：
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/context">
      <!-- configuration details go here -->
</beans>
```
>作为起步，在JavaConfig中所需要的只是@Configuration，但在使用XML时，需要在配置文件的顶部声明多个XML模式（XSD）文件，这些文件定义了配置Spring的XML元素。

- **借助Spring Tool Suite创建XML配置文件**：在Spring Tool Suite的菜单中，选择File>New>Spring Bean Configuration File，能够创建Spring XML配置文件，并且可以选择可用的配置命名空间。
- 用来装配bean的最基本的XML元素包含在spring-beans模式之中，在上面这个XML文件中，它被定义为根命名空间。<beans>是该模式中的一个元素，它是所有Spring配置文件的根元素。

#### 二、声明一个简单的`<bean>`
`<bean>`元素类似于JavaConfig中的@Bean注解。我们可以按照如下的方式声明CompactDiscbean：
```
<bean class="soundsystem.SgtPeppers" />
```
>创建这个bean的类通过class属性来指定的，并且要使用全限定的类名。因为没有明确给定ID，所以这个bean将会根据全限定类名来进行命名。在本例中，bean的ID将会是“soundsystem.SgtPeppers#0”。其中，“#0”是一个计数的形式，用来区分相同类型的其他bean。如果声明了另外一个SgtPeppers，并且没有明确进行标识，那么它自动得到的ID将会是“soundsystem.SgtPeppers#1”。

- 通常来讲更好的办法是借助id属性，为每个bean设置一个你自己选择的名字：
```
<bean id="compactDisc" class="soundsystem.SgtPeppers" />
```
>这个简单bean声明的一些特征：第一件需要注意的事情就是你不再需要直接负责创建SgtPeppers的实例，在基于JavaConfig的配置中，是需要这样做的。当Spring发现这个`<bean>`元素时，它将会调用SgtPeppers的默认构造器来创建bean。在XML配置中，bean的创建显得更加被动，不过，它并没有JavaConfig那样强大，在JavaConfig配置方式中，你可以通过任何可以想象到的方法来创建bean实例。

>在这个简单的`<bean>`声明中，我们将bean的类型以字符串的形式设置在了class属性中。谁能保证设置给class属性的值是真正的类呢？Spring的XML配置并不能从编译期的类型检查中受益。即便它所引用的是实际的类型，如果你重命名了类，会发生什么呢？**借助IDE检查XML的合法性使用**能够感知Spring功能的IDE，如Spring Tool Suite，能够在很大程度上帮助你确保Spring XML配置的合法性。

#### 三、借助构造器注入初始化bean
- 在Spring XML配置中，只有一种声明bean的方式：使用<bean>元素并指定class属性。Spring会从这里获取必要的信息来创建bean。但是，在XML中声明DI时，会有多种可选的配置方案和风格。具体到构造器注入，有两种基本的配置方案可供选择：
	1. `<constructor-arg>`元素
	2. 使用Spring 3.0所引入的c-命名空间

- `<constructor-arg>`元素比使用c-命名空间会更加冗长，从而导致XML更加难以读懂。另外，有些事情`<constructor-arg>`可以做到，但是使用c-命名空间却无法实现。
##### 构造器注入bean引用
- 现在已经声明了SgtPeppers bean，并且SgtPeppers类实现了CompactDisc接口，所以实际上我们已经有了一个可以注入到CDPlayerbean中的bean。我们所需要做的就是在XML中声明CDPlayer并通过ID引用SgtPeppers：
```
<bean id="cdPlayer" class="soundsystem.CDPlayer">
  <constructor-arg ref="compactDisc" />
</bean>
```
>当Spring遇到这个`<bean>`元素时，它会创建一个CDPlayer实例。`<constructor-arg>`元素会告知Spring要将一个ID为compactDisc的bean引用传递到CDPlayer的构造器中。

- c-命名空间是在Spring 3.0中引入的，它是在XML中更为简洁地描述构造器参数的方式。要使用它的话，必须要在XML的顶部声明其模式，如下所示：

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:c="http://www.springframework.org/schema/c"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://www.springframework.org/schema/beans
  http://www.springframework.org/schema/beans/spring-beans.xsd">
 ...

</beans>
```
- 在c-命名空间和模式声明之后，就可以使用它来声明构造器参数了，如下所示：
```
<bean id="cdPlayer" class="soundsystem.CDPlayer"
      c:cd-ref="compactDisc" />
```
> 使用了c-命名空间来声明构造器参数，它作为`<bean>`元素的一个属性。

- 
![通过Spring的c-命名空间将bean引用注入到构造器参数中](/images/pasted-9.png)
>它直接引用了构造器参数的名称。引用参数的名称看起来有些怪异，因为这需要在编译代码的时候，将调试标志（debug symbol）保存在类代码中。如果你优化构建过程，将调试标志移除掉，那么这种方式可能就无法正常执行了。

- 替代的方案是使用参数在整个参数列表中的位置信息：
```
<bean id="cdPlayer" class="soundsystem.CDPlayer"
      c:_0-ref="compactDisc" />
```
>将参数的名称替换成了“0”，也就是参数的索引。因为在XML中不允许数字作为属性的第一个字符，因此必须要添加一个下画线作为前缀。使用索引来识别构造器参数感觉比使用名字更好一些。即便在构建的时候移除掉了调试标志，参数却会依然保持相同的顺序。如果有多个构造器参数的话，这当然是很有用处的。

- 在这里因为只有一个构造器参数，所以我们还有另外一个方案——根本不用去标示参数：
```
<bean id="cdPlayer" class="soundsystem.CDPlayer"
      c:_-ref="compactDisc" />
```

##### 将字面量注入到构造器中
- 迄今为止，我们所做的DI通常指的都是类型的装配——也就是将对象的引用装配到依赖于它们的其他对象之中——而有时候，我们需要做的只是用一个字面量值来配置对象。为了阐述这一点，假设要创建CompactDisc的一个新实现，如下所示：

```
package soundsystem;

public class BlankDisc implements CompactDisc {

  private String title;
  private String artist;

  public BlankDisc(String title, String artist) {
    this.title = title;
    this.artist = artist;
  }

  public void play() {
    System.out.println("Playing " + title + " by " + artist);
  }
}
```
- 现在可以将已有的SgtPeppers替换为这个类：
```
<bean id="compactDisc"
      class="soundsystem.BlankDisc">
  <constructor-arg value="Sgt. Pepper's Lonely Hearts Club Band" />
  <constructor-arg value="The Beatles" />
</bean>
```
>再次使用`<constructor-arg>`元素进行构造器参数的注入。但是这一次没有使用“ref”属性来引用其他的bean，而是使用了value属性，通过该属性表明给定的值要以字面量的形式注入到构造器之中。

- 如果要使用c-命名空间的话，第一种方案是引用构造器参数的名字：
```
<bean id="compactDisc"
      class="soundsystem.BlankDisc"
      c:_title="Sgt. Pepper's Lonely Hearts Club Band"
      c:_artist="The Beatles" />
```
>装配字面量与装配引用的区别在于属性名中去掉了“-ref”后缀。

- 与之类似，也可以通过参数索引装配相同的字面量值，如下所示：
```
<bean id="compactDisc"
      class="soundsystem.BlankDisc"
      c:_0="Sgt. Pepper's Lonely Hearts Club Band"
      c:_1="The Beatles" />
```

- XML不允许某个元素的多个属性具有相同的名字。因此，如果有两个或更多的构造器参数的话，不能简单地使用下画线进行标示。但是如果只有一个构造器参数的话就可以这样做了。假设BlankDisc只有一个构造器参数，这个参数接受唱片的名称。在这种情况下可以在Spring中这样声明它：
```
<bean id="compactDisc" class="soundsystem.BlankDisc"
      c:_="Sgt. Pepper's Lonely Hearts Club Band" />
```
- 在装配bean引用和字面量值方面，`<constructor-arg>`和c-命名空间的功能是相同的。但是有一种情况是`<constructor-arg>`能够实现，c-命名空间却无法做到的。

##### 装配集合
- 如果使用CompactDisc为真正的CD建模，那么它也应该有磁道列表（大多数的CD都会包含十多个磁道，每个磁道上包含一首歌）的概念。请考虑下面这个新的BlankDisc：

```
package soundsystem.collections;
import java.util.List;
import soundsystem.CompactDisc;

public class BlankDisc implements CompactDisc {

  private String title;
  private String artist;
  private List<String> tracks;
  public BlankDisc(String title, String artist, List<String> tracks) {
    this.title = title;
    this.artist = artist;
    this.tracks = tracks;
  }
  public void play() {
    System.out.println("Playing " + title + " by " + artist);
    for (String track : tracks) {
      System.out.println("-Track: " + track);
    }
  }

}
```
- 这个变更会对Spring如何配置bean产生影响，在声明bean的时候，必须要提供一个磁道列表。最简单的办法是将列表设置为null。因为它是一个构造器参数，所以必须要声明它，不过可以采用如下的方式传递null给它：
```
<bean id="compactDisc" class="soundsystem.BlankDisc">
  <constructor-arg value="Sgt. Pepper's Lonely Hearts Club Band" />
  <constructor-arg value="The Beatles" />
  <constructor-arg><null/></constructor-arg>
</bean>
```
><null/>元素所做的事情与你的期望是一样的：将null传递给构造器。这并不是解决问题的好办法，但在注入期它能正常执行。当调用play()方法时，会遇到NullPointerException异常，因此这并不是理想的方案。

- 更好的解决方法是提供一个磁道名称的列表。要达到这一点，我们可以有多个可选方案。首先，可以使用<list>元素将其声明为一个列表：
```
<bean id="compactDisc" class="soundsystem.BlankDisc">
  <constructor-arg value="Sgt. Pepper's Lonely Hearts Club Band" />
  <constructor-arg value="The Beatles" />
  <constructor-arg>
    <list>
      <value>Sgt. Pepper's Lonely Hearts Club Band</value>
      <value>With a Little Help from My Friends</value>
      <value>Lucy in the Sky with Diamonds</value>
      <value>Getting Better</value>
      <value>Fixing a Hole</value>
      <!-- ...other tracks omitted for brevity... -->
    </list>
  </constructor-arg>
</bean>
```
>其中，`<list>`元素是`<constructor-arg>`的子元素，这表明一个包含值的列表将会传递到构造器中。其中，`<value>`元素用来指定列表中的每个元素。

- 与之类似，我们也可以使用`<ref>`元素替代`<value>`，实现bean引用列表的装配。例如，假设你有一个Discography类，它的构造器如下所示：
```
public Discography(String artist, List<CompactDisc> cds) { ... }
```
那么，可以采取如下的方式配置Discography bean：
```
<bean id="beatlesDiscography"
      class="soundsystem.Discography">
  <constructor-arg value="The Beatles" />
  <constructor-arg>
    <list>
      <ref bean="sgtPeppers" />
      <ref bean="whiteAlbum" />
      <ref bean="hardDaysNight" />
      <ref bean="revolver" />
      ...
    </list>
  </constructor-arg>
</bean>
```
当构造器参数的类型是java.util.List时，使用`<list>`元素是合情合理的。尽管如此，我们也可以按照同样的方式使用`<set>`元素：
```
<bean id="compactDisc" class="soundsystem.BlankDisc">
  <constructor-arg value="Sgt. Pepper's Lonely Hearts Club Band" />
  <constructor-arg value="The Beatles" />
  <constructor-arg>
    <set>
      <value>Sgt. Pepper's Lonely Hearts Club Band</value>
      <value>With a Little Help from My Friends</value>
      <value>Lucy in the Sky with Diamonds</value>
      <value>Getting Better</value>
      <value>Fixing a Hole</value>
      <!-- ...other tracks omitted for brevity... -->
    </set>
  </constructor-arg>
</bean>
```
>`<set>`和`<list>`元素的区别不大，其中最重要的不同在于当Spring创建要装配的集合时，所创建的是java.util.Set还是java.util.List。如果是Set的话，所有重复的值都会被忽略掉，存放顺序也不会得以保证。不过无论在哪种情况下，`<set>`或`<list>`都可以用来装配List、Set甚至数组。

- 在装配集合方面，<constructor-arg>比c-命名空间的属性更有优势。目前，使用c-命名空间的属性无法实现装配集合的功能。

#### 四、设置属性
- 到目前为止，CDPlayer和BlankDisc类完全是通过构造器注入的，没有使用属性的Setter方法。
- 关于如何使用Spring XML实现属性注入。假设属性注入的CDPlayer如下所示：

```
package soundsystem;
import org.springframework.beans.factory.annotation.Autowired;
import soundsystem.CompactDisc;
import soundsystem.MediaPlayer;

public class CDPlayer implements MediaPlayer {
  private CompactDisc compactDisc;

  @Autowired
  public void setCompactDisc(CompactDisc compactDisc) {
    this.compactDisc = compactDisc;
  }
  public void play() {
    compactDisc.play();
  }
}
```
>作为一个通用的规则，倾向于对强依赖使用构造器注入，而对可选性的依赖使用属性注入。按照这个规则，可以说对于BlankDisc来讲，唱片名称、艺术家以及磁道列表是强依赖，因此构造器注入是正确的方案。

- 现在，CDPlayer没有任何的构造器（除了隐含的默认构造器），它也没有任何的强依赖。因此，可以采用如下的方式将其声明为Spring bean：
```
<bean id="cdPlayer"
      class="soundsystem.CDPlayer" />
```

- Spring在创建bean的时候不会有任何的问题，但是CDPlayerTest会因为出现NullPointerException而导致测试失败，因为我们并没有注入CDPlayer的compactDisc属性。不过，按照如下的方式修改XML，就能解决该问题：
```
<bean id="cdPlayer"
      class="soundsystem.CDPlayer">
  <property name="compactDisc" ref="compactDisc" />
</bean>
```
>`<property>`元素为属性的Setter方法所提供的功能与`<constructor-arg>`元素为构造器所提供的功能是一样的。在本例中，它引用了ID为compactDisc的bean（通过ref属性），并将其注入到compactDisc属性中（通过setCompactDisc()方法）。如果现在运行测试的话，应该就能通过了。

- Spring为<constructor-arg>元素提供了c-命名空间作为替代方案，与之类似，Spring提供了更加简洁的p-命名空间，作为<property>元素的替代方案。为了启用p-命名空间，必须要在XML文件中与其他的命名空间一起对其进行声明：
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:p="http://www.springframework.org/schema/p"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd">
  ...
</bean>
```
- 可以使用p-命名空间，按照以下的方式装配compactDisc属性：
```
<bean id="cdPlayer"
      class="soundsystem.CDPlayer"
      p:compactDisc-ref="compactDisc" />
```
- p-命名空间中属性所遵循的命名约定与c-命名空间中的属性类似。如图阐述了p-命名空间属性是如何组成的：
![借助Spring的p-命名空间，将bean引用注入到属性中](/images/pasted-7.png)

>属性的名字使用了“p:”前缀，表明所设置的是一个属性。接下来就是要注入的属性名。最后，属性的名称以“-ref”结尾，这会提示Spring要进行装配的是引用，而不是字面量。

##### 将字面量注入到属性中
- 属性也可以注入字面量，这与构造器参数非常类似。示例：BlankDisc这次完全通过属性注入进行配置，而不是构造器注入。新的BlankDisc类如下所示：

```
package soundsystem;
import java.util.List;
import soundsystem.CompactDisc;

public class BlankDisc implements CompactDisc {

  private String title;
  private String artist;
  private List<String> tracks;

  public void setTitle(String title) {
    this.title = title;
  }

  public void setArtist(String artist) {
    this.artist = artist;
  }


  public void setTracks(List<String> tracks) {
    this.tracks = tracks;
  }
  public void play() {
    System.out.println("Playing " + title + " by " + artist);
    for (String track : tracks) {
      System.out.println("-Track: " + track);
    }
  }

}
```

- 现在，它不再强制要求我们装配任何的属性。你可以按照如下的方式创建一个BlankDiscbean，它的所有属性全都是空的：
```
<bean id="reallyBlankDisc"
      class="soundsystem.BlankDisc" />
```
- 如果在装配bean的时候不设置这些属性，那么在运行期CD播放器将不能正常播放内容。play()方法可能会遇到的输出内容是“Playing null by null”，随之会抛出NullPointerException异常，这是因为我们没有指定任何的磁道。所以，我们需要装配这些属性，可以借助`<property>`元素的value属性实现该功能：

```
<bean id="compactDisc"
      class="soundsystem.BlankDisc">
  <property name="title"
               value="Sgt. Pepper's Lonely Hearts Club Band" />
  <property name="artist" value="The Beatles" />
  <property name="tracks">
    <list>
      <value>Sgt. Pepper's Lonely Hearts Club Band</value>
      <value>With a Little Help from My Friends</value>
      <value>Lucy in the Sky with Diamonds</value>
      <value>Getting Better</value>
      <value>Fixing a Hole</value>
      <!-- ...other tracks omitted for brevity... -->
    </list>
  </property>
</bean>
```
>在这里，除了使用<property>元素的value属性来设置title和artist，我们还使用了内嵌的<list>元素来设置tracks属性，这与之前通过<constructor-arg>装配tracks是完全一样的。

- 另外一种可选方案就是使用p-命名空间的属性来完成该功能：
```
<bean id="compactDisc"
      class="soundsystem.BlankDisc"
      p:title="Sgt. Pepper's Lonely Hearts Club Band"
      p:artist="The Beatles">
  <property name="tracks">
    <list>
      <value>Sgt. Pepper's Lonely Hearts Club Band</value>
      <value>With a Little Help from My Friends</value>
      <value>Lucy in the Sky with Diamonds</value>
      <value>Getting Better</value>
      <value>Fixing a Hole</value>
      <!-- ...other tracks omitted for brevity... -->
    </list>
  </property>
</bean>
```
>与c-命名空间一样，装配bean引用与装配字面量的唯一区别在于是否带有“-ref”后缀。如果没有“-ref”后缀的话，所装配的就是字面量。

- 需要注意的是，我们不能使用p-命名空间来装配集合，没有便利的方式使用p-命名空间来指定一个值（或bean引用）的列表。但是，我们可以使用Spring util-命名空间中的一些功能来简化BlankDiscbean。首先，需要在XML中声明util-命名空间及其模式：

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:p="http://www.springframework.org/schema/p"
  xmlns:util="http://www.springframework.org/schema/util"
  xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/util
    http://www.springframework.org/schema/util/spring-util.xsd">
    ...
</beans>
```
util-命名空间所提供的功能之一就是`<util:list>`元素，它会创建一个列表的bean。借助`<util:list>`，我们可以将磁道列表转移到BlankDisc bean之外，并将其声明到单独的bean之中，如下所示：
```
<util:list id="trackList">
  <value>Sgt. Pepper's Lonely Hearts Club Band</value>
  <value>With a Little Help from My Friends</value>
  <value>Lucy in the Sky with Diamonds</value>
  <value>Getting Better</value>
  <value>Fixing a Hole</value>
  <!-- ...other tracks omitted for brevity... -->
</util:list>
```

- 现在，能够像使用其他的bean那样，将磁道列表bean注入到BlankDisc bean的tracks属性中：
```
<bean id="compactDisc"
      class="soundsystem.BlankDisc"
      p:title="Sgt. Pepper's Lonely Hearts Club Band"
      p:artist="The Beatles"
      p:tracks-ref="trackList" />
```
`<util:list>`只是util-命名空间中的多个元素之一。如表列出了util-命名空间提供的所有元素。

元 素 | 描 述
:-: | :-:
`<util:constant>` | 引用某个类型的public static域，并将其暴露为bean
util:list | 创建一个java.util.List类型的bean，其中包含值或引用
util:map | 创建一个java.util.Map类型的bean，其中包含值或引用
util:properties | 创建一个java.util.Properties类型的bean
util:property-path | 引用一个bean的属性（或内嵌属性），并将其暴露为bean
util:set | 创建一个java.util.Set类型的bean，其中包含值或引用

### 导入和混合配置
- 学习将自动化配置、JavaConfig以及XML配置混合并匹配在一起。

#### 在JavaConfig中引用XML配置
- 临时假设CDPlayerConfig已经变得有些笨重，我们想要将其进行拆分。尽管，它目前只定义了两个bean。我们所能实现的一种方案就是将BlankDisc从CDPlayerConfig拆分出来，定义到它自己的CDConfig类中，如下所示：

```
package soundsystem;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class CDConfig {
  @Bean
  public CompactDisc compactDisc() {
    return new SgtPeppers();
  }
}
```
- compactDisc()方法已经从CDPlayerConfig中移除掉了，需要有一种方式将这两个类组合在一起。一种方法就是在CDPlayerConfig中使用@Import注解导入CDConfig：

```
package soundsystem;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;

@Configuration
@Import(CDConfig.class)
public class CDPlayerConfig {

  @Bean
  public CDPlayer cdPlayer(CompactDisc compactDisc) {
    return new CDPlayer(compactDisc);
  }

}
```
或者采用一个更好的办法，也就是不在CDPlayerConfig中使用@Import，而是创建一个更高级别的SoundSystemConfig，在这个类中使用@Import将两个配置类组合在一起：
```
package soundsystem;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;

@Configuration
@Import({CDPlayerConfig.class, CDConfig.class})
public class SoundSystemConfig {
}
```
>不管采用哪种方式，我们都将CDPlayer的配置与BlankDisc的配置分开了。

- 现在，假设希望通过XML来配置BlankDisc，如下所示：

```
<bean id="compactDisc"
      class="soundsystem.BlankDisc"
      c:_0="Sgt. Pepper's Lonely Hearts Club Band"
      c:_1="The Beatles">
  <constructor-arg>
    <list>
      <value>Sgt. Pepper's Lonely Hearts Club Band</value>
      <value>With a Little Help from My Friends</value>
      <value>Lucy in the Sky with Diamonds</value>
      <value>Getting Better</value>
      <value>Fixing a Hole</value>
      <!-- ...other tracks omitted for brevity... -->
    </list>
  </constructor-arg>
</bean>
```
- 现在BlankDisc配置在了XML之中，该如何让Spring同时加载它和其他基于Java的配置呢？答案是@ImportResource注解，假设BlankDisc定义在名为cd-config.xml的文件中，该文件位于根类路径下，那么可以修改SoundSystemConfig，让它使用@ImportResource注解，如下所示：

```
package soundsystem;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;
import org.springframework.context.annotation.ImportResource;
@Configuration
@Import(CDPlayerConfig.class)
@ImportResource("classpath:cd-config.xml")
public class SoundSystemConfig {
}
```
两个bean——配置在JavaConfig中的CDPlayer以及配置在XML中。BlankDisc——都会被加载到Spring容器之中。因为CDPlayer中带有@Bean注解的方法接受一个CompactDisc作为参数，因此BlankDisc将会装配进来，此时与它是通过XML配置的没有任何关系。

#### 在XML配置中引用JavaConfig
- 假设正在使用Spring基于XML的配置并且XML逐渐变得无法控制，我们决定将XML配置文件进行拆分。在JavaConfig配置中，我们已经展现了如何使用@Import和@ImportResource来拆分JavaConfig类。在XML中，我们可以使用import元素来拆分XML配置。
- 比如，假设希望将BlankDisc bean拆分到自己的配置文件中，该文件名为cd-config.xml，这与我们之前使用@ImportResource是一样的。我们可以在XML配置文件中使用<import>元素来引用该文件：

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:c="http://www.springframework.org/schema/c"
  xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd">
  <import resource="cd-config.xml" />

  <bean id="cdPlayer"
        class="soundsystem.CDPlayer"
        c:cd-ref="compactDisc" />
</beans>
```
- 现在，我们假设不再将BlankDisc配置在XML之中，而是将其配置在JavaConfig中，CDPlayer则继续配置在XML中。基于XML的配置该如何引用一个JavaConfig类呢？`<import>`元素只能导入其他的XML配置文件，并没有XML元素能够导入JavaConfig类。但是，有一个你已经熟知的元素能够用来将Java配置导入到XML配置中：`<bean>`元素。为了将JavaConfig类导入到XML配置中，我们可以这样声明bean：

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:c="http://www.springframework.org/schema/c"
  xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd">

  <bean class="soundsystem.CDConfig" />
  <bean id="cdPlayer"
        class="soundsystem.CDPlayer"
        c:cd-ref="compactDisc" />

</beans>
```
>采用这样的方式，两种配置——其中一个使用XML描述，另一个使用Java描述——被组合在了一起。

- 类似地，你可能还希望创建一个更高层次的配置文件，这个文件不声明任何的bean，只是负责将两个或更多的配置组合起来。例如，你可以将CDConfig bean从之前的XML文件中移除掉，而是使用第三个配置文件将这两个组合在一起：

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:c="http://www.springframework.org/schema/c"
  xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd">

  <bean class="soundsystem.CDConfig" />

  <import resource="cdplayer-config.xml" />

</beans>
```
>不管使用JavaConfig还是使用XML进行装配，我通常都会创建一个根配置（root configuration），也就是这里展现的这样，这个配置会将两个或更多的装配类和/或XML文件组合起来。我也会在根配置中启用组件扫描（通过`<context:component-scan>`或@ComponentScan）。