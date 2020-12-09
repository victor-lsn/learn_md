# JavaWeb学习

导入的jar包放在web下面WEB-INF下面的lib里

## XML

### 与HTML区别

1. xml标签自定义
2. xml语法严格，HTML松散
3. xml用于存储数据，HTML用于展示

### 语法

#### 基本语法

1. 第一行必须是文档声明，前方不能有空行
2. 只有一个根标签
3. 属性用单引号引起来
4. 区分大小写

#### 组成部分

1. 文档声明
   - 格式：<?xml version='1.0' 属性列表 ?>
   - 属性列表：
     - version：版本号，必须的属性
     - encoding：编码方式
     - standalone：是否独立，yes：不依赖其他，no：依赖其他
2. 指令：结合CSS
3. 标签：名称自定义
4. 属性：id属性唯一
5. 文本：CDATA区，这里面的数据原样展示
   1. 格式：<![CDATA][数据内容]>

#### 约束

规定xml文档的书写规则

**分类**

1. DTD：简单
   1. 引入
      1. 引入内部：不常用
      2. 引入外部
         1. 本地：<!DOCTYPE 根标签名 SYSTEM "dtd文件路径">
         2. 网络：<!DOCTYPE 根标签名 PUBLIC "dtd文件名字" "dtd文件路径">
2. Schema：复杂
   1. 后缀：.xsd

### XML的解析

#### 操作XML文档

- 解析：将文档中的数据读取到内存中
- 写入：将内存中的数据保存到XML文档中，持久化存储

#### 解析XML的方式

1. DOM：将标记语言文档一次加载到内存，在内存中形成一颗DOM树
   - 优点：操作方便，对文档可以CRUD操作
   - 缺点：占内存
2. SAX：逐行读取，基于事件驱动
   - 优点：不占内存
   - 缺点：只能读取，不能增删改

#### XML常见解析器

1. JAXP：sun公司提供的，支持SAX和DOM两种
2. DOM4J：一款非常优秀的解析器
3. Jsoup：常用解析HTML的解析器
4. PULL：Android的内置解析器，支持SAX

#### Jsoup

**快速入门**

1. 导入jar包

2. 获取Document对象(DOM树形对象)

3. 获取对应的Element对象

4. 获取数据

   ~~~java
   public static void main(String[] args) throws IOException {
           String path = demo01_Jsoup.class.getClassLoader().getResource("student.xml").getPath();
           Document document = Jsoup.parse(new File(path), "utf-8");
           Elements elements = document.getElementsByTag("name");
           for (Element element : elements) {
               System.out.println(element.text());
           }
       }
   ~~~



**对象使用**

1. Jsoup：

   parse：解析XML或HTML，返回Document对象

   ​		parse（File in,String charsetName）解析文件

   ​		parse（String html）解析html字符串

   ​		parse（URL url，int timeoutMill）解析网络网址

2. Document：文档对象，代表内存中的DOM树

   获取element对象

   ​		getElementById（String id）：根据id属性值获取element对象

   ​		getElementsByTag（String tagname）：根据标签名称获取element集合

   ​		getElementsByAttribute（String key）：根据属性名称获取元素对象集合

   ​		getElementsByAttributeValue（String key，String value）：根据属性名与属性值获取元素对象集合

3. Elements：元素element的集合

4. Element：元素对象

   1. 获取子元素对象

      getElementById（String id）：根据id属性值获取element对象

      ​		getElementsByTag（String tagname）：根据标签名称获取element集合

      ​		getElementsByAttribute（String key）：根据属性名称获取元素对象集合

      ​		getElementsByAttributeValue（String key，String value）：根据属性名与属性值获取元素对象集合

   2. 获取属性值

      attr（String key）：根据属性名称获取属性值

   3. 获取文本内容

      text（）；获取文本内容

      html（）；获取标签体的所有内容（包含标签）

5. Node：节点对象

   document和element的父类

**快捷查询方式**

1. selector：选择器

   element或document.select（String cssQuery）

2. XPath：需要额外导入jar包



## Servlet

### 概念

server applet运行于服务器端的小程序

servlet就是一个接口，定义了Java类被浏览器访问（tomcat识别）的规则

我们定义一个类，实现servlet接口，实现方法

### 步骤

#### 普通实现

1. 创建javaEE项目
2. 定义一个类，实现servlet接口
3. 实现接口中的抽象方法
4. 在web.xml中配置servlet

