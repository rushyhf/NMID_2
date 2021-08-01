## SpringMVC:

+ MVC思想：客户端发送请求后，controller控制层进行处理，model模型层装载并传输数据，view在视图层进行展示

#### SpringMVC示例：

1. 创建web项目，并添加jar包：

   ```
   spring-aop-4.0.0.RELEASE.jar
   spring-beans-4.0.0.RELEASE.jar
   spring-context-4.0.0.RELEASE.jar
   spring-core-4.0.0.RELEASE.jar
   spring-expression-4.0.0.RELEASE.jar
   commons-logging-1.1.3.jar
   spring-web-4.0.0.RELEASE.jar
   spring-webmvc-4.0.0.RELEASE.jar
   ```

2. 或创建maven项目，导入依赖：

   ```xml
   <dependency>
       <groupId>org.springframework</groupId>
       <artifactId>spring-webmvc</artifactId>
       <version>RELEASE</version>
   </dependency>
   <dependency>
       <groupId>javax.servlet</groupId>
       <artifactId>javax.servlet-api</artifactId>
       <version>4.0.1</version>
   </dependency>
   <dependency>
       <groupId>javax.servlet.jsp</groupId>
       <artifactId>javax.servlet.jsp-api</artifactId>
       <version>2.3.3</version>
   </dependency>
   ```

3. 编写web.xml：

   ```xml
   <!-- 配置springMVC的核心（前端）控制器DispatcherServlet
   作用：加载springMC的配置文件，在下方的配置方式下，DispatcherServlet自动加裁配置文件，此时的配置文件，
   默认位置：WEB-INF下，默认名称<servlet-name>-servlet.xml，例如以下配置后，文件名为：mvc-servlet.xml，加载了配置文件后，SpringMVC会根据扫描路径找到控制层 !-->
   
   <servlet>
       <servlet-name>mvc</servlet-name>
       <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
   </servlet>
       
   <servlet-mapping>
       <servlet-name>mvc</servlet-name>
       <url-pattern>/</url-pattern>
   </servlet-mapping>
   ```

4. 编写mvc-servlet.xml：

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:context="http://www.springframework.org/schema/context"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
          http://www.springframework.org/schema/beans/spring-beans.xsd
   http://www.springframework.org/schema/context
          http://www.springframework.org/schema/context/spring-context.xsd">
   
       <context:component-scan base-package="com.example.mvc.controller"/>
   
       <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
           <property name="prefix" value="/WEB-INF/page/"/>
           <property name="suffix" value=".jsp"/>
       </bean>
   
   </beans>
   ```

5. 编写controller：

   ```java
   import org.springframework.stereotype.Controller;
   import org.springframework.web.bind.annotation.RequestMapping;
   
   import javax.servlet.http.HttpServletRequest;
   
   @Controller
   public class IndexController {
       @RequestMapping("/hello")
       public String hello(HttpServletRequest req){
           System.out.println("controller");
           System.out.println(req.getRemoteAddr());
           return "success";
       }
   }
   ```

6. 创建view：

   ```jsp
   <%-- /WEB-INF/包下的 index.jsp: %-->
   <%@ page contentType="text/html;charset=UTF-8" language="java" %>
   <html>
   <body>
   <h2>Hello World!</h2>
   <a href="/hello">hello</a>
   </body>
   </html>
       
   
   <%-- /WEB-INF/page/包下的 success.jsp: %-->
   <%@ page contentType="text/html;charset=UTF-8" language="java" %>
   <html>
   <head>
       <title>Title</title>
   </head>
   <body>
   <h1>success</h1>
   </body>
   </html>
   ```



#### 三层架构（MVC模型）：

+ 自下而上：Model层（**模型，**数据访问层）、Cotroller层（**控制，**逻辑控制层）、View层（**视图，**页面显示层）
+ 主要作用：解耦。
+ 好处：普遍接受的，系统分层，有利于系统的维护、系统的扩展。即增强系统的可维护性、可扩展性。



#### 控制器：

+ 前端控制器：DispatcherServlet：
  + 用户请求到达前端控制器时，相当于 mvc 模式中的c，DispatcherServlet 是整个流程控制的中心，相当于 SpringMVC 的大脑，由它调用其它组件处理用户请求，DispatcherServlet 的存在降低了组件之间的耦合性。
+ 应用控制器：
  + 处理器映射器（Handler Mapping）进行处理器管理
  + 视图解析器（View Resolver）进行视图管理
+ 页面控制器 / 动作 / 处理器为：Controller 接口的实现（也可以是任何的 POJO 类）

+ 创建控制器，控制器提供两个功能：访问 jsp 页面；提供添加接口： 

```java
@Controller
public class BookController {
    @RequestMapping("/book")
    public String addBook() {
        return "addbook";
    }

