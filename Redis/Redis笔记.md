## 1.认识Redis
是一个C语言编写的基于内存的键值型NoSQL数据库
#### 1-1.特征
（1）键值型，value支持多种不同的数据结构
（2）单线程，每个命令具有原子性
（3）低延迟，速度快（基于内存、io多路复用、良好的编码）
（4）支持数据持久
（5）支持主从集群、分片集群
## 2.基础篇
#### 2-1.安装：
（1）wget https://download.redis.io/releases/redis-6.2.6.tar.gz
（2）解压，tar zxvf redis-6.2.6.tar.gz
（3）安装，make && make install
#### 2-2.配置配置文件启动
（1）redis.conf中设置 daemonize yes
（2）redis-server redis.conf
#### 2-3.连接
（1）redis-cli -p IP地址 -h 端口 -a 密码

#### 2-4.通用命令
（1）keys [pattern]：查看符合模板的所有key,不建议在生产设备上使用；例：keys \*name\*
（2）del \[key ...\]：删除key
（3）exists \[key\]：判断可以是否存在
（4）expire \[key\] \[seconds\]：设置key的有效时间，到期后删除该key
（5）ttl [key]：查看key的有效时间
#### 2-5.Redis数据结构
Redis是一个key-value的数据库，key一般为String类型，value由八种数据类型
redis没有数据表的概念，我们可以用 <mark style="background: #BBFABBA6;">项目名:业务名:类型:id</mark> 的方式区分不同类型的key，redis的key允许由多个单词构成层级关系，多个单词间用 ":" 隔开
##### 2-5-1.String类型
根据字符串格式不同，又可分为三类，但底层都是<mark style="background: #BBFABBA6;">字节数组</mark>形式存储，只不过编码格式不同
（1）String：普通字符串，最大空间不能超过<mark style="background: #BBFABBA6;">512m</mark>
（2）int：整型，可自增、自减
（3）float：浮点型，可自增、自减

###### 2-5-1-1 常见命令
set：添加或修改一个String类型的键值对
get：根据key获取String类型的value
mset：批量添加
mget：批量获取
incr：整形自增
incrby：自增并指定步长
incrbyfloat：浮点型自增并指定步长
setnx：添加一个String类型的键值对，前提是key不存在，否则不执行
setex：添加一个String类型的键值对，并指定有效期

##### 2-5-2.Hash类型
hash类型，也叫散列，其vakue是一个无序字典，类似于Java中的HashMap；Hash结构可以将对象中的每个字段独立存储，可以针对单个字段做CRUD

###### 2-5-2-1 常见命令
hset key field value：添加或修改Hash类型key的field的值
hget key field：获取key的field的值
hmset：批量添加多个key的field的值
hmget：批量获取多个key的field的值
hgetall：获取key中所有的field和value
hkeys：获取key的所有field
hvals：获取key的所有value
hincrby：是key的field的值自增并指定步长
hsetnx：给key添加一对field、value，前提是field不存在

##### 2-5-3 List类型
Redis中的List和Java中的LinkedList类型，可以将看作一个双向链表（复杂一些），<mark style="background: #BBFABBA6;">支持正向和反向检索</mark>
###### 2-5-3-1 常见命令
lpush key element...：向链表左侧插入一个或多个元素
lpop key：移除链表左侧元素，没有则返回nil
rpush key element...：向链表右侧插入一个或多个元素
rpop key：移除链表右侧元素
lrange key start end：返回一段角标范围内所有元素
blpop和brpop：和lpop、rpop类似，只不过在没有元素时指定等待时间，而不是直接返回nil

##### 2-5-4.Set类型
Redis的Set结构和Java中HashSet类似，可以看作一个没有value的HashMap。因为也是一个<mark style="background: #BBFABBA6;">Hash表</mark>，因此也具有HashSet类似的特征：<mark style="background: #BBFABBA6;">无需、不可重复、查找快、支持交集 并集 差集</mark>等功能。

###### 2-5-4-1.常见命令
sadd key member...：向key中添加成员
srem key member...：移除key中指定成员
scard key：返回key中成员个数
sismember key member：判断member是否存在key中
smembers key：获取key中所有元素
sinter key1 key2：求两个集合的交集
sdiff key1 key2：求两个集合的差集
sunion key1 key2：求两个集合并集

