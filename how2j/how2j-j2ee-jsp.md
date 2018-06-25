JSP
===
可以写html代码又可以写java代码

```javascript
<%@ page contentType="text/html; charset=UTF-8"
  pageEncoding="UTF-8" import="java.util.*"%>
  <!-- <%@page指令 -->
  <!-- contentType通知浏览器以何种方式解码 -->
  <!-- pageEncoding，若果jsp文件出现了中文，这些中文使用UTF8进行编码 -->
  <!-- 导入多个java类或包，多个之间使用,号分隔 -->
  你好jsp
  <br>
  <%=new Date().toLocaleString()%>
```

执行过程
---
> JSP被转义成了Servlet
1. hello.jsp转移为hello_jsp.java（是一个servlet）
2. 再将其编译为hello_jsp.class
3. 再执行hello_jsp生成html
4. 通过http协议把html响应返回给浏览器

> 注：把jsp编译成为.class文件是tomcat自动完成的事情

> jsp文件会被自动编译到D:\Tomcat\apache-tomcat-8.5.3\work\Catalina\localhost\ROOT\org\apache\jsp路径下

JSP页面元素
---
- jsp由如下元素组成
  1. 静态内容
    - html,css,javascript等
  2. 指令
    - 以**<%@开始%>结尾的**代码
  3. 表达式
    - **<%=%>** 用于输出一段html
  4. Scriptlet
    - 再 **<%  %>** 之间可以写任何java代码，需要**分号**结尾
  5. 声明
    - <%%>之间声明字段，方法，不建议
  6. 动作
    - <jsp:include page="Filename">
  7. 注释<%-- --%>

```js
<%-- 纯jsp-for循环看看就行 --%>
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8" import="java.util.*"%>
<%
    List<String> words = new ArrayList<String>();
    words.add("today");
    words.add("is");
    words.add("a");
    words.add("great");
    words.add("day");
%>

<table width="200px" align="center" border="1" cellspacing="0">

<%for (String word : words) {%>

<tr>
    <td><%=word%></td>
</tr>

<%}%>

</table>
```

include在一个页面中包含另一个页面
---

- 指令include
  - `<%@ include file="footer.jsp" %>`
  - footer.jsp内容会被插入写这个代码的页面
  - 最后转译出一个.java文件
- 动作include
  - `<jsp:include page="footer.jsp" />`
    - 单独有一个转译出来的footer_jsp.java独立存在
    - 会在服务端访问该footer的时候将其内容嵌入到响应中。
  - **该中方式有传参数的需要**
- hello.jsp
```js
<%@page contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8" import="java.util.*"%>

你好 JSP
<%=new Date().toLocaleString()%>

<jsp:include page="footer.jsp">
    <jsp:param  name="year" value="2017" />
</jsp:include>
```
在hello.jsp中使用jsp动作设定了参数

- footer.jsp
```js
<hr>
<p style="text-align:center">
    copyright@<%= request.getParameter("year") %>
</p>
```
在footer.jsp中使用requestdgetParameter方法获取了参数

跳转
---
jsp的跳转也分为**客户端跳转**和**服务端跳转**

- **客户端跳转**
  - 与Servlet中相同
```js
<%
response.sendRedirect("hello.jsp");
%>
```
- **服务端跳转**
  - `request.getRequestDispatcher("hello.jsp").forward(request, response);`
    - 或 使用动作
  - `<jsp:forward page="hello.jsp" />`

cookie
---
> - cookie是浏览器和服务器交换数据的方式
> - Cookie由 服务器创建 创建好后 发送给 浏览器
> - 浏览器保存在  本地
> - 下一次访问网站就把cookie发送给服务器

- **setCookie.jsp**
```js
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8" import="javax.servlet.http.Cookie"%>

<%
    Cookie c = new Cookie("name", "Gareen");
    c.setMaxAge(60 * 24 * 60); // 保留时间
    c.setPath("127.0.0.1"); // 主机名，通过该主机名访问服务器才会提交cookie到服务端
    response.addCookie(c);
%>

<a href="getCookie.jsp">跳转到获取cookie的页面</a>
```

- **getCookie.jsp**
```js
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8" import="javax.servlet.http.Cookie"%>

<%
    Cookie[] cookies = request.getCookies();
    if (null != cookies)
        for (int d = 0; d <= cookies.length - 1; d++) {
            out.print(cookies[d].getName() + ":" + cookies[d].getValue() + "<br>");
        }
%>
```

session
---
Session对应的中文翻译是会话。
会话指的是从用户打开浏览器访问一个网站开始，无论在这个网站中访问了多少页面，点击了多少链接，都属于同一个会话。 直到该用户关闭浏览器为止，都属于同一个会话。

- setSession.jsp
```js
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8" import="javax.servlet.http.Cookie"%>

<%
   session.setAttribute("name", "teemo");
%>

<a href="getSession.jsp">跳转到获取session的页面</a>
```
session对象保存数据的方式，有点像Map的键值对(key-value)

- getSession.jsp
```js
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8" import="javax.servlet.http.Cookie"%>

<%
    String name = (String)session.getAttribute("name");
%>

session中的name: <%=name%>
```

#### session和cookie的关系

回到健身房的储物柜这一段：

李佳汜和毛竞都有自己的盒子，那么他们怎么知道哪个盒子是自己的呢？
通过钥匙就能找到自己的盒子了。

盒子对应服务器上的Session。
钥匙对应浏览器上的Cookie。

#### 如果没有cookie,session如何工作
略
#### session的有效期
略

