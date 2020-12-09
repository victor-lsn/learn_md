# Redis

## 存储结构

1. 多文本类信息：MongoDB
2. 基本不经常改变信息：MySQL
3. 图片信息：分布式文件系统，Hadoop
4. 商品关键字：ES
5. 波段性高频信息：Redis

## CAP

1. C：强一致性

2. A：可用性

3. P：分区容错性

   注：这三个要素最多只能同时实现两点，不可能三者兼顾。AP是大多数网站架构的选择，CA传统数据库，CP：Redis，Mongodb



~~~shell
sudo redis-server /etc/redis/redis6380.cof
sudo redis-cli -p 6380


sudo service redis start
sudo service redis stop
sudo service redis restart
sudo systemctl status redis

redis-cli  进入redis
sudo gedit /etc/redis/redis.conf 打开配置文件

select 1 选择第二个库
dbsize   存储的数量
keys *   查看所有的key

flushdb    删除当前库所有的key
flushAll    删除所有库的所有数据

EXISTS  判断key是否存在
move test01 2   把这个数据移动到2库

expire test01 10    设置数据的存活时间
type  test01   查看数据表类型	
ttl   test01    查看过期时间

del test01   删除数据
~~~



## 五大数据类型

1. String（字符串）
2. Hash（类似于hashmap）
3. List（列表）
4. Set（集合）
5. Zset（sorted set）

### String

~~~shell
set k1 v1   设置值
append k1 23455    追加
get k1    获取值
strlen k1   长度
incr k2    增加1
DECR k2     减少1

INCRBY k2 4    增加4
DECRBY k2 4    减少4
GETRANGE k1 0 1    查看下标为0到1的数值
SETRANGE k1 0 xx   在0这个起始位置设置值为xx

SETEX k4 10 v4    设置k4的过期时间为10秒
SETNX k1 1111    如果没有k1则设置值

MSET k6 v6 k7 v7 k8 v8   设置多个值
MGET k6 k7 k8		读取多个值

MSETNX     如果一个存在不成功则全都不成功
~~~



### List

~~~shell
 LPUSH k1 0 1 2 3 4 5     添加list类型数据，正着进反着出
 LRANGE k1 0 -1       取出当前key所有数据
 RPUSH k2 1 2 3 4 5      正着进去正着出来
 
LPOP k2    出最顶的
RPOP k2    出最底的

LINDEX k2 0     从上到下第一个
llen k2    长度
LREM k2 1 2			删除一个2
LTRIM k2 0 1       截取0到1位置的值，并重新赋给k2

RPOPLPUSH k1 k2 	 k1出一个k2进一个
lset  k1  1   3    把k1的1位置设置为3

LINSERT k1 before x  java   在x面前插入java
~~~



### Set

~~~shell
SADD k1 0 1 1 1 			设置值
SMEMBERS k1				读取值
SISMEMBER k1 1      判断1是否存在于k1中

SCARD k1            获取集合中的个数
SREM k1 1				删除

SRANDMEMBER k1 2		随机出现两个集合中的值
SPOP k1 2					随机出两个

SMOVE k1 k2 1       把k1的1赋值给k2

交集：sinter
并集：sunion
差集：sdiff
~~~



### Hash

~~~shell
HSET user id 1234
hget user id
HMSET student id 123 name victor age 20        设置多个
HMGET student id name age			读取多个
HGETALL student				获取所有键值
HDEL user id				删除

HLEN student				长度
HEXISTS student name   			判断是否存在
HKEYS student			获取所有key
HVALS student  			获取所有值

HINCRBY student age 1				在int值的基础上加1
HINCRBYFLOAT student score 0.6			在小数2的基础上加小数
HSETNX student age 20				如果不存在再设置值
~~~



### ZSet

