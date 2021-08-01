## 框架：

+ 框架特点：约束性（定义规范），支撑性（对类、功能进行封装），半成品（封装了功能，但缺少业务逻辑）
+ MVC思想：客户端发送请求后，controller控制层进行处理，model模型层装载并传输数据，view在视图层进行展示

+ 框架：struts1（封装servlet）, struts2（封装filter）, hibernate（全自动的持久层框架）, Spring, SpringMVC, Mybatis（半自动的持久层框架）
  + MVC框架：struts1，struts2，SpringMVC
  + 持久层框架：hibernate，Mybatis
  + 整合型（与其他框架进行整合，项目更完善）框架，设计型（代码从根本上发生了变化）框架：Spring



#### Spring：

特点：

+ （**）IOC（Inversion of Control 反转控制，一种思想）：把对对象的控制权交给程序本身。原来需要买菜做饭，用了Spring会自动把饭做好。
+ DI（Dependency Injection 依赖注入，IOC最经典的实现）：组件以预定义好的方式接受来自容器的资源注入，比如xml文件。注入即赋值。
+ （*）AOP（Aspect oriented programming 面向切面编程）
+ 非侵入性（轻量型）即：项目体量小 / 不过度依赖某些东西。对原来的东西不造成影响，可用可不用。
+ 容器：Spring是一个容器，包含并管理，应用对象的，生命周期。
+ 组件化（降低耦合）：组件就是sprng管理的对象，使用xml和注解组合这些对象
+ 一站式：可以整合各种企业应用框架和第三方类库，自身也提供了springMVC 和 Spring JDBC

依赖包包名的简单解释：

+ beans：IOC，context：上下文，core：核心，expression：表达式



*注：尽量不用基本数据类型，用其对应的包装类——数据库查的null不能给基本类型赋值；有时需要用它的默认值，比如0和null都是默认值，意义却不一样*



#### xml

+ （可扩展(想写什么就写什么)标记语言）
+ 命名空间（规定当前文件里写的东西）：

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