#### 注解实现

1. 创建javaEE项目

2. 定义一个类，实现servlet接口

3. 实现接口中的抽象方法

4. 在类上添加注解

   ~~~java
   @WebServlet(urlPatterns = "/demo1", loadOnStartup = 1)
   ~~~

   

### 原理

1. 当服务器接受到客户端浏览器的请求后，会解析URL路径，获取servlet的资源路径
2. 查找web.xml中对应的<url-pattern>是否有相应的servlet
3. 如果有就找到他的全类名
4. 将这个servlet类的字节码文件加载到内存，实例化对象
5. 调用其中的方法

### 方法

1. init：初始化，只执行一次，默认第一次访问时初始化

   ~~~xml
   <servlet>
           <servlet-name>demoServlet</servlet-name>
           <servlet-class>com.victor.web.servlet.demoServlet</servlet-class>
           <!--负数为默认第一次访问初始化，正数在服务器启动时就开始初始化-->
           <load-on-startup>1</load-on-startup>
       </servlet>
   ~~~

   init只执行一次说明，servlet在内存中只存在一个对象，Servlet是单例的

   ​		多个用户同释放时可能会存在线程安全问题，解决：尽量不要在servlet中定义成员变量，即使定义了，也不要对其修改

2. service：在servlet被访问是调用，提供服务

3. destroy：销毁，服务器关闭时（正常关闭）执行，servlet被销毁之前执行，一般用于释放资源

4. getServletConfig：获取servlet配置对象

5. getServletInfo：获取Servlet信息

### 体系结构

```java
HttpServlet  继承自  GenericServlet   实现了   Servlet接口
```

GenericServlet：继承之后，实现service方法即可

HttpServlet：继承之后，实现doget与dopost方法



jdk版本问题

~~~java
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <configuration>
    <source>1.8</source>
    <target>1.8</target>
    </configuration>  
</plugin>
~~~

### ServletConfig类

配置信息类

三大作用

1. 可以获得servlet程序的别名 servlet-name
2. 获取初始化参数 init-param
3. 获取servletContext对象

servlet程序与servletConfig对象都是由tomcat负责创建，我们负责使用，servlet程序默认是第一次访问时创建，servletConfig是每个servlet创建时，就创建一个与之对应的ServletConfig对象

注：如果重写了init方法，需要显示调用super.init(config)，因为父类中的init方法保存了一个ServletConfig对象，

如果重写则没有保存config对象，则会报空指针异常

## HTTP

### 概念：

定义了，客户端和服务端通信时，发送数据的格式

### 特点：

1. 基于TCP/IP的高级协议
2. 默认端口：80
3. 基于请求/响应模型的：一次请求对应一次响应
4. 无状态的：每次请求之间相互独立，不能交互数据

历史版本：

- 1.0：每次请求都建立新的连接
- 1.1：复用链接

### 请求消息的格式

1. 请求行

   格式：请求方式     请求URL    请求协议/版本

   ​			GET       /login.html      HTTP/1.1

   请求方式：

   HTTP有7种请求方式，常用的两种

   - GET
     - 请求参数在请求行中 ，在URL后拼接着
     - 请求的URL长度有限
     - 不太安全
   - POST
     - 请求参数在请求体中
     - 请求的URL长度没有限制
     - 相对安全

2. 请求头：客户端浏览器告诉服务器的一些信息

   格式：请求头名称：请求头值

   常见请求头：

   - User-Agent：浏览器版本信息，可以用来解决浏览器兼容问题
   - Referer：告诉服务器当前请求是从哪个地址过来的
     - 防盗链
     - 统计工作

3. 请求空行

   格式：空行

   用于分隔POST请求的请求头与请求体

4. 请求体

   用于封装POST请求参数的



### Request

![](/home/victor/learn/学习总结/request原理.jpg)

#### Request和Response原理

1. 他俩都是由服务器创建，我们使用
2. request对象是用来获取请求消息，response是来设置响应消息

#### 体系结构

~~~java
tomcat服务器实现的类     实现了     HttpServletRequest     继承自     ServletRequest     
~~~

#### 功能