    @RequestMapping(value = "/doAdd",method = RequestMethod.POST)
    @ResponseBody
    public void doAdd(String name, String author, Double price, Boolean ispublic) {
        System.out.println(name);
        System.out.println(author);
        System.out.println(price);
        System.out.println(ispublic);
    }
}
```



#### 请求响应流程 ：

1. 用户发送请求至前端控制器DispatcherServlet。
2. DispatcherServlet收到请求，调用HandlerMapping处理器映射器。
3. 处理器映射器找到具体的处理器（可根据xml配置、注解进行查找），生成处理器对象及处理器拦截器（如果有则生成）一并返回给DispatcherServlet。
4. DispatcherServlet调用HandlerAdapter处理器适配器。
5. HandlerAdapter经过适配调用具体的处理器（Controller，也叫后端控制器）。
6. Controller执行完成返回ModelAndView。
7. HandlerAdapter将controller执行结果ModelAndView返回给DispatcherServlet。
8. DispatcherServlet将ModelAndView传给ViewReslover视图解析器。
9. ViewReslover解析后返回具体View。
10. DispatcherServlet根据View进行渲染视图（即将模型数据填充至视图中）。
11. DispatcherServlet响应用户。



#### 参数绑定机制 ：

+ 将URL中的的请求参数，进行类型转换（String或其他类型）后，将值赋给Controller方法的形参中。 



#### 自定义类型转换器：

+  使用SpringMVC时，常需把表单中的参数映射为对象的属性中，可以在默认的spring-servlet.xml加上配置后做到普通数据类型的转换，如将String转换成Integer和Double等。
+ 例：SpringMVC只支持日期格式2021/01/01自动封装到模型数据中的Date类型，自定义Date类型转换器可覆盖SpringMVC原有的Date转换器：
  + 创建一个类继承Converter<String, Date>接口：

```java
public class StringToDateConverter implements Converter<String, Date>{
    @Override    //参数source为需要转换的字符串
    public Date convert(String source) {
        SimpleDateFormat simpleDateFormat1 = new SimpleDateFormat("yyyy-MM-dd");
        Date date = null;
        try {
             date = simpleDateFormat1.parse(source);
        } catch (ParseException e) {
            SimpleDateFormat simpleDateFormat2 = new SimpleDateFormat("yyyy/MM/dd");
            try {
                date = simpleDateFormat2.parse(source);
            } catch (ParseException e1) {
                System.err.println("输入日期格式错误！");
                e.printStackTrace();
            }
        }
        return date;
    }
}
```
+ 添加springMVC-servlet.xml文件配置：

```xml
<!-- 开启springMVC注解驱动（带类型转换服务）  如果不带类型转换服务加上conversion-service="conversionService"会报错   （可选）-->

<mvc:annotation-driven conversion-service="conversionService"/>

<!-- 配置自定义类型转换服务 （可选）-->
<bean id="conversionService" class="org.springframework.format.support.FormattingConversionServiceFactoryBean">
    <!-- 注册自定义类型转换器 -->
    <property name="converters">
        <list>
            <bean class="com.example.mvc.converter.StringToDateConverter" />
            <!-- 添加其它自定义类型转换器 -->
        </list>
    </property>