##### 2-5-5.SortedSet类型
Redis中SortedSet是一个可排序的Set集合，和Java中的TreeSet有些类似，但底层实现完全不同，SortedSet每一个元素都带一个score属性，可以基于score对元素排序，底层实现是一个<mark style="background: #BBFABBA6;">跳表</mark>加<mark style="background: #BBFABBA6;">Hash表</mark>

###### 2-5-5-1.常见命令
zadd key score member：添加一个元素，如果该元素已存在则修改其score
zrem key member：移除一个元素
zscore key member：查看元素的score
zrank key member：获取指定元素的排名
zcard key：获取集合中的元素个数
zcount key min max：获取score在指定范围内的元素个数
zincrby key increment member：元素自增，步长为increment
zrange key min max：按照score排序后，获取排名在指定范围内的元素
zrangebyscore key min max：获取score在指定范围内的元素
zinter、zdiff，zunion：交、差、并集
注：所有排名为升序，降序在后面加参数 rev



#### 2-6.jedis 
##### 2-6-1.jedis快速入门
（1）导入坐标
```xml
<dependency>  
    <groupId>redis.clients</groupId>  
    <artifactId>jedis</artifactId>  
    <version>3.7.0</version>  
</dependency>
```
（2）基本使用
```java
// 建立连接
jedis = new Jedis("192.168.100.11",6379);  
// 密码
jedis.auth("123456");  
// 选择数据库
jedis.select(0);
// 添加String类型的值
jedis.set("name","楚子航")
//关闭连接
jedis.close();
```
##### 2-6-2.jedis 连接池
```java
public class JedisConnectionFactory {  
    static class pool{  
        private static JedisPool jedisPool;  
        static {
	        // jedis连接池配置文件
            JedisPoolConfig jedisPoolConfig = new JedisPoolConfig();  
            jedisPoolConfig.setMaxTotal(8);  
            jedisPoolConfig.setMinIdle(0);  
            jedisPoolConfig.setMinIdle(8);  
            jedisPoolConfig.setMaxWaitMillis(2000);  
            // 创建连接池对象
            jedisPool = new JedisPool(jedisPoolConfig,"192.168.100.11",6379);  
        }  
    }  
    public static JedisPool getInstance(){  
        return pool.jedisPool;  
    }  
}
```


#### 2-7.SpringDataRedis
SpringData是Spring中数据操作的模块，包含对各种数据库的继承，其中对Redis的集成模块叫SpringDataRedis
##### 2-7-1.快速入门
（1）导入坐标
```xml
<dependency>  
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-starter-data-redis</artifactId>  
</dependency>
// 实现对象池化组件
<dependency>  
    <groupId>org.apache.commons</groupId>  
    <artifactId>commons-pool2</artifactId>  
</dependency>
```
（2）配置
```yaml
spring:  
  redis:  
    host: 192.168.100.11  
    port: 6379  
    password: 123456  
    database: 0  
    lettuce:  
      pool:  
        max-active: 8  
        max-idle: 8  
        min-idle: 0  
        max-wait: 3000
```
（3）注入RedisTemplate并使用，RedisTemplate提供了操作Redis的各种API
```java
@Autowired  
private RedisTemplate<String,String> redisTemplate;
```
##### 2-7-1.RedisSerializer
默认jdkSerializationRedisSerializer(转成字节，不好)
（1）自定义序列化去
```java
@Configuration  
public class RedisConfig {  
  
    @Bean(name = "redisTemplate")  
    public RedisTemplate<String,Object> redisTemplate(RedisConnectionFactory connectionFactory){  
        RedisTemplate<String, Object> template = new RedisTemplate<>();  
        // 设置连接工厂  
        template.setConnectionFactory(connectionFactory);  
  
        // 序列化器  
        GenericJackson2JsonRedisSerializer genericJackson2JsonRedisSerializer = 
			new GenericJackson2JsonRedisSerializer();  
  
        // 设置key和value的序列化器  
        template.setKeySerializer(RedisSerializer.string());  
        template.setValueSerializer(genericJackson2JsonRedisSerializer);  
        return template;  
    } 
}
```