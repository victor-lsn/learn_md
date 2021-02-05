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



### (4)Eureka构建步骤

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

### (5)OpenFeign的使用

#### 1.pom依赖

~~~xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-openfeign-core</artifactId>
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

