Spring
===
- **Spring**是一个基于**IOC**和**AOP**的结构J2EE系统的框架
  - IOC(Inversion Of Control)控制反转
    - Spring的基础
    - 简单的说就是对象由以前的coder自己new构造方法来创建
    - 变成交**由Spring创建对象**
  - DI(Dependency Inject) 依赖注入
    - 简单地说就是拿到的对象的属性，是已经被注入好相关值了，直接可以使用

IOC/DI
---
- 名词解释
  - pojo(Plain Ordinary Java Objects)简单的java对象
    - 普通的javaBeans
    - 不允许有业务方法，也不能携带connection之类的方法

#### 1. 导入各种所需要的jar包
#### 2. 创建pojo Category，用来演示IOC和DI
  - Category 类有
    - 属性：int id; String name;
    - 方法：两个属性的getter和setter方法
#### 3. applicationContext.html
在src目录下新建applicationContext.xml文件
- applicationContext.xml是Spring的**核心配置文件**
  - 通过关键字c可以获得Category对象
  - 该对象获取的时候，即被注入了字符串"category 1"到名为name属性中
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:aop="http://www.springframework.org/schema/aop"
    xmlns:tx="http://www.springframework.org/schema/tx"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="
   http://www.springframework.org/schema/beans
   http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
   http://www.springframework.org/schema/aop
   http://www.springframework.org/schema/aop/spring-aop-3.0.xsd
   http://www.springframework.org/schema/tx
   http://www.springframework.org/schema/tx/spring-tx-3.0.xsd
   http://www.springframework.org/schema/context
   http://www.springframework.org/schema/context/spring-context-3.0.xsd">
   <!-- 以上是基本配置 固定格式 -->
   <bean name="c" class="com.how2java.pojo.Category">
    <property name="name" value="category 1" />
   </bean>
```

4. TestSpring

```java
public Class TestSpring {
  public static void main(String[] args) {
    ApplicationContext applicationContext = new ClassPathXmlApplicationContext(new String[] {"applicationContext.xml"});
    Category c = (Category) context.getBean("c");
    System.out.print(c.getName());
  }
}
```

5. 原理图

以获取对象的方式来进行比较
`
传统的方式：
通过new 关键字主动创建一个对象
IOC方式
对象的生命周期由Spring来管理，直接从Spring那里去获取一个对象。 IOC是反转控制 (Inversion Of Control)的缩写，就像控制权从本来在自己手里，交给了Spring。

> 打个比喻：
传统方式：相当于你自己去菜市场new 了一只鸡，不过是生鸡，要自己拔毛，去内脏，再上花椒，酱油，烤制，经过各种工序之后，才可以食用。

> 用 IOC：相当于去馆子(Spring)点了一只鸡，交到你手上的时候，已经五味俱全，你就只管吃就行了。

注入对象
---
在上一个例子中，对Category的name属性注入了"category 1" 字符串（注入基本类型）

在本例中，对Product对象，注入一个Category对象（注入~~引用类型~~）

- Product.java
```java
public class Product {
  private int id;
  private String name;
  private Category category;
  // 各种getter和setter方法
}
```

- 在applicationContext.xml中配置bean
  - 配置的时候使用ref来注入另一对象
```xml
<!-- 基本固定配置省略 -->
<bean name="c" class="com.how2java.pojo.Category">
  <property name="name" value="category 1" />
</bean>
<bean name="p" class="com.how2java.pojo.Product">
  <property name="name" value="product1" />
  <property name="category" ref="c" />
</bean>
```

注解方式IOC/DI
---
使用注解方式步骤
#### 1. 修改applicationContext.xml
在固定配置之后，bean之前添加如下代码
`<context:annotation-config/>`
表示告诉Spring要用注解的方式进行配置
并注释掉使用注解来完成的代码

