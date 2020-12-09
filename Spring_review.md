# Spring_review

## 解耦

1. 使用反射创建对象，避免使用new关键字
2. 通过读取配置文件获得反射创建对象所需的全限定类名

## IOC

### 依赖

~~~xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>5.2.0.RELEASE</version>
</dependency>
~~~

### 约束

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

</beans>
~~~

### ApplicationContaxt的常用实现类

~~~java
ClassPathXmlApplicationContext("bean.xml");//读取类路径下的配置文件
FileSystemXmlApplicationContext();//读取磁盘任意路径下的配置文件（必须有访问权限）
AnnotationConfigApplicationContext();//获取注解创建容器的
~~~

### 核心容器的两个接口

#### ApplicationContext

在构建容器时，创建对象采用的是立即加载方式，当读取完配置文件就立即给配置文件中的创建对象。**单例对象适用**

#### BeanFactory

采用的是延时加载，当读取完配置文件，并调用了哪个对象时，才进行这个对象的创建。**多例对象适用**

### 创建bean对象的三种方式

- 默认构造函数创建

  ~~~xml
  <bean id="accountDao" class="com.victor.dao.impl.AccountDaoImpl"></bean>
  ~~~

- 使用普通工厂中的方法创建

  ~~~java
  public class Factory {
      public AccountDaoImpl accountDao(){
          return new AccountDaoImpl();
      }
  }
  ~~~

  ~~~xml
  <bean id="factory" class="com.victor.Factory"></bean>
  <bean id="accountDao2" factory-bean="factory" factory-method="accountDao"></bean>
  ~~~

- 使用工厂中的静态方法创建

  ~~~java
  public class Factory {
      public static AccountDaoImpl accountDao(){
          return new AccountDaoImpl();
      }
  }
  ~~~

  ~~~xml
  <bean id="factory" class="com.victor.Factory" factory-method="accountDao"></bean>
  ~~~

### Bean的作用范围

bean标签的scope属性值:

- singleton：单例的（默认值）
- prototype：多例
- request：作用于web应用的请求范围
- session：作用于web应用的会话范围
- global-session：作用于集群环境的会话范围（全局会话范围），如果不是集群，他就是session

### Bean的生命周期

#### 单例对象

- 出生：当容器创建时出生
- 活着：只要容器在，对象就活着
- 死亡：容器销毁，对象消亡

#### 多例对象

- 出生：调用该对象时创建
- 活着：对象只要在使用过程中就活着
- 死亡：在对象长时间没有使用时，由Java垃圾回收器回收

### 依赖注入

方式：

- 使用构造函数
- set方法
- 注解

#### 构造方法

~~~java
public class Student {
    private String name;
    private Date birthday;

    public Student(String name, Date birthday) {
        this.name = name;
        this.birthday = birthday;
    }

    @Override
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                ", birthday=" + birthday +
                '}';
    }
}
~~~

~~~xml
<bean id="student" class="com.victor.Student">
    <constructor-arg name="name" value="victor"></constructor-arg>
    <constructor-arg index="1" ref="birthday"></constructor-arg>
</bean>

<bean id="birthday" class="java.util.Date"></bean>
~~~

#### set方法

~~~java
public class AccountServiceImpl implements IAccountService {

    private AccountDaoImpl accountDao;

    public AccountDaoImpl getAccountDao() {
        return accountDao;
    }

    public void setAccountDao(AccountDaoImpl accountDao) {
        this.accountDao = accountDao;
    }

    public void saveAccount() {
        accountDao.saveAccount();
    }
}
//name为set后面的名称
~~~

~~~xml
<bean id="accountService" class="com.victor.service.impl.AccountServiceImpl">
    <property name="accountDao" ref="accountDao"/>
</bean>
~~~

## 注解实现IOC

### 注解

- @Component：把这个类交给spring容器并创建实例对象

- @Controller：表现层，功能与component一样

- @Service：业务层，功能与component一样

- @Repository：持久层，功能与component一样

- @Autowired：

  **作用**：自动按照类型注入，只要容器中有唯一的一个bean对象类型与要注入的变量类型匹配，就可以注入成功，如果出现多个匹配类型时，就看注入的变量名称与哪个bean的id匹配，如果没有则报错

  使用注解注入时，set方法就不需要了

