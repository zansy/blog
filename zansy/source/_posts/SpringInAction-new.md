title: Spring实战笔记(2019.07.16 C3)
author: zansy
tags: []
categories:

  - 读薄专业书计划
date: 2019-07-08 23:32:03
toc: true
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

![bean在Spring容器中从创建到销毁经历了若干阶段](/images/image-20190709031755602.png)

### 1.3 俯瞰Spring风景线

![Spring框架由6个定义良好的模块分类组成](/images/image-20190709031845520.png)

## 第二章 装配Bean

更为详细地介绍DI，展现应用程序中的各个组件（bean）如何装配在一起。这包括基于XML装配、基于Java装配以及自动装配。

本章可以看到如何使用自动装配让Spring完全负责将bean引用注入到构造参数和属性中。

- <u>创建应用对象之间协作关系的行为</u>通常称为**装配（wiring）**，这也是依赖注入（DI）的本质。

### 自动化装配bean

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

- Spring profile
- 条件化的bean声明
- 自动装配与歧义性
- bean的作用域
- Spring表达式语言

### 3.1 环境与profile

> 开发阶段中，需要将应用程序从一个环境迁移到另外一个环境，某些环境相关做法可能并不适合迁移到生产环境中，甚至即便迁移过去也无法正常工作。

在不同的环境中某个bean会有所不同。我们必须要有一种方法使其在每种环境下都会选择最为合适的配置。其中一种方式就是**在单独的配置类（或XML文件）中配置每个bean，然后在构建阶段（可能会使用Maven的profiles）确定要将哪一个配置编译到可部署的应用中**。这种方式的问题在于<u>要为每种环境重新构建应用</u>。

Spring所提供的解决方案并不需要重新构建。

#### 配置profile bean

Spring并不是在构建的时候做出决策<font color = 'green'>需要根据环境决定该创建哪个bean和不创建哪个bean</font>，而是等到运行时再来确定。这样的结果就是同一个部署单元（可能会是WAR文件）能够适用于所有的环境，没有必要进行重新构建。

要使用**bean profile**的功能，首先要将所有不同的bean定义整理到一个或多个profile之中，在将应用部署到每个环境时，要确保对应的profile处于激活（active）的状态。

在Java配置中，可以使用`@Profile`注解指定某个bean属于哪一个profile。例如，在配置类中，嵌入式数据库的`DataSource`可能会配置成如下所示：

```Java
@Configuration
@Profile("dev")
public class DevelopmentProfileConfig {
  @Bean(destroyMethod="shutdown")
  public DataSource dataSource() {
      return new EmbeddedDatabaseBuilder()
          .setType(EmbeddedDatabaseType.H2)
          .addScript("classpath:schema.sql")
          .addScript("classpath:test-data.sql")
          .build();
  }
}
```

> `@Profile`注解应用在了类级别上。它会告诉Spring这个配置类中的bean只有在`dev` profile激活时才会创建。如果`dev` profile没有激活的话，那么带有`@Bean`注解的方法都会被忽略掉。

从Spring 3.2开始，也可以在方法级别上使用`@Profile`注解，与`@Bean`注解一同使用。这样的话，就能将这两个bean的声明放到同一个配置类之中，如下所示：

![@Profile注解基于激活的profile实现bean的装配](/images/image-20190713065638093.png)

**没有指定profile的bean始终都会被创建，与激活哪个profile没有关系。**

#### 激活profile

- Spring在确定哪个profile处于激活状态时，需要依赖两个独立的属性：`spring.profiles.active`和`spring.profiles.default`。

- 如果设置了`spring.profiles.active`属性，那么它的值就会用来确定哪个profile是激活的。但如果没有设置`spring.profiles.active`属性的话，那Spring将会查找`spring.profiles.default`的值。

- 如果`spring.profiles.active`和`spring.profiles.default`均没有设置的话，那就没有激活的profile，因此只会创建那些没有定义在profile中的bean。

- 有多种方式来设置这两个属性：

  - 作为`DispatcherServlet`的初始化参数；

  - 作为Web应用的上下文参数；
  - 作为JNDI条目；
  - 作为环境变量；
  - 作为JVM的系统属性；
  - 在集成测试类上，使用`@ActiveProfiles`注解设置。

- 作者建议的一种方式是**使用`DispatcherServlet`的参数将`spring.profiles.default`设置为开发环境的profile，在Servlet上下文中进行设置（为了兼顾到`ContextLoaderListener`）**。例如，在Web应用中，设置`spring.profiles.default`的web.xml文件会如下所示：

![在Web应用的web.xml文件中设置默认的profile](/images/image-20190713072309139.png)

