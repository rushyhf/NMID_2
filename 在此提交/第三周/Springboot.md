## Springboot

#### Springboot 项目创建：

1. 创建新项目时，在左侧菜单找到并点击 Spring Initializr，点击next。（注：idea默认使用https://start.spring.io提供的在线模板，所以需保证网络畅通。也可以选择Custom从指定的链接加载模板，或在本地搭建spring Initializr服务器），然后选定需要的依赖即可。

2. 在类下新建四个包：bean、controller、dao、service

3. bean层下新建实体类User：

   ```java
   public class User {
       private int id;
       private String username;
       private String sex;
       private String address;
   
       @Override
       public String toString() {
           return "User{" +
                   "id=" + id +
                   ", username='" + username + '\'' +
                   ", sex='" + sex + '\'' +
                   ", address='" + address + '\'' +
                   '}';
       }
       
       getter....
       setter....
   }
   ```

4. dao层下新建查询接口UserDao：

   ```java
   @Mapper
   @Repository
   public interface UserDao {
       @Select("select * from user")
       User[] getUsers();
   }
   ```

5. service层下新建接口UserService：

   ```java
   public interface UserService {
       User[] getUsers();
   }
   ```

6. service层下新建实现类UserServiceImpl：

   ```java
   @Service
   public class UserServiceImpl implements UserService{
       @Autowired
       UserDao userDao;
       
       @Override
       public User[] getUsers() {
           return userDao.getUsers();
       }
   }
   ```

7. controller层下新建控制类UserController：

   ```java
   @Controller
   public class UserController {
       @Autowired
       UserService userService;
   
       @RequestMapping("/getUsers")
       User[] getUsers(){
           return userService.getUsers();
       }
   }
   ```

8. 将属性配置文件更改为application.yml，然后填充参数：

   ```yml
   server:
     port: 8080
   
   spring:
     datasource:
       url: jdbc:mysql://localhost:3306/user?useUnicode=true&characterEncoding=UTF-8
       driver-class-name: com.mysql.cj.jdbc.Driver
       username: root
       password: 
   ```



#### 全局错误处理器：

SpringBoot中有一个`ControllerAdvice`注解，使用该注解表示开启了全局异常的捕获，只需在自定义一个方法使用`ExceptionHandler`注解，然后定义捕获异常的类型即可对这些捕获的异常进行统一的处理。

+  定义一个基础的接口类：

  ```java
  public interface BaseErrorInfoInterface {
      /** 错误码*/
  	 String getResultCode();
  	
  	/** 错误描述*/
  	 String getResultMsg();
  }
  ```

+ 自定义枚举类：

  ```java
  public enum CommonEnum implements BaseErrorInfoInterface {
  	// 数据操作错误定义
  	SUCCESS("200", "成功!"), 
  	BODY_NOT_MATCH("400","请求的数据格式不符!"),
  	SIGNATURE_NOT_MATCH("401","请求的数字签名不匹配!"),
  	NOT_FOUND("404", "未找到该资源!"), 
  	INTERNAL_SERVER_ERROR("500", "服务器内部错误!"),
  	SERVER_BUSY("503","服务器正忙，请稍后再试!")
  	;
  
  	/** 错误码 */
  	private String resultCode;
  
  	/** 错误描述 */
  	private String resultMsg;
  
  	CommonEnum(String resultCode, String resultMsg) {
  		this.resultCode = resultCode;
  		this.resultMsg = resultMsg;
  	}
  
  	@Override
  	public String getResultCode() {
  		return resultCode;
  	}
  
  	@Override
  	public String getResultMsg() {
  		return resultMsg;
  	}
  }
  ```

+ 自定义异常类：

  ```java
  
  public class BizException extends RuntimeException {
  
  	private static final long serialVersionUID = 1L;
  
  	//错误码
  	protected String errorCode;
  	
      //错误信息
  	protected String errorMsg;
  
  	public BizException() {
  		super();
  	}
  
  	public BizException(BaseErrorInfoInterface errorInfoInterface) {
  		super(errorInfoInterface.getResultCode());
  		this.errorCode = errorInfoInterface.getResultCode();
  		this.errorMsg = errorInfoInterface.getResultMsg();
  	}
  	
  	public BizException(BaseErrorInfoInterface errorInfoInterface, Throwable cause) {
  		super(errorInfoInterface.getResultCode(), cause);
  		this.errorCode = errorInfoInterface.getResultCode();
  		this.errorMsg = errorInfoInterface.getResultMsg();
  	}
  	
  	public BizException(String errorMsg) {
  		super(errorMsg);
  		this.errorMsg = errorMsg;
  	}
  	
  	public BizException(String errorCode, String errorMsg) {
  		super(errorCode);
  		this.errorCode = errorCode;
  		this.errorMsg = errorMsg;
  	}
  
  	public BizException(String errorCode, String errorMsg, Throwable cause) {
  		super(errorCode, cause);
  		this.errorCode = errorCode;
  		this.errorMsg = errorMsg;
  	}
  	
  
  	getter....
      setter....
  
  	@Override
  	public Throwable fillInStackTrace() {
  		return this;
  	}
  
  }
  ```

