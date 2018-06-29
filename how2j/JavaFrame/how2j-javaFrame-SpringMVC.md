SpringMVC
===

hello spring SpringMVC
---
#### 1. 创建dynamic web project
#### 2. 导入jar包
#### 3. 创建web.xml
- 在WEB-INF目录下创建web.xml
  - 配置Spring MVC的入口**DispatcherServlet**，把所有的请求都提交到该Servlet
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app version="2.4" xmlns="http://java.sun.com/xml/ns/j2ee"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee
http://java.sun.com/xml/ns/j2ee/web-app_2_4.xsd">

    <servlet>
        <servlet-name>springmvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
```
  - 再在WEB-INF目录下创建springmvc-servlet.xml，
    - 与上一步中的`<servlet-name>springmvc</servlet-name>`  **springmvc**对应

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE beans PUBLIC "-//SPRING//DTD BEAN//EN" "http://www.springframework.org/dtd/spring-beans.dtd">
<beans>
  <bean id="simpleUrlHandlerMapping" class="org.springframework.web.servlet.handler.simpleUrlHandlerMapping">
    <property name="mappings">
      <props>
        <prop key="/index">indexController</prop>
      </props>
    </property>
  </bean>

  <bean id="indexController" class="controller.IndexController" />
</beans>
```
- 这是Spring MVC 的映射配置文件
- 表示访问路径**/index**会交给id=indexContoller的bean处理
- id=indexController的bean配置为类:**IndexController**

#### 4. 控制类IndexController
控制类IndexController实现接口Controller，提供方法handleRequest处理请求

SpringMVC通过**ModelAndView**对象把模型和视图结合在一起
```java
ModelAndView mav = new ModelAndView("index.jsp");
mav.addObject("message", "Hello Spring MVC");
```
表示视图是index.jsp，模型数据是message,内容是"Hello Spring MVC"

```java
package controller;

public class IndexController implements Controller {
  public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
    ModelAndView mav = new ModelAndView("index.jsp");
    mav.addObject("message", "Hello Spring MVC");
    return mav;
  }
}
```

#### 5. 准备index.jsp
在WebContent目录下创建index.jsp，
index.jsp很简单，通过**EL表达式**现实message的内容
```js
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8" isELIgnored="false"%>

<h1>${message}</h1>
```

#### 6.访问地址：
http://127.0.0.1:8080/springmvc/index

视图定位
---
如果代码写成这样`new ModelAndView("index.jsp");`,表示跳转到页面index.jsp

**视图定位** 指代码写成`new ModelAndView("index");`但是会跳转到**/WEB-INF/page/index.jsp

虽然上述两种写法效果是一样的，但是视图的配置方式发生了变化

#### 1. 修改springmvc-servlet.xml
增加：
```xml
<bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
  <property name="prefix" value="WEB-INF/page/" />
  <property name="suffix" value=".jsp"/>
</bean>
```
其作用是把视图约定在**/WEB-INF/page/*.jsp**这个位置

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE beans PUBLIC "-//SPRING//DTD BEAN//EN" "http://www.springframework.org/dtd/spring-beans.dtd">
<beans>
  <bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <property name="prefix" value="WEB-INF/page/" />
    <property name="suffix" value=".jsp"/>
  </bean>

  <bean id="simpleUrlHandlerMapping"
        class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
        <property name="mappings">
            <props>
                <prop key="/index">indexController</prop>
            </props>
        </property>
    </bean>

    <bean id="indexController" class="controller.IndexController"></bean>
</beans>
```

#### 2. 修改IndexController
把
`ModelAndView mav = new ModelAndView("index.jsp");`
修改成
`ModelAndView mav = new ModelAndView("index");`

```java
package controller;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.springframework.web.servlet.ModelAndView;
import org.springframework.web.servlet.mvc.Controller;

public class IndexController implements Controller {
    public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
        ModelAndView mav = new ModelAndView("index");
        mav.addObject("message", "Hello Spring MVC");
        return mav;
    }
}
```

#### 3.把index.jsp移动到WEB-INF/page目录下

注解方式
---
上述都是讲解使用配置方式进行跳转

本例讲解如何使用注解的方式进行跳转的配置

#### 1. 修改IndexController
- 在类前面加上**@Controller**表示该类是一个控制器
- 在方法handleRequest前面加上 **@RequestMapping("/index")** 表示路径/index会映射到该方法上
- 注意：**不再** 让IndexController实现Controller接口

```java
package controller;

@Controller
public class IndexController {
  @RequestMapping("/index")
  public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
    ModelAndView mav = new ModelAndView("index");
    mav.addObject("message", "Hello Spring MVC");
    return mav;
  }
}
```

#### 2. 修改springmvc-servlet.xml的相关配置
去掉映射相关的配置，因为已经使用**注解方式**了

增加
`<context:component-scan base-package="controller"/>`

表示从包controller下扫描有@Controller注解的类

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context-3.0.xsd">

    <context:component-scan base-package="controller" />
    <bean id="irViewResolver"
        class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/page/" />
        <property name="suffix" value=".jsp" />
    </bean>
<!--     <bean id="simpleUrlHandlerMapping" -->
<!--         class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping"> -->
<!--         <property name="mappings"> -->
<!--             <props> -->
<!--                 <prop key="/index">indexController</prop> -->
<!--             </props> -->
<!--         </property> -->
<!--     </bean> -->
<!--     <bean id="indexController" class="controller.IndexController"></bean> -->
</beans>
```

#### 访问http://127.0.0.1:8080/springmvc/index测试
