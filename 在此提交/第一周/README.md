## 前端：

#### HTML（结构）

+ HTML（结构）：超文本标记语言（Hyper Text Markup Language），决定网页的结构和内容

#### CSS（表现）

+ 层叠样式表（Cascading Style Sheets），主要用来设置网页的表现样式
+ css的层叠样式表只是一门标记语言，也就是说没有任何的语法支持，用过的人都知道css应该有如下的缺点：

    css选择完标签后需要反复写一些css样式，重复工作量大，代码冗余度高。
    css样式无复用的机制，所以代码的可维护性极差。

+ 基于如上的缺点诞生了【css预处理器】
  + CSS预处理器定义了一种新的语言
  + 其基本思想是，用一种专门的编程语言，为CSS增加了一些编程的特性，将CSS作为目标生成文件，然后开发者就只需要使用这种语言进行CSS的编码工作。转化成通俗易懂的话来说就是用一种专门的编程语言，进行Web页面样式设计，再通过编译器转化为正常的CSS文件，以供项目使用

+ 市面上常见的CSS预处理器

    Sass：基于Ruby ，通过服务端处理，功能强大。解析效率高。需要学习Ruby语言，上手难度高于Less。
    Less：基于NodeJS，通过客户端处理，使用简单。功能比Sass简单，解析效率也低于Sass，但在实际开发中足够了，所以如果我们后台人员如果需要的话，建议使用LESS。
    Stylus：基于nodejs，Stylus比Less更强大，而且基于nodejs比Sass更符合我们的思路，作为比较年轻的新型语言，Stylus 可以以近似脚本的方式去写css代码，创建健壮的、动态的、富有表现力的css。

#### JavaScript（行为）

+ JavaScript一门弱类型脚本语言，其源代码在发往客户端运行之前不需要经过编译，而是将文本格式的字符代码发送给浏览器，由浏览器解释运行，用于控制网页的行为动作

+ 原生JS开发
  + 我们日常开发都是按照【ECMAScript】标准的开发方式，简称ES
  + 特点是所有浏览器都支持此规范，我们需要知道的是ES5规范对所有的游览器都支持
  + 但是ES6是市面上用的最多的规范版本，主流游览器支持，所以为了兼容某些游览器，需要将ES6规范通过webpack降级为ES5

#### JavaScript框架

+ JQuery：大家熟知的JavaScript库，优点就是简化了DOM操作，缺点就是DOM操作太频繁，影响前端性能
+ Angular：Google收购的前端框架，由一群Java程序员开发，其特点是将后台的MVC模式搬到了前端并增加了模块化开发的理念，与微软合作，采用了TypeScript语法开发；对后台程序员友好，对前端程序员不太友好；最大的缺点是版本迭代不合理（如1代–>2 代，除了名字，基本就是两个东西）
+ React：Facebook 出品，一款高性能的JS前端框架；特点是提出了新概念 【虚拟DOM】用于减少真实 DOM 操作，在内存中模拟DOM操作，有效的提升了前端渲染效率；缺点是使用复杂，因为需要额外学习一门【JSX】语言
+ Vue：一款渐进式 JavaScript 框架，所谓渐进式就是逐步实现新特性的意思，如实现模块化开发、路由、状态管理等新特性。其特点是综合了Angular（模块化）和React(虚拟 DOM) 的优点
+ Axios：前端通信框架；因为 Vue 的边界很明确，就是为了处理 DOM，所以并不具备通信能力，此时就需要额外使用一个通信框架与服务器交互；当然也可以直接选择使用jQuery 提供的AJAX 通信功能



## Servlet：

Servlet就是一个接口

#### 实现Servlet：

1. 创建JavaEE项目
2. 新建类并实现Servlet接口

```java
//现在的重点是service()方法
public class ServletDemo implements Servlet {
    
    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
		//to-do here
    }
}
```

3. 在web.xml中进行配置

```xml
<!--在根标签中加入serlet配置和servlet映射-->
<servlet>
    <servlet-name>demo</servlet-name> <!--servlet名-->
    <servlet-class>com.example.servlet.ServletDemo</servlet-class> <!--对应的类路径，内部通过反射来使用-->
</servlet>

<servlet-mapping>
    <servlet-name>demo</servlet-name> <!--servlet名-->
    <url-pattern>/demo</url-pattern> <!--url的路径-->
</servlet-mapping>
```

#### 原理：

1. 浏览器解析url，通过域名查找服务器，通过端口查找应用
2. 服务器在应用的web.xml的映射 <servlet-mapping> 中查找对应的path（资源名称）
3. 通过path即 <url-pattern> 查找到 <servlet-name>
4. 通过 <servlet-name> 在 <servlet> 块中查找到该类的全类名 <servlet-class>
5.  tomcat将全类名对应的字节码加载进内存 Class.forName();
6.  创建其对象 cls.newInstance();
7.  调用其service()方法

