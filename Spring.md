#	Spring-learn

[spring官方文档][https://docs.spring.io/spring/docs/5.2.5.RELEASE/spring-framework-reference/core.html#beans]

### 常用依赖

~~~xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.2.0.RELEASE</version>
</dependency>
~~~



###	1、第一个spring程序

* 首先创建applicationContext.xml文件

* ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.springframework.org/schema/beans
          https://www.springframework.org/schema/beans/spring-beans.xsd">
  
  </beans>
  ```

* 创建一个实体类（生成set、get方法）依赖注入依靠set方法，无参构造

* ```java
  public class Hello {
      private String str;
  }
  ```

* 在applicationContext.xml文件中配置

* ```xml
  <bean class="com.victor.pojo.Hello" id="hello">
  	<property name="str" value="hello Spring"/>
  </bean>
  ```

 * 编写测试类

 * ```java
   ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
   Hello hello = (Hello) context.getBean("hello");
   System.out.println(hello.getStr());
   ```

	*	测试结果：hello spring



###	2、不同类型的依赖注入方式

1. 实体类如下

   ```java
   public class People {
       private String name;
       private Address address;
       private String[] books;
       private List<String> hobbys;
       private Map<String,String> card;
       private Set<String> games;
       private String wife;
       private Properties info;
   }
   ```

2. 配置相应的xml文件（其中地址为另一个实体类，需要用先注入再ref引用）

   ~~~xml
   <bean id="address" class="com.victor.pojo.Address">
       <property name="address" value="云南省"/>
   </bean>
   
   <bean class="com.victor.pojo.People" id="people">
       <property name="name" value="victor"/>
       <property name="address" ref="address"/>
   
       <!--数组注入-->
       <property name="books">
           <array>
               <value>西游记</value>
               <value>三国演义</value>
               <value>水浒传</value>
               <value>红楼梦</value>
           </array>
       </property>
   
       <!--list集合注入-->
       <property name="hobbys">
           <list>
               <value>敲代码</value>
               <value>打篮球</value>
           </list>
       </property>
   
       <!--map注入-->
       <property name="card">
           <map>
               <entry key="身份证" value="123"/>
               <entry key="银行卡" value="1223"/>
           </map>
       </property>
   
       <!--set注入-->
       <property name="games">
           <set>
               <value>拳皇</value>
               <value>反恐</value>
           </set>
       </property>
   
       <!--null注入-->
       <property name="wife">
           <null/>
       </property>
   
   
       <!--properties注入-->
       <property name="info">
           <props>
               <prop key="学号">172054136</prop>
               <prop key="成绩">750</prop>
           </props>
       </property>
   </bean>
   ~~~

3. 编写测试类

   ```java
   ApplicationContext context = new ClassPathXmlApplicationContext("beans2.xml");
   People people = (People) context.getBean("people");
   System.out.println(people);
   ```

4. 测试结果：

   ```java
   People{
       name='victor',
       address=Address{address='云南省'},
       books=[西游记, 三国演义, 水浒传, 红楼梦],
       hobbys=[敲代码, 打篮球],
       card={身份证=123, 银行卡=1223},
       games=[拳皇, 反恐],
       wife='null',
       info={学号=172054136, 成绩=750}
   }
   ```

   * 无参注入

     ```xml
     <bean class="com.victor.pojo.Hello" id="hello">
         <property name="str" value="hello Spring"/>
     </bean>
     ```

   * 有参注入（可以根据类型、下标、名称）

     ```xml
     <bean class="com.victor.pojo.Student" id="student">
         <constructor-arg name="id" value="172054136"/>
         <constructor-arg index="1" value="victor"/>
     </bean>
     
     ```

     

   * p标签代替无参（先引入名称空间）

     ~~~xml
     xmlns:p="http://www.springframework.org/schema/p"
     ~~~

     ~~~xml
     <bean class="com.victor.pojo.Hello" id="hello" p:str="hello spring oooooo"/>
     ~~~

     

   * c标签代替有参（先引入名称空间）

     ~~~xml
     xmlns:c="http://www.springframework.org/schema/c"
     ~~~

     ~~~xml
     <bean class="com.victor.pojo.Student" id="student" c:id="172054136" c:_1="victor"/>
     ~~~

### 3、bean的作用域

![image-20200409183909909](C:\Users\Victor\AppData\Roaming\Typora\typora-user-images\image-20200409183909909.png)

1. 单例模式（spring默认）-->一般单线程使用

   ~~~xml
   <bean id="accountService" class="com.something.DefaultAccountService" scope="singleton"/>
   ~~~

   

2. 原型模式（每次从容器中get的时候，都会产生一个新对象）-->一般多线程使用

   ~~~xml
   <bean id="accountService" class="com.something.DefaultAccountService" scope="prototype"/>
   ~~~

3. 其余在web开发中使用到 



### 4、bean的自动装配

* 自动装配是Spring满足bean依赖的一种方式
* Spring会在上下文中自动寻找，并自动给bean装配属性



在Spring中有三种装配的方式

1. 在xml中显示的配置
2. 在Java中显示配置
3. 隐式的自动装配（重要）



* **autowire="byName"：需要id和set方法后的名字一样并且唯一才能完成自动装配**

  ~~~xml
  <bean class="com.victor.pojo.Cat" id="cat"/>
  <bean class="com.victor.pojo.Dog" id="dog"/>
  <bean id="person" class="com.victor.pojo.Person" autowire="byName">
      <property name="name" value="victor"/>
  </bean>
  ~~~

* **autowire="byType"：保证class（类）唯一，可以省略id，但是类型必须一致**

  ~~~xml
  <bean class="com.victor.pojo.Cat"/>
  <bean class="com.victor.pojo.Dog"/>
  <bean id="person" class="com.victor.pojo.Person" autowire="byType">
      <property name="name" value="victor"/>
  </bean>
  ~~~

* **注解实现自动装配**

  1. 导入约束

     ~~~xml
     <?xml version="1.0" encoding="UTF-8"?>
     <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns:context="http://www.springframework.org/schema/context"
         xsi:schemaLocation="http://www.springframework.org/schema/beans
             https://www.springframework.org/schema/beans/spring-beans.xsd
             http://www.springframework.org/schema/context
             https://www.springframework.org/schema/context/spring-context.xsd">
     
         <context:annotation-config/>
     
     </beans>
     ~~~

  2. ==配置注解的支持：<context:annotation-config/>==

  3. @Autowired

     ~~~xml
     <bean id="cat" class="com.victor.pojo.Cat"/>
     <bean id="dog" class="com.victor.pojo.Dog"/>
     <bean id="person" class="com.victor.pojo.Person"/>
     ~~~
     
     ~~~java
     @Autowired
     private Cat cat;
     @Autowired
     private Dog dog;
     ~~~
     
     注：使用注解可以省略set方法，注解使用的是反射，
     
     
     
     **当有多个同类型的时，@Qualifier可以指定某个具体的**
     
     ~~~xml
     <bean id="dog11" class="com.victor.pojo.Dog"/>
     <bean id="dog112" class="com.victor.pojo.Dog"/>
     ~~~
     
     ~~~java
     @Autowired
     @Qualifier(value = "dog11")
     private Dog dog;
     ~~~
     
     
     
     **@Resource功能更强大一些，首先按名字找，其次按类型寻找**
     
     ~~~xml
     <bean id="cat11" class="com.victor.pojo.Cat"/>
     <bean id="cat22" class="com.victor.pojo.Cat"/>
     ~~~
     
     ~~~java
     @Resource(name = "cat11")
     private Cat cat;
     ~~~
     
     **@Autowired 与 @Resource区别：**
     
     @Autowired：先按类型查找，当有多个类型时，按名字
     
     @Resource：先按名字查找，当找不到名字时，按类型
     
     



### 5、使用注解开发

***若注解不起作用检查aop包是否导入**

1. 使用注解需要导入约束

   ~~~xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
           https://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/context
           https://www.springframework.org/schema/context/spring-context.xsd">
   
       <context:annotation-config/>
   
   </beans>
   ~~~

2. ==配置注解的支持：<context:annotation-config/>==



##### 属性注入

~~~xml
 <context:component-scan base-package="com.victor"/>		扫描包
~~~

~~~java
@Component
public class User {
    @Value("victor")
    private String name;
    public String getName() {
        return name;
    }
}
~~~



##### @Component	衍生出的注解，web开发中，会按mvc三层架构分层

| 包         |     注解     |
| ---------- | :----------: |
| pojo       |  @Component  |
| dao        | @Repository  |
| service    |   @Service   |
| controller | @Controller  |
|            | 功能全部一样 |



##### 无xml注解开发（使用Java方式注解开发）

* 编写配置类

  ~~~java
  @Configuration
  public class VictorConfig {
  
      @Bean
      public User getUser(){
          return new User();
      }
  }
  ~~~

* 实体类

  ~~~Java
  @Component
  public class User {
  
      @Value("victor")
      private String name;
  
      public String getName() {
          return name;
      }
  }
  ~~~

* ~~~java
  ApplicationContext context = new AnnotationConfigApplicationContext(VictorConfig.class)
  ~~~





### 6、代理模式

1. 静态代理

2. 动态代理

   ~~~java
   public class ProxyInvocationHandler implements InvocationHandler {
   
       private Object target;
   
       public void setTarget(Object target) {
           this.target = target;
       }
   
       public Object getProxy() {
           return Proxy.newProxyInstance(this.getClass().getClassLoader(), target.getClass().getInterfaces(), this);
       }
   
       @Override
       public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
           log(method.getName());
           return method.invoke(target, args);
       }
   
       public void log(String msg){
           System.out.println("调用了"+msg);
       }
   }
   ~~~

   ~~~java
   public class Client {
       public static void main(String[] args) {
           UserServiceImpl userService = new UserServiceImpl();
           Host host = new Host();
   
           ProxyInvocationHandler pih = new ProxyInvocationHandler();
           pih.setTarget(host);
           Rent proxy = (Rent) pih.getProxy();
           proxy.rent();
       }
   }
   ~~~

   ~~~java
   public interface UserService {
       public void add();
       public void del();
       public void update();
       public void query();
   }
   ~~~

   ~~~java
   public class UserServiceImpl implements UserService {
       @Override
       public void add() {
           System.out.println("添加了用户");
       }
   
       @Override
       public void del() {
           System.out.println("删除了用户");
       }
   
       @Override
       public void update() {
           System.out.println("修改了用户");
       }
   
       @Override
       public void query() {
           System.out.println("查询了用户");
       }
   }
   ~~~

   



### 7、AOP