按照这种方式设置`spring.profiles.default`，所有的开发人员都能从版本控制软件中获得应用程序源码，并使用开发环境的设置（如嵌入式数据库）运行代码，而不需要任何额外的配置。

当应用程序部署到QA、生产或其他环境之中时，负责部署的人根据情况使用系统属性、环境变量或JNDI设置`spring.profiles.active`即可。当设置`spring.profiles.active`以后，至于`spring.profiles.default`置成什么值就已经无所谓了；系统会优先使用`spring.profiles.active`中所设置的profile。

- 可以同时激活多个profile，这可以通过列出多个profile名称，并以逗号分隔来实现。

- **使用profile进行测试**：Spring提供了`@ActiveProfiles`注解，我们可以使用它来指定运行测试时要激活哪个profile。

  在集成测试时，通常想要激活的是开发环境的profile。例如，下面的测试类片段展现了使用`@ActiveProfiles`激活`dev` profile

  ```Java
  @RunWith(SpringJUnit4ClassRunner.class)
  @ContextConfiguration(classes={PersistenceTestConfig.class})
  @ActiveProfiles("dev")//notice
  public class PersistenceTest {
    ...
  }
  ```

### 3.2 条件化的bean

> 背景：假设希望一个或多个bean只有在应用的类路径下包含特定的库时才创建；或者希望某个bean只有当另外某个特定的bean也声明了之后才会创建；还可能要求只有某个特定的环境变量设置之后，才会创建某个bean。
>
> 解决：引入`@Conditional`注解，它可以用到带有`@Bean注`解的方法上。如果给定的条件计算结果为`true`，就会创建这个bean，否则的话，这个bean会被忽略。

例：希望只有设置了`magic`环境属性的时候，Spring才会实例化这个类。如果环境中没有这个属性，那么`MagicBean`将会被忽略。

![条件化地配置bean](/images/image-20190713081549006.png)

设置给`@Conditional`的类可以是任意实现了`Condition`接口的类型，只需提供`matches()`方法的实现即可。需要创建`Condition`的实现并根据环境中是否存在`magic`属性来做出决策。

![在Condition中检查是否存在magic属性](/images/image-20190713082052473.png)

- 通过`ConditionContext`，我们可以做到如下几点：
  - 借助`getRegistry()`返回的`BeanDefinitionRegistry`检查bean定义；
  - 借助`getBeanFactory()`返回的`ConfigurableListableBeanFactory`检查bean是否存在，甚至探查bean的属性；
  - 借助`getEnvironment()`返回的`Environment`检查环境变量是否存在以及它的值是什么；
  - 读取并探查`getResourceLoader()`返回的`ResourceLoade`r所加载的资源；
  - 借助`getClassLoader()`返回的`ClassLoader`加载并检查类是否存在。
- `AnnotatedTypeMetadata`则能够让我们检查带有`@Bean`注解的方法上还有什么其他的注解。像`ConditionContext`一样，`AnnotatedTypeMetadata`也是一个接口。

### 3.3　处理自动装配的歧义性

> 背景：仅有一个bean匹配所需的结果时，自动装配才是有效的。如果不仅有一个bean能够匹配结果的话，这种歧义性会阻碍Spring自动装配属性、构造器参数或方法参数。

- 当确实发生歧义性的时候，Spring提供了多种可选方案来解决这样的问题。
  - 将可选bean中的某一个设为首选（primary）的bean
  - 使用限定符（qualifier）来帮助Spring将可选的bean的范围缩小到只有一个bean。

#### 标示首选的bean

在Spring中，可以通过`@Primary`来表达最喜欢的方案。`@Primary`能够与`@Component`组合用在组件扫描的bean上，也可以与`@Bean`组合用在Java配置的bean声明中。

```Java
@Component
@Primary
public class IceCream implements Dessert { ... }
```

```Java
@Bean
@Primary
public Dessert iceCream() {
    return new IceCream();
}
```

#### 限定自动装配的bean

- 设置首选bean的局限性在于`@Primary`无法将可选方案的范围限定到唯一一个无歧义性的选项中。它只能标示一个优先的可选方案。<u>当首选bean的数量超过一个时，我们并没有其他的方法进一步缩小可选范围</u>。

- Spring的限定符能够在所有可选的bean上进行缩小范围的操作，最终能够达到只有一个bean满足所规定的限制条件。
- `@Qualifier`注解是使用限定符的主要方式。它可以与`@Autowired`和`@Inject`协同使用，在注入的时候指定想要注入进去的是哪个bean。

```Java
@Autowired
@Qualifier("iceCream")
public void setDessert(Dessert dessert) {
    this.dessert = dessert;
}
```