#### 生命周期：

1. 被创建（init() 方法在整个应用期间只有一次运行；一般用于加载资源）说明一个Servlet在内存中只有一个对象，Servlet是单例的，多个用户同时访问时可能会有线程安全问题，加锁的话性能影响太严重（多个用户会等待一个用户使用完毕）。解决：尽量不要在Servlet中定义成员变量，定义局部变量；或不对成员变量进行修改

   + 可以更改 init() 方法的加载时机：

   ```xml
   <servlet>
       <servlet-name>demo</servlet-name>
       <servlet-class>com.example.servlet.ServletDemo</servlet-class>
   
       <load-on-startup>0</load-on-startup>
   </servlet>
   ```

   + 在 <servlet> 标签下
   + <load-on-startup> 的值为负数：第一次访问时创建
   + 为0或正整数：服务器启动时创建
   + 默认值：-1

2. 提供服务

3. 被销毁（只在整个应用被正常关闭时执行，只执行这一次；不正常关闭时不执行；一般用于释放资源）

```java
	/**
     * 初始化方法
     * Servlet被创建（即第一次有用户调用时）时执行，只执行这一次，刷新时不会执行
     */
    @Override
    public void init(ServletConfig servletConfig) throws ServletException {
        System.out.println("init.....");
    }

    /**
     * 提供服务方法
     * 每次Servlet被访问时都执行
     */
    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
        System.out.println("service.....");
    }

    /**
     * 销毁方法
     * 在Servlet被杀死（服务器正常关闭，即点击红色方框按钮时）时执行，只执行这一次
     */
    @Override
    public void destroy() {
        System.out.println("destroy.....");
    }


    /**
     * 获取ServletConfig对象
     */
    @Override
    public ServletConfig getServletConfig() {
        return null;
    }
    /**
     * 获取Servlet的信息：版本、作者等等
     */
    @Override
    public String getServletInfo() {
        return null;
    }
```



#### 注解配置（Servlet3.0及以上版本的功能）：

在类上使用@WebServlet注解

```java
@WebServlet({"/web", "/dd"})
public class ServletDemo2 implements Servlet {
}
```

#### 断点调试：

同样是打上断点之后，点击debug启动即可

#### 体系结构：

Servlet接口 ---继承--- GenericServlet抽象类（子类） ---继承--- HttpServlet抽象类（子类的子类）

GenericServlet将Servlet接口中其他方法都做了默认空实现，只有service()方法作了抽象

HttpServlet：对http协议的一种封装，简化操作

+ 定义类继承HttpServlet
+ 覆写doGet() / doPost() 方法

http协议有七种请求方式

浏览器不带参数直接请求接口是get方式

get方式提交参数会将参数跟在连接后，post方式不会

#### 路径配置：

可以多个路径组成字符串数组：@WebServlet({"/web", "/dd"})

3种路径规则：

1. /xxx
2. /xxx/xxx 多层路径，目录结构
3. *.do

#### http：

超文本传输协议

定义了，客户端和服务器端，通信时，发送数据的格式

特点：

+ 基于TCP/IP
+ 默认端口：80
+ 基于，请求/响应，模型（一次请求对应一次响应）
+ 无状态的（每次请求间相互独立，不能交互数据）
+ HTTP 1.0：每次请求响应建立新的连接
+ HTTP 1.1：复用连接（连接的数据发送完成之后等一会，在此期间如果没有数据请求了再关闭）

#### 请求消息数据格式：

1. 请求行：
   + 请求方式 请求URL 请求协议/版本
   + GET /login.html HTTP/1.1
   + GET的请求参数在请求行中
   + POST的请求参数在请求体中
   + 请求的url长度，GET有限制，POST没有
   + GET不太安全，POST相对安全（参数暴露）

2. 请求头（告知服务器各种浏览器的信息）：
   + 请求名称：请求头值（多个值以逗号分隔）
   + 常见请求头：
     + User-Agent：告知服务器，浏览器的版本信息（在服务器获取此头信息，可以解决浏览器的兼容问题）
     + Accept：可以获取的资源类型
     + Accept-Language：可以支持的语言
     + Referer：当前页面从哪个页面过来（在浏览器直接访问当前页面时为null）
       + 防盗链（其他网站不得使用本站资源）
       + 统计工作（推广网站时统计流量从哪里来）

3. 请求空行：
   + 空行：分割POST请求的请求头和请求体
4. 请求体（正文）（GET方式时没有）
   + 封装POST请求消息的请求参数

字符串格式：

```http
GET /login.html HTTP/1.1
Host: localhost:8080
Connection: keep-alive
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/83.0.4103.116 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Sec-Fetch-Site: none
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9
If-None-Match: W/"260-1627197887996"
If-Modified-Since: Sun, 25 Jul 2021 07:24:47 GMT

username=jack
```

#### request和response：