</beans>
```

+ 一个<bean>就代表一个对象



#### Spring示例（通过xml配置）：

操作：

1. 新建bean类

2. 在src包下新建xml文件（配置的默认名字：applicationContext.xml）

3. xml中配置<bean>（id为唯一标识属性）

   ```xml
   <bean id="person" class="com.company.spring.bean.Person"></bean>
   ```

4. 新建test类

5. 在test类中，初始化容器，即实例化ApplicationContext

   ```java
   ApplicationContext ac = new ClasspathApplicationContext("xml文件名");
   ```

6. 通过容器获取对象（getBean()的参数为反射时，可以不用在xml中写id，但此时xml中只能配置此类的一个bean）

   ```java
   Person person = (Person)ac.getBean("xml中对应的id");
   ```

7. 获取对象时最好用以下写法：

   ```java
   Person person = ac.getBean("person", Person.class);
   ```



其他操作：

1. 可以在xml文件中给属性赋值（property标签用的是set方法）

   ```xml
   <bean id="person" class="com.company.spring.bean.Person">
       <property name="id" value="2324"> </property>
       <property name="name" value="jack"> </property>
   </bean>
   ```

2. 也可以使用构造方法赋值（类中必须有对应的构造方法）：

   ```xml
   <bean id="p2" class="com.company.spring.bean.Person">
       <constructor-arg  value="2133"> </constructor-arg>
       <constructor-arg  value="yhf"> </constructor-arg>
   </bean>
   ```

3. 构造方法冲突时可以用index和type属性设置变量类型：

   ```xml
   <bean id="p3" class="com.company.spring.bean.Person">
       <constructor-arg  value="2133" type="java.lang.Integer"> </constructor-arg>
       <constructor-arg  value="yhf"> </constructor-arg>
   </bean>
   ```

4. 使用P命名空间赋值：

   + 添加P命名空间：

     ```xml
     xmlns:p="http://www.springframework.org/schema/p"
     ```

   + 注入：

     ```xml
     <bean id="p4" class="com.company.spring.bean.Person" p:id="3245" p:name="yy" p:score="54"> </bean>
     ```

     

*注：Spring构造对象时使用的是反射，所以所管理的对象必须要有一个无参构造方法*



#### 小tips：

##### Spring提供两种IOC的实现：

+ BeanFactory：最基本实现的接口，不提供给开发人员
+ ApplicationContext：实现BeanFactory的子接口，面向开发人员
  + ClasspathXmlApplicationContext：类路径下xml格式的配置文件（路径相对）
  + FileSystemXmlApplicationContext：文件系统中xml格式的配置文件（路径绝对）

##### bean的作用域：

+ 为singleton时，只在第一次创建容器时创建此对象
+ prototype时，在每次使用容器时创建对象
+ request：每次HTTP请求时创建一次对象
+ session：一次会话创建一次

##### 生命周期：

1. 调用构造方法，创建对象
2. 赋值，依赖注入
3. 初始化
4. 使用
5. 销毁



#### 通过注解进行配置（基于注解的组件化管理）：

##### 加上注解的类就是组件

1. 创建对应的类，controller，dao，service，类上添加相应的注解（@Component可以和@Controller@Repository@Service进行交换，即功能都是一样的，只是为了识别组件，但换为后三个会使类的使作用更清晰）且除这三个类之外的类就可以用@Component

2. 创建xml文件，添加context命名空间：

   ```xml
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:context="http://www.springframework.org/schema/context" <!-- 添加的-->
          xsi:schemaLocation="http://www.springframework.org/schema/beans
          http://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/context <!-- 添加的-->
           http://www.springframework.org/schema/context/spring-context.xsd"> <!-- 添加的-->
   
   </beans>
   ```

3. xm里配置组件扫描路径（即所有组件所在的包）：

   ```xml
   <context:component-scan base-package="com.company.spring"> </context:component-scan>
   ```

4. 即可在测试类中将配置注解的类当作组件使用，即相当于在xml中添加了bean标签，且自动将类名作为id（首字母要小写）（id也可以自己设置）示例：

   ```java
   @Controller(value="id名")
   public class UserController{
       
   }
   
   //或者：
   
   @Controller("id名")
   public class UserController{
       
   }
   ```

   

#### 扫描组件的包含和排除：

1. （*）context标签中更改默认filter（否则无论如何都会全扫描）：

   ```xml
   <context:component-scan base-package="com.company.spring" use-default-filters="false"> </context:component-scan>
   ```

2. 包含扫描组件时：添加 ”包含过滤器“ 标签（以 “注解” 为type）：

   ```xml
   <context:component-scan base-package="com.company.spring" use-default-filters="false">
           <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
   </context:component-scan>
   ```

3. 包含.....：.......（以 “全类名” 为type）

   ```xml
   <context:include-filter type="assignable" expression="com.company.spring.service.UserService"/>
   ```

4. 排除扫描时写法与上面相同（但此时要将 use-default-filters 设为 true ）：

   ```xml
   <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Repository"/>
   ```

**不能同时包含和排除**



#### 基于注解的自动装配：

+ 在私有变量的上面添加@Autowired即可，默认使用的是byType策略，此时要求Spring容器中只有一个能够为其赋值；但byType不成立的时候可以自动切换至byName，此时要求Spring容器中有一个bean的id和属性名一致

+ 该接口有多个实现类时，可以用@Qualifier(value="实现类的首字母小写的类名")来选定注入类：

```java
@Autowired
@Qualifier(value = "userDaoImpl")
UserDao userDao;
```

#### AOP：

+ 动态代理：不管目的类是什么样，都能通过代理类来生成目标对象（需要保证结果的一致性）

+ AOP是对OOP的补充，思想：横向抽取机制——将非业务代码和公共代码完全从业务逻辑代码中抽取出来（OOP做不到）
+ 横切关注点：每个方法中抽取出来的同一类非核心业务
+ 切面：存储公共功能的类（即封装横切关注点的类）
+ 通知：存储在切面中的横切关注点（即切面要完成的各个具体工作），包含：
  + 前置通知（@Before）：作用于方法执行之前
  + 后置通知（@After）：作用于方法的finally语句块，即不管有无异常都会执行
  + 返回通知 / 最终通知（@AfterReturning）：作用于方法执行之后，可通过returning接收方法的返回值（需写在注解的参数和方法的形参中，且参数名字相同）
  + 异常通知 / 例外通知（@AfterThrowing）：作用于方法抛出异常时，可通过throwing接收方法返回的异常
  + 环绕通知（@Around）：包含上述四种通知

+ 连接点：从方法中抽取代码的位置
+ 切入点：查找到连接点的条件



#### 切面的写法：

1. 引入jar包

2. 引入aop命名空间

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:aop="http://www.springframework.org/schema/aop"
          xmlns:context="http://www.springframework.org/schema/context"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
          http://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/context
           http://www.springframework.org/schema/context/spring-context.xsd
           http://www.springframework.org/schema/aop
           http://www.springframework.org/schema/aop/spring-aop.xsd">
   
   </beans>
   ```

