# Nginx

### 命令

首先进入到nginx目录中的sbin目录中，随后执行命令

~~~nginx
查看版本号
./nginx -v

启动
./nginx

关闭
./nginx -s stop

重新加载Nginx
./nginx -s reload
~~~



### 配置文件

1. 全局块

   ~~~nginx
   从配置文件开始到events块之间的内容，主要设置一些影响nginx服务器整体运行的配置明命令
   worker_processes  1;表示越大处理的并发请求越多，但是也会收到硬件的限制
   ~~~

2. events块

   ~~~nginx
   主要影响Nginx服务器与用户的网络连接
   worker_connections  1024;支持最大连接数量
   ~~~

3. http块

   ~~~nginx
   配置最频繁的部分，http块可以包含http全局块和server块
   ~~~



### 反向代理

#### 实例1

效果1：打开www.123.com然后跳转到linux中的tomcat

~~~shell
#首先防火墙开放8080端口
firewall-cmd --zone=public --add-port=8080/tcp --permanent

#重启防火墙使之生效
firewall-cmd --reload

firewall-cmd --state # 显示防火墙状态

systemctl start  firewalld # 启动

systemctl status firewalld # 或者 firewall-cmd --state 查看状态

systemctl disable firewalld # 停止

systemctl stop firewalld  # 禁用
~~~

在windows系统中的host文件中配置域名与ip地址的对应关系，加入192.168.1.11	www.123.com

![](C:\Users\victor\learn\学习资料\Nginx\img\Snipaste_2020-12-30_23-18-12.png)



在nginx.conf配置文件中配置

![](C:\Users\victor\learn\学习资料\Nginx\img\Snipaste_2020-12-31_19-52-42.png)

如果没有实现效果关闭防火墙即可

效果

![](C:\Users\victor\learn\学习资料\Nginx\img\Snipaste_2020-12-31_21-23-53.png)

#### 实例2

效果2：根据不同路径跳转到不同端口（8080与8081）的tomcat中去，

如：访问192.168.1.11:9001/edu/   则直接跳转到127.0.0.1:8080

​		访问192.168.1.11:9001/vob/   则直接跳转到127.0.0.1:8081

​		因为hosts文件中配置了www.123.com与192.168.1.11的对应关系所以访问www.123.com:9001/edu/也是一     		样的道理



首先准备两个tomcat并启动，在webapps中创建文件夹和测试文件

配置nginx.conf配置文件

![](C:\Users\victor\learn\学习资料\Nginx\img\Snipaste_2020-12-31_21-16-54.png)

效果

![](C:\Users\victor\learn\学习资料\Nginx\img\Snipaste_2020-12-31_21-22-56.png)

![](C:\Users\victor\learn\学习资料\Nginx\img\Snipaste_2020-12-31_21-23-03.png)



### 负载均衡



### 动静分离