1. 获取请求消息数据

   1. 获取请求行数据

      1. 获取请求方式：getMethod

      2. 获取项目虚拟目录：getContextPath

      3. 获取Servlet路径：getServletPath

      4. 获取get方式请求参数：getQueryString

      5. 获取请求URI、URL：getRequestURI，getRequestURL

         URI：统一资源标识符

         URL：统一资源定位符

      6. 获取协议、版本：getProtocal

      7. 获取客户机的IP：getRemoteAddr

   2. 获取请求头数据

      方法：

      1. getHeader（String name）：通过请求头名称获取值
      2. getHeaderNames（）：获取所有请求头名称

   3. 获取请求体数据

      请求体：只有POST请求方式才有请求体，在请求中封装了POST请求的请求参数

      步骤：

      1. 获取流对象
         - getReader：获取字符输入流，只能操作字符数据
         - getInputStream：获取字节流，可以操作所有类型数据
      2. 在从流对象中拿数据

2. 其他功能

   1. 获取请求参数：post与get都可以使用

      - getParameter（String name）根据参数名称获取参数值

      - getParameterValues（String name）根据参数名称获取参数值数组

      - getParameterNames（）获取参数名称集合

      - getParameterMap（）获取参数Map集合

      - 中文乱码问题：

        - GET方式：tomcat8已经解决了get方式的乱码问题

        - POST方式：需要在所有请求参数前添加

          ~~~java
          req.setCharacterEncoding("utf-8")
          ~~~

   2. 请求转发：一种服务器内部的资源跳转方式

      1. 步骤

         1. 通过Request对象获得请求转发器对象

         2. 使用请求转发器对象进行转发

            ~~~java
            req.getRequestDispatcher("/demo3").forward(req, resp);
            ~~~

      2. 特点

         1. 浏览器地址路径不发生改变
         2. 只能转发到当前服务器内部资源中
         3. 转发是一次请求

   3. 共享数据

      - 域对象：一个有作用范围的对象，可以在范围内共享数据
      - request域：代表一次请求的范围，一般用于请求转发的资源共享
      - 方法：
        - setAttribute（String name，Object o）
        - getAttribute（String name）
        - removeAttribute（String name）

   4. 获取ServletContext

      ```java
      getServletContext()
      ```



### 响应消息的格式

1. 响应行
   - 组成：协议/版本   响应状态吗    状态描述
   - 响应状态吗：
     - 1XX：服务器接受客户端消息但没有接受完成，等待一段时间，发送1XX状态吗
     - 2XX：成功。代表：200
     - 3XX：重定向。代表：302（重定向），304（访问缓存）
     - 4XX：客户端错误。代表：404（请求路径没有对应资源），405（请求方式没有对应的doXXX方法）
     - 5XX：服务端错误。500（服务器内部异常）
2. 响应头
   - 组成：头名称：值
   - 常见响应头：
     - Content-Type：服务器告诉客户端当前响应体数据的格式
     - Content-disposition：服务器告诉客户端以什么格式打开相应题数据
       - 值：in-line：是默认值，在当前页面打开
       - attachment：以附件形式打开响应体，文件下载
3. 响应空行
4. 响应体：就是响应的数据

/的代表路径：

在html中代表的是ip+端口号

在服务器中除了重定向都代表的是ip+端口号+工程路径/

重定向代表的是是ip+端口号

### Response

#### 功能：

1. 设置响应行：设置状态码setStatus(int sc)

2. 设置响应头：setHeader(String name,String value)

3. 设置响应体

   步骤：

   1. 获取输出流
      - 字符输出流：getWriter()
      - 字节输出流：getOutputStream()
      - 两个流只能同时使用一个
   2. 使用输出流，讲数据输出到客户端浏览器

4. 重定向

   ~~~java
   //resp.setStatus(302);
   //resp.setHeader("location", "/day01_servlet_anno/demo04");
   
   resp.sendRedirect("/day01_servlet_anno/demo04");//效果和上面两行一样，
   ~~~

   特点：

   - 浏览器地址栏改变
   -  可以访问服务器外部资源
   - 是两次请求，不能用request域对象共享数据了
   - 不可以访问WEB-INF下的资源

#### 案例

- 服务器输出字符数据到浏览器

  ~~~java
  /*resp.setCharacterEncoding("utf-8");
          resp.setHeader("Content-Type","text/html;charset=utf-8");*/
  
  resp.setContentType("text/html; charset=UTF-8");//解决中文乱码问题,同时设置服务器与浏览器的字符集，一定在获取流对象之前设置
  
  PrintWriter writer = resp.getWriter();
  writer.write("hello demo01你好啊");//流自动关闭，因为是response对象获取的，在一次请求后response就销毁了，流自然也就关闭了
  ~~~

  