+ 自定义数据格式：

  ```java
  
  public class ResultBody {
  	//响应代码
  	private String code;
      
  	//响应消息
  	private String message;
      
  	//响应结果
  	private Object result;
  
  	public ResultBody() {
  	}
  
  	public ResultBody(BaseErrorInfoInterface errorInfo) {
  		this.code = errorInfo.getResultCode();
  		this.message = errorInfo.getResultMsg();
  	}
  
  	getter....
      setter....
  
  	//成功
  	public static ResultBody success() {
  		return success(null);
  	}
  
  	//成功
  	public static ResultBody success(Object data) {
  		ResultBody rb = new ResultBody();
  		rb.setCode(CommonEnum.SUCCESS.getResultCode());
  		rb.setMessage(CommonEnum.SUCCESS.getResultMsg());
  		rb.setResult(data);
  		return rb;
  	}
  
  	//失败
  	public static ResultBody error(BaseErrorInfoInterface errorInfo) {
  		ResultBody rb = new ResultBody();
  		rb.setCode(errorInfo.getResultCode());
  		rb.setMessage(errorInfo.getResultMsg());
  		rb.setResult(null);
  		return rb;
  	}
  
  	//失败
  	public static ResultBody error(String code, String message) {
  		ResultBody rb = new ResultBody();
  		rb.setCode(code);
  		rb.setMessage(message);
  		rb.setResult(null);
  		return rb;
  	}
  
  	//失败
  	public static ResultBody error( String message) {
  		ResultBody rb = new ResultBody();
  		rb.setCode("-1");
  		rb.setMessage(message);
  		rb.setResult(null);
  		return rb;
  	}
  
  	@Override
  	public String toString() {
  		return JSONObject.toJSONString(this);
  	}
  }
  ```

+ 自定义全局异常处理类：

  ```java
  
  @ControllerAdvice
  public class GlobalExceptionHandler {
  	private static final Logger logger = LoggerFactory.getLogger(GlobalExceptionHandler.class);
      
  	//处理自定义的业务异常
      @ExceptionHandler(value = BizException.class)  
      @ResponseBody  
  	public  ResultBody bizExceptionHandler(HttpServletRequest req, BizException e){
      	logger.error("发生业务异常！原因是：{}",e.getErrorMsg());
      	return ResultBody.error(e.getErrorCode(),e.getErrorMsg());
      }
      
  	//处理空指针的异常
  	@ExceptionHandler(value =NullPointerException.class)
  	@ResponseBody
  	public ResultBody exceptionHandler(HttpServletRequest req, NullPointerException e){
  		logger.error("发生空指针异常！原因是:",e);
  		return ResultBody.error(CommonEnum.BODY_NOT_MATCH);
  	}
  
      //处理其他异常
      @ExceptionHandler(value =Exception.class)
  	@ResponseBody
  	public ResultBody exceptionHandler(HttpServletRequest req, Exception e){
      	logger.error("未知异常！原因是:",e);
         	return ResultBody.error(CommonEnum.INTERNAL_SERVER_ERROR);
      }
  }
  ```

+ 实体类：

  ```java
  public class User implements Serializable{
  	private static final long serialVersionUID = 1L;
  	//编号
  	private int id;
      
  	//姓名
  	private String name;
      
  	//年龄
  	private int age;
  	 
  	getter....
      setter....
  }
  ```

+ 控制类：

  ```java
  @RestController
  @RequestMapping(value = "/api")
  public class UserRestController {
  
  	@PostMapping("/user")
      public boolean insert(@RequestBody User user) {
      	System.out.println("开始新增...");
      	//如果姓名为空就手动抛出一个自定义的异常！
          if(user.getName()==null){
              throw  new BizException("-1","用户姓名不能为空！");
          }
          return true;
      }
  
      @PutMapping("/user")
      public boolean update(@RequestBody User user) {
      	System.out.println("开始更新...");
         //这里故意造成一个空指针的异常，并且不进行处理
          String str=null;
          str.equals("111");
          return true;
      }
  
      @DeleteMapping("/user")
      public boolean delete(@RequestBody User user)  {
          System.out.println("开始删除...");
          //这里故意造成一个异常，并且不进行处理
          Integer.parseInt("abc123");
          return true;
      }
  
      @GetMapping("/user")
      public List<User> findByUser(User user) {
      	System.out.println("开始查询...");
          List<User> userList =new ArrayList<>();
          User user2=new User();
          user2.setId(1L);
          user2.setName("xuwujing");
          user2.setAge(18);
          userList.add(user2);
          return userList;
      }
  }
  ```