- @Qualifier：

  与@Autowired组合使用，指定bean的id，给类成员注入的时候，不能单独使用，但是在给方法参数注入的时候可以

- @Resource：可以直接单独使用，指定bean的id

- @Value：用于注入基本类型与String类型，上面方法不可以注入基本类型与String，集合类型注入必须使用xml

- @Scope：用于指定bean的作用域

- @PreDestroy：用于指定销毁方法

- @PostConstruct：用于指定初始化方法

### 配置注解扫描包

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <!--告知spring要扫描的包-->
    <context:component-scan base-package="com.victor"></context:component-scan>

</beans>
~~~

### 新注解

- @Configuration：指定当前类是一个配置类，代替xml

- @ComponentScan：用注解指定要扫描的包

- @Bean：把当前方法的返回值作为bean对象注入到spring容器中

  细节：当方法中有参数时，spring会在容器中寻找是否有相同类型的，有则自动注入，有多个就看参数名称是否有一样的id

- @Import：在一个配置类中导入其他配置类，有@Import的都是父配置类，被导入的都是子配置类

- @PropertySource：用于properties文件的位置，classpath:文件名

  classpath：表示类路径下

## AOP

导入aspectjweaver坐标，解析excution表达式

使用动态代理技术实现

优点：

- 减少重复代码
- 提高开发效率
- 维护方便

### xml实现AOP步骤

1. 把通知bean交给spring管理

2. 使用aop:config标签表明开始aop的配置

3. 使用sop:aspect标签表明配置切面

   1. id属性：给切面提供唯一标识
   2. ref属性：指定通知类的bean的id

4. 在aop:aspect标签内使用对应标签配置通知的类型

   1. aop:before：表示前置通知，在切入点方法执行前执行

      - method属性：指定通知类的哪个方法为前置通知

      - pointcut属性：指定切入点表达式，该表达式的含义是给业务层中哪个方法增强

        切入点表达式格式：

        关键字：execution(访问修饰符(可省略) 返回值 包名.包名.类名.方法名(参数列表))

        全通配写法：excution(`* *..*.*(..)`)

        通常写法：excution(`* com.victor.service.impl.*.*(..)`)

   2. aop:after-returning：在切入点方法正常执行后出现

   3. aop:afetr-throwing：在切入点方法产生异常之后执行，与returning只能执行一个

   4. aop:after：无论方法是否有异常都最后执行

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd">


    <bean id="userService" class="com.victor.service.impl.UserServiceImpl"></bean>

    <bean id="logger" class="com.victor.logger.Logger"></bean>


    <aop:config>
        <aop:pointcut id="pt1" expression="execution(* com.victor.service.impl.UserServiceImpl.updateUser(..))"/>
        <aop:aspect id="log" ref="logger">
            <!--只能当前标签内使用pt2-->
            <aop:pointcut id="pt2" expression="execution(* com.victor.service.impl.UserServiceImpl.updateUser(..))"/>
            <aop:before method="beforeLog" pointcut-ref="pt1"></aop:before>
            <aop:after method="afterLog" pointcut="execution(* com.victor.service.impl.UserServiceImpl.updateUser(..))"></aop:after>
        </aop:aspect>
       
    </aop:config>


</beans>
~~~

### 环绕通知

他是spring框架为我们提供的一种可以在代码中手动控制增强方法何时执行的方式

#### 问题

当我们配置了环绕通知后，切入点方法没有执行，而通知方法执行了

#### 解决

spring框架为我们提供了一个接口，ProceedingJoinPoint，该接口有一个方法proceed（），此方法相当于明确的调用了切入点方法，该切口作为环绕通知的方法参数，在程序执行时，spring框架为我们提供该接口的实现类供我们使用

~~~java
public Object arroundLog(ProceedingJoinPoint pjp){
        Object proceed=null;
        try {
            Object[] args = pjp.getArgs();//得到方法执行的参数列表
            System.out.println("前置通知");
            proceed = pjp.proceed(args);//明确调用业务层方法
            System.out.println(pjp.getSignature());//显示调用的方法
            System.out.println("后置通知");
            return proceed;
        } catch (Throwable throwable) {
            throwable.printStackTrace();
            System.out.println("异常通知");
        }finally {
            System.out.println("最终通知");
        }
        return proceed;
    }