```xml
<!-- 固定配置代码 -->
<context:annotation-config/>
<bean name="c" class="com.how2java.pojo.Category">
        <property name="name" value="category 1" />
    </bean>
    <bean name="p" class="com.how2java.pojo.Product">
        <property name="name" value="product1" />
<!--         <property name="category" ref="c" /> -->
    </bean>
```
#### 2. @Autowired
在Product.java的category属性前加上`@Autowwired`注解
```java
package com.how2java.pojo;

import org.springframework.beans.factory.annotation.Autowired;

public class Product {

    private int id;
    private String name;
    @Autowired
    private Category category;

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Category getCategory() {
        return category;
    }

    public void setCategory(Category category) {
        this.category = category;
    }
}
```

#### 3. 运行测试
运行测试程序

```java
package com.how2java.test;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import com.how2java.pojo.Product;

public class TestSpring {

    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext(new String[] { "applicationContext.xml" });
        Product p = (Product) context.getBean("p");
        System.out.println(p.getName());
        System.out.println(p.getCategory().getName());
    }
}
```

#### 4. @Autowired的位置
除了上述**在属性前加上@Autowired**这种方式外

也可以在 ==setCategory**方法**== 前加上@Autowired,来达到相同效果

```java
package com.how2java.pojo;

import org.springframework.beans.factory.annotation.Autowired;

public class Product {

    private int id;
    private String name;

    private Category category;

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Category getCategory() {
        return category;
    }
    @Autowired
    public void setCategory(Category category) {
        this.category = category;
    }
}
```
也就是说@Autowired可以标在属性上，也可以标在方法上。

#### 5. @Resource
除了@Autowired之外，`@Resource`也是常用手段
```java
package com.how2java.pojo;

import javax.annotation.Resource;

public class Product {

    private int id;
    private String name;
    @Resource(name="c")
    private Category category;

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Category getCategory() {
        return category;
    }

    public void setCategory(Category category) {
        this.category = category;
    }
}
```

> - 小结：
>  - `@Autowired和@Resource`的作用
>  - 都是对 **注入对象行为** 的注解。
> 以下介绍对Bean进行注解

对Bean的注解
---

#### 1.修改applicationContext.xml
什么都去掉只保留固定配置，再加上`<context:component-scan base-package="com.how2java.pojo" />`

作用是告诉Spring，bean都放在com.how2java.pojo这个包下

```xml
 <!-- 固定配置省略 -->
 <context:component-scan base-package="com.how2java.pojo" />
```

#### 1. @Component
为Proudct类加上`@Component`注解，表明此类是bean
```java
@component
public class Product {
  private int id;
  private String name="product 1";

  @Autowired
  private Category category;

  public int getId() { return id; }

  public void setId() { return id; }
}
```

为Category加上@Component注解，即表明此类是bean
```java
@Component("c")
public class Category{}
```

另外，因为配置从applicationContext.xml中移出来了，所以属性初始化放在属性声明上进行。
```java
private String name="product 1";
private String name="category 1";
```

#### 2. 运行测试类，发现结果都一样

- 小结：
  - 用于注入**属性**的注解
    - **@Autowired**
      - 默认按照**类型方式**进行bean匹配
      - 是Spring的注解
    - **@Resource**
      - `@Resource(name="c")`或`@Resource`
      - 默认按照**名称方式**进行bean匹配
      - (import javax.annotation.Resource)
    - 添加`<context:annotation-config/>`
  - 用于对**Bean**进行注解配置
    - 添加`<context:component-scan base-package="com.how2java.pojo"/>`
    - **@Component("beanId")**

AOP Aspect Oriented Program 面向切面编程
---
- 在面向切面编程的思想里，把功能分为**核心业务功能**和**周边功能**
  - 所谓核心业务：
    - 登录、增加数据、删除数据
  - 所谓周边功能：
    - 性能统计、日志、事物管理
- 周边功能在Spring的面向切面编程AOP思想里，即被定义为**切面**

在面向切面编程AOP思想里，和兴业务功能和切面功能分别**独立进行开发**然后把切面功能和核心业务功能**编制**在一起，这就叫AOP