> 为`@Qualifier`注解所设置的参数就是想要注入的bean的ID。所有使用`@Component`注解声明的类都会创建为bean，并且bean的ID为首字母变为小写的类名。

- **创建自定义的限定符**：指定的限定符与要注入的bean的名称是<u>紧耦合</u>的，对类名称的任意改动都会导致限定符失效，因此要创建自定义的限定符。可以为bean设置自己的限定符，而不是依赖于将bean ID作为限定符。在这里所需要做的就是在bean声明上添加`@Qualifier`注解。
- 可以与`@Component`组合使用
```Java
@Component
@Qualifier("cold")
public class IceCream implements Dessert { ... }
```

- 在注入的地方，只要引用cold限定符就可以了

```Java
@Autowired
@Qualifier("cold")
public void setDessert(Dessert dessert) {
  this.dessert = dessert;
}
```
- 在注入点和bean定义的地方同时再添加另外一个`@Qualifier`注解，使用更多的限定符来将可选范围限定到只有一个bean。
```Java
@Component
@Qualifier("cold")
@Qualifier("creamy")
public class IceCream implements Dessert { ... }
```

```Java
@Autowired
@Qualifier("cold")
@Qualifier("creamy")
public void setDessert(Dessert dessert) {
    this.dessert = dessert;
}
```

- 当不想用`@Qualifier`注解的时候，可以类似地创建`@Soft`、`@Crispy`和`@Fruity`。通过在定义时添加`@Qualifier`注解，它们就具有了`@Qualifier`注解的特性。它们本身实际上就成为了限定符注解。

```Java
@Component
@Cold
@Fruity
public class Popsicle implements Dessert { ... }
```

### 3.4　bean的作用域

> 在默认情况下，Spring应用上下文中所有bean都是作为以<font color = 'red'>单例（singleton）</font>的形式创建的。也就是说，<u>不管给定的一个bean被注入到其他bean多少次，每次所注入的都是同一个实例</u>。在大多数情况下，单例bean是很理想的方案。初始化和垃圾回收对象实例所带来的成本只留给一些小规模任务，在这些任务中，让对象保持无状态并且在应用中反复重用这些对象可能并不合理。有时候，可能会发现，你所使用的类是易变的（mutable），它们会保持一些状态，因此重用是不安全的。在这种情况下，将class声明为单例的bean就不是什么好主意了，因为对象会被污染，稍后重用的时候会出现意想不到的问题。

- Spring定义了多种作用域，可以基于这些作用域创建bean，包括：
  - 单例（Singleton）：在整个应用中，只创建bean的一个实例。
  - 原型（Prototype）：每次注入或者通过Spring应用上下文获取的时候，都会创建一个新的bean实例。
  - 会话（Session）：在Web应用中，为每个会话创建一个bean实例。
  - 请求（Rquest）：在Web应用中，为每个请求创建一个bean实例。
- 如果选择其他的作用域，要使用`@Scope`注解，它可以与`@Component`或`@Bean`一起使用。

```Java
@Component
@Scope(ConfigurableBeanFactory.SCOPE_PROTOTYPE)
public class Notepad { ... }
```

#### 使用会话和请求作用域

> 在Web应用中，如果能够实例化在会话和请求范围内共享的bean，那将是非常有价值的事情。例如，在典型的电子商务应用中，可能会有一个bean代表用户的购物车。如果购物车是单例的话，那么将会导致所有的用户都会向同一个购物车中添加商品。另一方面，如果购物车是原型作用域的，那么在应用中某一个地方往购物车中添加商品，在应用的另外一个地方可能就不可用了，因为在这里注入的是另外一个原型作用域的购物车。

- 就购物车bean来说，会话作用域是最为合适的，因为它与给定的用户关联性最大。

```Java
@Component
@Scope(
    value=WebApplicationContext.SCOPE_SESSION,
    proxyMode=ScopedProxyMode.INTERFACES)
public ShoppingCart cart() { ... }
```

`@Scope`同时还有一个`proxyMode`属性，它被设置成了`ScopedProxyMode.INTERFACES`。这个属性解决了将会话或请求作用域的bean注入到单例bean中所遇到的问题。

- 假设要将`ShoppingCart` bean注入到单例`StoreService` bean的Setter方法中

```Java
@Component
public class StoreService {

  @Autowired
  public void setShoppingCart(ShoppingCart shoppingCart) {
    this.shoppingCart = shoppingCart;
  }
  ...
}
```

> 我们希望的是当`StoreService`处理购物车功能时，它所使用的`ShoppingCart`实例恰好是当前会话所对应的那一个。

