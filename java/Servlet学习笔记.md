#### ServletContxt

web容器在启动的时候，会为每个web程序创建一个ServletContxt对象，它代表当前的web应用

##### 共享数据

我在这个A：servlet中保存的数据，可以在另外一个B：servlet中拿到，，拿到的是唯一对象ServletContxt

```java
ServletContext context = this.getServletContext();
```

##### A：servlet中设置

```java
ServletContext context = this.getServletContext();
String username = "狗太白";
context.setAttribute("username",username);

resp.getWriter().println("Hello");
```

##### B：servlet中获取

```java
ServletContext context = this.getServletContext();
String username = (String) context.getAttribute("username");
resp.getWriter().println(username);
```

##### 配置初始化值

```xml
<context-param>
    <param-name>url</param-name>
    <param-value>jdbc:mysql://localhost:3306</param-value>
</context-param>
```

##### 获取初始化值

```java
ServletContext context = this.getServletContext();
String url = (String) context.getInitParameter("url");
resp.getWriter().println(url);
```

##### 请求转发

前端路径不会变化

```java
context.getNamedDispatcher("hello").forward(req,resp);
```

##### 读取资源文件Properties

```java
InputStream asStream = this.getServletContext().getResourceAsStream("/WEB-INF/classes/db.properties");

Properties prop = new Properties();
//防止中文乱码
prop.load(new InputStreamReader(asStream,"UTF-8"));
String name = prop.getProperty("name");

resp.getWriter().println(name);
```

#### HttpServletResponse

web服务器接收到客户端的http请求，针对这个请求分别创建一个代表HttpServletRequest对象，代表响应一个HttpservletResponse

##### 向浏览器发送数据方法

getOutputStream()

getWriter() 获取输出流对象

setCharacterEncoding("UTF-8")  解决接收参数中文乱码问题

##### 向浏览器发送响应头方法

addDateHeader

addHeader

addIntHeader

containsHeader

setDateHeader

SetHeader

SetIntHeader

##### 向客户端发送响应码状态方法

setStatus

##### 响应状态码的常量

SC_NOT_FOUND 404

SC_OK  200

SC_INTERNAL_SERVER_ERROR 500

##### 请求重定向方法

```java
resp.sendRedirect("url")
```

面试题：重定向和转发的区别

相同点：页面会发生改变

不同点：重定向是在浏览器的行为302，转发是服务器中的行为307

#### HttpServletRequest

代表客户端的一个请求，用户通过http协议访问服务器，请求中的所有信息，都会被封装到HttpServletRequest中，获取客户端的所有信息

##### 获取请求方式

getMethod()     获得String类型；

##### 获取请求参数以及请求转发

setCharacterEncoding("UTF-8")  解决接收参数中文乱码问题

getParameter("demo")   请求体中(比如：表单)获取单个参数

getParameterValues("demo")   请求体中(比如：表单)获取多个参数

getNamedDispatcher("url").forward(req,resp);    其中ServletContxt中也可以做类似的效果

##### 获取请求资源

getContextPath()  获得String类型的 web应用的名称（项目名）

getQueryString()  获得 get提交url 地址后的参数字符串；

getRequestURI()  获取URI地址String类型

getRequestURL()  获取URL地址StringBuffer类型

request.getRemoteAddr()  获得访问的客户端IP地址

#### Cookie,Session

服务器留在浏览器中的信息

cookie 是客户端技术，每次请求都会带上去

session 是服务器技术，把用户信息保存在session中

##### 请求中获取cookie

req.getCookies()  返回cookies对象是数组

cookie.getName()  获取cookie中的key

cookie.getValue()  获取cookie中的value

##### 响应中设置cookie

new Cookie() 

cookie.setMaxAge(24\*60\*60)  设置cookie的有效期

resp.addCookie(cookie)  响应给客服端一个cookie

##### cookie细节问题

- 一个cookie只能保存一个信息
- 一个web站点可以给浏览器发送多个cookie,最多发送20个
- cookie 大小限制4kb
- 一个浏览器上限3000个cookie
- 不设置有效期，关闭浏览器，自动消失
- 设置cookie有效期为0，会展自动删除

##### session(重点)

- 服务器会给每一个用户(浏览器)创建一个 session对象
- 一个session独占一个浏览器，主要浏览器没有关闭，这个session就存在
- 用户登录之后，整个网站都可以访问
- session基于cookie做的，相当cookie升级版

##### 设置session

getSession(boolean)  参数为true时没有会创建，为false时没有返回null

setAttribute(String name,Object obj) 设置值

removeAttribute(String name) 移除值

##### session超时，注销

为了节省内存空间，大部分服务器默认设置的超时时长是30分钟

方式一 修改web.xml

```xml
<session-config>
            <session-timeout>30</session-timeout>
</session-config>
```

方式二 编程的方式

session.setMaxInactiveInterval(int seconds);  默认是分钟

session.invalidate();   删除

##### cookie和session区别

- Cookie是把用户的数据，写给浏览器，浏览器中保存(可以写多个)
- session把用户的数据写到用户独占session中，服务端保存(主要保存重要信息)
- session优点：安全、可以存放大量的数据、支持更丰富的数据类型
- session缺点：会占用服务器端的内存空间，如果session对象过多，会占用过多的内存空间。

