+ 步骤：

  + tomcat服务器，根据请求url中的资源路径，创建对应的ServletDemo1的对象
  + tomcat服务器，创建request和response对象，request对象中封装请求消息数据
  + tomcat，将request和response两个对象，传递给service方法，并调用service方法
  + 程序员通过request对象获取请求信息，通过response对象设置响应数据

+ request和response对象是服务器创建的，我们来使用
+ ServletRequest接口 ---继承--- HttpServletRequest接口（子接口）---实现---org.apache.catalina.connector.RequestFacade类（由tomcat进行实现和创建对象）

#### request功能：

1. 获取请求消息数据：
   + 请求行
     + 请求方式：String getMethod()
     + （*）虚拟目录：String getContextPath()
     + Servlet路径：String getServletPath()
     + GET方式的请求参数：String getQueryString()
     + （*）请求URI（虚拟目录+Servlet路径）（/web/demo2）：String getRequestURI()
     + 请求URL（http://localhost/web/demo2）：StringBuffer getRequestURL()
     + 协议及版本：String getProtocol()
     + 客户机的IP地址：String getRemoteAddr()
     + URI（统一资源标识符，没有域名和端口的限制，范围比URL更大）和URL（统一资源定位符）
   + 请求头
     + （*）String getHeader(String name)：通过请求头的名称获取请求头的值（name不区分大小写）
     + Enumeration<String> getHeaderNames()：获取所有请求头的名称
     + Enumeration可当作迭代器使用
   + 请求体
     + 获取流对象：
       + BufferedReader getReader()（获取字符输入流，只能操作字符数据）（返回的参数形式与GET方法相同：username=jack&password=123）
       + ServletInputStream getInputStream()（获取字节输入流，可以操作所有类型数据）
     + 再从流对象中拿数据：

2. 其他功能：
   + 获取请求参数，通用方式（GET和POST都适用）
     + （*）String getParameter(String name)：根据参数名称获取参数值
     + String[] getParameterValues(String name)：根据参数名称获取参数值的数组（hobby=xx&hobby=yy）（多用于复选框）
     + Enumeration<String> getParameterNames()：获取所有请求参数的名称
     + （*）Map<String,String[]> getParameterMap()：获取所有参数的map集合
     + 中文乱码问题(GET不会乱，POST乱)：request.setCharacterEncoding("utf-8")（编码跟html页面相同）
   + 请求转发（浏览器地址栏不变，服务器内部的资源跳转方式，是一次请求）
     + 通过request对象获取请求转发器对象：RequestDispatcher getRequestDispatcher(String path)
     + 使用RequestDispatcher对象进行转发：forward(ServlerRequest req, ServletResponse res)
     + request.getRequestDispatcher("/web2").forward(req, res);
   + 共享数据
     + 域对象：一个有作用范围的对象，可以在范围内共享数据
     + request域：一次请求的范围，用于请求转发的，多个资源中，共享数据（请求转发是一次请求，所以是同一个request域，可以共享数据）
     + void setAttribute(String name, Object obj)：存储数据
     + Object getAttribute(String name)：通过键获取值
     + void removeAttribute(String name)：通过键移除键值对
   + 获取ServletContext
     + ServletContext getServletContext()

#### 响应消息数据格式：

+ 响应行
  + 协议/版本  响应状态码  状态码描述
  + 响应状态码（三位数字）：服务器告知浏览器，本次请求和响应的状态
    + 1xx：消息（服务器接收客户端消息，但没有接收完成，等待一段时间后发送）
    + 2xx：成功，代表：200
    + 3xx：重定向（也是成功），代表：302（重定向）304（告知浏览器去找缓存）
    + 4xx：客户端错误，代表：404（该路径没有对应资源）405（请求方式(GET、POST等)没有对应的方法）
    + 5xx：服务器错误，代表：500（服务器内部出现异常）
+ 响应头
  + 头名称：值
  + Content-Type：本次响应体数据格式及编码格式（可以解决乱码）
  + Content-Disposition：告知客户端以什么格式打开响应体数据
    + in-line：默认值，在当前页面打开
    + attachment;filename=xxx：以附件形式打开（文件下载时使用）
+ 响应空行
+ 响应体
  + 真实的传输的数据

字符串格式：

```http
HTTP/2.0 200 oK
content-Type: text/html;charset=UTF-8
content-Length: 101
Date: wed，06 Jun 2018 07:08:42 GMT

<html>
    <head>
    	<title>$Title$</title>
    </head>
    <body>
    	hello,response
    </body>
</html>
```

#### Response对象：

+ 功能：设置响应消息
+ 设置响应行：
  + 设置状态码：setStatus(int sc)
+ 设置响应头：
  + setHeader(String name, String value)
+ 设置响应体
  + 获取输出流：
    + 字符输出流：PrinterWriter getWriter()
    + 字节输出流：ServletOutputStream getOutputStream()
  + 使用输出流，将数据输出到浏览器