</bean>
```



#### 异常处理器：

+ 使用 `@ControllerAdvice` + `@ExceptionHandler` 进行全局的 `Controller` 层异常处理。只要设计得当，可不需在 `Controller` 层进行 `try-catch`
+ 步骤：

1. 定义一个统一结果返回类，将结果类的内容返回给前端：

   ```java
   //Api统一的返回结果类
   public class ApiResult {
       //结果码
       private String code;
   
       //结果码描述
       private String msg;
   
       public ApiResult() {
   
       }
   
       public ApiResult(ResultCode resultCode) {
           this.code = resultCode.getCode();
           this.msg = resultCode.getMsg();
       }
   
       //生成一个ApiResult对象并返回
       public static ApiResult of(ResultCode resultCode) {
           return new ApiResult(resultCode);
       }
   
       getter....
       setter....
   
       @Override
       public String toString() {
           return "ApiResult{" +
                   "code='" + code + '\'' +
                   ", msg='" + msg + '\'' +
                   '}';
       }
   }
   ```

2.  定义一个枚举类， 包含所有自定义结果码:

   ```java
   //错误码
   public enum ResultCode {
   
       //成功
       SUCCESS("0", "success"),
   
       //未知错误
       UNKNOWN_ERROR("0x10001", "unkonwn error"),
   
       //用户名错误或不存在
       USERNAME_ERROR("0x10002", "username error or does not exist"),
   
       //密码错误
       PASSWORD_ERROR("0x10003", "password error"),
   
       //用户名不能为空
       USERNAME_EMPTY("0x10004", "username can not be empty");
   
       //结果码
       private String code;
   
       //结果码描述
       private String msg;
   
       ResultCode(String code, String msg) {
           this.code = code;
           this.msg = msg;
       }
   
       public String getCode() {
           return code;
       }
   
       public String getMsg() {
           return msg;
       }
   }
   ```

3.  定义业务异常类，业务相关的异常均抛出此异常类，将错误码枚举变量的值存于其中：

   ```java
   //自定义业务异常
   public class BusinessRuntimeException extends RuntimeException {
   
       //结果码
       private String code;
   
       //结果码描述
       private String msg;
   
       //结果码枚举
       private ResultCode resultCode;
   
       public BusinessRuntimeException(ResultCode resultCode) {
           super(resultCode.getMsg());
           this.code = resultCode.getCode();
           this.msg = resultCode.getMsg();
           this.resultCode = resultCode;
       }
   
       getter....
       setter....
   
       public ResultCode getResultCode() {
           return resultCode;
       }
   
       public void setResultCode(ResultCode resultCode) {
           this.resultCode = resultCode;
       }
   }
   ```

4. 定义全局异常处理类：

   + 通过 `@ControllerAdvice` 指定该类为 `Controller` 增强类。
   + 通过 `@ExceptionHandler` 自定捕获的异常类型。
   + 通过 `@ResponseBody` 返回 `json` 到前端。
   + 注：`@ControllerAdvice`注解的全局异常处理类也为 `Controller` ，需配置扫描路径，确保能扫描到此Controller。 

   ```java
   import org.slf4j.Logger;
   import org.slf4j.LoggerFactory;
   import org.springframework.web.bind.annotation.ControllerAdvice;
   import org.springframework.web.bind.annotation.ExceptionHandler;
   import org.springframework.web.bind.annotation.ResponseBody;
   
   //全局Controller层异常处理类
   @ControllerAdvice
   public class GlobalExceptionResolver {
   
       private static final Logger LOG = LoggerFactory.getLogger(GlobalExceptionResolver.class);
   
       //处理所有不可知异常
       @ExceptionHandler(Exception.class)
       @ResponseBody
       public ApiResult handleException(Exception e) {
           // 打印异常堆栈信息
           LOG.error(e.getMessage(), e);
           return ApiResult.of(ResultCode.UNKNOWN_ERROR);
       }
   
       //处理所有业务异常
       @ExceptionHandler(BusinessRuntimeException.class)
       @ResponseBody
       public ApiResult handleOpdRuntimeException(BusinessRuntimeException e) {
           // 不打印异常堆栈信息
           LOG.error(e.getMsg());
           return ApiResult.of(e.getResultCode());
       }
   }
   ```

5. 测试：

   ```java
   import com.tao.smp.exception.BusinessRuntimeException;
   import com.tao.smp.exception.ResultCode;
   import org.springframework.stereotype.Controller;
   import org.springframework.web.bind.annotation.GetMapping;
   import org.springframework.web.bind.annotation.RequestMapping;
   
   //测试异常的抛出
   @Controller
   @RequestMapping("/")
   public class TestController {
   
       //测试返回异常信息
       @GetMapping("/exception")
       public String returnExceptionInfo() {
   
           if (1 != 2) {
               // 用户名错误或不存在异常
               throw new BusinessRuntimeException(ResultCode.USERNAME_ERROR);
           }
   
           return "success";
       }
   }
   ```



#### 拦截器：

+ 解释： 在 Spring MVC 框架中定义一个拦截器需要对拦截器进行定义和配置，定义一个拦截器可以通过两种方式：一种是通过实现  HandlerInterceptor 接口或继承 HandlerInterceptor 接口的实现类来定义；另一种是通过实现  WebRequestInterceptor 接口或继承 WebRequestInterceptor 接口的实现类来定义。 

+ 步骤：

  + 实现HandlerInterceptor：

  ```java
  /** 
  在拦截器的定义中实现 HandlerInterceptor 接口，并实现了接口中的 3 个方法。有关这 3 个方法的描述如下：
  
  preHandle 方法：该方法在控制器的处理请求方法前执行，其返回值表示是否中断后续操作，返回 true 表示继续向下执行，返回 false 表示中断后续操作。
  postHandle 方法：该方法在控制器的处理请求方法调用之后、解析视图之前执行，可以通过此方法对请求域中的模型和视图做进一步的修改。
  afterCompletion 方法：该方法在控制器的处理请求方法执行完成后执行，即视图渲染结束后执行，可以通过此方法实现一些资源清理、记录日志信息等工作。
  **/
  
  import javax.servlet.http.HttpServletRequest;
  import javax.servlet.http.HttpServletResponse;
  
  import org.springframework.web.servlet.HandlerInterceptor;
  import org.springframework.web.servlet.ModelAndView;
  
  public class TestInterceptor implements HandlerInterceptor {
      @Override
      public void afterCompletion(HttpServletRequest request,
              HttpServletResponse response, Object handler, Exception ex)
              throws Exception {
          System.out.println("afterCompletion方法在控制器的处理请求方法执行完成后执行，即视图渲染结束之后执行");
      }
  
      @Override
      public void postHandle(HttpServletRequest request,
              HttpServletResponse response, Object handler,
              ModelAndView modelAndView) throws Exception {
          System.out.println("postHandle方法在控制器的处理请求方法调用之后，解析视图之前执行");
      }
  
      @Override
      public boolean preHandle(HttpServletRequest request,
              HttpServletResponse response, Object handler) throws Exception {
          System.out.println("preHandle方法在控制器的处理请求方法调用之后，解析视图之前执行");
          return false;
      }
  }
  ```

  +  让自定义的拦截器生效需要在 Spring MVC 的配置文件中进行配置，配置示例代码如下：  

  ```xml
  <!-- 
  <mvc：interceptors> 元素用于配置一组拦截器，其子元素 <bean> 定义的是全局拦截器，即拦截所有的请求。
  
  <mvc：interceptor> 元素中定义的是指定路径的拦截器，其子元素 <mvc：mapping> 用于配置拦截器作用的路径，该路径在其属性 path 中定义。
  
  path 的属性值“/**”表示拦截所有路径，“/gotoTest”表示拦截所有以“/gotoTest”结尾的路径。如果在请求路径中包含不需要拦截的内容，可以通过 <mvc：exclude-mapping> 子元素进行配置。
  
  注：<mvc：interceptor> 元素的子元素必须按照 <mvc：mapping.../>、<mvc：exclude-mapping.../>、<bean.../> 的顺序配置。
  --!>
      
  <!-- 配置拦截器 -->
  <mvc:interceptors>
      
      <!-- 配置一个全局拦截器，拦截所有请求 -->
      <bean class="interceptor.TestInterceptor" /> 
      
      <mvc:interceptor>
          
          <!-- 配置拦截器作用的路径 -->
          <mvc:mapping path="/**" />
          
          <!-- 配置不需要拦截作用的路径 -->
          <mvc:exclude-mapping path="" />
          
          <!-- <mvc:interceptor>元素中，表示匹配指定路径的请求才进行拦截 -->
          <bean class="interceptor.Interceptor1" />
      </mvc:interceptor>
      
      <mvc:interceptor>
          <!-- 配置拦截器作用的路径 -->
          <mvc:mapping path="/gotoTest" />
          
          <!-- 定义在<mvc: interceptor>元素中，表示匹配指定路径的请求才进行拦截 -->
          <bean class="interceptor.Interceptor2" />
          
      </mvc:interceptor>
  </mvc:interceptors>
  ```



#### REST风格 url（REST：使数据作为资源可用）：

REST：使数据作为资源可用

+ REST 是一种无需解释的 API 架构风格，它由一系列的架构约束所定义，REST 使得服务端的数据可用，并以简单的格式（通常是 JSON 和 XML）来表示它。
+ REST 的工作机制：
  + RESTful 体系结构遵守如下六个体系结构约束：
    + 统一接口：无论设备或应用程序类型如何，都可采用统一的方式与给定的服务端进行交互。
    + 无状态：请求本身包含处理该请求所需要的状态，并且服务端不存储与会话相关的任何内容。
    + 缓存
    + 客户端—服务器体系结构：允许双方独立发展
    + 应用程序的层级系统
    + 服务端向客户端提供可执行代码的能力

+ 在 REST 中，使用例如 GET、POST、PUT、DELETE、OPTIONS 可能还有 PATCH 等 HTTP 方法来完成操作。
+ REST 的优势：
  + 客户端和服务端的解耦：由于 REST 尽可能地解耦了客户端和服务端，REST 可以提供更好的抽象性。具有抽象级别的系统能够封装其实现细节，以更好的标示和维持它的属性。这使得 REST API 足够灵活，可以随着时间的推移而发展，同时保持稳定的系统。
  + 可发现性：客户端和服务端之间的通信描述了所有内容，因此不需要外部文档即可了解如何与 REST API 进行交互。
  + 缓存友好：REST 重用了许多 HTTP 工具，也是唯一一种可以在 HTTP 层面上缓存数据的 API 架构风格。与其相对的是，在任何其他 API 上实现缓存都需要配置其他缓存模块。
  + 多种格式支持：REST 拥有支持多种格式用于存储和交换数据的能力，这是它如今成为搭建公共 API 的主要选择的原因之一。
+ REST 的不足
  + 没有标准的 REST 结构：在构建 REST API 方面，没有具体的正确方法。如何对资源进行建模以及哪些资源需要建模取决于不同的情况。这使得 REST 在理论上很简单，但在实践中却很困难。
  + 庞大的负载：REST 会返回大量丰富的元数据，以便客户端可以仅从响应中了解有关应用程序状态的所有必要信息。对于具有大量带宽容量的大型网络系统来说，这种“啰嗦”的通信并不算很大的负载。但带宽容量并非总是足够的。这也是 Facebook 在 2012 年提出 GraphQL 架构风格的关键驱动因素。
  + 响应过度和响应不足问题。REST 的响应包含的数据会过多或不足，通常会导致客户端需要发送另一个请求。



#### SpringMVC核心组件：

1. DispatcherServlet：前端控制器，核心
   + 作用：接收请求，响应结果，相当于转发器、中央处理器，降低了组件之间的耦合性。
   + 用户发送请求交给DispatcherServlet，DispatcherServlet是整个流程控制的中心，由它调用其他组件处理用户请求，分发到具体的对应Controller，从而获取到需要的业务数据Model，Model再通过DispatcherServlet传递给View完成页面呈现；DispatcherServlet的存在降低了组件的之间的耦合性。

2. HandlerMapping：处理器映射器
   + 作用：根据请求的URL，找到对应的Handler，帮助DispatcherServlet找到对应的Controller
   + HandlerMapping负责根据用户请求找到Handler即处理器，SpringMVC提供了多种不同的映射器实现不同的映射方式，例如：配置文件方式，实现接口方式，注解方式等。

3. HandlerInterceptor：Handler执行前后拦截器

   + HandlerInterceptor是个接口，里面包含三个方法：preHandle、postHandle、afterCompletion

   + 分别在Handler执行前、执行中、执行完成后执行的三个方法

4. HandlerExecutionChain：HandlerMapping返回给DispatcherServlet的执行链

   + HandlerMapping返回给DispatcherServlet的不光有Handler，还有HandlerInterceptor

   + preHandle -->ControllerMethod -->postHandle -->afterCompletion

   + 这个链使用Java的反射机制reflection实现

5. HandlerAdapter：处理器适配器

   + 作用：将各种Controller适配成DispatcherServlet可以使用的Handler，通过特定规则（HandlerAdapter要求的规则）去执行Handler

   + 通过HandlerAdapter对处理器进行执行，这是适配器模式的应用，通过扩展适配器可以对更多类型的处理器进行执行。

6. Handler：处理器（需要程序员开发）

   + 注：编写Handler时需按HandlerAdapter的要求去做，否则HandlerAdapter不能正确执行Handler

   + Handler是继DispatcherServlet前端控制器的后台控制器，在DispatcherServlet控制下对用户请求进行处理，Handler涉及业务需求，所以需程序员针对用户需求进行开发，最终返回业务数据

7. ModelAndView：SpringMVC中对Model的一种表示形式

   + SpringMVC中有Model、Map，但SpringMVC都会将其转化为ModelAndView，Model、Map都是ModelAndView的具体表现

8. ViewResolver：视图解析器

   + 作用：进行视图解析，根据逻辑视图名解析成真正的视图View

   + ViewResolver负责将处理结果生成View视图，ViewResolver首先根据逻辑视图名解析成具体的页面地址，然后对View进行渲染，将处理结果通过页面展示给用户；SpringMVC提供了很多类型View视图，包括：jstlView、freemarkerView、pdfView、jsp、html等。

9. View：视图（需程序员编写jsp、html）

   + View是一个接口，实现类支持不同的类型（jsp、html、freemarker、pdf等）
     