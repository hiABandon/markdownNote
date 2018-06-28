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
public class Product {}
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
