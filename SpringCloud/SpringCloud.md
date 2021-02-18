# SpringCloud

## 1.什么是微服务架构

> 微服务架构是一种架构模式，它提倡将单一应用程序划分成一组小的服务，服务之间互相协调，互相配合，为用户提供最终价值。每个服务运行在其独立的进程中，服务与服务间采取轻量级的通信机制互相协作（通常是基于HTTP协议的RESTfulAPI）。每个服务都围绕着具体业务进行构建，并且能够被独立的部署到生产环境，类生产环境等。另外，应当避免统一的，集中式的服务管理机制，对具体的一个服务而言，应根据业务上下文，选择合适的语言，工具对其进行构建。

## 2.技术组件

- 服务注册与发现
- 服务调用
- 服务熔断
- 负载均衡
- 服务降级
- 服务消息队列
- 配置中心管理
- 服务网关
- 服务监控
- 全链路追踪
- 自动化构架部署
- 服务定时任务调度操作

## 3.SpringBoot与SpringCloud的对应版本

![](.\img\Snipaste_2021-02-05_13-55-03.png)

### 4.开发环境

![](C:\Users\victor\learn\学习资料\SpringCloud\img\Snipaste_2021-02-05_13-55-57.png)

## 5.cloud组件目前的状态

![](C:\Users\victor\learn\学习资料\SpringCloud\img\Snipaste_2021-02-05_13-56-14.png)

## 6.事例

### （1）创建父工程

#### 	1.选择Maven创建

​		![](.\img\Snipaste_2021-02-05_14-15-47.png)

#### 	2.填写项目名称，选择maven路径，创建，修改编码，设置注解有效

​		![](.\img\20210122211759380.png)

#### 	3.Pom文件引入依赖

~~~xml
<packaging>pom</packaging>

<!--统一管理jar包和版本-->
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
    <junit.version>4.12</junit.version>
    <log4j.version>1.2.17</log4j.version>
    <lombok.version>1.16.18</lombok.version>
    <mysql.version>5.1.47</mysql.version>
    <druid.verison>1.1.16</druid.verison>
    <mybatis.spring.boot.verison>1.3.0</mybatis.spring.boot.verison>
</properties>

<dependencyManagement>
    <dependencies>
        <!--spring boot 2.2.2-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-dependencies</artifactId>
            <version>2.2.2.RELEASE</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
        <!--spring cloud Hoxton.SR1-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>Hoxton.SR1</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
        <!--spring cloud alibaba 2.1.0.RELEASE-->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-alibaba-dependencies</artifactId>
            <version>2.2.0.RELEASE</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
        <!-- MySql -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>${mysql.version}</version>
        </dependency>
        <!-- Druid -->
        <!-- https://mvnrepository.com/artifact/com.alibaba/druid-spring-boot-starter -->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-starter</artifactId>
            <version>1.1.13</version>
        </dependency>

        <!-- mybatis-springboot整合 -->
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>${mybatis.spring.boot.verison}</version>
        </dependency>
        <!--lombok-->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>${lombok.version}</version>
        </dependency>
        <!--junit-->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>${junit.version}</version>
        </dependency>
        <!-- log4j -->
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>${log4j.version}</version>
        </dependency>
    </dependencies>
</dependencyManagement>

<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <configuration>
                <fork>true</fork>
                <addResources>true</addResources>
            </configuration>
        </plugin>
    </plugins>
</build>
~~~

####   4.dependencyManagement

> - Maven使用dependencyManagement元素来提供了一种管理依赖版本号的方式，通常会在一个项目的最顶层的父pom中看到。
> - 使用pom.xml 中的dependencyManagement元素能让所有在子项目中引用一个依赖而不用显式的列出版本号。 Maven会沿着父子层次向上走，直到找到一个拥有dependencyManagement元素的项目，然后它就会使用这个dependencyManagement元素中指定的版本号。
> - 这样做的好处就是:如果有多个子项目都引用同一样依赖，则可以避免在每个使用的子项目里都声明一个版本号，这样当想升级或切换到另一个版本时，只需要在顶层父容器里更新，而不需要一个一个子项目的修改﹔另外如果某个子项目需要另外的一个版本，只需要声明version就可以。
> - **dependencyManagement里只是声明依赖，并不实现引入，因此子项目需要显示的声明需要用的依赖。**
> - **如果不在子项目中声明依赖，是不会从父项目中继承下来的;只有在子项目中写了该依赖项，并且没有指定具体版本，才会从父项目中继承该项，并且version和scope都读取自父pom;**
> - **如果子项目中指定了版本号，那么会使用子项目中指定的jar版本。**

####   5.maven跳过单元测试

​	![](.\img\20210123122002793.png)

####  	6.测试maven与idea是否整合成功，buildSeccess则成功

​	![](.\img\Snipaste_2021-02-05_14-25-15.png)

### (2)支付模块编写

#### 1.创建module

![](.\img\Snipaste_2021-02-05_14-28-39.png)

#### 2.引入pom依赖

~~~xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    <dependency>
        <groupId>org.mybatis.spring.boot</groupId>
        <artifactId>mybatis-spring-boot-starter</artifactId>
    </dependency>
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid-spring-boot-starter</artifactId>
    </dependency>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-jdbc</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <scope>runtime</scope>
        <optional>true</optional>
    </dependency>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <optional>true</optional>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <scope>runtime</scope>
        <optional>true</optional>
    </dependency>
    <dependency>
        <groupId>com.victor</groupId>
        <artifactId>cloud-common</artifactId>
        <version>1.0-SNAPSHOT</version>
    </dependency>
</dependencies>
~~~

#### 3.application.yaml

~~~yaml
server:
  port: 8001

spring:
  application:
    name: cloud-payment-service
  datasource:
    #当前数据源操作类型
    type: com.alibaba.druid.pool.DruidDataSource
    #mysql驱动包
    driver-class-name: org.gjt.mm.mysql.Driver
    url: jdbc:mysql://127.0.0.1:3306/test?useUnicode=true&characterEncoding=utf8&useSSL=false
    username: root
    password: 123456

