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
- 表示访问路径 **/index** 会交给id=indexContoller的bean处理
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

#### 7. 原理
1. 用户访问/index
2. 根据web.xml中的配置 所有的访问都会经过DispatcherServlet
3. 根据配置文件springmvc-servlet.xml，访问路径/index会进入IndexController类
4. 在IndexController中指定跳转到页面index.jsp,并传递message数据
5. 在index.jsp中显示message信息

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

- 完整xml
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
#### 4.测试

注解方式
---
上述都是讲解使用配置方式进行跳转

本例讲解如何使用**注解的方式**进行跳转的配置

#### 1. 修改IndexController
- 在类前面加上 **@Controller** 表示该类是一个控制器
- 在方法handleRequest前面加上 **@RequestMapping("/index")** 表示路径/index会映射到该方法上
- 注意：**不再** 让IndexController实现Controller接口

```java
package controller;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

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
- 注意：
  - springmvc-servlet.xml文件的文件名一定要为springmvc-servlet.xml；否则无法识别加载
  - web.xml中<servlet>的配置映射要正确，莫把**servlet**写成 **~~serlvet~~**

***

接受表单数据
----
浏览器提交数据是非常常见的场景，本例演示用户提交产品名称和价格到Spring MVC Spring MVC如何接受数据

#### 1. 创建实体类Product
```java
package pojo;
public class Product {
  private int id;
   private String name;
   private float price;
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
   public float getPrice() {
       return price;
   }
   public void setPrice(float price) {
       this.price = price;
   }
}
```

#### 2. addProduct.jsp
- 在**web目录下**（不是WEB-INF下）增加商品的页面addProduct.jsp
  - **注意**：产品名称input的name要使用**name**,**而不是**product.name

  - addProduct.jsp
```js
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8" import="java.util.*" isELIgnored="false"%>

<form action="addProduct">

    产品名称 ：<input type="text" name="name" value=""><br />
    产品价格： <input type="text" name="price" value=""><br />

    <input type="submit" value="增加商品">
</form>
```

#### 3. ProductController.java
控制器ProductController, 准备一个add方法映射/addProduct路径

为add方法准备一个Product参数，用于接收注入

最后跳转到showProduct页面显示用户提交的数据

  - 注：**addProduct.jsp** 提交的name和price会**自动注入**到参数product里
  - 注：参数product会**默认**被当做**值**加入到**ModelAndView**中，相当于:
    - `mav.addObject("product", product);`

```java
package controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.ModelAttribute;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.servlet.ModelAndView;

import pojo.Product;

@Controller
public class ProductController {

    @RequestMapping("/addProduct")
    public ModelAndView add(Product product) throws Exception {
        ModelAndView mav = new ModelAndView("showProduct");
        return mav;
    }
}
```

#### 4. showProduct.jsp
在**WEB-INF/page**目录下创建showProduct.jsp

用EL表达式显示用户提条的名称和价格

```js
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8" isELIgnored="false"%>