~~~

~~~xml
<aop:config>
        <aop:pointcut id="pt1" expression="execution(* com.victor.service.impl.UserServiceImpl.updateUser(..))"/>
        <aop:aspect id="log" ref="logger">
            <aop:around method="arroundLog" pointcut-ref="pt1"></aop:around>
        </aop:aspect>

    </aop:config>
~~~

### 注解实现AOP

使用自带的通知类型有顺序问题，注解实现AOP尽量使用环绕通知

**通知类**

~~~java
@Component("logger")
@Aspect
public class Logger {

    @Pointcut("execution(* victor.service.impl.UserServiceImpl.updateUser(..))")
    public void pt1(){

    }

    @Before("pt1()")
    public void beforeLog(){
        System.out.println("前置日志。。。。。。。");
    }

    @After("pt1()")
    public void afterLog(){
        System.out.println("后置日志。。。。。。。");
    }



    @Around("pt1()")
    public Object arroundLog(ProceedingJoinPoint pjp){
        Object proceed=null;
        try {
            Object[] args = pjp.getArgs();//得到方法执行的参数列表
            System.out.println("前置通知");
            proceed = pjp.proceed(args);//明确调用业务层方法
            System.out.println(pjp.getSignature());//显示调用的方法
            System.out.println("后置通知");
            return proceed;
        } catch (Throwable throwable) {
            throwable.printStackTrace();
            System.out.println("异常通知");
        }finally {
            System.out.println("最终通知");
        }
        return proceed;
    }
}
~~~

**xml**

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <!--自动扫描包-->
    <context:component-scan base-package="victor"></context:component-scan>

    <!--开启注解AOP的支持-->
    <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
</beans>
~~~

**纯注解**

配置类代替bean.xml

~~~java
@Configuration
@ComponentScan("victor")
@EnableAspectJAutoProxy//开启注解AOP的支持
public class LogConfig {

}
~~~

测试类

~~~java
public static void main(String[] args) {
        ApplicationContext context= new AnnotationConfigApplicationContext(LogConfig.class);
        UserService userService = context.getBean("userService", UserService.class);
        userService.updateUser(1);
    }
~~~

## JDBCTemplate

~~~java
@Test
    public void testJ(){
        ApplicationContext conetxt = new ClassPathXmlApplicationContext("bean.xml");
        JdbcTemplate jdbcTemplate=conetxt.getBean("jdbcTemplate",JdbcTemplate.class);
        /*jdbcTemplate.update("insert into account(account_name,money) value (?,?)","frank",300);*/
        List<Account> accountList = jdbcTemplate.query("select * from account where money> ?", new RowMapper<Account>() {
            public Account mapRow(ResultSet resultSet, int i) throws SQLException {
                Account account = new Account();
                account.setAccount_name(resultSet.getString(1));
                account.setMoney(resultSet.getDouble(2));
                return account;
            }
        }, 100);

        System.out.println(accountList);
        
        Integer count= jdbcTemplate.queryForObject("select count(*) from account", Integer.class);
        System.out.println(count);
    }


//其中的匿名内部类可以使用BeanPropertyRowMapper<T>代替，spring提供的封装好的类
List<Account> accountList = jdbcTemplate.query("select * from account where money> ?",
                new BeanPropertyRowMapper<Account>(Account.class),100);
        System.out.println(accountList);
~~~

## 声明式事务

### xml事务配置

#### 步骤：

1. 配置事务管理器

2. 配置事务通知，导入tx约束

   使用tx:advice配置事务通知

   属性：transaction-manager：给事务通知提供一个事务管理器引用

3. 配置AOP的通用切入点表达式

4. 建立事务通知和切入点表达式的对应关系

5. 在事务通知内配置事务的属性

   - isolation：用于指定事务的隔离级别，默认值default，表示使用数据库的隔离级别
   - propagation：指定事务的传播行为，默认值是required，表示一定有事务。查询方法可以使用supports
   - read-only：指定事务是否为只读，查询方法设置为true即可
   - timeout：指定超时时间，默认值为-1,表示永不超时，如果设置值，则以秒为单位
   - no-rollback-for：用于指定一个异常，当产生该异常时，事务回滚，产生其他异常不回滚，不使用则表示都回滚
   - rollback-for：用于指定一个异常，当产生该异常时，事务不回滚，产生其他异常才回滚，不使用则表示都回滚