- 服务器输出字节数据到浏览器

  ~~~java
  resp.setContentType("text/html; charset=UTF-8");
  
  ServletOutputStream outputStream = resp.getOutputStream();
  outputStream.write("你好".getBytes());
  ~~~

  

- 验证码

  ~~~java
  protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
          final int HEIGHT = 50;
          final int WIDTH = 100;
  
          //创建一个图片对象在内存中
          BufferedImage img = new BufferedImage(WIDTH, HEIGHT, BufferedImage.TYPE_INT_RGB);
  
  
          //填充背景色
          Graphics graphics = img.getGraphics();
          graphics.setColor(Color.gray);
          graphics.fillRect(0, 0, WIDTH, HEIGHT);
  
          //画边框
          graphics.setColor(Color.ORANGE);
          graphics.drawRect(0, 0, WIDTH - 1, HEIGHT - 1);
  
  
          String str = "QWERTYUIOPLKJHGFDSAZXCVBNMqwertyuioplkjhgfdsazxcvbnm0123456789";
  
          Random random = new Random();
  
  
          //写验证码
          for (int i = 0; i < 4; i++) {
              int index = random.nextInt(str.length());
              graphics.drawString(str.charAt(index) + "", 20 + i * 20, 25 + i * 3);
          }
  
  
          //画干扰线
          for (int i = 0; i < 5; i++) {
              graphics.setColor(Color.BLUE);
              graphics.drawLine(random.nextInt(WIDTH), random.nextInt(WIDTH),
                      random.nextInt(HEIGHT), random.nextInt(HEIGHT));
          }
  
          //将图片输出到页面展示
          ImageIO.write(img, "jpg", resp.getOutputStream());
  
      }
  ~~~



### ServletContext

作用

1. 获取web.xml中的上下文参数 context-param
2. 获取当前工程路径
3. 获取工程部署后服务器硬盘上的绝对路径
4. 像map存取数据

#### 概念

代表整个web应用，可以和程序的容器（服务器）来通信，一个工程只有一个

#### 获取

- request.getServletContext()
- this.getServletContext()：因为HttpServlet里有这个方法

#### 功能

1. 获取MIME类型

   互联网通信过程中定义的一种文件数据类型

   ~~~java
   ServletContext servletContext = this.getServletContext();
   String mimeType = servletContext.getMimeType("a.png");
   System.out.println(mimeType);//image/png
   ~~~

   

2. 域对象：共享数据

   方法：

   - setAttribute（String name，Object o）
   - getAttribute（String name）
   - removeAttribute（String name）

   范围：整个项目，服务器

3. 获取文件的真实（服务器）路径

   方法：getRealPath(String path)

#### 文件下载案例

~~~java
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String filename = req.getParameter("filename");
        //图片加载进内存
        ServletContext servletContext = this.getServletContext();
        String realPath = servletContext.getRealPath("/img/" + filename);
        FileInputStream fileInputStream = new FileInputStream(realPath);


        //解决中文名问题
        String agent = req.getHeader("User-Agent");
        filename = DownloadUtils.getFileName(agent, filename);


        //获取文件类型
        String mimeType = servletContext.getMimeType(filename);
        resp.setHeader("content-type", mimeType);

        //设置成下载
        resp.setHeader("content-disposition", "attachment;filename=" + filename);


        //把输入流数据写到输出流
        ServletOutputStream outputStream = resp.getOutputStream();
        byte[] bytes = new byte[1024];
        int len = 0;
        while ((len = fileInputStream.read(bytes)) != -1) {
            outputStream.write(bytes);
        }

        fileInputStream.close();

    }
~~~

解决中文名字问题的工具类

~~~java
public class DownloadUtils {
    public static String getFileName(String agent, String filename) throws UnsupportedEncodingException {
        if (agent.contains("MSIE")) {
            filename = URLEncoder.encode(filename, "utf-8");
            filename = filename.replace("+", " ");
        } else if (agent.contains("FireFox")) {
            BASE64Encoder base64Encoder = new BASE64Encoder();
            filename = "=?utf-8?B?" + base64Encoder.encode(filename.getBytes("UTF-8")) + "?=";
        } else {
            filename = URLEncoder.encode(filename, "UTF-8");
        }
        return filename;
    }
}
~~~