~~~shell
ZADD zset01 70 v2 80 v3				
ZRANGE zset01 0 -1
ZRANGE zset01 0 -1 withscores		连分数一起搜索出来
ZRANGEBYSCORE zset01 70 80				设置区间搜索范围
ZRANGEBYSCORE zset01 70 (80				不包含80
ZRANGEBYSCORE zset01 70 (80 limit	2 2     		从2的基础上截取两个

ZREM zset01 v1				移出v1
 ZCARD zset01			统计个数
 
 ZCOUNT zset01 60 80			统计这个分数范围的个数
 ZRANk zset01 v2					拿到这个值得下标
 ZSCORE zset01 v2					拿到分数
 ZREVRANk zset01 v2				拿到逆序下标
 ZREVRANGE zset01 0 -1			逆序输出
 
 ZREVRANGEBYSCORE zset01 70 60

~~~



### GEO

~~~shell
127.0.0.1:6379> GEOADD china:city 116.40 39.90 beijing				添加数据
(integer) 1
127.0.0.1:6379> GEOADD china:city 121.47 31.23 shanghai

GEOPOS china:city beijing  					获取
GEODIST china:city beijing shanghai m		计算两地距离多少m
	
~~~



### hyperloglog

统计用户信息，活跃，不活跃

~~~shell
PFADD mykey1 a b c d e f g				添加
PFCOUNT mykey2							统计个数
PFMERGE mykey3 mykey1 mykey2				合并


~~~



### Bitmaps

~~~shell
127.0.0.1:6379> SETBIT sign 0 1				设置第一天打卡
(integer) 0
127.0.0.1:6379> SETBIT sign 1 0				设置第二天不打卡

 GETBIT sign 1									查看第一天有没有打卡
 
BITCOUNT sign 				查看总共有多少个1

~~~



## 解析配置文件





## 持久化

### RDB

Redis会单独创建（fork）一个子进程来进行持久化，将数据写入一个临时文件中，持久化都结束了，在用这个文件替换上次持久化的文件。整个过程中个主进程不进行IO操作，确保了极高的性能，如果需要进行大规模数据的恢复，切对于数据恢复的完整性不是非常敏感，RDB就比AOF更加的高效，RDB的缺点就是最后一次持久化后的数据可能丢失

- fork：复制一个与当前进程一模一样的进程，所有都与原进程一致，但是是一个全新的进程，并作为原进程的子进程
- 使用save命令可以立即备份或者bgsave进行后台备份

- 优势：适合大规模数据恢复，对数据完整性和一致性要求不高
- 劣势：意外挂掉，最后一次备份不到，fork会备份一模一样的浪费内存空间

### AOF

以日志的形式来记录每个写操作，将Redis执行过的写操作都记录下来，只需追加不许更改，redis启动之初读取该文件重新构建数据，也就是说重启redis就是根据日志文件中的操作重新执行一遍来进行数据的恢复工作

rewrite：auto-aof-rewrite-percentage 100

​				auto-aof-rewrite-min-size 64mb

- 优势：每秒同步（默认）、每修改同步、不同步
- 劣势：越来越大，运行速率慢于RDB



注：.rdb与.aop可以同时存在，redis会使用.aof，如果aof损坏则redis启动不了，可以使用redis-check-aof  --fix  appendonly.aof来进行修复

一般RDB做后备用途，只要15分钟备份一次就够了，只保留 save 900 1 这条规则即可



## 事务

### 命令：

1. DISCARD：取消事务，放弃执行事务块内的所有命令
2. EXEC：执行所有事务块内的命令
3. MULTI：标记一个事务块的开始
4. UNWATCH：取消WATCH命令堆key的监控
5. WATCH key：监视一个或多个key，如果在事务执行之前这个key被其他命令改动，那么事务将被打断

### 乐观锁

锁表，每次操作数据都要上锁，性能低

### 悲观锁

很乐观，每次拿数据都认为不会修改，所以不上锁，但是在更新时会确认有没有别人进行修改过，可以使用版本号机制，适用于多读的应用类型，提高吞吐量（一般使用）



## 发布订阅

~~~shell
SUBSCRIBE c1 c2 c3              一次性订阅多个
SUBSCRIBE c*						可以用通配符
PUBLISH c1 hello					发布消息

1) "message"
2) "c1"
3) "hello"         收到消息
~~~



## 主从复制

~~~shell
info replication		查看状态
SLAVEOF 127.0.0.1 6379    

ps -ef|grep redis

SLAVEOF no one        反客为主，从机变为主机


哨兵模式:自动反客为主
主机挂了就投票自动产生新的主机，当之前的主机回来也会编程新主机的从机

创建sentinel.conf文件
sentinel monitor host6379 127.0.0.1 6379 1

~~~

注：从机与主机断开需要重新连接



## SpringBoot整合

### pom.xml

~~~xml
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
~~~

### application.yaml

~~~yaml
redis:
  database: 0
  host: 127.0.0.1
  port: 6379
  lettuce:
    pool:
      max-active: 8
      max-idle: 10
      max-wait: -1
      min-idle: 2
    shutdown-timeout: 6000
~~~

### 自定义配置类解决终端乱码问题

~~~java
@Configuration
public class RedisConfig {

    @Bean
    public RedisTemplate redisTemplate(RedisConnectionFactory redisConnectionFactory){
        RedisTemplate<Object, Object> redisTemplate = new RedisTemplate<>();

        redisTemplate.setConnectionFactory(redisConnectionFactory);

        Jackson2JsonRedisSerializer<Object> jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer<>(Object.class);

        ObjectMapper mapper = new ObjectMapper();
        mapper.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);

        mapper.activateDefaultTyping(LaissezFaireSubTypeValidator.instance,ObjectMapper.DefaultTyping.NON_FINAL);
        jackson2JsonRedisSerializer.setObjectMapper(mapper);


        redisTemplate.setValueSerializer(jackson2JsonRedisSerializer);
        redisTemplate.setKeySerializer(new StringRedisSerializer());


        redisTemplate.setHashKeySerializer(new StringRedisSerializer());
        redisTemplate.setHashValueSerializer(jackson2JsonRedisSerializer);


        return redisTemplate;
    }
}
~~~

### 测试

~~~java
@Autowired
    @Qualifier("redisTemplate")
    private RedisTemplate redisTemplate;

    @Test
    void contextLoads() {
        //操作数据库
        /*RedisConnection connection = redisTemplate.getConnectionFactory().getConnection();
        connection.flushDb();*/


        User victor = new User("1234", "rita");
        redisTemplate.opsForValue().set("user6",victor);

        Object user = redisTemplate.opsForValue().get("user6");
        System.out.println(user);
    }
~~~



























