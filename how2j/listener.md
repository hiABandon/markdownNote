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
- public void attributeAdded(ServletContextaAttributeEVents e);

监听Session
---


监听Request
---


统计在线人数
---