~~~xml
 <!--配置事务管理器-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <!--配置事务通知-->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <!--配置事务属性-->
        <tx:attributes>
            <tx:method name="transfer" propagation="REQUIRED" read-only="false"/>
        </tx:attributes>
    </tx:advice>

    <!--AOP配置切入点-->
    <aop:config>
        <aop:pointcut id="pt1" expression="execution(* com.victor.service.impl.*.*(..))"/>
        <!--建立切入点与事务通知的联系-->
        <aop:advisor advice-ref="txAdvice" pointcut-ref="pt1"/>
    </aop:config>
~~~

### 注解事务

1. 配置事务管理器
2. 开启spring对注解事务的支持
3. 在需要的地方使用@Transactional注解

详看day04_anno_tx项目

## 编程式事务



# Mybatis_review

## 第一个mybatis程序

注：增删该需要手动提交事务，SqlSession.commit()

1. 编写与数据库对应的实体类

2. 写出相应的接口

3. 在resource目录下创建mybatis的配置文件

   ~~~xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE configuration
           PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-config.dtd">
   
   <configuration>
       <!--配置环境-->
       <environments default="mysql">
           <environment id="mysql">
               <!--配置事务类型-->
               <transactionManager type="JDBC"></transactionManager>
               <!--配置数据源（连接池）-->
               <dataSource type="POOLED">
                   <property name="driver" value="com.mysql.jdbc.Driver"/>
                   <property name="url" value="jdbc:mysql://localhost:3306/test01"/>
                   <property name="username" value="root"/>
                   <property name="password" value="123456"/>
               </dataSource>
           </environment>
       </environments>
   
       <!--指定映射文件的位置，如果是注解则使用class属性定位-->
       <mappers>
           <mapper resource="com/victor/dao/UserDao.xml"/>
       </mappers>
   
   </configuration>
   ~~~