### 会话技术

#### 会话概念：

浏览器第一次给服务器发送请求，会话建立，直到有一方断开为止。一次会话中包含多次请求与响应

#### 功能

在一次会话范围内多次请求间，共享数据

#### 方式

1. 客户端会话技术：Cookie，将数据保存在客户端浏览器
2. 服务器端会话技术：Session，将数据保存在服务器端

#### Cookie

**步骤**

1. 创建cookie对象，并保存数据

2. 使用response对象发送cookie对象

3. 使用request对象获取cookie对象

   ~~~java
   Cookie cookie = new Cookie("name", "victor");
   
   resp.addCookie(cookie);
   
   Cookie[] cookies = req.getCookies();
   if (cookies != null) {
       for (Cookie cookie1 : cookies) {
           System.out.println(cookie1);
           String name = cookie1.getName();
           String value = cookie1.getValue();
           System.out.println(name + "===" + value);
       }
   }
   ~~~

**实现原理**

基于响应头set-cookie和请求头cookie实现

**细节**

1. 可以一次发送多个对象

2. cookie保存时间

   - 默认情况下，浏览器关闭后，cookie数据被销毁

   - 持久化存储

     setMaxAge(int seconds)

     - 正数：将cookie写入硬盘中，持久化存储，秒数到了自动删除
     - 负数：默认值，浏览器关闭后，cookie数据被销毁
     - 0：删除cookie信息

3. cookie存中文

   tomecat8之前不可以存储中文信息，8之后可以存储。如果8之前想存中文需要进行数据转码，使用URLEncoder

4. cookie共享问题

   - 如果一个tomcat服务器不输了多个项目，在默认情况下每个项目只能访问自己项目发送的cookie，如果想访问到别的cookie的信息，就需要使用setPath（"/"）方法修改成"/"下，这样这个cookie就可以互相访问了
   - 不同的tomcat服务器间cookie的访问，如果他们的一级域名相同则可以共享这个cookie，使用setDomain（）方法设置
   
5. 可以设置path属性，来指定一些路径时可用，默认为工程路径

**特点**

1. cookie存储到客户端浏览器
2. 对单个cookie有大小限制，对同一个域名下的cookie有个数限制

**作用**

1. 一般用于存储少量且不太敏感的信息
2. 在不登陆的情况下，完成服务器对客户端的身份识别



#### Session

**步骤**

~~~java
HttpSession session = req.getSession();

session.setAttribute("name","lanzi");
System.out.println(session.getAttribute("name"));

session.invalidate()、//让他马上超时失效
setMaxInactiveInterval()//设置失效时间
getMaxInactiveInterval()//获取失效时间
~~~

**原理**

是基于cookie之上的，当第一次获取session对象时，就创建一个session对象，把这个session对象利用set-cookie存储一个名为JSESSIONID的cookie，当下一次获取session对象时，首先利用cookie找出这个session的ID，如果有取出，这样那就保证了是一个session对象。

**细节**

- 当客户端浏览器关闭时，两次获取的session对象不一样

  如果想要相同，那么就使保存JSESSIONID的cookie持久化，设置他的存活时间，是他保存到磁盘

- 服务器关闭，默认两次获取的session也不一样

  session的钝化：在服务器正常关闭之前，将session对象序列化到硬盘上

  session的活化：在服务器启动后，将session文件转化到内存中的session对象即可。

  活化与钝化tomcat都自动完成了，只要手动部署项目就可以实现，但是idea并不会。

- session被销毁

  1. 服务器关闭

  2. session对象自己调用invalidate（）方法

  3. session默认失效时间为30分钟

     可以在web.xml配置默认时间

     ```java
     <session-config>
         <session-timeout>40</session-timeout>
     </session-config>
     ```

**特点**

- 存储一次会话的多次请求数据，存在服务器
- session可以存储任意类型数据，任意大小





## JSP

java服务器端页面

### 原理

1. 找到对应的.jsp文件
2. 转换成.java文件
3. 编译.java成为.class字节码文件
4. 这个字节码文件其实就是一个servlet

### 脚本

1. <%    代码    %>：定义的java代码在service方法中。service方法中可以定义什么，这个脚本就可以定义什么，而且其中的对象可以随意使用
2. <%!   代码    %>：定义的java代码在jsp转换后的java类的成员位置，声明类属性、静态代码块、类方法、内部类
3. <%=  代码    %>：定义的java代码输出到页面上，表达式脚本，输出整形、浮点型、字符串、对象，调用了out.print()

