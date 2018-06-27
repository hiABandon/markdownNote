过滤器Filter
===
Filter就像一个一个哨卡，用户的请求需要经过Filter
并且可以有多个过滤器

Filter学习是java类+web.xml配置

```java
// import Filter是servlet-api.jar包里的；
public class FirstFilter implements Filter {

    @Override
    public void destroy() {

    }

    @Override
    public void doFilter(ServletRequest req, ServletResponse res, FilterChain chain)
            throws IOException, ServletException {
        HttpServletRequest request = (HttpServletRequest) req;
        HttpServletResponse response = (HttpServletResponse) res;

        String ip = request.getRemoteAddr();
        String url = request.getRequestURL().toString();
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        Date d = new Date();
        String date = sdf.format(d);

        System.out.printf("%s %s 访问了 %s%n", date, ip, url);
        chain.doFilter(request, response);
        // 过滤器放行，表示继续运行下一个过滤器，或者最终访问的某个servlet,html,jsp等等
    }

    @Override
    public void init(FilterConfig arg0) throws ServletException {
      //  init的方法一定会随着tomcat的启动而启动。
    }

}
```

```xml
<filter>
    <filter-name>FirstFilter</filter-name>
    <filter-class>filter.FirstFilter</filter-class>
</filter>

<filter-mapping>
    <filter-name>FirstFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

- Filter启动失败
~~Filter是web应用非常重要的一个环节，如果Filter启动失败，或者本身有编译错误，不仅这个Filter不能使用，整个web应用会启动失败，导致用户无法访问页面~~

在启动tomcat过程中，也会看到这样的字样：

`严重: Context [] startup failed due to previous errors`

这常常用于提示Filter启动失败了

使用拦截器简化Servlet的中文处理
---

在通过Servlet获取中文参数 的章节中知道，可以通过

`request.setCharacterEncoding("UTF-8");`

正确获取UTF-8编码的中文，但是如果有很多servlet都需要获取中文，那么就必须在每个Servlet中增加这段代码。

有一个简便的办法，那就是通过Filter过滤器进行中文处理 ，那么所有的Servlet都不需要单独处理了。

```java
public class EncodingFilter implements Filter {
  @Override
  public void destroy() { }
  @Override
  public void doFilter(ServletRequest req, ServletResponse res, FilterChain chain) throws IOException, ServletException {
    HttpServletRequest request = (HttpServletRequest) req;
    HttpServletResponse response = (HttpServletResponse) res;

    request.setCharacterEncoding("UTF-8");

    chain.doFilter(request, response);
  }
  @Override
  public void init(FilterConfig arg0) throws ServletException {  }
}
```

登录验证
---

#### 在Servlet中进行登陆验证的局限性
在用户是否登陆的验证中，我们可以通过在HeroListServlet中增加对session的判断代码来做到登陆验证。

局限性和filter处理中文问题一样，代码重复，可维护性差。

所以使用Filter一次性解决所有的登录验证问题。

#### 使用Filter处理

- 创建一个AuthFilter类
```java
String uri = request.getRequestURI();
if (uri.endsWiht("login.html") || uri.endsWith("login")) {
  chain.doFilter(request, response);
  return ;
}
```
判断是否是访问的login.html和loginHero, 因为这两个页面就是在海**没有登录之前就需要访问的**
```java
  String userName = (String) request.getSession().getAttribute("userName");
  if(null == userName) {
    response.sendRedirect("login.html");
    return ;
  }
```
从Session中获取userName,如果没有，就表示不曾登陆过，跳转到登陆页面。

```java
public class AuthFilter implements Filter {
  @Override
  public void destroy() {}
  @Override
  public void doFilter(ServletRequest req, ServletResponse res) {
    HttpServletRequest request = (HttpServletRequest) req;
    HttpServletResponse response = (HttpServletResponse) res;

    String uri = request.getRequestURI();
    if(uri.endsWiht("login.html") || uri. endsWiht("login")) {
      chain.doFilter(requst, response);
      return ;
    }
    String userName = (String) request.getSession().getAttribute("userName");
    if (null == userName) {
      response.sendRedirect("login.html");
      return;
    }
    chain.doFilter(request, response);
  }
  @Override
  public void init() throws ServletException {}
}
```

- 配置web.xml

```xml
<filter>
    <filter-name>AuthFilter</filter-name>
    <filter-class>filter.AuthFilter</filter-class>
</filter>

<filter-mapping>
    <filter-name>AuthFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

- 练习：

因为这个过滤器的存在，在登陆之前所有的资源都不能访问。 所以在login.jsp上如果有图片，js和css，也不能够正常显示和工作。

这样做当然是不行的，那么如何让js css和图片文件即使在不登陆的情况下，也可以访问呢？

```java
package filter;

import java.io.IOException;
import java.text.SimpleDateFormat;
import java.util.Date;

import javax.servlet.Filter;
import javax.servlet.FilterChain;
import javax.servlet.FilterConfig;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class AuthFilter implements Filter {

    @Override
    public void destroy() { }

    @Override
    public void doFilter(ServletRequest req, ServletResponse res, FilterChain chain)
            throws IOException, ServletException {
        HttpServletRequest request = (HttpServletRequest) req;
        HttpServletResponse response = (HttpServletResponse) res;

        String uri = request.getRequestURI();
        //如果访问的资源是以css或者js结尾的，那么就不需要判断是否登录
        if (uri.endsWith(".css") || uri.endsWith(".js")) {
            chain.doFilter(request, response);
            return;
        }
        if (uri.endsWith("login.html") || uri.endsWith("login")) {
            chain.doFilter(request, response);
            return;
        }
        String userName = (String) request.getSession().getAttribute("userName");
        if (null == userName) {
            response.sendRedirect("login.html");
            return;
        }
        chain.doFilter(request, response);
    }

    @Override
    public void init(FilterConfig arg0) throws ServletException { }
}
```