Spring并不会将实际的`ShoppingCart` bean注入到StoreService中，Spring会注入一个到`ShoppingCart` bean的代理。这个代理会暴露与`ShoppingCart`相同的方法，所以`StoreService`会认为它就是一个购物车。但是，当`StoreService`调用`ShoppingCart`的方法时，代理会对其进行懒解析并将调用委托给会话作用域内真正的`ShoppingCart` bean。

### 3.5　运行时值注入

- 将一个值注入到bean的属性或者构造器参数中

```Java
@Bean
public CompactDisc sgtPeppers() {
  return new BlankDisc(
      "Sgt. Pepper's Lonely Hearts Club Band",
      "The Beatles");
}
```

但它在实现的时候是将值<u>硬编码</u>在配置类中的。避免硬编码值，让这些值在运行时再确定，Spring提供了两种在运行时求值的方式：

- 属性占位符（Property placeholder）。
- Spring表达式语言（SpEL）。

#### 3.5.1　注入外部的值

在Spring中，处理外部值的最简单方式就是声明属性源并通过Spring的`Environment`来检索属性。

![使用@PropertySource注解和Environment](/images/image-20190715192743396.png)

`@PropertySource`引用了类路径中一个名为app.properties的文件。

```Java
disc.title=Sgt. Peppers Lonely Hearts Club Band
disc.artist=The Beatles
```

- `getProperty()`方法有四个重载的变种形式：
  - String getProperty(String key)
  - String getProperty(String key, String defaultValue)//在指定属性不存在的时候，会使用一个默认值
  - T getProperty(String key, Class<T> type)
  - T getProperty(String key, Class<T> type, T defaultValue)
- <FONT COLOR = 'RED'>**解析属性占位符**</FONT>: Spring一直支持将属性定义到外部的属性的文件中，并使用占位符值将其插入到Spring bean中。在Spring装配中，占位符的形式为使用“`${ ... }`”包装的属性名称。

```Java
public BlankDisc(
      @Value("${disc.title}") String title,
      @Value("${disc.artist}") String artist) {
  this.title = title;
  this.artist = artist;
}
```

为了使用占位符，我们必须要配置一个`PropertySourcesPlaceholderConfigurer` bean，因为它能够基于Spring `Environment`及其属性源来解析占位符。

如下的`@Bean`方法在Java中配置了`PropertySourcesPlaceholderConfigurer`：

```Java
@Bean
public
static PropertySourcesPlaceholderConfigurer placeholderConfigurer() {
  return new PropertySourcesPlaceholderConfigurer();
}
```

#### 3.5.2　使用Spring表达式语言进行装配

- **Spring表达式语言（Spring Expression Language，SpEL）**，它能够以一种强大和简洁的方式将值装配到bean属性和构造器参数中，在这个过程中所使用的表达式会在运行时计算得到值。
- SpEL拥有很多特性，包括：

  - 使用bean的ID来引用bean；
  - 调用方法和访问对象的属性；
  - 对值进行算术、关系和逻辑运算；
  - 正则表达式匹配；
  - 集合操作。
- 部分样例：

  - 最简单的SpEL表达式：`#{1}`
  - `#{T(System).currentTimeMillis()}`——计算表达式的那一刻当前时间的毫秒数。`T()`表达式会将`java.lang.System`视为Java中对应的类型，因此可以调用其`static`修饰的`currentTimeMillis()`方法。
  - 引用其他的bean或其他bean的属性：`#{sgtPeppers.artist}`——计算得到ID为`sgtPeppers`的bean的`artist`属性。
  - 通过`systemProperties`对象引用系统属性：`#{systemProperties['disc.title']}`
- 在bean装配的时候如何使用这些表达式：在注入属性和构造器参数时，可以使用`@Value`注解，这与之前看到的属性占位符非常类似。不过，在这里我们所使用的不是占位符表达式，而是SpEL表达式。

```Java
public BlankDisc(
      @Value("#{systemProperties['disc.title']}") String title,
      @Value("#{systemProperties['disc.artist']}") String artist) {
  this.title = title;
  this.artist = artist;
}
```

##### 1. 表示字面值

- SpEL所支持的基础表达式：
  - 整数字面量：`#{1}`
  - 浮点值：`#{3.14159}`
  - String类型：`#{'Hello'}`
  - Boolean类型：`#{false}`

##### 2. 引用bean、属性和方法