### 指令

格式：<%@   指令名称      属性名=属性值 %>

分类：

- page：配置jsp页面

  属性：

  1. contentType：等同于response.setContentType()，设置响应体的mime类型以及字符集
  2. language：表示jsp翻译后是什么文件，暂时只支持java
  3. pageEncoding：设置当前页面的字符集（高级开发工具会根据contentType自动设置页面字符集）
  4. import：导包
  5. autofluth：设置当前缓冲区满了之后，是否自动刷新缓冲区，默认值true
  6. buffer：默认缓冲区大小，默认8kb
  7. errorPage：当前页面发生异常时自动跳转到指定的页面
  8. isErrorPage：标识当前页面是否为错误页面，如果设置为true则可使用exception内置对象来获取异常
  9. session：设置访问当前jsp页面，是否会创建HttpSession对象，默认是true
  10. extends：设置jsp页面翻译出来的默认继承谁  

- include：页面包含   <%@  include  file="/"%> 并不会对被包含的页面进行翻译成class文件，直接原封不动拼接到包含页面中所使用的位置

- 动态包含：动态传递参数

  <jsp:include page=" /" >

  ​		<jsp:param name="username" vlaue="root"/>

  </jsp:include >

- 请求转发：<%@  forward  page="/"%>

- taglib：导入资源，JSTL等

### 内置对象

**前四个为四大域对象**，优先顺序就是从小到大的顺序，pageContext < request < session < application

- pageContext：当前jsp页面存取数据，还可以获取其他8个内置对象   PageContextImpl

- request：一次请求访问多个资源存取数据（请求转发）HttpServletRequest对象实例

- session：一次会话的多个请求存取数据 HttpSession对象实例

- application：所有用户间存取数据，ServletContext对象实例

- response：响应对象

- out：字符输出流对象，可以把数据输出到页面上，out.write是只能输出字符串不会出错，out.print是把所有都转换成字符串类型在调用out.write方法输出的。统一使用out.print

  注：在tomcat服务器真正给客户端做出响应之前，会先找response缓冲区数据，再找out缓冲区的数据，因此，response.getWriter().write()数据永远在out.write()前面，避免这样全部使用out

- page：当前页面对象（servlet） this

- config：servlet的配置对象，ServletConfig对象

- exception：异常对象，只有标识isErrorPage的才有这个对象



## EL

语法：${语句}

empty判断是否为null，长度是否为0              ${empty  list}

jsp中默认支持EL表达式，如果需要忽略则有两种方法：

1. 在page指令里设置isELignore属性
2. 在${}前加\

获取值

1. 从指定的域对象中获取相应的值
2. ${键名}，从小到大依次在域对象中寻找对应的键值对
3. 获取对象：本质就是调用getter方法，可自定义

11个隐士对象：

1. pageContext：可以获得其他8个内置对象

2. pageScope：获取pageContext域中的数据

3. requestScope：获取request中的数据

4. sessionScope：获取session中的数据

5. applicationScope：获取servletContext中的数据

6. param：获取请求参数的值

7. paramValues：获取请求参数的值。获取多个值的时候使用

8. header：可以获取请求头的信息

9. headerValues：可以获取请求头的信息，获取多个值的时候使用

10. cookie：可以活的当前请求的cookie信息

11. initParam：可以获取web.xml中的上下文参数 <context - param>

    ${pageeContext.request.getContextPath}：获取虚拟目录

注：使用.运算时，其中有特殊字符及可以使用[""]代替

~~~java
System.out.println("get");
System.out.println("获取请求协议："+req.getScheme());
System.out.println("获取ip地址或域名："+req.getServerName());
System.out.println("获取端口号："+req.getServerPort());
System.out.println("获取当前工程路径："+req.getContextPath());
System.out.println("获取请求方式："+req.getMethod());
System.out.println("获取客户端IP："+req.getRemoteHost());
获取请求协议：http
获取ip地址或域名：127.0.0.1
获取端口号：8080
获取当前工程路径：/javaweb01
获取请求方式：GET
获取客户端IP：127.0.0.1
~~~



## JSTL

使用步骤：

1. 导入jar包

2. 引入标签库

   ~~~java
   <%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
   ~~~

3. 使用标签

常用标签：