作用域
-----
JSP有4个作用域
  - **pageContext** 当前页面
  - **requestContext** 一次请求
  - **sessionContext** 当前会话
  - **applicationContext** 全局，所有用户共享

### pageContext
  - 通过``pageContext.setAttribute(key,value)`的数据只能当前页访问，其他页不可访问

  - setContext.jsp
```js
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>

<%
    pageContext.setAttribute("name","gareen");
%>

<%=pageContext.getAttribute("name")%>
```

  - getContext.jsp
```js
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>

<%=pageContext.getAttribute("name")%>
```

### requestContext
  - 随着本次请求的结束，其中的数据也被回收
- setContext.jsp
```js
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>

<%
    request.setAttribute("name","gareen");
%>

<%=request.getAttribute("name")%>
```

- getContext.jsp
```js
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>

<%=request.getAttribute("name")%>
```

### requestContext与服务端跳转
  - requestContext指的是一次请求
  - 如果发生了**服务端跳转**，从setContext.jsp跳转到getContext.jsp其实这**还是一次请求**。
  - 所以在getContext.jsp中，可以取到在requestContext中设置的值
  - 这也是一种**页面间传递数据的方式**

- setContext.jsp
```js
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%
    request.setAttribute("name","gareen");
%>

<jsp:forward page="getContext.jsp"/>
```

- getContext.jsp
```js
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>

<%=request.getAttribute("name")%>
```

### 客户端跳转
因为客户端跳转，浏览器会发生一次新的访问，会产生一个新的request对象所以无法通过request传递数据

### sessionContext
sessionContext 指的是会话，从一个用户打开网站的那一刻起，无论访问了多少网页，链接都属于同一个会话，直到浏览器关闭。

所以页面间传递数据，也是可以通过session传递的。

但是，不同用户对应的session是不一样的，所以session无法在不同的用户之间共享数据。

与requestContext类似的，也可以用如下方式来做

- setContext.jsp
```js
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%
    session.setAttribute("name","gareen");
    response.sendRedirect("getContext.jsp");
%>
```
- getContext.jsp
```js
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>

<%=session.getAttribute("name")%>
```

### applicationContext
applicationContext 指的是全局，所有用户共享同一个数据

在JSP中使用application对象， application对象是ServletContext接口的实例
也可以通过 request.getServletContext()来获取。
所以 application == request.getServletContext() 会返回true
application映射的就是web应用本身。

与requestContext类似的，也可以用如下方式来做

- setContext.jsp
```js
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%
    application.setAttribute("name","gareen");
    System.out.println(application == request.getServletContext());
    response.sendRedirect("getContext.jsp");
%>
```

- getContext.jsp
```js
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>

<%=application.getAttribute("name")%>
```

JSTL
---
> JSP Standard Tag Library 标准标签库
> - 允许开发者像使用HTML标签一样在JSP中开发JAVA功能
> JSTL库有core,i18n,fmt,sql等等
略

EL表达式语言
---
EL表达式非常好用，**好用的吓死人~**

#### 取值
不同版本的tomcat是否默认开启对EL表达式的支持，是不一定的。

所以为了保证EL表达式能够正常使用，需要在<%@page 标签里加上isELIgnored="false"

使用EL表达式，非常简单

比如使用JSTL输出要写成
`<c:out value="${name}" />`

但是用EL只需要
${name}

```js
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8" isELIgnored="false"%>

<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>

<c:set var="name" value="${'gareen'}" scope="request" />

通过标签获取name: <c:out value="${name}" /> <br>

通过 EL 获取name: ${name}
```

#### 作用域与优先级
EL表达式可以从pageContext,request,session,application四个作用域中取到值，如果4个作用域都有name属性怎么办？

EL会按照从小到大的优先级顺序获取

pageContext>request>session>application

```js
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8" isELIgnored="false"%>

<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>

<c:set var="name" value="${'gareen-pageContext'}" scope="page" />
<c:set var="name" value="${'gareen-request'}" scope="request" />
<c:set var="name" value="${'gareen-session'}" scope="session" />
<c:set var="name" value="${'gareen-application'}" scope="application" />

4个作用域都有name,优先获取出来的是 ： ${name}
```

#### JavaBean概念
EL可以很方便的访问JavaBean的属性

JavaBean的标准
1. 提供无参public的构造方法(默认提供)
2. 每个属性都有pulic的getter和setter
3. 如果属性是boolean类型的，就有对应的is 和 setter方法

#### 获取JavaBean的属性
像这样 ${hero.name} ，就会自动调用getName方法了

```js
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8" isELIgnored="false" import="bean.*"%>

<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>

<%
    Hero hero =new Hero();
    hero.setName("盖伦");
    hero.setHp(616);

    request.setAttribute("hero", hero);
%>

英雄名字 ： ${hero.name} <br>
英雄血量 ： ${hero.hp}
```

### 结合JSTL的<c:forEach>
EL还可以结合 JSTL的<c:forEach 使用，进一步简化代码

原代码中的

<c:out value="${hero}" />


可以简写为

${hero}

```js
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8" import="java.util.*"%>

<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>

<%
    List<String> heros = new ArrayList<String>();
    heros.add("塔姆");
    heros.add("艾克");
    heros.add("巴德");
    heros.add("雷克赛");
    heros.add("卡莉丝塔");
    request.setAttribute("heros",heros);
%>

<table width="200px" align="center" border="1" cellspacing="0">
<tr>
    <td>编号</td>
    <td>英雄</td>
</tr>

<c:forEach items="${heros}" var="hero" varStatus="st"  >
    <tr>
        <td>${st.count}</td>
        <td>${hero}</td>
    </tr>
</c:forEach>
</table>
```
