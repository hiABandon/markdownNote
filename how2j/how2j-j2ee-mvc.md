MVC
===

mvc是一种分层的设计模式

- 仅仅使用Servlet的短处
  - Servlet要准备数据，还要准备html，且html可读性差，维护麻烦
- 仅仅使用JSP的短处
  - java代码编写没有在Servlet中方便
- 结合Servlet和JSP
  - 让Servlet负责从数据库中查询数据，然后跳转到JSP页面
  - 让JSP使用EL表达式把Servlet查得的数据显示出来。、

#### 例:
- **HeroEditServlet**：只用来从数据库中查询Hero对象，然后跳转到JSP页面
```java
request.setAttribute("hero", hero);
```
在request中设置hero
```java
request.getRequestDispatcher("editHero.jsp").forward(request, response);
```
服务端跳转到editHero.jsp,因为是服务端跳转，都属于**同一次请求**，所以可以在editHero.jsp通过request取出来

- **editHero.jsp**:不做查询数据库的事情，直接获取从HeroEditServlet传过来的Hero对象，通过EL表达式把request中的hero显示出来

- HeroEditServlet.java
```java
public class HeroEditServlet extends HttpServlet {
  protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    int id = Integer.parseInt(request.getParameter("id"));
    // 通过request的getParameter方法获取字段名为id的值
    // 获取出来是一个String使用Integer.parseInt();将其转换为Integer
    Hero hero = new HerDAO().get(id);
    // 通过HeroDAO的get方法，从数据库获取id为id的英雄
    request.setAttribute("hero", hero);
    // setAttribte和getAttribute用来在 进行服务端跳转  的时候，在不同的Servlet之间进行数据共享
    request.getRequestDispatcher("editHero.jsp").forward(request, response);
    // 服务端向editHero.jsp跳转
  }
}
```

- editHero.jsp
```javascript
<%@ page language="java" contentType = "text/html; charset=UTF-8" pageEncoding="UTF-8" import="java.util.*,bean.*,java.sql.*" %>
<form action='updateHero' method='post' >
  名字 ： <input type='text' name='name' value='${hero.name}'> <br>
  血量 ：<input type='text' name='hp' value='${hero.hp}'> <br>
  伤害： <input type='text' name='damage' value='${hero.damage}'> <br>
  <input type='hidden' name='id' value='${hero.id}'>
  <input type='submit' value='更新'>
</form>
```

#### MVC设计模式
- M 模型 （Model） 模型就是数据，就是dao,bean
- V 视图 （View） 视图就是网页,JSP用来展示模型中的数据
- C 控制器 (Controller) 控制器就是把不同的数据（Model）显示在不同的视图(view)上
  - 上述例子中，Servlet就是充当控制器的角色，把hero对象显示在jsp上
