Listener监听器
===
Listener的作用是用于监听web应用的创建和销毁，以及在其上attribute发生的变化。

web应用即ServletContext对象（jsp的**隐式对象applicaiton**）

处理对web应用的监听外，还能监听session和request的生命周期，以及他们的attribute发生的变化

监听Context
---
- 对Context的监听分
  - 生命周期的监听
  - Context上Attribute变化的监听两种。

#### 编写ContextListener
- ContextListener实现接口ServletContextListener有两个方法
  - `public void contextDestroyed(ServletContextEvent arg0)`
    - 对应当前web应用的销毁
  - `public void contextInitialized(ServlerContextEvent arg0)`
    - 对应当前web应用的初始化

```java
package listener;

public class ContextListener implements ServletContextListener {
  @Override
  public void contextDestroyed(ServletContextEvent arg0) {
    System.out.print("web应用销毁");
  }

  public void contextInitialized(ServletContextEvent arg0) {
    System.out.print("web应用初始化");
  }
}
```

#### 配置web.xml
配置监听器
```xml
<listener>
  <listener-class>listener.ContextListener</listener-class>
</listener>
```
#### 编写ContextAttributeListener
```java
package listener;

public class ContextArrtibuteListener implements ServletContextListener {
  @Override
  public void attributeAdded(ServletContextAttributeEvent e) {
    System.out.print("增加属性 ");
    System.out.print("属性是" + e.getName());
    System.out.print("值是" + e.getValeu());
  }

  @Override
  public void attributeRemoved(ServletContextAttributeEvent e) {
    System.out.print("移除属性");
  }

  @Override
  public void attributeReplaced(ServletContextAttributeEvent e) {
    System.out.print("替换属性");
  }
}
```

- 配置web.xml
  - 编写ContextAttributeListener
```xml
<listener>
  <listener-class>listener.ContextAttributeListener</listener-class>
</listener>
```

- testCibtext.jsp
  - 故意造成Context属性的增加，替换和移除
```js
<&@ page language="java" contentType="text/html; charset=UTF-8"
  pageEncoding="UTF-8" &>
  <%
    application.setAttribute("test", 1);
    application.setAttribute("test", 2);
    application.removeAttribute("test");
  %>
```
监听Session
---
对Session的监听分生命周期监听，和Session上Attribute变化的监听哪两种。

```java
public class SessionListener implements HttpSessionListener {
  @Override
  public  void sessionCreated(HttpSessionEvent e) {
    System.out.print("监听到session 创建，sessonid是：" + e.getSession().getId());
  }
  @Override
  public void sessionDestroyed(HttpSessionEvent e) {
    System.out.print("监听到session销毁，sessionid是：" + e.getSession().getId());
  }
}
```

配置 web.xml
```xml
<listener>
  <listener-class>listener.SessionListener</listener-class>
</listener>
```

略
略
略

监听Request
---
对Request的监听分生命周期的监听，和Request上Attribute变化的监听两部分

略
略
略

统计在线人数
---
Http协议是短连接的，所以无法在服务端根据建立了多少连接来统计当前有多少人在线。
- 不过可以通过统计session有多少来估计在线人数。
- 一个用户访问服务器就会创建一个session,如果用户持续访问，那么session持续有效
- 若长时间9(比如30分钟)该用户没有任何操作，就表示用户下线了，对应session夜会被销毁

```java
package listener;

import javax.servlet.ServletContext;

import javax.servlet.http.HttpSessionEvent;
import javax.servlet.http.HttpSessionListener;

public class OnlineNumberListener implements HttpSessionListener {

    @Override
    public void sessionCreated(HttpSessionEvent e) {

        ServletContext application = e.getSession().getServletContext();

        Integer online_number = (Integer) application.getAttribute("online_number");

        if (null == online_number)
            online_number = 0;
        online_number++;
        application.setAttribute("online_number", online_number);

        System.out.println("新增一位在线用户");
    }

    @Override
    public void sessionDestroyed(HttpSessionEvent e) {

        ServletContext application = e.getSession().getServletContext();

        Integer online_number = (Integer) application.getAttribute("online_number");

        if(null==online_number){
            online_number = 0;
        }
        else
            online_number--;
        application.setAttribute("online_number", online_number);
        System.out.println("一位用户离线");
    }
}
```
- 配置web.xml

```xml
<listener>
    <listener-class>listener.OnlineNumberListener</listener-class>
</listener>
```

- checkOnlineNumber.jsp

通过EL表达式，直接获取applicaiton中的值
```js
<%@ page language="java" contentType="text/html; charset=UTF-8"
pageEncoding="UTF-8" isELignored="false"%>
当前在线人数：${online_number}
```