3. xml中配置自动代理（动态代理）：

   ```xml
   <beans>
   	<aop:aspectj-autoproxy/>
   </beans>
   ```

4. 写切面：

   前置通知

   ```java
   @Aspect
   public class MyLoggerAspect {
   
       //@Aspect：注明是切面
       //@Before()：前置通知
       //value：切入的表达式，必须设置
   
       @Before(value = "execution(public int com.company.spring.proxy.MathI.add(int, int))")
       public void beforeMethod(){
   		System.out.println("前置通知");
       }
   }
   ```

   **此时还是不能成功实现切面，因为AOP也要依赖IOC管理组件**

   

5. 在xml中添加context命名空间

6. 配置包扫描路径

7. 在切面上添加@Component

8. 同时涉及到的其他类也都应该被Spring管理，所以在实现类上也添加@Component

9. 使用Test类进行测试：

   ```java
   public class Test {
       public static void main(String[] args) {
           ApplicationContext ac = new ClassPathXmlApplicationContext("aop.xml");
           MathI mathI = ac.getBean("mathIImpl", MathI.class);
   
           System.out.println(mathI.add(2,4));
       }
   }
   ```



+ 其他：

  + 通知可以接收JoinPoint参数来获取调用信息：

    ```java
    @Before(value = "execution(public int com.company.spring.proxy.MathI.add(int, int))")
    public void beforeMethod(JoinPoint joinPoint){
    
        Object[] args = joinPoint.getArgs();
        String methodName = joinPoint.getSignature().getName();
    
        System.out.println("arguments: " + Arrays.toString(args));
        System.out.println("method: " + methodName);
        
        System.out.println("前置通知");
    }
    ```

  + @Before的参数可以用*：

    ```java
    //该类下的符合条件的方法（修饰符、返回类型、参数限定）
    @Before(value = "execution(public int com.company.spring.proxy.MathI.*(int, int))")
    //该类下的符合条件的方法（参数限定）
    @Before(value = "execution(* com.company.spring.proxy.MathI.*(int, int))")
    //该包下所有类的所有方法
    @Before(value = "execution(* com.company.spring.proxy.*.*(..))")
    ```

  + 后置通知@After用法与上述相同

  + 返回通知：

    可以接收运行结果

    ```java
    @AfterReturning(value = "execution(* com.company.spring.proxy.*.*(..))", returning = "result")
    public void afterRe(JoinPoint joinPoint, Object result){
        System.out.println("method: " + joinPoint.getSignature().getName() + ", " + "result: " + result);
    }
    ```

  + 异常通知：

    ```java
    @AfterThrowing(value = "execution(* com.company.spring.proxy.*.*(..))", throwing = "ex")
    public void afterTh(Exception ex){
        System.out.println(ex);
    }
    ```

  + 环绕通知：

    ```java
    @Around(value = "execution(* com.company.spring.proxy.*.*(..))")
    public Object around(ProceedingJoinPoint joinPoint)  {
        Object result;
    
        try {
            //前置通知
            result = joinPoint.proceed();
            return result;
            //返回通知
        } catch (Throwable e){
            e.printStackTrace();
            //异常通知
        } finally {
            System.out.println();
            //后置通知
        }
        return -1;
    }
    ```

  + 公共切入点 / 切入点重用：

    ```java
    //设置公共切入点：
    @Pointcut(value = "execution(* com.company.spring.proxy.*.*(..))")
    public void share(){}
    
    //value的值可以改为公共切入点的方法名
    @AfterThrowing(value = "share()", throwing = "ex")
    public void afterTh(Exception ex){
        System.out.println(ex);
    }
    ```

  + 设置切面加载的优先级：

    ```java
    //@Order的参数值越小，优先级越高，默认值：int的最大值
    @Component
    @Aspect
    @Order(1)
    public class MyLoggerAspect {
    }
    ```

    