4. 编写与接口对应的xml配置文件，并且放到resource下与接口的一样的目录下

   ~~~xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE mapper
           PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
   
   <mapper namespace="com.victor.dao.UserDao">
       <select id="findAll" resultType="com.victor.domain.User">
           select * from user
       </select>
       
       <insert id="saveUser" parameterType="com.victor.domain.User">
           /*查询最后插入数据的id号，keyProperty对应实体类，keyColumn对应表列*/
           <selectKey keyProperty="id" keyColumn="id" resultType="int" order="AFTER">
               select last_insert_id();
           </selectKey>
           insert into user(username,password) value (#{username},#{password})
       </insert>
   </mapper>
   ~~~

5. 编写测试类

   ~~~java
   public static void main(String[] args) throws Exception{
           //获取配置文件
           InputStream inputStream = Resources.getResourceAsStream("SqlMapConfig.xml");
           //创建SqlSessionFactoryBuilder对象
           SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
           //创建工厂
           SqlSessionFactory factory = sqlSessionFactoryBuilder.build(inputStream);//构建者模式
           //获取SqlSession对象
           SqlSession sqlSession = factory.openSession();//工厂模式
           //使用sqlSession对象获取UserDao的代理对象
           UserDao userDao = sqlSession.getMapper(UserDao.class);
   
           //执行代理对象的方法
           List<User> users = userDao.findAll();
           System.out.println(users);
   
           //关闭流
           sqlSession.close();
           inputStream.close();
       }
   ~~~



# Spring_MVC

![](/home/victor/learn/学习总结/springMVC流程.jpg)

![](/home/victor/learn/学习总结/mvc流程详解.jpg)

## springmvc与struts

共同点：

1. 都是表现层框架，基于mvc设计模型
2. 都离不开原始的servletAPI
3. 处理请求的机制都是一个控制核心

不同点：

1. springmvc入口是servlet，而struts是filter
2. springmvc基于方法设计，struts是基于类，所以springmvc要比struts快一些
3. springmvc更加简洁，支持JSR303，处理ajax更加方便
4. struts的OGNL表达式使页面开发效率相比mvc更高一些，但执行效率没有比jstl提升，尤其是struts2的表单标签，远没有html执行效率高



## 案例

1. ### web.xml

   ~~~xml
   <web-app>
     <display-name>Archetype Created Web Application</display-name>
   
     <servlet>
       <servlet-name>dispatcherServlet</servlet-name>//<!--配置前端控制器-->
       <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
         <!--服务器一启动就加载springmvc的配置文件-->
       <init-param>
         <param-name>contextConfigLocation</param-name>
         <param-value>classpath:springmvc.xml</param-value>
       </init-param>
       <load-on-startup>1</load-on-startup>
     </servlet>
     
     <servlet-mapping>
         <!-- 响应所有请求-->
       <servlet-name>dispatcherServlet</servlet-name>
       <url-pattern>/</url-pattern>
     </servlet-mapping>
       
       <!--post请求乱码过滤器-->
     <filter>
       <filter-name>characterEncodingFilter</filter-name>
       <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
       <init-param>
         <param-name>encoding</param-name>
         <param-value>UTF-8</param-value>
       </init-param>
     </filter>
     <filter-mapping>
       <filter-name>characterEncodingFilter</filter-name>
       <url-pattern>/*</url-pattern>
     </filter-mapping>
   </web-app>
   ~~~

   

2. ### springmvc.xml

   ~~~xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:context="http://www.springframework.org/schema/context"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:mvc="http://www.springframework.org/schema/mvc"
          xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                               http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
   http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd">
   
       <!--处理器适配器、处理器映射器、视图解析器是SpringMVC的三大组件，当配置这句话的时候自动加载处理器适配器与处理器映射器-->
       <!--开启springmvc注解支持，-->
       <mvc:annotation-driven/>
   
       <!--开启spring注解扫描-->
       <context:component-scan base-package="com.victor"/>
   
       <!--视图解析器-->
       <bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
           <property name="prefix" value="/WEB-INF/"/>
           <property name="suffix" value=".jsp"/>
       </bean>
       
       <!--配置不拦截js的请求-->
       <mvc:resources mapping="/js/**" resource="/js/**"/>
   
   </beans>
   ~~~

3. ### Controller

   ~~~java
   @Controller
   public class UserControlledr {
   
       @RequestMapping("/hello")
       public String hello(Model model){
           model.addAttribute("msg","xxxx");//添加到request域中
           return "success";
       }
   }
   ~~~



### DispatcherServlet

前端控制器：当用户的请求到达前端控制器，它就相当于整个mvc模式中的整个流程的控制中心，由它调用其他组件来处理用户的请求，它的存在降低了程序的耦合性



### HandlerMapping

处理器映射器：负责根据用户请求找到Handler即处理器，根据用户的请求返回相应的类和类中的方法



### HandlerAdapter

通过HandlerAdapter对处理器进行执行，这是适配器模式的应用，通过扩展适配器可以对更多的处理器进行执行



### Handler

开发中需要编写的具体业务控制器，对用户的具体请求进行处理，返回modelandview



### ViewReslover

视图解析器：对modelandview进行解析，返回某某路径下的某某页面，返回jsp页面



## 常用注解

1. **@RequestParam绑定单个请求参数值**
2. **@PathVariable绑定URI模板变量值，适用于rest风格**
3. **@Responsebody将数据输出到页面**
4. **@RequestBody可以获得请求体**

如果Controller类中的方法有void修饰的，没有返回值，默认去寻找视图解析器解析requestmapping中的路径



## 转发与重定向

转发：

~~~java
return "forward:/WEB-INF/index.jsp";
或
request.getRequestDispacther("/WEB-INF/index.jsp").forward(request,response);
~~~

重定向：

~~~java
return "redirect:/index.jsp";//mvc处理了，所以不需要request.getContextPath
或
response.sendRedirect(request.getContextPath+"/index.jsp");
~~~

如果SpringMVC要对json字符串操作需要导jackson的相关依赖

## ModelAndView



## 拦截器

过滤器：servlet的一部分，任何javaweb工程都可以使用，配置/*后对所有资源都进行拦截

拦截器：只有springMVC有拦截器技术，普通servlet是没有的，只会拦截访问的Controller方法，静态资源是不会拦截的

### 实现HandlerInterceptor

~~~java
public class InterceptorTest implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("Controller方法执行之前执行");
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("Controller方法执行之后执行，返回的jsp页面语句执行之前执行");
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("最后执行");
    }
}

~~~



### 配置

~~~xml
<mvc:interceptors>
    <mvc:interceptor>
        <mvc:mapping path="/user/**"/>
        <bean class="com.victor.interceptor.InterceptorTest"/>
        <!--不拦截的路径
            <mvc:exclude-mapping path=""/>-->
    </mvc:interceptor>
</mvc:interceptors>
~~~



# SSM整合

## POM

~~~xml
<dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>compile</scope>
    </dependency>
    <!--    spring   + springwebmvc-->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>5.2.2.RELEASE</version>
    </dependency>

    <!--    mybatis-->
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis-spring</artifactId>
      <version>2.0.3</version>
    </dependency>

    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.5.2</version>
    </dependency>

    <!--    JDBC-->
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>5.1.44</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-jdbc</artifactId>
      <version>5.2.2.RELEASE</version>
    </dependency>

    <!--事务管理-->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-tx</artifactId>
      <version>5.2.2.RELEASE</version>
    </dependency>


    <!--AspectJ-->

    <dependency>
      <groupId>org.aspectj</groupId>
      <artifactId>aspectjweaver</artifactId>
      <version>1.9.6</version>
    </dependency>


    <!--    jstl   jsp  servlet-->
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>jstl</artifactId>
      <version>1.2</version>
    </dependency>

    <dependency>
      <groupId>javax.servlet.jsp</groupId>
      <artifactId>javax.servlet.jsp-api</artifactId>
      <version>2.3.3</version>
      <scope>provided</scope>
    </dependency>

    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>4.0.1</version>
      <scope>provided</scope>
    </dependency>

    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-core</artifactId>
      <version>2.9.5</version>
    </dependency>

    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>2.9.5</version>
    </dependency>

  </dependencies>


<build>
    <resources>
      <resource>
        <directory>src/main/java</directory>
        <includes>
          <include>**/*.properties</include>
          <include>**/*.xml</include>
        </includes>
        <filtering>  </filtering>
      </resource>
      <resource>
        <directory>src/main/resources</directory>
        <includes>
          <include>**/*.properties</include>
          <include>**/*.xml</include>
        </includes>
        <filtering>false</filtering>
      </resource>
    </resources>
</build>
~~~



## 1.首先整合spring框架

开启spring的注解扫描

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd">

    <!--开启注解扫描，只扫描业务层与持久层，不要理Controller，它交给mvc处理-- >
    <context:component-scan base-package="com.victor">
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>


</beans>
~~~

注解注入

~~~java
@Service("userService")
public class UserServiceImpl implements UserService {
    @Override
    public void getAllUser() {
        System.out.println("业务层：获取所有用户");
    }
}
~~~

测试，看能否拿到注入的值

~~~java
public class TestSpring {

    @Test
    public void test(){
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
        UserService userService = applicationContext.getBean("userService", UserService.class);
        userService.getAllUser();
    }
}
~~~

## 2.测试SpringMVC

配置web.xml

~~~xml
<servlet>
    <servlet-name>dispatcherServlet</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:springmvc.xml</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
</servlet>
<servlet-mapping>
    <servlet-name>dispatcherServlet</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>

<filter>
    <filter-name>characterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>characterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
~~~

配置springmvc.xml，且注解扫描只扫描Controller

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/mvc
       https://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <!--注解支持-->
    <mvc:annotation-driven/>

    <!--扫描注解-->
    <context:component-scan base-package="com.victor">
        <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>

    <!--视图解析器-->
    <bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/pages/"/>
        <property name="suffix" value=".jsp"/>
    </bean>

    <!--静态资源过滤-->
    <mvc:resources mapping="/js/**" location="/js/**"/>
</beans>
~~~

编写测试Controller层，配置Tomcat服务器，对SpringMVC进行测试

~~~java
@Controller
@RequestMapping("/user")
public class UserController {

    @RequestMapping("/getAll")
    public String getUser(){
        System.out.println("表现层：获取所有用户");
        return "success";
    }
}
~~~

## 3.Spring对SpringMVC进行整合

分析：在web工程启动时并没有加载spring的applicationContext.xml配置文件，要想整合就需要加载，所以对ServletContext进行监听，当他一创建就加载Spring的applicationContext.xml，在web.xml中加入监听器

~~~xml
<!--配置Spring的监听器，默认只加载WEB-INF下的applicationContext.xml-->
<listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>
<context-param>
    <!--设置加载的路径-->
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:applicationContext.xml</param-value>
</context-param>
~~~

这时就可以在Controller层对业务层Service进行注入测试

~~~java
@Controller
@RequestMapping("/user")
public class UserController {

    @Autowired
    private UserService userService;

    @RequestMapping("/getAll")
    public String getUser(){
        System.out.println("表现层：获取所有用户");
        userService.getAllUser();
        return "success";
    }
}
~~~

## 4.编写单独的Mybatis

首先编写Mybatis的配置文件mybatis-config.xml

~~~xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>
    <properties resource="jdbc.properties"/>

    <environments default="mysql">
        <environment id="mysql">
            <transactionManager type="JDBC"></transactionManager>
            <dataSource type="POOLED">
                <property name="driver" value="${jdbc.driver}"/>
                <property name="url" value="${jdbc.url}"/>
                <property name="username" value="${jdbc.username}"/>
                <property name="password" value="${jdbc.password}"/>
            </dataSource>
        </environment>
    </environments>

    <mappers>
        <mapper resource="com/victor/mapper/UserMapper.xml"/>
    </mappers>
</configuration>
~~~

连接的配置jdbc.properties

~~~properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/test01
jdbc.username=root
jdbc.password=123456
~~~

对应UserMapper的映射配置文件，这个文件要在resource下且包路径与UserMapper所在的路径一致

~~~xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.victor.mapper.UserMapper">
    <select id="getAllUser" resultType="com.victor.pojo.User">
        select * from user
    </select>
</mapper>
~~~

对mybatis进行测试

~~~java
@Test
    public void testMybatis() throws IOException {
        InputStream resourceAsStream = Resources.getResourceAsStream("mybatis-config.xml");
        SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
        SqlSessionFactory build = sqlSessionFactoryBuilder.build(resourceAsStream);
        SqlSession session = build.openSession();
        UserMapper mapper = session.getMapper(UserMapper.class);
        System.out.println(mapper.getAllUser());
        session.close();
        resourceAsStream.close();
    }
~~~

## 5.Spring与Mybatis整合

首先配置数据源、SqlSessionFactorBean、MapperScannerConfigurer

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd">

    <context:component-scan base-package="com.victor">
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>

    <!--配置数据源-->
    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/test01"/>
        <property name="username" value="root"/>
        <property name="password" value="123456"/>
    </bean>

    <bean id="factory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <!--配置mapper包-->
    <bean id="mapperScannerConfigurer" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.victor.mapper"/>
    </bean>
</beans>
~~~

在UserMapper上面加注解，测试通过

~~~java
@Repository
public interface UserMapper {

    List<User> getAllUser();
}
~~~

最后进行事务管理配置，顺便把数据源配置放到配置文件中

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/tx
       http://www.springframework.org/schema/tx/spring-tx.xsd
       http://www.springframework.org/schema/aop
       https://www.springframework.org/schema/aop/spring-aop.xsd">

    <context:property-placeholder location="classpath:jdbc.properties"/>

    <context:component-scan base-package="com.victor">
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>

    <!--配置数据源-->
    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="${jdbc.driver}"/>
        <property name="url" value="${jdbc.url}"/>
        <property name="username" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>

    <bean id="factory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <!--配置mapper包-->
    <bean id="mapperScannerConfigurer" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.victor.mapper"/>
    </bean>

    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <!--配置事务通知-->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <tx:attributes>
            <tx:method name="get*" read-only="true"/>
            <tx:method name="*" isolation="DEFAULT"/>
        </tx:attributes>
    </tx:advice>

    <!--配置AOP增强-->
    <aop:config>
        <aop:advisor advice-ref="txAdvice" pointcut="execution(* com.victor.service.impl.*.*(..))"/>
    </aop:config>
</beans>
~~~

大功告成

具体查看/home/victor/projects/JavaProjects/java_review2/ssm2