产品名称： ${product.name}<br>
产品价格： ${product.price}
```

***

客户端跳转
---
前面的例子中，无论是/index跳转到index.jsp还是/addProduct跳转到showProduct.jsp，都是**服务端跳转**（切方式都为GET即带着参数的）

本例讲解如何进行**客户端跳转**

最终效果是，访问地址http://127.0.0.1:8080/springmvc/jump结果客户端跳转到了127.0.0.1:8080/springmvc/index

#### 1. 修改IndexController
- 首先映射/jum到jump()方法
- 在jump()中编写如下代码
`ModelAndView mav = new ModelAndView("redirect:/index");`
- **redirect:index**即表示客户端跳转的意思
```java
@Controller
public class indexController {
  @RequestMapping("/index")
  public ModelAndView handleRequest(HttpServletRequest request, HttpServlerResponse response) throws Exception {
    ModelAndView mav = new ModelAndView("index");
    mav.addObject("message", "HelloSpring MVC");
    return mav;
  }
  @RequestMapping("/jump")
  public ModelAndView jump() {
    ModelAndView mav = new ModelAndView("redirect:/index");
    return mav;
  }
}
```

- ###### 拓展：请求转发（forward)和重定向(Redirect)的区别
  - 略

***

Session
---
Session在用户登录，一些特殊场合的页面间传递数据的时候经常会用到

最终效果：访问路径/check，可以不停刷新session中记录的访问次数

#### 1. 修改IndexController
- 映射/check到方法check()
- 为方法check()提供**参数HttpSession session**，这样就可以在方法体重使用session
- 接下来的逻辑就是每次访问为session中的count+1
- 最后跳转到check.jsp页面

```java
@controller
public clas IndexController {
  @RequestMapping("/check")
  public ModelAndView check(HttpSession session) {
    Integer i = (Integer) session.getAttribute("count");
    if(i == null)
      i = 0;
    i++;
    session.setAttribute("count", i);
    ModelAndView mav = new ModelAndView("check");
    return mav;
  }
}
```

#### 2. check.jsp
```js
<%@ page language="java" contentType="text/html; charset=UTF-8"
  pageEncoding="UTF-8" isELIgnored="false" %>
session中记录的访问次数: ${count}
```

3. 访问页面测试

***

中文问题
---
在Spring MVC中处理中文问题和**Filter处理中文问题**是一样的手段

- 效果：
  - 在之前的例子（addProduct）中提交中文参数不可以正常显示
  - 本例中药提交中文参数，切正常显示

#### 1. Filter
修改web.xml添加Filter过滤器

- 添加Filter配置
```xml
<filter>
  <filter-name>CharacterEncodingFilter</filter-name>
  <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter>
  <init-param>
    <param-name>encoding</param-name>
    <param-value>utf-8</param-value>
  </init-param>