- if

  ~~~java
  <c:if test="${6>5}">
      hello
  </c:if>//没有eles一说
  ~~~

- choose

  ~~~java
  <c:choose>
      <c:when test="${requestScope.number==1}">
          星期一
      </c:when>
      <c:otherwise>
          <c:choose>
              <c:otherwise>
                  星期二
              </c:otherwise>
  		</c:choose>
      </c:otherwise>
  </c:choose>
  ~~~

- foreach

  ~~~java
  <c:forEach var="a" items="${requestScope.list}">
      ${a}
  </c:forEach>
  ~~~



## 过滤器Filter

### 步骤：

1. 实现接口

2. 重写方法

3. 配置路径

   ~~~java
   @WebFilter(urlPatterns = "/*",dispatcherTypes = {DispatcherType.FORWARD})
   public class demoFilter implements Filter {
       @Override
       public void init(FilterConfig filterConfig) throws ServletException {
   		//服务器启动后，创建filter对象，自动调用初始化方法
       }
   
       @Override
       public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
           //每一次请求被拦截资源，调用此方法
           System.out.println("过滤器");
           filterChain.doFilter(servletRequest, servletResponse);
       }
   
       @Override
       public void destroy() {
   		//服务器关闭后，filter对象销毁，如果是正常关闭，则调用destroy
       }
   }
   ~~~

### 细节

1. 过滤器执行流程

2. 生命周期方法

   1. 构造器方法

   2. init初始化方法

      注：在web工程启动后就执行了上面2个方法

   3. dofilter方法

   4. destroy方法：停止web工程时调用

3. 配置详解

   拦截路径配置：

   - 具体资源路径：/index.jsp
   - 拦截目录：/user/*
   - 后缀名拦截：*.jsp
   - 拦截所有资源：/*

   拦截方式配置：

   - 注解配置

     设置dispatcherTypes属性

     - request：默认值，浏览器直接访问资源
     - forward：转发访问资源
     - include：包含访问资源
     - error：错误跳转资源
     - async：异步访问资源

   - web.xml配置

     ~~~xml
     <filter-mapping>
         <filter-name>demoFilter</filter-name>
         <url-pattern>/*</url-pattern>
         <dispatcher>FORWARD</dispatcher>
     </filter-mapping>
     ~~~

     

4. 过滤器链（多个过滤器）

   执行顺序：有两个过滤器1和过滤器2

   ​		过滤器1->过滤器2->资源执行->过滤器2->过滤器1

   先后顺序

   ​		注解配置：按照类名，字符串的比较规则比较，值小限制性

   ​		web.xml配置：谁定义在上边谁先执行

FilterConfig类

1. 获得filter名称
2. 获取在filter中配置的initparam参数
3. 获取servletContext对象



## Listener

作用：监听某种事物的变化，通过回调函数，反馈给程序做出相应的变化

#### ServletContextListener

使用步骤：

1. 编写一个类实现ServletContextListener
2. 实现两个回调方法
3. web.xml配置







~~~XML
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>3.0.1</version>
</dependency>
<dependency>
    <groupId>javax.servlet.jsp</groupId>
    <artifactId>jsp-api</artifactId>
    <version>2.1</version>
</dependency>
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>jstl</artifactId>
    <version>1.2</version>
</dependency>
<dependency>
    <groupId>commons-io</groupId>
    <artifactId>commons-io</artifactId>
    <version>2.5</version>
</dependency>
<dependency>
    <groupId>com.itxiaoer.commons</groupId>
    <artifactId>commons-upload</artifactId>
    <version>2.3.4</version>
</dependency>
~~~



## 文件上传

步骤

1. 有一个表单，方法设置为post
2. form表单的标签encType属性值必须为multipart/form-data
3. 在form标签中使用input type=file添加上传的文件
4. 编写服务器代码接受文件

multipart/form-data表示提交的数据以多段的形式拼接，然后以二进制流的形式发送给服务器

![image-20201107171452205](/home/victor/.config/Typora/typora-user-images/image-20201107171452205.png)



## ThreadLocal

可以解决多线程中的数据安全问题

ThreadLOcal可以像map一样存取数据，只是他的键是当前线程，可以来实现数据库事物的管理，因为想要几个操作具有事物必须是同一个数据库链接connection对象

![image-20201108110129514](/home/victor/.config/Typora/typora-user-images/image-20201108110129514.png)







