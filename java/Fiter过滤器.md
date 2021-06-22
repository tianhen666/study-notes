#### Filter过滤器

Filter过滤器，用来过滤网站数据

应用场景：

- 处理中文乱码问题
- 登录验证问题
- 所有请求都会经过过滤器

##### 代码实现

```java
package cn.xhl.filter;
import jakarta.servlet.*;
import java.io.IOException;

public class FilterTest implements Filter {

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        System.out.println("初始化");
    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        servletRequest.setCharacterEncoding("UTF-8");
        servletResponse.setCharacterEncoding("UTF-8");
        servletResponse.setContentType("text/html;charset=UTF-8");
		
        System.out.println("执行前");
        //这个必须写，如果不写会被拦截
        filterChain.doFilter(servletRequest,servletResponse);
    }

    @Override
    public void destroy() {
        System.out.println("销毁");
    }
}
```

##### web.xml中配置

```xml
<filter>
    <filter-name>charset</filter-name>
    <filter-class>cn.xhl.filter.FilterTest</filter-class>
</filter>
<filter-mapping>
    <filter-name>charset</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

url-pattern 标签中的是正则表达式

#### 监听器

也是利用Filter来实现的，是一个观察着的角色，可以监听很多，有很多监听器，实现的接口不同，可以监听不同的对象

##### 代码实现

```java
package cn.xhl.filter;


import jakarta.servlet.ServletContext;
import jakarta.servlet.http.HttpSessionEvent;
import jakarta.servlet.http.HttpSessionListener;

public class ListenerTest implements HttpSessionListener {
    @Override
    public void sessionCreated(HttpSessionEvent se) {
        ServletContext context = se.getSession().getServletContext();
        Integer online = (Integer) context.getAttribute("online");

        if (online == null){
            online=1;
        }else {
            online +=1;
        }

        context.setAttribute("online",online);
    }

    @Override
    public void sessionDestroyed(HttpSessionEvent se) {

    }
}

```

##### web.xml中

```xml
<listener>
	<listener-class>cn.xhl.filter.ListenerTest</listener-class>
</listener>
```

##### 扩展监听器GUI中的理解

[参考文档一](https://blog.csdn.net/qq_42035966/article/details/82258199)
