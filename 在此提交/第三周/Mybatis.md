## Mybatis

#### ORM 思想：

+ 对象-关系映射（object relation mapping，简称ORM），把对象模型表示的对象映射到基于SQL 的关系模型数据库结构中去。这样，在具体的操作实体对象的时候，就不需要再去和复杂的 SQL 语句打交道，只需简单的操作实体对象的属性和方法 。ORM 技术是在对象和关系之间提供了一条桥梁，前台的对象型数据和数据库中的关系型的数据通过这个桥梁来相互转化。



#### 使用 Mybatis 简单地操作：

1. 创建maven项目，添加依赖：

   ```xml
   <dependencies>
       <!-- mybatis依赖 -->
       <dependency>
           <groupId>org.mybatis</groupId>
           <artifactId>mybatis</artifactId>
       </dependency>
       <!-- mysql依赖 -->
       <dependency>
           <groupId>mysql</groupId>
           <artifactId>mysql-connector-java</artifactId>
       </dependency>
   </dependencies>
   ```

2. 全局配置文件，mybatis-config.xml：

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE configuration
           PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-config.dtd">
   <configuration>
       <properties resource="db.properties"> </properties>
       <environments default="development">
           <environment id="development">
               <transactionManager type="JDBC" />
               <dataSource type="POOLED">
                   <property name="driver" value="${db.driver}" />
                   <property name="url" value="${db.url}" />
                   <property name="username" value="${db.username}" />
                   <property name="password" value="${db.password}" />
               </dataSource>
           </environment>
       </environments>
       <mappers>
           <mapper resource="mapper/UserMapper.xml" />
       </mappers>
   </configuration>
   ```

3. 属性文件，db.properties：

   ```properties
   db.driver=com.mysql.cj.jdbc.Driver
   db.url=jdbc:mysql:///user?useUnicode=true&characterEncoding=UTF-8
   db.username=root
   db.password=
   ```

4. 实体类，User：

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
   
       getter...
       setter...
   }
   ```