- SpEL所能做的另外一件基础的事情就是<u>通过ID引用其他的bean</u>。
  - 引用sgtPeppers的artist属性：`#{sgtPeppers.artist}`
  - 调用bean上的方法：`#{artistSelector.selectArtist()}`
  - 对于被调用方法的返回值来说，同样可以调用它的方法：`#{artistSelector.selectArtist().toUpperCase()}`
  - 为了避免出现NullPointerException，我们可以使用类型安全的运算符：`#{artistSelector.selectArtist()?.toUpperCase()}`使用了“?.”运算符。这个运算符能够在访问它右边的内容之前，确保它所对应的元素不是null。所以，如果selectArtist()的返回值是null的话，那么SpEL将不会调用toUpperCase()方法。表达式的返回值会是null。


##### 3. 在表达式中使用类型
- 如果要在SpEL中访问类作用域的方法和常量的话，要依赖T()这个关键的运算符。T()运算符的真正价值在于它能够访问目标类型的静态方法和常量。
	- 在SpEL中表达Java的Math类：`T(java.lang.Math)`
	- 将PI值装配到bean属性中：`T(java.lang.Math).PI`
	- 调用T()运算符所得到类型的静态方法：`T(java.lang.Math).random()`

##### 4. SpEL运算符

![用来操作表达式值的SpEL运算符](/images/image-20190716021457013.png)

- 样例：
  - 计算circle bean中所定义圆的周长：`#{2 * T(java.lang.Math).PI * circle.radius}`
  - 使用String类型的值时，“+”运算符执行的是连接操作：`#{disc.title + ' by ' + disc.artist}`
  - 要比较两个数字是不是相等，可以使用双等号运算符（==）：`#{counter.total == 100}` or `#{counter.total eq 100}`

##### 5. 计算正则表达式

SpEL通过matches运算符支持表达式中的模式匹配。matches运算符对String类型的文本（作为左边参数）应用正则表达式（作为右边参数）。matches的运算结果会返回一个Boolean类型的值：如果与正则表达式相匹配，则返回true；否则返回false。
例：判断一个字符串是否包含有效的邮件地址 `#{admin.email matches '[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\\.com'}`

##### 6. 计算集合
- 例：
	- 引用列表中的一个元素：`#{jukebox.songs[4].title}`
	- 从jukebox中随机选择一首歌：`#{jukebox.songs[T(java.lang.Math).random() * jukebox.songs.size()].title}` 
	- 从String中获取一个字符：`#{'This is a test'[3]}`
	- <font color = 'red'>查询运算符（.?[]）</font>，它会用来对集合进行过滤，得到集合的一个子集。使用查询运算符得到了Aerosmith的所有歌曲：`#{jukebox.songs.?[artist eq 'Aerosmith']}`
	- 另外两个<font color = 'red'>查询运算符：“.^[]”和“.$[]”</font>，它们分别用来在集合中查询第一个匹配项和最后一个匹配项。查找列表中第一个artist属性为Aerosmith的歌曲：`#{jukebox.songs.^[artist eq 'Aerosmith']}`
	- <font color = 'red'>投影运算符（.![]）</font>，它会从集合的每个成员中选择特定的属性放到另外一个集合中。将title属性投影到一个新的String类型的集合中：`#{jukebox.songs.![title]}`
	- <u>投影操作可以与其他任意的SpEL运算符一起使用</u>。比如，获得Aerosmith所有歌曲的名称列表：`#{jukebox.songs.?[artist eq 'Aerosmith'].![title]}`

### 第三章小结

首先，学习了Spring profile，它解决了Spring bean要跨各种部署环境的通用问题。在运行时，通过将环境相关的bean与当前激活的profile进行匹配，Spring能够让相同的部署单元跨多种环境运行，而不需要进行重新构建。

Profile bean是在运行时条件化创建bean的一种方式，但是Spring 4提供了一种更为通用的方式，通过这种方式能够声明某些bean的创建与否要依赖于给定条件的输出结果。结合使用@Conditional注解和Spring Condition接口的实现，能够为开发人员提供一种强大和灵活的机制，实现条件化地创建bean。

还看了两种解决自动装配歧义性的方法：首选bean以及限定符。尽管将某个bean设置为首选bean是很简单的，但这种方式也有其局限性，所以讨论了如何将一组可选的自动装配bean，借助限定符将其范围缩小到只有一个符合条件的bean。除此之外，还看到了如何创建自定义的限定符注解，这些限定符描述了bean的特性。

尽管大多数的Spring bean都是以单例的方式创建的，但有的时候其他的创建策略更为合适。Spring能够让bean以单例、原型、请求作用域或会话作用域的方式来创建。在声明请求作用域或会话作用域的bean的时候，还学习了如何创建作用域代理，它分为基于类的代理和基于接口的代理的两种方式。

最后，学习了Spring表达式语言，它能够在运行时计算要注入到bean属性中的值。


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