mybatis:
  mapperLocations: classpath:mapper/*.xml
  #所有entity别名类所在包
  type-aliases-package: com.victor.entities
~~~

#### 4.主启动类（放在项目包下）

~~~java
@SpringBootApplication
public class PaymentMain8001 {

    public static void main(String[] args) {
        SpringApplication.run(PaymentMain8001.class,args);
    }
}
~~~

#### 5.实体类(lombok注解无法生成泛型的全参构造)

> ```java
> @Data
> @AllArgsConstructor
> @NoArgsConstructor
> public class Payment {
>     private Integer id;
>     private String name;
> }
> ```

> ```java
> @Data
> @NoArgsConstructor
> public class CommonResult<T> {
> 
>     private Integer code;
>     private String message;
>     private T data;
> 
>     public CommonResult(Integer code, String message, T data) {
>         this.code = code;
>         this.message = message;
>         this.data = data;
>     }
> }
> ```

#### 6.基础的dao、mapper.xml、service、serviceImpl、controller

> ```java
> @Mapper
> @Repository
> public interface PaymentDao {
>     int createPayment(Payment payment);
> 
>     Payment getPaymentById(Integer id);
> }
> ```

> ```xml
> <?xml version="1.0" encoding="UTF-8" ?>
> <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
>         "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
> <mapper namespace="com.victor.dao.PaymentDao">
> 
>     <insert id="createPayment" parameterType="com.victor.entity.Payment" useGeneratedKeys="true" keyProperty="id">
>         insert into payment (name) values (#{name});
>     </insert>
> 
>     <select id="getPaymentById" parameterType="Integer" resultType="com.victor.entity.Payment">
>         select * from payment where id = #{id};
>     </select>
> </mapper>
> ```

> ```java
> public interface PaymentService {
>     int createPayment(Payment payment);
> 
>     Payment getPaymentById(Integer id);
> }
> ```

> ```java
> @Service
> public class PaymentServiceImpl implements PaymentService {
> 
>     @Autowired
>     private PaymentDao paymentDao;
> 
>     @Override
>     public int createPayment(Payment payment) {
>         return paymentDao.createPayment(payment);
>     }
> 
>     @Override
>     public Payment getPaymentById(Integer id) {
>         return paymentDao.getPaymentById(id);
>     }
> }
> ```

> ```java
> @RestController
> public class PaymentController {
> 
>     @Autowired
>     private PaymentService paymentService;
> 
>     @PostMapping(value = "/payment/create")
>     public CommonResult create(Payment payment){
>         int i = paymentService.createPayment(payment);
> 
>         if(i>0){
>             return new CommonResult(200,"插入数据库成功",i);
>         }else{
>             return new CommonResult(444,"插入数据库失败",null);
>         }
>     }
> 
>     @GetMapping(value = "/payment/get/{id}")
>     public CommonResult getPaymentById(@PathVariable("id") Integer id){
>         Payment paymentById = paymentService.getPaymentById(id);
>         if(paymentById!=null){
>             return new CommonResult(200,"查询成功",paymentById);
>         }else{
>             return new CommonResult(444,"没有对应记录",null);
>         }
>     }
> }
> ```

#### 7.启动测试

#### 8.设置热更新

##### （1）子模块引入pom依赖

~~~xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <scope>runtime</scope>
    <optional>true</optional>
</dependency>
~~~

##### （2）父工程加入如下配置

~~~xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <configuration>
                <fork>true</fork>
                <addResources>true</addResources>
            </configuration>
        </plugin>
    </plugins>
</build>
~~~

##### （3）配置IDEA(ctrl + shift + Alt + /)

![](.\img\Snipaste_2021-02-05_14-42-22.png)

![](C:\Users\victor\learn\学习资料\SpringCloud\img\Snipaste_2021-02-05_14-45-04.png)

![](C:\Users\victor\learn\学习资料\SpringCloud\img\Snipaste_2021-02-05_14-45-31.png)

#### 9.重启IDEA，配置完成生效

### (3)订单模块编写

#### 1.创建module

![](.\img\Snipaste_2021-02-05_14-55-27.png)

#### 2.引入依赖

~~~xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <scope>runtime</scope>
        <optional>true</optional>
    </dependency>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <optional>true</optional>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>com.victor</groupId>
        <artifactId>cloud-common</artifactId>
        <version>1.0-SNAPSHOT</version>
    </dependency>
</dependencies>
~~~

#### 3.编写实体类与订单模块Entity包一致，复制即可

#### 4.RestTemplate配置

~~~java
@Configuration
public class ApplicationContextConfig {

    @Bean
    public RestTemplate getRestTemplate(){
        return new RestTemplate();
    }
}
~~~

#### 5.编写Controller

~~~java
@RestController
@Slf4j
public class ConsumerController {

    public static final String str = "http://localhost:8001/";

    @Autowired
    private RestTemplate restTemplate;

    @GetMapping("/consumer/payment/get/{id}")
    public CommonResult getPayment(@PathVariable("id") Integer id) {
        return restTemplate.getForObject(str + "payment/get/" + id.toString(), CommonResult.class);
    }

    @PostMapping("/consumer/payment/create")
    public CommonResult createPayment(Payment payment) {
        System.out.println(payment);
        return restTemplate.postForObject(str + "/payment/create", payment, CommonResult.class);
    }
}
~~~

#### 6.application.yaml

~~~yaml
server:
  port: 8080
~~~

#### 7.启动类

~~~~java

@SpringBootApplication
public class ConsumerOrderMain80 {
    public static void main(String[] args) {
        SpringApplication.run(ConsumerOrderMain80.class, args);
    }
}
~~~~

#### 8.测试出错

> 在PaymentController中添加方法需要更改，加入@RequestBody注解方可成功
>
> ~~~java
> @PostMapping(value = "/payment/create")
> public CommonResult create(@RequestBody Payment payment){
>     int i = paymentService.createPayment(payment);
> 
>     if(i>0){
>         return new CommonResult(200,"插入数据库成功",i);
>     }else{
>         return new CommonResult(444,"插入数据库失败",null);
>     }
> }
> ~~~

#### 9.抽离公共部分

##### （1）创建新的module

![](.\img\Snipaste_2021-02-05_15-03-43.png)

##### （2）抽出Entity包，并执行maven的clean方法

##### （3）在使用这个Entity的module的pom文件中引入此module

~~~xml
<dependency>
    <groupId>com.victor</groupId>
    <artifactId>cloud-common</artifactId>
    <version>1.0-SNAPSHOT</version>
</dependency>
~~~



### (4)Eureka构建步骤（注册中心）

#### 1.创建module

![](.\img\Snipaste_2021-02-05_15-56-08.png)

#### 2.引入pom依赖

~~~xml
<dependencies>
    <!--eureka-server-->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
    </dependency>
    <dependency>
        <groupId>com.victor</groupId>
        <artifactId>cloud-common</artifactId>
        <version>1.0-SNAPSHOT</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    <!--一般为通用配置-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <scope>runtime</scope>
        <optional>true</optional>
    </dependency>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <optional>true</optional>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
~~~

#### 3.application.yaml

~~~yaml
server:
  port: 7001

eureka:
  instance:
    hostname: localhost  #eureka服务端的实例名字
  client:
    register-with-eureka: false    #表识不向注册中心注册自己
    fetch-registry: false   #表示自己就是注册中心，职责是维护服务实例，并不需要去检索服务
    service-url:
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/    #设置与eureka server交互的地址查询服务和注册服务都需要依赖这个地址
~~~

#### 4.启动类

~~~java
@SpringBootApplication
@EnableEurekaServer     //Eureka的自动服务端注解
public class EurekaMain7001 {
    public static void main(String[] args) {
        SpringApplication.run(EurekaMain7001.class, args);
    }
}
~~~

#### 5.访问127.0.0.1:7001，测试

![](.\img\Snipaste_2021-02-05_16-01-06.png)

#### 6.将支付模块添加进注册中心

##### (1)在需要添加进注册中心的模块（支付模块）添加pom依赖

~~~xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
~~~

##### (2)在需要添加进注册中心的模块（支付模块）添加yaml

~~~yaml
eureka:
  client:
    register-with-eureka: true #是否将自己注册到注册中心，默认true
    fetchRegistry: true #是否从注册中心抓取已有的注册信息，默认true。单节点无所谓，集群必须设置为true才能配合ribbon使用负载均衡
    service-url:
      defaultZone: http://localhost:7001/eureka
~~~

##### (3)在需要添加进注册中心的模块（支付模块）启动类添加注解

> @EnableEurekaClient

##### (4)重启，继续访问127.0.0.1:7001

![](.\img\Snipaste_2021-02-05_16-12-05.png)

#### 7.将订单模块同理加入注册中心

![](.\img\Snipaste_2021-02-05_16-19-32.png)

### (5)OpenFeign的使用（服务调用）

#### 1.pom依赖

~~~xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-openfeign-core</artifactId>
</dependency>
或
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
~~~

#### 2.主启动类加上注解

> ```
> @EnableFeignClients
> ```

#### 3.编写接口调用Eureka中的服务方法

~~~java
@Service
@FeignClient(value = "CLOUD-PAYMENT-SERVICE")//服务名必须与Eureka中注册的一致
public interface PaymentService {

    @GetMapping(value = "/payment/get/{id}")
    public CommonResult getPaymentById(@PathVariable("id") Integer id);//方法必须一模一样，参数与参数注解也必须一致
}
~~~

#### 4.controller中注入此接口，调用

~~~java
@RestController
public class ConsumerController {

    @Autowired
    private PaymentService paymentService;

    @GetMapping("/consumer/payment/get/{id}")
    public CommonResult getPayment(@PathVariable("id") Integer id) {
        return paymentService.getPaymentById(id);
    }
}

~~~

#### 5.application.yaml不变

~~~yaml
server:
  port: 8080

spring:
  application:
    name: cloud-order-service


eureka:
  client:
    register-with-eureka: false #是否将自己注册到注册中心，默认true
    fetchRegistry: true #是否从注册中心抓取已有的注册信息，默认true。单节点无所谓，集群必须设置为true才能配合ribbon使用负载均衡
    service-url:
      defaultZone: http://localhost:7001/eureka
~~~

#### 6.测试接口

#### 7.测试超时

> 在8001服务端controller中编写方法
>
> ```java
> @GetMapping("/payment/feign/timeout/{port}")
> public Integer getServerPort(@PathVariable("port") Integer port) {
>     try {
>         TimeUnit.SECONDS.sleep(3);//故意停止3秒钟
>     } catch (InterruptedException e) {
>         e.printStackTrace();
>     }
>     return port;
> }
> ```

> 在客户端8080端口在service接口中调用8001中的方法
>
> ```java
> @Service
> @FeignClient(value = "CLOUD-PAYMENT-SERVICE")
> public interface PaymentService {
> 
>     @GetMapping(value = "/payment/get/{id}")
>     public CommonResult getPaymentById(@PathVariable("id") Integer id);
> 
>     @GetMapping("/payment/feign/timeout/{port}")
>     public Integer getServerPort(@PathVariable("port") Integer port);
> }
> ```

> 在客户端controller中调用service中的方法
>
> ```java
> @GetMapping("/consumer/payment/feign/timeout")
> public Integer getServerPort(HttpServletRequest servlet) {
>     int port = servlet.getServerPort();
>     return paymentService.getServerPort(port);
> }
> ```

> feign默认1秒，而故意暂停了3秒，超时页面出现
>
> ![](.\img\Snipaste_2021-02-06_10-10-55.png)

> 客户端的application.yaml进行配置超时时间
>
> ```yml
> #设置feign客户端超时时间（openFeign默认支持ribbon）
> ribbon:
>   ReadTimeout: 5000 #建立连接所用的时间，适用于网络状态良好的情况下，两端连接所用的时间
>   ConnecTimeout: 5000 #建立连接后从服务端取到数据所用的时间
> ```

> 测试成功，返回信息

#### 8.日志功能

> 在8080客户端配置类中加入配置
>
> ```java
> @Bean
> Logger.Level feignLoggerLevel() {
>     return Logger.Level.FULL;
> }
> ```

> 在application.yaml中加入配置
>
> ```yaml
> logging:
>   level:
>     #feign日志以什么级别监听哪个包
>     com.victor: debug
> ```

> 效果
>
> ![](.\img\Snipaste_2021-02-06_10-55-09.png)

### (6)GateWay的使用（服务网关）

#### 1.创建module

#### 2.引入POM依赖(不需要spring-boot-starter-web与spring-boot-starter-actuator)

~~~xml
<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-gateway</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
    <!--一般为通用配置-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <scope>runtime</scope>
        <optional>true</optional>
    </dependency>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <optional>true</optional>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
~~~

#### 3.添加主启动类

~~~java
@SpringBootApplication
@EnableEurekaClient
public class GateWayMain9527 {
    public static void main(String[] args) {
        SpringApplication.run(GateWayMain9527.class, args);
    }
}
~~~

#### 4.写application.yaml

~~~yaml
server:
  port: 9527

spring:
  application:
    name: cloud-gateway-service
  cloud:
    gateway:
      routes:
        - id: payment_routh    #路由名字，唯一即可
          uri: http://localhost:8001   #匹配的地址
          predicates:
            - Path=/payment/get/**     #路径相匹配的进行路由
        - id: payment_routh2
          uri: http://localhost:8001
          predicates:
            - Path=/payment/feign/**
        - id: payment_routh3
          uri: http://news.baidu.com/guonei
          predicates:
            - Path=/guonei


eureka:
  client:
    register-with-eureka: true #是否将自己注册到注册中心，默认true
    fetchRegistry: true #是否从注册中心抓取已有的注册信息，默认true。单节点无所谓，集群必须设置为true才能配合ribbon使用负载均衡
    service-url:
      defaultZone: http://localhost:7001/eureka
~~~

#### 5.测试

> 把原本的http://localhost:8080/payment/get/1路径换成http://localhost:9527/payment/get/1
>
> http://localhost:8080/payment/feigin/下边的也是如此

#### 6.配置动态路由

> 修改application.yaml
>
> ```yaml
> server:
>   port: 9527
> 
> spring:
>   application:
>     name: cloud-gateway-service
>   cloud:
>     gateway:
>       discovery:
>         locator:
>           enabled: true
>       routes:
>         - id: payment_routh    #路由名字，唯一即可
>           #uri: http://localhost:8001    #匹配的地址
>           uri: lb://CLOUD-PAYMENT-SERVICE   #更改为注册中心的服务名称
>           predicates:
>             - Path=/payment/get/**     #路径相匹配的进行路由
>         - id: payment_routh2
>           #uri: http://localhost:8001
>           uri: lb://CLOUD-PAYMENT-SERVICE
>           predicates:
>             - Path=/payment/feign/**
>         - id: payment_routh3
>           uri: http://news.baidu.com/guonei
>           predicates:
>             - Path=/guonei
> 
> 
> eureka:
>   client:
>     register-with-eureka: true #是否将自己注册到注册中心，默认true
>     fetchRegistry: true #是否从注册中心抓取已有的注册信息，默认true。单节点无所谓，集群必须设置为true才能配合ribbon使用负载均衡
>     service-url:
>       defaultZone: http://localhost:7001/eureka
> ```

#### 7.测试同第5步

#### 8.Predeicate常用配置

> ```yml
> predicates:
>   - Path=/payment/get/**     #路径相匹配的进行路由
>   - After=2021-02-06T14:57:15.542+08:00[Asia/Shanghai] #在这个时间之后访问
>   - Cookie=name,victor #带上cookie并且键值对一致
>   - Header=token,\d+   #带上请求头，前为名称，后为正则表达式
> ```

> 时间格式获取方式
>
> ```java
> ZonedDateTime now = ZonedDateTime.now();
> ```

> curl测试带上cookie与header，否则报错
>
> ![](.\img\Snipaste_2021-02-06_15-26-52.png)

#### 9.GateWay的filter

> 重写GlobalFilter, Ordered两个接口方法，可以对其中进行操作
>
> ```java
> @Component
> public class MyGateWayFilter implements GlobalFilter, Ordered {
> 
>     @Override
>     public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
>         System.out.println("----------------全局过滤器------------------------");
>         HttpCookie name = exchange.getRequest().getCookies().getFirst("name");
> 
>         if (name == null) {
>             System.out.println("非法用户");
>             exchange.getResponse().setStatusCode(HttpStatus.NOT_ACCEPTABLE);
>             return exchange.getResponse().setComplete();  //拒绝进入
>         }
>         String value = name.getValue();
>         System.out.println(value);
>         return chain.filter(exchange);//放行
>     }
> 
>     @Override
>     public int getOrder() {
>         return 0;
>     }
> }
> ```

### (7)Nacos服务注册与配置中心

> ==Eureka（服务注册）+Config（服务配置）+Bus（消息总线）
>
> https://github.com/alibaba/nacos/releases/tag/1.1.4

#### 1.服务注册

##### 1.创建服务module

![](.\img\Snipaste_2021-02-07_11-28-30.png)

##### 2.引入pom依赖

~~~xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    <dependency>
        <groupId>org.mybatis.spring.boot</groupId>
        <artifactId>mybatis-spring-boot-starter</artifactId>
    </dependency>
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid-spring-boot-starter</artifactId>
    </dependency>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-jdbc</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <scope>runtime</scope>
        <optional>true</optional>
    </dependency>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <optional>true</optional>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <scope>runtime</scope>
        <optional>true</optional>
    </dependency>

    <!--使用nacos主要配置-->
    <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
    </dependency>

	
    <dependency>
        <groupId>com.victor</groupId>
        <artifactId>cloud-victor-common</artifactId>
        <version>1.0-SNAPSHOT</version>
    </dependency>
</dependencies>
~~~

##### 3.创建主启动类，并引入注解@EnableDiscoveryClient

~~~java
@SpringBootApplication
@EnableDiscoveryClient
public class NacosPayment8001 {
    public static void main(String[] args) {
        SpringApplication.run(NacosPayment8001.class, args);
    }
}
~~~

##### 4.配置application.yaml

~~~yml
server:
  port: 8001

spring:
  application:
    name: cloud-nacos-payment-service
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848
  datasource:
    #当前数据源操作类型
    type: com.alibaba.druid.pool.DruidDataSource
    #mysql驱动包
    driver-class-name: org.gjt.mm.mysql.Driver
    url: jdbc:mysql://127.0.0.1:3306/test?useUnicode=true&characterEncoding=utf8&useSSL=false
    username: root
    password: 123456

mybatis:
  mapperLocations: classpath:mapper/*.xml
  #所有entity别名类所在包
  type-aliases-package: com.victor.entities
~~~

##### 5.编写业务逻辑

> dao
>
> mapper.xml
>
> service
>
> serviceimpl
>
> controller
>
> ```java
> @RestController
> public class NacosPaymentController {
>     @Autowired
>     private NacosPaymentService paymentService;
> 
>     @GetMapping("/nacos/payment/get/{id}")
>     public CommonResult getNacosPayment(@PathVariable("id") Integer id) {
>         Payment payment = paymentService.getPayment(id);
>         if (payment == null) {
>             return new CommonResult(444, "没有消息", null);
>         } else {
>             return new CommonResult(200, "查询成功", payment);
>         }
>     }
> }
> ```

##### 6.测试

![](.\img\Snipaste_2021-02-07_13-11-04.png)

##### 7.创建客户端module

![](.\img\Snipaste_2021-02-07_13-12-00.png)

##### 8.引入pom依赖

~~~xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <scope>runtime</scope>
        <optional>true</optional>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>

    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-openfeign</artifactId>
    </dependency>

    <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
    </dependency>

    <dependency>
        <groupId>com.victor</groupId>
        <artifactId>cloud-victor-common</artifactId>
        <version>1.0-SNAPSHOT</version>
    </dependency>
</dependencies>
~~~

##### 9.创建主启动类

~~~java

@SpringBootApplication
@EnableDiscoveryClient
public class NacosOrder8080 {
    public static void main(String[] args) {
        SpringApplication.run(NacosOrder8080.class, args);
    }
}
~~~

##### 10.配置application.yml

~~~yml
server:
  port: 8080

spring:
  application:
    name: cloud-nacos-consumer-order-service
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848
~~~

##### 11.创建配置类，注入RestTemplate

~~~java

@Configuration
public class ApplicationConfig {

    @Bean
    public RestTemplate getRestTemplate(){
        return new RestTemplate();
    }
}
~~~

##### 12.写业务代码，调用服务端

> ```java
> @RestController
> public class NacosOrderController {
> 
>     @Autowired
>     private RestTemplate restTemplate;
> 
>     @GetMapping("/nacos/order")
>     public CommonResult getPayment() {
>         return restTemplate.getForObject("http://localhost:8001/nacos/payment/get/3", CommonResult.class);
>     }
> }
> ```

##### 13.测试

![](C:\Users\victor\learn\学习资料\SpringCloud\img\Snipaste_2021-02-07_13-14-28.png)

##### 14.下载nacos，启动，用户与密码：nacos，服务注册成功

![](.\img\Snipaste_2021-02-07_13-16-18.png)

##### 15.再次创建一个spring.application.name为cloud-nacos-payment-service的module，两个module基本内容一致，形成分别为8001与8002两个端口，成为一个集群，并在两个module中controller加入方法

~~~java
@Value("${server.port}")
private String port;

@GetMapping("/nacos/get/port")
public String getPort(){
    return port;
}
~~~

##### 16.在客户端module中加入依赖，使他支持openFeign

~~~xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
~~~

##### 17.在客户端主启动类上加注解

> ```java
> @EnableFeignClients
> ```

##### 18.创建调用服务端的service

> ```java
> @Component
> @FeignClient("cloud-nacos-payment-service")
> public interface PymentService {
> 
>     @GetMapping("/nacos/get/port")
>     public String getPort();
> }
> ```

##### 19.在controller中调用

> ```java
> @Autowired
> private PymentService pymentService;
> 
> @GetMapping("/nacos/port/get")
> public String getPaymentMethod(){
> 	return pymentService.getPort();
> }
> ```

##### 20.测试客户端接口，多次请求返回不同的8001与8002端口号，实现负载均衡

#### 2.配置中心

##### 1.创建module

##### 2.引入pom依赖

~~~xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <scope>runtime</scope>
        <optional>true</optional>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>

    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-openfeign</artifactId>
    </dependency>

    <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
    </dependency>

    <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
    </dependency>

    <dependency>
        <groupId>com.victor</groupId>
        <artifactId>cloud-victor-common</artifactId>
        <version>1.0-SNAPSHOT</version>
    </dependency>
</dependencies>
~~~

##### 3.bootstrap.yaml

~~~yml
server:
  port: 3377

spring:
  application:
    name: cloud-nacos-config-client
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848    #nacos作为服务中心
      config:
        server-addr: localhost:8848    #nacos作为配置中心
        file-extension: yaml    #指定yaml格式的配置
~~~

##### 4.application.yaml

~~~yaml
spring:
  profiles:
    active: dev   #表示开发环境
~~~

##### 5.主启动类

~~~java
@SpringBootApplication
@EnableDiscoveryClient
public class NacosConfigClient3377 {
    public static void main(String[] args) {
        SpringApplication.run(NacosConfigClient3377.class, args);
    }
}
~~~

##### 6.Controller

~~~java
@RestController
@RefreshScope    //支持Nacos的动态刷新功能
public class ConfigClientCOntroller {

    @Value("${config.info}")
    private String config;

    @GetMapping("/nacos/config/get/")
    public String getConfig() {
        return config;
    }
}
~~~

##### 7.在nacos的服务端添加配置信息

> 配置信息的名字：spring.application.name + spring.profiles.active + spring.cloud.nacos.config.file-extension（只能用yaml，不能用yml）
>
> ![](.\img\Snipaste_2021-02-07_15-20-49.png)
>
> ![](.\img\Snipaste_2021-02-07_15-22-45.png)

##### 8.启动服务，访问controller中方法地址，成功返回配置文件的信息

##### 9.由于在controller上加了注解所以支持动态自动刷新

##### 10.支持分组

> 在bootstrap.yaml中加入一行group即可
>
> ```yaml
> server:
>   port: 3377
> 
> spring:
>   application:
>     name: cloud-nacos-config-client
>   cloud:
>     nacos:
>       discovery:
>         server-addr: localhost:8848    #nacos作为服务中心
>       config:
>         server-addr: localhost:8848    #nacos作为配置中心
>         file-extension: yaml    #指定yaml格式的配置
>         group: TEST_GROUP
> ```
>
> 测试接口输出配置文件内容

##### 11.支持不同的命名空间，与分组一个意思

> 在bootstrap.yaml中加入一行namespace即可，值为nacos服务端中的命名空间id，在命名空间中加入配置信息

> ```yaml
> server:
>   port: 3377
> 
> spring:
>   application:
>     name: cloud-nacos-config-client
>   cloud:
>     nacos:
>       discovery:
>         server-addr: localhost:8848    #nacos作为服务中心
>       config:
>         server-addr: localhost:8848    #nacos作为配置中心
>         file-extension: yaml    #指定yaml格式的配置
>         group: DEFAULT_GROUP
>         namespace: 129fb9de-6c77-445f-9ec7-6beda65af8ae
> ```
>
> 测试接口输出配置文件内容

##### 12.数据持久化配置

> 1.在nacos的目录下里的conf目录里有个nacos_mysql.sql，并在mysql中创建一个nacos的库并执行脚本
>
> ![](.\img\Snipaste_2021-02-08_09-50-07.png)

> 2.Windows将配置加到application.properties中，访问的是虚拟机中的mysql
>
> ~~~properties
> spring.datasource.platform=mysql
> 
> db.num=1
> db.url.0=jdbc:mysql://192.168.147.128:3306/nacos?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true
> db.user=root
> db.password=123456
> ~~~

> 3.Windows访问127.0.0.1:8848/nacos/

##### 13.集群配置（linux版本nacos，小公司项目上面已经够用，大公司采用如下集群架构）

> 1.下载并解压nacos1.1.4

> 2.修改application.properties文件，这个127.0.0.1:3306的数据库为linux上的，上边已经把库建好这里省略
>
> ~~~properties
> spring.datasource.platform=mysql
> 
> db.num=1
> db.url.0=jdbc:mysql://127.0.0.1:3306/nacos?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true
> db.user=root
> db.password=123456
> ~~~

> 3.复制 cluster.conf.example 出为 cluster.conf 并修改( ip地址必须是ens33中的，不能为127.0.0.1 )
>
> ![](.\img\Snipaste_2021-02-08_14-53-35.png)

> 4.修改bin下startup.sh脚本文件，使它可以接受 -p 端口参数
>
> ![](.\img\Snipaste_2021-02-08_15-09-36.png)
>
> 
>
> ![](.\img\Snipaste_2021-02-08_15-10-17.png)

> 5.修改 nginx.conf 的配置文件，首先先复制出一份保底
>
> ![](.\img\Snipaste_2021-02-08_15-46-15.png)

> 6.分别启动 nacos
>
> ~~~shell
> ./startup.sh -p 3333
> ./startup.sh -p 4444
> ./startup.sh -p 5555
> ~~~

> 7.启动 nginx，到sbin目录下执行（后面是启动的所读取的配置文件地址）
>
> ~~~shell
> ./nginx -c /usr/local/nginx/conf/nginx.cong
> ~~~

> 8.记得开放 linux 防火墙的1111端口

> 9.访问 http://192.168.147.128:1111/nacos 访问linux服务器，测试成功，并可以成功添加配置
>
> ![](.\img\Snipaste_2021-02-08_16-16-09.png)
>
> ![](.\img\Snipaste_2021-02-08_16-16-39.png)

> 10.测试添加服务到nacos
>
> 创建module，导入POM，application.yaml
>
> ~~~yaml
> server:
>   port: 9001
> 
> spring:
>   application:
>     name: linux-nacos-client9001
>   cloud:
>     nacos:
>       discovery:
>         server-addr: 192.168.147.128:1111   #写上服务器的ip与端口号，即使是Nginx代理的端口号
> ~~~
>
> 主启动类加上注解，启动，注册成功
>
> 注：这里有个小错误，即使 discovery的 server-addr 写为服务器的地址也会访问本地 localhost：8848 的 nacos ，当 config 的 server-addr 也写完后报错消失

### (8)Sentinel服务降级

#### 1.下载

https://github.com/alibaba/Sentinel/releases/tag/1.7.0下载相应的版本

sentinel-dashboard-1.7.0.jar

#### 2.安装

运行sentinel-dashboard-1.7.0.jar

访问http://localhost:8080，用户密码sentinel![Snipaste_2021-02-09_09-09-13](.\img\Snipaste_2021-02-09_09-09-13.png)

#### 3.演示工程

##### 1.启动本地nacos8848成功

##### 2.添加微服务8401项目module

> 1.pom文件
>
> ```xml
> <dependencies>
>     <dependency>
>         <groupId>org.springframework.boot</groupId>
>         <artifactId>spring-boot-starter-web</artifactId>
>     </dependency>
>     <dependency>
>         <groupId>org.springframework.boot</groupId>
>         <artifactId>spring-boot-starter-actuator</artifactId>
>     </dependency>
> 
>     <dependency>
>         <groupId>org.springframework.boot</groupId>
>         <artifactId>spring-boot-devtools</artifactId>
>         <scope>runtime</scope>
>         <optional>true</optional>
>     </dependency>
>     <dependency>
>         <groupId>org.springframework.boot</groupId>
>         <artifactId>spring-boot-starter-test</artifactId>
>         <scope>test</scope>
>     </dependency>
> 
>     <dependency>
>         <groupId>org.springframework.cloud</groupId>
>         <artifactId>spring-cloud-starter-openfeign</artifactId>
>     </dependency>
> 
>     <dependency>
>         <groupId>com.alibaba.cloud</groupId>
>         <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
>     </dependency>
> 
>     <dependency>
>         <groupId>com.alibaba.cloud</groupId>
>         <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
>     </dependency>
> 
>     <dependency>
>         <groupId>com.alibaba.cloud</groupId>
>         <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
>     </dependency>
>     
>     <!--持久化-->
>     <dependency>
>         <groupId>com.alibaba.csp</groupId>
>         <artifactId>sentinel-datasource-nacos</artifactId>
>     </dependency>
> 
> </dependencies>
> ```
>
> 2.application.yaml
>
> ```yaml
> server:
>   port: 8401
> 
> spring:
>   application:
>     name: cloudalibaba-sentinel-service
> 
>   cloud:
>     nacos:
>       discovery:
>         server-addr: localhost:8848
>     sentinel:
>       transport:
>         dashboard: localhost:8080
>         port: 8719
> 
>   devtools:
>     livereload:
>       enabled: true
> 
> management:
>   endpoints:
>     web:
>       exposure:
>         include: '*'
> ```
>
> 3.controller
>
> ```java
> @RestController
> public class FlowLimitController {
> 
>     @GetMapping("/a")
>     public String testA(){
>         return "---------------testA-----------------";
>     }
> 
>     @GetMapping("/b")
>     public String testB(){
>         return "------------------testB--------------------";
>     }
> }
> 
> ```
>
> 4.主启动类
>
> ~~~java
> @SpringBootApplication
> @EnableDiscoveryClient
> public class SentinelMain8401 {
>     public static void main(String[] args) {
>         SpringApplication.run(SentinelMain8401.class, args);
>     }
> }
> ~~~

##### 3.启动sentinel8080

##### 4.启动微服务8401

##### 5.登录sentinel8080控制台查看

查看发现并没有，因为sentinel采用的是懒加载机制，所以服务必须访问后才能展示出来

![](.\img\Snipaste_2021-02-09_09-48-27.png)

此时实时监控没有数据是因为GetMapping("")中只能写一个/的路径，比如只能写“/a”，而不能写“/get/a”，页面加载也需要一点时间慢慢等

![](.\img\Snipaste_2021-02-09_13-51-35.png)

#### 4.流控规则

##### 1.直接：是当达到流控条件直接报错

![](.\img\Snipaste_2021-02-09_14-26-14.png)

##### 2.关联：/b达到流控条件，限制/a

![](.\img\Snipaste_2021-02-09_14-31-01.png)

利用postman模拟20个线程发送并发请求 http://localhost:8401/b，请求a直接返回报错

![](.\img\Snipaste_2021-02-09_14-42-42.png)



##### 3.链路：

##### 4.warm up：经过10s（预热时长）慢慢会从一开始 30(设置的单机阈值) / 3(默认负载因子) 的10的阈值升至30

![](.\img\Snipaste_2021-02-09_14-51-57.png)

##### 5.排队等待：匀速排队，让请求以均匀的速度通过，阈值类型必须是QPS否则无效，需要设置超时时间

#### 5.降级规则

##### 1.RT：平均响应时间

200毫秒处理任务，如果超过200毫秒未完成并且请求数大于5个则会在未来的1秒钟的时间窗口内，断路器打开（微服务不可用）

![](.\img\Snipaste_2021-02-09_15-24-10.png)

##### 2.异常比例：

在请求每秒大于等于5且异常比例大于20%时候在未来3秒进行熔断，微服务停止访问（异常比例0-1）

![](.\img\Snipaste_2021-02-09_15-38-32.png)

##### 3.异常数：

在一分钟内出现10次错误后就进行熔断，停止服务

![](.\img\Snipaste_2021-02-09_15-47-23.png)

#### 6.热点key限流

##### 1.创建controller方法

~~~java
@GetMapping("/hotkey")
@SentinelResource(value = "hotkey", blockHandler = "hotKeyHandler")
public String testHotKey(@RequestParam(value = "p1", required = false) String p1) {
    return "--------------------testHotKey---------------------";
}

//限制时处理的方法，只处理流量控制违规行为，不管其他异常
public String hotKeyHandler(String p1, BlockException exception) {
    return "hotKey挂了";
}
~~~

##### 2.设置规则

注：这里的第0个参数为方法中第0个已经固定了的，比如（String p1,String p2）传入带有p1的请求会被限制，不带的不会限制

![](.\img\Snipaste_2021-02-10_09-14-26.png)

##### 3.测试

一秒一次请求可以通过，一秒多次请求则会返回"hotKey挂了"；

##### 4.参数例外项

在上一步的基础上如果第0个参数的值是222和333时，限流阈值为10，这两个值可以接受每秒10次请求

![](.\img\Snipaste_2021-02-10_09-32-12.png)

#### 7.系统规则

对系统的所有访问路径都进行限制规则

#### 8.@SentinelResource

根据下方代码使用路径进行限制则会返回默认的兜底方法，使用自定义的 SentinelResource注解 的 value 值资源名称，如果配置违规则会返回自定义的兜底方法，注：只处理sentinel配置违规，不处理java的异常

~~~java
@GetMapping("/resource")
@SentinelResource(value = "resource", blockHandler = "resourceBlockHandler")
public CommonResult myResource() {
    return new CommonResult(200, "通过", "数据");
}

public CommonResult resourceBlockHandler(BlockException exception) {
    return new CommonResult(444, "未通过", null);
}
~~~

如果像上面代码中为每个方法都设置一个兜底方法，会使代码臃肿，重复量增多，逻辑代码与异常处理混乱，所以配置全局处理类进行处理

~~~java
@Component
public class CustomerBlockHandler {

    public static CommonResult blockExceptionHandler1(BlockException exception){
        return new CommonResult(444,"全局处理类方法-----1",null);
    }

    public static CommonResult blockExceptionHandler2(BlockException exception){
        return new CommonResult(444,"全局处理类方法-----2",null);
    }
}
~~~

~~~java
@GetMapping("/myhandler")
@SentinelResource(value = "myhandler", blockHandlerClass = CustomerBlockHandler.class,blockHandler = "blockExceptionHandler2")
public CommonResult myHandler() {
    return new CommonResult(200, "通过", "数据");
}
~~~

#### 9.服务熔断

1.搭建熔断环境

> 首先创建两个服务端module，注册到nacos中  ，alibaba-cloud-payment8001 和 alibaba-cloud-payment8002 使用同一个微服务名称
>
> 创建一个客户端module，注册nacos中，并使用sentinel检测，使用openFeign调用服务端的方法，实现负载均衡，alibaba-cloud-consumer84

2.配置fallback处理java异常

> ```java
> @GetMapping("/port")
> @SentinelResource(value = "port", fallback = "fallbackTest")
> public String getPort(Integer i) {
>     int a[] = {1, 2, 34};
>     //int b = a[i];
>     return consumerService.getPort();
> }
> 
> public String fallbackTest(Integer i,Throwable throwable) {
>     return "程序出错了。。。。。。";
> }
> ```

3.fallback 与 blockHandler 可以同时设置，当配置违规时，直接blockHandler处理，因为还没有达到java逻辑方法报错

#### 10.规则持久化

1.需要的模块pom中导入依赖

> ```xml
> <!--持久化-->
> <dependency>
>     <groupId>com.alibaba.csp</groupId>
>     <artifactId>sentinel-datasource-nacos</artifactId>
> </dependency>
> ```

2.yaml修改

> ```yml
> server:
>   port: 84
> 
> spring:
>   application:
>     name: alibaba-cloud-consumer84
>   cloud:
>     nacos:
>       discovery:
>         server-addr: localhost:8848
>     sentinel:
>       transport:
>         dashboard: localhost:8080
>       datasource: 
>         ds1:
>           nacos:
>             server-addr: localhost:8848
>             dataId: ${spring.application.name}
>             groupId: DEFAULT_GROUP
>             data-type: json
>             rule-type: flow
> ```

3.到nacos中新建配置

![](.\img\Snipaste_2021-02-18_09-50-35.png)

4.测试路径，重启项目后访问资源路径规则仍然保存，持久化成功

### (9)处理分布式事务(seata)

#### 1.处理过程

- TM：
- TC：
- RM：

1. TM向TC申请一个全局事务，全局事务创建成功并成为一个全库唯一的XID；
2. XID在微服务调用链路的上下文中传播；
3. RM向TC注册分支事务，将其纳入XID对应全局事务的管辖；
4. TM向TC发起针对XID的全局提交或回滚决议；
5. TC调度XID下管辖的全部分支事务完成提交与回滚请求；

#### 2.安装

1.修改 file.conf  的 service 和 store 模块

![](.\img\Snipaste_2021-02-18_16-20-31.png)

![](.\img\Snipaste_2021-02-18_14-27-23.png

![](.\img\Snipaste_2021-02-18_14-29-57.png)

2.创建名为 seata 的数据库，并导入seata下conf目录下的 db_store.sql 文件，创建表

3.修改 registry.conf 文件

![](.\img\Snipaste_2021-02-18_14-37-31.png)

4.启动nacos 端口8848

5.启动seata

#### 3.环境准备

##### 1.在测试的数据库中运行 db_undo_log.conf 添加 undo_log 表

##### 2.创建客户端 module

> pom文件
>
> ```xml
> <dependencies>
>     <dependency>
>         <groupId>org.springframework.boot</groupId>
>         <artifactId>spring-boot-starter-web</artifactId>
>     </dependency>
>     <dependency>
>         <groupId>org.springframework.boot</groupId>
>         <artifactId>spring-boot-starter-actuator</artifactId>
>     </dependency>
> 
>     <dependency>
>         <groupId>org.springframework.boot</groupId>
>         <artifactId>spring-boot-devtools</artifactId>
>         <scope>runtime</scope>
>         <optional>true</optional>
>     </dependency>
>     <dependency>
>         <groupId>org.springframework.boot</groupId>
>         <artifactId>spring-boot-starter-test</artifactId>
>         <scope>test</scope>
>     </dependency>
> 
>     <dependency>
>         <groupId>org.springframework.cloud</groupId>
>         <artifactId>spring-cloud-starter-openfeign</artifactId>
>     </dependency>
> 
>     <dependency>
>         <groupId>com.alibaba.cloud</groupId>
>         <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
>     </dependency>
> 
>     <dependency>
>         <groupId>com.alibaba.cloud</groupId>
>         <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
>     </dependency>
> 
>     <dependency>
>         <groupId>com.alibaba.cloud</groupId>
>         <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
>     </dependency>
> 
>     <dependency>
>         <groupId>com.alibaba.cloud</groupId>
>         <artifactId>spring-cloud-starter-alibaba-seata</artifactId>
>         <exclusions>
>             <exclusion>
>                 <groupId>io.seata</groupId>
>                 <artifactId>seata-all</artifactId>
>             </exclusion>
>         </exclusions>
>     </dependency>
> 
>     <dependency>
>         <groupId>io.seata</groupId>
>         <artifactId>seata-all</artifactId>
>         <version>0.9.0</version>
>     </dependency>
> 
>     <!--持久化-->
>     <dependency>
>         <groupId>com.alibaba.csp</groupId>
>         <artifactId>sentinel-datasource-nacos</artifactId>
>     </dependency>
> 
>     <dependency>
>         <groupId>com.victor</groupId>
>         <artifactId>cloud-victor-common</artifactId>
>         <version>1.0-SNAPSHOT</version>
>     </dependency>
> </dependencies>
> ```

> yaml
>
> ```yaml
> server:
>   port: 2001
> 
> spring:
>   application:
>     name: cloud-2001-seata-service
>   cloud:
>     alibaba:
>       seata:
>         tx-service-group: my_test_tx_group    #与file.conf中的一致
>     nacos:
>       discovery:
>         server-addr: localhost:8848
> ```

> 复制 file.conf 与 regitry.conf 到module的resource路径下

> 编写service
>
> ```java
> public interface OrderService {
> 
>     void transfer();
> }
> ```
>
> ```java
> @Component
> @FeignClient("seata-de-2003")
> public interface DeService {
> 
>     @GetMapping("/seata/de")
>     void de();
> }
> ```
>
> ```java
> @Component
> @FeignClient("seata-add-2002")
> public interface AddService {
> 
>     @GetMapping("/seata/add")
>     void add();
> }
> ```
>
> ```java
> @Service
> @Slf4j
> public class OrderServiceImpl implements OrderService {
> 
>     @Autowired
>     private AddService addService;
> 
>     @Autowired
>     private DeService deService;
> 
>     @Override
>     public void transfer() {
>         log.info("---------------开始转账");
>         deService.de();
>         log.info("---------------已经减钱");
>         //int a = 10 / 0;
>         addService.add();
>         log.info("---------------已经加钱");
>         log.info("---------------转账成功");
>     }
> }
> ```

> 编写controller
>
> ```java
> @RestController
> public class OrderController {
> 
>     @Autowired
>     private OrderService orderService;
> 
>     @GetMapping("/transfer")
>     public String transfer() {
>         orderService.transfer();
>         return "转账成功";
>     }
> }
> ```

##### 3.创建服务端 module

> pom
>
> ```xml
> <dependencies>
>     <dependency>
>         <groupId>org.springframework.boot</groupId>
>         <artifactId>spring-boot-starter-web</artifactId>
>     </dependency>
>     <dependency>
>         <groupId>org.springframework.boot</groupId>
>         <artifactId>spring-boot-starter-actuator</artifactId>
>     </dependency>
> 
>     <dependency>
>         <groupId>org.springframework.boot</groupId>
>         <artifactId>spring-boot-devtools</artifactId>
>         <scope>runtime</scope>
>         <optional>true</optional>
>     </dependency>
>     <dependency>
>         <groupId>org.springframework.boot</groupId>
>         <artifactId>spring-boot-starter-test</artifactId>
>         <scope>test</scope>
>     </dependency>
> 
>     <dependency>
>         <groupId>org.springframework.cloud</groupId>
>         <artifactId>spring-cloud-starter-openfeign</artifactId>
>     </dependency>
> 
>     <dependency>
>         <groupId>com.alibaba.cloud</groupId>
>         <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
>     </dependency>
> 
>     <dependency>
>         <groupId>com.alibaba.cloud</groupId>
>         <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
>     </dependency>
> 
>     <dependency>
>         <groupId>com.alibaba.cloud</groupId>
>         <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
>     </dependency>
> 
>     <dependency>
>         <groupId>com.alibaba.cloud</groupId>
>         <artifactId>spring-cloud-starter-alibaba-seata</artifactId>
>         <exclusions>
>             <exclusion>
>                 <groupId>io.seata</groupId>
>                 <artifactId>seata-all</artifactId>
>             </exclusion>
>         </exclusions>
>     </dependency>
> 
>     <dependency>
>         <groupId>io.seata</groupId>
>         <artifactId>seata-all</artifactId>
>         <version>0.9.0</version>
>     </dependency>
> 
>     <!--持久化-->
>     <dependency>
>         <groupId>com.alibaba.csp</groupId>
>         <artifactId>sentinel-datasource-nacos</artifactId>
>     </dependency>
> 
>     <dependency>
>         <groupId>com.victor</groupId>
>         <artifactId>cloud-victor-common</artifactId>
>         <version>1.0-SNAPSHOT</version>
>     </dependency>
> 
>     <dependency>
>         <groupId>org.projectlombok</groupId>
>         <artifactId>lombok</artifactId>
>     </dependency>
> 
>     <dependency>
>         <groupId>org.mybatis.spring.boot</groupId>
>         <artifactId>mybatis-spring-boot-starter</artifactId>
>     </dependency>
>     <dependency>
>         <groupId>com.alibaba</groupId>
>         <artifactId>druid-spring-boot-starter</artifactId>
>     </dependency>
>     <dependency>
>         <groupId>mysql</groupId>
>         <artifactId>mysql-connector-java</artifactId>
>     </dependency>
>     <dependency>
>         <groupId>org.springframework.boot</groupId>
>         <artifactId>spring-boot-starter-jdbc</artifactId>
>     </dependency>
> </dependencies>
> ```

> yaml
>
> ```yaml
> server:
>   port: 2002
> 
> spring:
>   application:
>     name: seata-add-2002
>   cloud:
>     nacos:
>       discovery:
>         server-addr: localhost:8848
>     alibaba:
>       seata:
>         tx-service-group: my_test_tx_group
>   datasource:
>     driver-class-name: com.mysql.jdbc.Driver
>     url: jdbc:mysql://127.0.0.1:3306/test?useUnicode=true&characterEncoding=utf8&useSSL=false
>     username: root
>     password: 123456
> 
> 
> mybatis:
>   mapperLocations: classpath:mapper/*.xml
> ```

> dao
>
> ```java
> @Mapper
> @Repository
> public interface UserDao {
> 
>     void update();
> }
> ```

> 在resource目录下创建mapper目录，UserDao.xml
>
> ```xml
> <?xml version="1.0" encoding="UTF-8" ?>
> <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
>         "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
> <mapper namespace="com.victor.dao.UserDao">
> 
>     <update id="update">
>         update account set money = money+50 where id = 1
>     </update>
> </mapper>
> ```

> controller
>
> ```java
> @RestController
> public class UserController {
>     @Autowired
>     private UserDao userDao;
> 
>     @GetMapping("/seata/add")
>     public void add(){
>         userDao.update();
>     }
> }
> ```

> 同样复制 file.conf 与 regitry.conf 到module的resource路径下

##### 4.另一个服务端module与上面那个一致

#### 4.测试

![](.\img\Snipaste_2021-02-18_16-55-33.png)



如果在总业务方法中减钱与加钱中间加入一个异常 int a = 10 / 0；则会报错，但是减钱成功却没有加钱，出现事务问题

在总业务方法上加入注解 @GlobalTransactional，实现异常回滚，同步减钱与加钱操作

其中name随便起，唯一即可。rollbackFor = Exception.class为任何异常都回滚

```java
@Service
@Slf4j
public class OrderServiceImpl implements OrderService {

    @Autowired
    private AddService addService;

    @Autowired
    private DeService deService;

    @Override
    @GlobalTransactional(name = "order",rollbackFor = Exception.class)
    public void transfer() {
        log.info("---------------开始转账");
        deService.de();
        log.info("---------------已经减钱");
        int a = 10 / 0;
        addService.add();
        log.info("---------------已经加钱");
        log.info("---------------转账成功");
    }
}
```