#### Springboot 启动流程:

总览：

- SpringApplication.run中执行了两步操作，先封装了一个SpringApplication的实例，再执行该实例的重载run方法
- SpringApplication封装实例时，读取了classpath下所有的`MTEA-INF/spring.factories` xml配置文件的ApplicationContextInitializer（容器初始化器）还有ApplicaiontListener（侦听器），将这两者封装到SpringApplication实例中
- 执行SpringApplication实例的run方法
- run方法中默认初始化了Annotation配置的容器AnnotationConfigApplicationContext
- 执行上面ApplicationContextInitializer的initial方法
- 然后加载Bean到容器中

详解：

-  @SpringBootApplication主要由三个注解构成：@SpringBootConfiguration、@EnableAutoConfiguration、@ComponentScan
- @EnableAutoConfiguration 底层是由两个注解组成：@AutoConfigurationPackage、@Import(AutoConfigurationImportSelector.class)
- @Import(AutoConfigurationImportSelector.class) 自动配置的奥妙就在这里，这个类导入了很多自动配置类，debug一下可以发现，其读取的是classpath下的`META-INF/spring.factories`下的自动配置类 

总结：

+ Spring Boot通过主启动类上的@SpringBootApplication中的@EnableAutoConfiguration读取了类路径下的`META-INF/spring.factories`下EnableAutoConfiguration的配置类，但是这些配置类使用了@ConditionalOnClass，需满足一定的条件才会激活配置，这些配置类写入了默认的配置。



#### 日志的使用：

+  直接使用 logback 打印信息 ：

  ```java
  import org.slf4j.Logger;
  import org.slf4j.LoggerFactory;
  
  public class TestLog {
  
      /**
       * getLogger 参数为当前类的类对象
       * org.slf4j.Logger;
       * org.slf4j.LoggerFactory;
       */
      private static Logger logger = LoggerFactory.getLogger(TestLog.class);
  
      public static void main(String[] args) {
          /**
           * 通过 Logger 的api打印信息
           * 日志的级别由低到高   
           * trace < debug < info < warn < error
           */
          logger.debug("这是日志");
          logger.info("这是日志");
          logger.warn("这是日志");
          logger.error("这是日志");
      }
  }
  ```

+ 也可使用log4j，依赖如下：

  ```java
  <!--导入log4j的依赖-->
  <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-log4j12</artifactId>
  </dependency>
  ```



#### 扩展与接管 SpringMVC：

1. 扩展springmvc:

在springboot配置类中实现WebMvcConfigurer 接口

例如添加一个视图控制器

```java
@Configuration
public class MySpringMVCConfig implements WebMvcConfigurer {
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/atguigu").setViewName("success");
    }
}
```

2. 全面接管springmvc

在实现WebMvcConfigurer 接口的配置类上加上@EnableWebMvc注解

因为在Web Mvc 自动配置类中有

```java
@Configuration(
    proxyBeanMethods = false
)
@ConditionalOnWebApplication(
    type = Type.SERVLET
)
@ConditionalOnClass({Servlet.class, DispatcherServlet.class, WebMvcConfigurer.class})
@ConditionalOnMissingBean({WebMvcConfigurationSupport.class}) //注意这里
@AutoConfigureOrder(-2147483638)
@AutoConfigureAfter({DispatcherServletAutoConfiguration.class, TaskExecutionAutoConfiguration.class, ValidationAutoConfiguration.class})
public class WebMvcAutoConfiguration {
```

在Web Mvc 自动配置类中@ConditionalOnMissingBean({WebMvcConfigurationSupport.class}) 说明在IOC容器中没有WebMvcConfigurationSupport类型的bean时，自动配置才生效，但是在@EnableWebMvc注解中有

```java
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.TYPE})
@Documented
@Import({DelegatingWebMvcConfiguration.class}) //注意这里
public @interface EnableWebMvc {
}
```

在@EnableWebMvc中导入了一个配置类DelegatingWebMvcConfiguration，而DelegatingWebMvcConfiguration类是这样的

```java
@Configuration(
    proxyBeanMethods = false
)
public class DelegatingWebMvcConfiguration extends WebMvcConfigurationSupport {
```

也就是说@EnableWebMvc注解向IOC容器中注入了一个WebMvcConfigurationSupport类型的组件，导致Web Mvc 自动配置类失效，从而达到了全面接管springmvc