5. 映射文件，UserMapper.xml：

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE mapper PUBLIC "-//mybatis.org/DTD Mapper 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
   
   <mapper namespace="test">
       <!-- 根据id获取用户信息 -->
       <select id="findUserById" parameterType="int" resultType="com.example.mybatis.bean.User">
           select * from user where id = #{id}
       </select>
   
       <!-- 根据名称模糊查询用户列表 -->
       <select id="findUsersByName" parameterType="java.lang.String"  resultType="com.example.mybatis.bean.User">
           select * from user where username like '%${value}%'
       </select>
   
   </mapper>
   ```

6. 编写dao层接口，UserDao：

   ```java
   public interface UserDao {
       public User findUserById(int id);
       public List<User> findUsersByName(String name);
   }
   ```

7. 编写Dao层的实现类，UserDaoImpl：

   ```java
   public class UserDaoImpl implements UserDao {
       private final SqlSessionFactory sqlSessionFactory;
   
       public UserDaoImpl(SqlSessionFactory sqlSessionFactory){
           this. sqlSessionFactory = sqlSessionFactory;
       }
   
       @Override
       public User findUserById(int id){
           SqlSession session = sqlSessionFactory.openSession();
           User user = session.selectOne("test.findUserById", id);
           session.close();
           return user;
       }
   
       @Override
       public List<User> findUsersByName(String name) {
           SqlSession session = sqlSessionFactory.openSession();
           List<User> users = session.selectList("test.findUsersByName", name);
           session.close();
           return users;
       }
   }
   ```

8. 数据库内容，user.sql：

   ```sql
   create database user;
   create table user(id int,username varchar(32),sex varchar(16),address varchar(32));
   insert into user values(11,'jack','m','cqupt');
   insert into user values(22,'amy','f','pku');
   insert into user values(33,'amy','f','thu');
   ```

9. 进行测试，MybatisTest：

   ```java
   public class MybatisTest {
       private static SqlSessionFactory sqlSessionFactory;
   
       public static void main(String[] args) throws IOException {
           SqlSessionFactoryBuilder sessionFactoryBuilder = new SqlSessionFactoryBuilder();
           InputStream inputStream = Resources.getResourceAsStream("mybatis-config.xml");
           sqlSessionFactory = sessionFactoryBuilder.build(inputStream);
   
           UserDao userDao = new UserDaoImpl(sqlSessionFactory);
           User user = userDao.findUserById(22);
           System.out.println(user);
           List<User> users = userDao.findUsersByName("amy");
           System.out.println(users);
       }
   }
   ```



#### #{}和${}的区别：

+ #{} ：相当于JDBC SQL语句中的占位符? (PreparedStatement)

  ${}：相当于JDBC SQL语句中的连接符合 + (Statement)

+ #{} ： 进行输入映射的时候，会对参数进行类型解析（如果是String类型，那么SQL语句会自动加上’’）

  ${}  :进行输入映射的时候，将参数原样输出到SQL语句中

+ #{} ： 如果进行简单类型（String、Date、8种基本类型的包装类）的输入映射时，#{}中参数名称可以任意

  ${}  : 如果进行简单类型（String、Date、8种基本类型的包装类）的输入映射时，${}中参数名称必须是value

+ ${} :存在SQL注入问题



#### 动态 sql ：

+ if：

  ```xml
  <select id="findUserById" resultType="user">
          select * from user where
          <if test="id != null">
              id=#{id}
          </if>
          and score=0;
  </select>
  
  <!--
  if的用法：在本例中，当传入的id不为空时，sql才拼接id=#{id}。
  但如果传入的id为null，那么sql语句就成了：select * from user where and score=0，所以又引入了where标签。
  其中，test属性用于判断真假。-->
  ```

+ where：

  ```xml
  <select id="findUserById" resultType="user">
          select * from user
          <where>
              <if test="id != null">
                  id=#{id}
              </if>
                  and score=0;
          </where>
  </select>
  
  <!-- 
  where的用法：在本例中，如果传入的id为null，看似sql语句还是会成为：select * from user where and score=0。但实际上where标签会进行处理，即如果where后紧跟的是AND或OR时，就去除AND或OR。-->
  ```

+ trim：

  ```xml
  <trim prefix="WHERE" prefixOverrides="AND | OR ">
    ... 
  </trim>
  
  <!--  
  trim的用法：对于上述where标签的功能，也可以用trim标签自己来实现，比如此例即表示：当WHERE紧跟AND或OR时就去除AND或OR。-->
  ```

+ set：

  ```xml
  <update id="updateUserById">
          update user
          <set>
              <if test="username != null">
                  username=#{username},
              </if>
              <if test="password != null">
                  password=#{password}
              </if>
          </set>
          where id=#{id}
  </update>
  
  <!--
  set的用法：与where标签类似，不过set是将不需要的逗号去掉。-->
  ```

+ choose：

  ```xml
  <select id="findActiveBlogLike" resultType="Blog">
          select * from user where state=‘graduated’
          <choose>
              <when test="name != null">
                  and name like #{name}
              </when>
              <when test="score != null and score.math != 0">
                  and school like #{school}
              </when>
              <otherwise>
                  and score = 100
              </otherwise>
          </choose>
  </select>
  
  <!-- 
  choose的用法：可以选择合适的变量。-->
  ```

+ foreach：

  ```xml
  <select id="getUserInCities" resultMap="BaseResultMap">
          select * from user
          where address in
          <foreach collection="cities" index="index" open="(" separator="," close=")" item="city">
              #{city}
          </foreach>
      </select>
  
  <!--
  传入List集合，内容为城市名，以逗号分隔，如成都和重庆：collection表示传入的参数中集合的名称，index表示是当前元素在集合中的下标，open和close则表示如何将集合中的数据包装起来，separator表示分隔符，item则表示循环时的当前元素。本例最终组合成的sql就是select * from user where address in（'成都','重庆'）-->
  ```

+ bind：

  ```xml
  <select id="getUserByName" resultMap="">
          <bind name="param" value="username + '%'"></bind>
              select * from user where name like #{param}
  </select>
  
  <!--
  bind的用法：可以通过bind标签预先编写变量中重复的内容-->
  ```

  

#### 了解常用注解：

| **注解**                                                     | **目标** | **对应的XML标签**                                   |
| ------------------------------------------------------------ | -------- | --------------------------------------------------- |
| @CacheNamespace                                              | 类       | <cache>                                             |
| @CacheNamespaceRef                                           | 类       | <cacheRef>                                          |
| @Results                                                     | 方法     | <resultMap>                                         |
| @Result                                                      | 方法     | <result> <id>                                       |
| @One                                                         | 方法     | <association>                                       |
| @Many                                                        | 方法     | <collection>                                        |
| @Insert @Update @Delete                                      | 方法     | <insert> <update> <delete>                          |
| @InsertProvider @UpdateProvider @DeleteProvider @SelectProvider | 方法     | <insert> <update> <delete> <select> 允许创建动态SQL |
| @Param                                                       | 参数     | N/A                                                 |
| @Options                                                     | 方法     | 映射语句的属性                                      |
| @select                                                      | 方法     | <select>                                            |

+ @Select：

  ```java
  @Select(" Select * from user")
      @Results({
              @Result(id = true, column = "id", property = "id"),
              @Result(column = "name", property = "name"),
              @Result(column = "sex", property = "sex"),
              @Result(column = "address", property = "address")
      })
      List<User> queryAllUser();
  ```

+ Insert：

  ```java
  @Insert("insert into user(name,sex,age) values(#{name},#{sex},#{age}" )
  int saveUser(User user);
  ```

+ @Update：

  ```java
  @Update("update user set name=#{name},sex=#{sex},age=#{age} where id=#{id}")
  void updateUserById(User user);
  ```

+ @Delete：

  ```java
  @Delete("delete from user where id=#{id}")
  void deleteById(Integer id);
  ```

+ @One：

  ```java
  @Select("select * from user where id=#{id}")
      @Results(
              value = {
                      @Result(column = "name",property = "name"),
                      @Result(column = "type",property = "type",
                      //one表示查询出来的结果只有一个。
                      one = @One(select="com.xxxx.UserMapper.findTypeById",
                      //及时加载  
                      fetchType = FetchType.EAGER))
              }
      )
      User findUserById(Integer id);
  ```

+ @Many：

  ```java
  @Select("select * from dept")
      @Results({
              @Result(id = true, column = "did", property = "did"),
              @Result(column = "name", property = "name"),
              @Result(column = "address", property = "address"),
              @Result(column = "id",property = "emps",
              //many表示查询出来的结果有很多个
              many = @Many(
              //select = sql语句
              select = "com.xxxx.EmpMapper.findAllEmpByDid",
              //懒加载
              fetchType = FetchType.LAZY))
      })
      List<Dept> findAllDept();
  ```

  