</filter>
<filter-mapping>
  <filter-name>CharacterEncodingFilter</filter-name>
  <url-pattern>/*</url-pattern>
</filter-mapping>
```

- 完整xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app version="2.4" xmlns="http://java.sun.com/xml/ns/j2ee"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee
http://java.sun.com/xml/ns/j2ee/web-app_2_4.xsd">
    <servlet>
        <servlet-name>springmvc</servlet-name>
        <servlet-class>
            org.springframework.web.servlet.DispatcherServlet
        </servlet-class>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
    <filter>
        <filter-name>CharacterEncodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>utf-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>CharacterEncodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
</web-app>
```

#### 2. addProduct.jsp 为form设置method="post"
为form设置method="post"

```js
<%@ page language="java" contentType="text/html; charset=UTF-8"
  pageEncoding="UTF-8" import="java.util.*" isELIgnored="false"%>

  <form action="addProduct" method="post">
    产品名称：<input type="text" name="name" value=""><br/>
    产品价格：<input type="text" name="price" value=""><br/>
    <input type="submit" value="增加商品">
  </form>
```

#### 3.访问页面测试

***

上传文件
---
访问localhost:8080/springmvc/upload.jsp并上传图片显示效果

#### 1. 配置web.xml使其允许访问*.jpg
在web.xml中新增一段
```xml
<servlet-mapping>
  <servlet-name>default</servlet-name>
  <url-pattern>*.jpg</url-pattern>
</servlet-mapping>
```
表示允许访问*.jpg

- **Why add this code here?**
  - 因为配置springmvc的servlet的时候，使用的路径是 **"/"** 导致静态资源在默认情况下不能访问，所以要加上这一段，允许访问jpg资源。**并且必须加在springmvc的servlet之前**
  - 若配置spring-mvc使用的路径是/*.do，就不会有这个问题了
- **注**：这里仅仅是允许访问jpg，如果要显示png,git需要额外进行配置

- 自此完整web.xml配置代码
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app version="2.4" xmlns="http://java.sun.com/xml/ns/j2ee"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee
http://java.sun.com/xml/ns/j2ee/web-app_2_4.xsd">

    <servlet-mapping>
        <servlet-name>default</servlet-name>
        <url-pattern>*.jpg</url-pattern>
    </servlet-mapping>

    <servlet>
        <servlet-name>springmvc</servlet-name>
        <servlet-class>
            org.springframework.web.servlet.DispatcherServlet
        </servlet-class>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
    <filter>
        <filter-name>CharacterEncodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>utf-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>CharacterEncodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
</web-app>
```

#### 2. 配置springmvc-serlvet.xml
- 新增配置
`<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver"/>`
- 开放对上传功能的支持

- 自此springmvc-servler完整代码
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
    <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver"/>
</beans>
```

#### 3. upload.jsp上传页面
- 上传页面，需要注意到的是form的两个属性必须提供
  - **method="post"**
  - **encytpe="multipart/form-data"**
    - 缺一不可
  - 上传组件 增加一个属性 **accept="image/*"**表示只能选择图片进行上传
  - 留意`<input type="file" name="image" accept="image/*"/>`这个image，后面会使用到
- upload.jsp
```js
<%@ page language="java" contentType="text/html; charset=UTF-8"
  pageEncoding="UTF-8" import="java.util.*" isELIgnored="false"%>
  <form action="uploadImage" method="post" encytpe="multipart/form-data">
选择图片：<input type="file" name="image" accept="image/*" /> <br/>
  <input type="sumbit" value="上传">
</form>
```

#### 4. 准备UploadedImageFile.java
在UploadedImageFile类中封装MultipartFile类型的字段image，用于接受页面的注入

这里的字段image必须和上传页面upload.jsp中的image`<input type="file" name="image" accept="image/*"/>`保持一致

```java
package pojo;

import org.springframework.web.multipart.MultipartFile;

public class UploadedImageFile {
    MultipartFile image;

    public MultipartFile getImage() {
        return image;
    }

    public void setImage(MultipartFile image) {
        this.image = image;
    }
}
```

#### 5. UploadController上传控制器
- 新建类**UploadController**作为上传控制器
  - 准备方法upload映射上传路径/uploadImage
    1. 方法的第二个参数UploadedImageFile中已经注入好了image
    2. 通过RandomStringUtils.randomAlphanumeric(10);获取一个随机文件名。因为用户可能上传相同的文件名，为了不覆盖原来的文件，通过随机文件名的办法来规避。
    3. 根据`request.getServletContext().getRealPath`
    4. 调用`file.getImage().transferTo(newFile);`复制文件
    5. 把生成的随机文件名提交给视图，用于后续的显示

```java
package controller;

import java.io.File;
import java.io.IOException;

import javax.servlet.http.HttpServletRequest;

import org.apache.commons.lang.xwork.RandomStringUtils;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

import pojo.UploadedImageFile;

@Controller
public class UploadController {

    @RequestMapping("/uploadImage")
    public ModelAndView upload(HttpServletRequest request, UploadedImageFile file)
            throws IllegalStateException, IOException {
        String name = RandomStringUtils.randomAlphanumeric(10);
        String newFileName = name + ".jpg";
        File newFile = new File(request.getServletContext().getRealPath("/image"), newFileName);
        newFile.getParentFile().mkdirs();
        file.getImage().transferTo(newFile);

        ModelAndView mav = new ModelAndView("showUploadedFile");
        mav.addObject("imageName", newFileName);
        return mav;
    }
}
```

#### 6. showUploadedFile.jsp显示图片的页面
在WEB-INF/page下新建文件showUploadedFile显示上传的图片
```js
<%@ page language="java" contentType="text/html; charset=UTF-8"
  pageEncoding="UTF-8" isELIgnored="false" %>
  <img src="image/${imageName}/">
```

#### 7. 访问页面

***

拦截器
---
本知识点给基于**视图定位**进行

#### 拦截器类：IndexInterceptor