- 再来些废话
  - 功能分两大类，辅助功能和核心业务功能
  - 粗朱功能和核心业务功能**彼此独立**进行开发
  - 例如登陆功能，即使没有新能统计和日志输出，也可以正常运行
  - 如果有需要，就把"日志输出"功能和"登陆"功能**编织**在一起，这样登陆的时候，就可以看到日志输出了。
  - 辅助功能，又叫做**切面**，这种能够**选择性的，低耦合的**把切面和核心业务功能结合在一起的编程思想，就叫做切面。

### xml方式配置AOP
- 业务类
```java
public class ProductService() {
  public void doSomeService() {
    System.out.print("doSomeService");
  }
}
```
- 切面类
```java
public class LoggerAspect {
  public Object log(ProceedingJoinPoint joinPoint) throws Throwable {
    System.out.print("start log:" + joinPoint.getSignature().getName());
    Object object = joniPoint.proceed();
    System.out.print("end log" + joinPoint.getSignature().getName());
  }
}
```
- xml配置
```xml
<bean id="productService" class="xxxxxxxx">
<bean id="LoggerAspect" class="xxxxxxxx">

<aop:config>
  <aop:pointcut id="loggerCutpoint"
    expression="execution(* com.spring.xml.service.ProductService.*(..))" />
    <!-- 定义切入点方法 -->
    <!-- execution[执行]*[任意返回类型]com.spring....ProductService[这个包下].*(..)[任意参数的方法] -->
    <!-- 也就是说execution句意思是执行的是com..ProductService包下任意方法 -->
  <aop:aspect id="logAspect" ref="loggerAspect">
    <!-- 定义切面 -->
    <aop:around pointcut-rec="loggerCutpoint" method="log" />
    <!-- 指定切入点方法 -->
  </aop:aspect>
</aop:config>
```
### 注解方式配置AOP
- @Aspect注解表示这是一个切面
- @Component注解表示这是一个bean，由Spring进行管理
- @Around(value="execution(* com.how2java.service.ProductService).* ")
  - 表示对com.how2java.service.ProductService这个类中的所有的方法进行切面操作

```java
package com.how2java.aspect;

@Aspect
@Component
public class LoggerAspect {
  @Around(value = "execution(* com.how2java.service.ProductService.*(..))")
  public Object log(ProceedingJoinPoint joinPoint) throws Throwable {
    System.out.print("start log:" + joinPoint.getSignature().getName());
    Object object = joinPoint.proceed();
    System.out.print("end log:" + joinPoint.getSignature().getName());
    return object;
  }
}
```

- applicationContext.xml配置
去掉原有信息，添加如下3行

```xml
<context:component-scan base-package="com.how2java.aspect"/>
<context:component-scan base-package="com.how2java.service"/>
```
扫描包com.how2java.aspect和com.how2java.service，定位业务类和切面类

```xml
<aop:aspectj-autoproxy/>
```
找到被注解了的切面类，进行切面配置

```xml
  <context:component-scan base-package="com.how2java.aspect"/>
  <context:component-scan base-package="com.how2java.service"/>
  <aop:aspectj-autoproxy/>
```

注解方式测试
---
Spring注解方式测试

#### TestSpring
```java
package com.how2java.test;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext.xml")
public class TestSpring {
  @Autowired
  Category c;

  @Test
  public void test() {
    System.out.print(c.getName());
  }
}
```
- 一些解释：
- 1. @RunWith(SprigJUnit4ClassRunner.class)
  - 表示这是一个Spring的测试类
- 2. @ContextConfiguration("classpath:applicationContext.xml")
  - 定位Spring的配置文件
- 3. @Autowired
  - 给这个测试类装配Category对象
- 4. @Test
  - 测试逻辑，打印c对象的名称

怎么也不成功，不能理解....

- 总结：
  - 使用注解需要在xml里面添加相应配置信息
    - 设值注入使用`<context:annotation-config/>`
    - 对bean的注解使用``<context:component-scan base-package="com.how2java.pojo" />``