#### 事务：

1. 新建xml文件并配置：

   ```xml
   <!--
   1.导入context的名称空间  
   2.设置Spring要扫描的包  
   3.开始Spring对注解式事务的支持。并且指定事务管理器类  
   4.把Dao和service都交给Spring管理  
   5.由于我们使用了注解，所以不能用JdbcDaoSupport，这个时候我们需要定义JdbcTemplate，为其注入数据源 
   -->
   
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:aop="http://www.springframework.org/schema/aop"
          xmlns:tx="http://www.springframework.org/schema/tx"
          xmlns:context="http://www.springframework.org/schema/context"
          xsi:schemaLocation="http://www.springframework.org/schema/beans   
          http://www.springframework.org/schema/beans/spring-beans.xsd   
          http://www.springframework.org/schema/tx   
          http://www.springframework.org/schema/tx/spring-tx.xsd  
          http://www.springframework.org/schema/aop   
          http://www.springframework.org/schema/aop/spring-aop.xsd   
          http://www.springframework.org/schema/context   
          http://www.springframework.org/schema/context/spring-context.xsd">
   
       <context:component-scan base-package="com.company.spring.transaction"> </context:component-scan>
   
       <tx:annotation-driven transaction-manager="transactionManager"/>
   
       <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
           <property name="dataSource" ref="driverManagerDataSource"> </property>
       </bean>
       
       <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
           <property name="dataSource" ref="driverManagerDataSource"> </property>
       </bean>
   
       <!-- 配置Spring的内置数据源 -->
       <bean id="driverManagerDataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
           
           <!-- 给数据源注入参数 -->
           <property name="driverClassName" value="com.mysql.jdbc.Driver"> </property>
           <property name="url" value="jdbc:mysql://localhost:3306/srping_tx"> </property>
           <property name="username" value="root"> </property>
           <property name="password" value="1234"> </property>
       </bean>
   </beans>  
   ```

2. 在需要事务支持的地方加入@Transactional注解（一般在service层控制事务） 

3. @Transactional注解配置的位置：

   + 用在业务实现类上：该类所有的方法都在事务的控制范围 

     ```java
     @Component(accountService)
     @Transactional
     public class AccountServiceImpl implements AccountService{
         
     }
     ```

   + 用在业务接口上：该接口的所有实现类都起作用

     ```java
     @Transactional
     public interface accountService{
     
     }
     ```

   + 通过注解指定事务的定义信息 

     ```java
     @Transactional(readOnly = true)
     public Object query(){
     	return null;
     }
     ```

     