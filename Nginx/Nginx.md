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

   