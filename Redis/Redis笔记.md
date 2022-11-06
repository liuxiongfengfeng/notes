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
（1）keys pattern：查看符合模板的所有key,不建议在生产设备上使用；例：keys \*name\*
（2）del key ...：删除key
（3）exists key：判断可以是否存在
（4）expire key seconds：设置key的有效时间，到期后删除该key
（5）ttl key：查看key的有效时间
#### 2-5.Redis数据结构
Redis是一个key-value的数据库，key一般为String类型，value由八种数据类型
redis没有数据表的概念，我们可以用 <mark style="background: #BBFABBA6;">项目名:业务名:类型:id</mark> 的方式区分不同类型的key，redis的key允许由多个单词构成层级关系，多个单词间用 ":" 隔开
##### 2-5-1.String类型
根据字符串格式不同，又可分为三类，但底层都是<mark style="background: #BBFABBA6;">字节数组</mark>形式存储，只不过编码格式不同
（1）String：普通字符串，最大空间不能超过<mark style="background: #BBFABBA6;">512m</mark>
（2）int：整型，可自增、自减
（3）float：浮点型，可自增、自减

###### 2-5-1-1 常见命令
```redis
set：添加或修改一个String类型的键值对

get：根据key获取String类型的value

mset：批量添加

mget：批量获取

incr：整形自增

incrby：自增并指定步长

incrbyfloat：浮点型自增并指定步长

setnx：添加一个String类型的键值对，前提是key不存在，否则不执行

setex：添加一个String类型的键值对，并指定有效期
```
##### 2-5-2.Hash类型
hash类型，也叫散列，其vakue是一个无序字典，类似于Java中的HashMap；Hash结构可以将对象中的每个字段独立存储，可以针对单个字段做CRUD

###### 2-5-2-1 常见命令
```redis
hset key field value：添加或修改Hash类型key的field的值

hget key field：获取key的field的值

hmset：批量添加多个key的field的值

hmget：批量获取多个key的field的值

hgetall：获取key中所有的field和value

hkeys：获取key的所有field

hvals：获取key的所有value

hincrby：是key的field的值自增并指定步长

hsetnx：给key添加一对field、value，前提是field不存在

hexists key field：判断field是否存在
```

##### 2-5-3 List类型
Redis中的List和Java中的LinkedList类型，可以将看作一个双向链表（复杂一些），<mark style="background: #BBFABBA6;">支持正向和反向检索</mark>
###### 2-5-3-1 常见命令
```redis
lpush key element...：向链表左侧插入一个或多个元素

lpop key：移除链表左侧元素，没有则返回nil

rpush key element...：向链表右侧插入一个或多个元素

rpop key：移除链表右侧元素

lrange key start end：返回一段角标范围内所有元素

blpop和brpop：和lpop、rpop类似，只不过在没有元素时指定等待时间，而不是直接返回nil
```

##### 2-5-4.Set类型
Redis的Set结构和Java中HashSet类似，可以看作一个没有value的HashMap。因为也是一个<mark style="background: #BBFABBA6;">Hash表</mark>，因此也具有HashSet类似的特征：<mark style="background: #BBFABBA6;">无需、不可重复、查找快、支持交集 并集 差集</mark>等功能。

###### 2-5-4-1.常见命令
```redis
sadd key member...：向key中添加成员

srem key member...：移除key中指定成员

scard key：返回key中成员个数

sismember key member：判断member是否存在key中

smembers key：获取key中所有元素

sinter key1 key2：求两个集合的交集

sdiff key1 key2：求两个集合的差集

sunion key1 key2：求两个集合并集
```

##### 2-5-5.SortedSet类型
Redis中SortedSet是一个可排序的Set集合，和Java中的TreeSet有些类似，但底层实现完全不同，SortedSet每一个元素都带一个score属性，可以基于score对元素排序，底层实现是一个<mark style="background: #BBFABBA6;">跳表</mark>加<mark style="background: #BBFABBA6;">Hash表</mark>

###### 2-5-5-1.常见命令
```redis
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
```

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
（1）自定义序列化器
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
（2）StringRedisTemplate
使用上面的序列化器可以实现自动的序列化和反序列化，但是会存入一个class的全路径（占用空间），所以一般我们手动序列化和反序列化

## 3.缓存
缓存就是数据交换的缓冲区，是存储数据的临时地方，读写性能高
#### 3-1.缓存的作用与成本
##### 3-1-1.作用
（1）降低后端负载
（2）提高读写效率，降低响应时间
##### 3-1-2.成本
（1）数据一致性成本
（2）代码维护成本
（3）运维成本
#### 3-2.缓存更新策略
![[缓存更新策略.png]]
先更新数据库再删缓存
#### 3-3.缓存穿透
缓存穿透是指客户端请求的数据在缓存和数据库中都不存在，这样缓存永远不会生效，这些请求都会到达数据库
##### 3-3-1.常见方案
（1）缓存空对象
优点：实现简单
```java
private Shop queryWithPenetrate(Long id) {  
    String key = RedisConstants.CACHE_SHOP_KEY + id;  
    String cacheShopJson = stringRedisTemplate.opsForValue().get(key);  
    if (StrUtil.isNotBlank(cacheShopJson)){  
        return JSONUtil.toBean(cacheShopJson, Shop.class);  
    }  
  
    if (cacheShopJson != null){  
        return null;  
    }  
  
    Shop shop = shopMapper.selectById(id);  
    if (shop == null){  
        stringRedisTemplate.opsForValue().set(key,"",Duration.ofMinutes(RedisConstants.CACHE_NULL_TTL));  
        return null;    }  
    String shopJson = JSONUtil.toJsonStr(shop);  
    stringRedisTemplate.opsForValue().set(key,shopJson);  
    stringRedisTemplate.expire(key, Duration.ofMinutes(RedisConstants.CACHE_SHOP_TTL));  
    return shop;  
}
```
缺点：额外的内存消耗，短期的不一致
（2）布隆过滤
优点：内存占用少，无多余key
缺点：实现复杂，存在误判

#### 3-4.缓存雪崩
缓存雪崩是指，同一时段大量缓存的key同时失效或Redis服务宕机，导致大量请求到达数据库，到来巨大压力。
##### 3-4-1.解决方案
（1）给不同的key设置随机的TTL
（2）利用Redis集群提高服务的可用性
（3）给缓存业务添加降级限策略
（4）添加多级缓存
#### 3-5.缓存击穿
缓存击穿也叫热点key问题，就是一个被高并发访问并且缓存重建比较复杂的key突然失效，大量的请求到达数据库，带来巨大压力
##### 3-5-1.解决方案
（1）互斥锁
```java
private Shop queryMutex(Long id){  
	String key = RedisConstants.CACHE_SHOP_KEY + id;  
	String cacheShopJson = stringRedisTemplate.opsForValue().get(key);  
	if (StrUtil.isNotBlank(cacheShopJson)){  
		Shop shop = JSONUtil.toBean(cacheShopJson, Shop.class);  
		return shop;  
	}  

	if (cacheShopJson != null){  
		return null;  
	}
	String lockKey = RedisConstants.LOCK_SHOP_KEY + id;  
	boolean isLock = isLock(lockKey);  
	Shop shop = null;  
	try {  
//        没获取到锁  
		if (!isLock){  
			Thread.sleep(50);  
			return queryMutex(id);  
		}  
		shop = shopMapper.selectById(id);  
		if (shop == null){ stringRedisTemplate.opsForValue().set(key,"",Duration.ofMinutes(RedisConstants.CACHE_NULL_TTL));  
			return null;           
		 }  
		String shopJson = JSONUtil.toJsonStr(shop);  
		stringRedisTemplate.opsForValue().set(key,shopJson);  
		stringRedisTemplate.expire(key, Duration.ofMinutes(RedisConstants.CACHE_SHOP_TTL));  
		}catch (InterruptedException e){  
			throw new RuntimeException(e);  
		}finally {  
			//        释放锁  
			unLock(lockKey);  
		}  
	return shop;  
}
//   是否获取到锁  
    private boolean isLock(String key){  
        Boolean flag = stringRedisTemplate.opsForValue().setIfAbsent(key, "1");  
        return BooleanUtil.isTrue(flag);  
    }  
  
//    释放锁  
    private void unLock(String key){  
        stringRedisTemplate.delete(key);  
    }
```
（2）逻辑过期：不添加TTL，在逻辑上添加一个逻辑过期时间字段
```java
//需首先將數據緩存到内存中
private Shop queryWithLogicExpire(Long id) {  
	String key = RedisConstants.CACHE_SHOP_KEY + id;  
	String cacheShopJson = stringRedisTemplate.opsForValue().get(key);  
//        未命中返回空  
	if (StrUtil.isBlank(cacheShopJson)){  
		return null;  
	}  
	RedisData redisData = JSONUtil.toBean(cacheShopJson, RedisData.class);  
	Shop cacheShop = JSONUtil.toBean((JSONObject) redisData.getData(), Shop.class);  
//        未过期直接返回  
	if (redisData.getExpireTime().isAfter(LocalDateTime.now())){  
		return cacheShop;  
	}  

	String lockKey = RedisConstants.LOCK_SHOP_KEY+id;  
	boolean isLock = tryLock(lockKey);  

	if (isLock){  
		//获取到锁，加载数据到缓存中  
		CACHE_THREAD_POOL.submit(() -> {  
			try {  
				if (redisData.getExpireTime().isBefore(LocalDateTime.now())){  
					log.debug("重建緩存");  
					saveShop2Redis(id,20L);  
				}  
			}catch (Exception e){  
				throw new RuntimeException(e);  
			}finally {  
				unLock(lockKey);  
			}  
		});  
	}  
	//未获取到锁，直接返回缓存数据  
	return cacheShop;  
}
```


## 4.利用Redis生成全局唯一ID
```java
public class RedisIdWorker {  
    private static final long BEGIN_TIMESTAMP = 1640995200L;  // 时间戳起始时间
    private static final short COUNT_BITS = 32;  // 偏移位数
  
    private StringRedisTemplate stringRedisTemplate;  
    public RedisIdWorker(StringRedisTemplate stringRedisTemplate){  
        this.stringRedisTemplate = stringRedisTemplate;  
    }  
    /**  
     * @param keyPrefix  
     * @return  
     * 获取唯一id  
     * 前31位为时间戳  
     * 后32为
     * 
     * 
     * 
     * 用redis自增  
     */  
    public long nextId(String keyPrefix){  
        LocalDateTime now = LocalDateTime.now();  
        long nowSecond = now.toEpochSecond(ZoneOffset.UTC);  
        long timeStamp = nowSecond - BEGIN_TIMESTAMP;  
        String format = now.format(DateTimeFormatter.ofPattern("yyyy:MM:dd"));  
        String key = keyPrefix + format;  
        Long increment = stringRedisTemplate.opsForValue().increment(key);  
        return timeStamp << COUNT_BITS | increment;  
    }  
}
```
## 5.超卖问题
超卖问题是典型的线程安全问题，针对这一问题的常见解决方案是加锁
![[悲观锁和乐观锁.png]]
#### 5-1.乐观锁
乐观锁的关键在于判断之前的数据有没有被修改过，常见的实现方式有两种：
##### 5-1-1.版本号法
给表添加一个version字段，每次修改表的时候version + 1
```sql
set stock = stock - 1
	version = version + 1
where id = [id]
	  and version = [上一次查出来的version值]
```
##### 5-1-2.CAS法(compare and swap)
直接用数据作为更新条件
```sql
set stock = stock - 1
where id = [id]
	  and stock > 0
```

## 6.单体项目一人一单问题
#### 6-1.单体项目解决方案
集群模式下无效，因为有多个jvm，多个方法区，多个锁监视器
```java
@Override  
public Result seckillVoucher(Long voucherId) {  
    SeckillVoucher voucher = seckillVoucherService.getById(voucherId);  
    LocalDateTime now = LocalDateTime.now();  
    if (voucher.getBeginTime().isAfter(now)){  
        return Result.fail("活动未开始！");  
    }  
  
    if (voucher.getEndTime().isBefore(now)){  
        return Result.fail("活动已结束！");  
    }  
  
    if (voucher.getStock() < 1){  
        return Result.fail("库存不足！");  
    }  
  
    Long userId = UserHolder.getUser().getId();  
  
    synchronized (userId.toString().intern()){  
        IVoucherOrderService proxy = (IVoucherOrderService)AopContext.currentProxy();  
        return proxy.createVoucherOrder(userId,voucherId);  
    }  
}  
  
@Override  
@Transactional  
public Result createVoucherOrder(Long userId,Long voucherId){  
    Integer count = voucherOrderService.query()  
            .eq("user_id", userId)  
            .eq("voucher_id", voucherId)  
            .count();  
  
    if (count > 0){  
        return Result.fail("每人限购一张优惠卷！");  
    }  
  
    boolean update = seckillVoucherService.update()  
            .setSql("stock = stock - 1")  
            .eq("voucher_id", voucherId)  
            .gt("stock", 0)  
            .update();  
    if (!update){  
        Result.fail("库存不足！");  
    }  
    VoucherOrder voucherOrder = new VoucherOrder();  
    long orderId = idWorker.nextId(RedisConstants.ICR_ORDER_KEY);  
  
    voucherOrder.setId(orderId);  
    voucherOrder.setVoucherId(voucherId);  
    voucherOrder.setUserId(userId);  
    voucherOrderService.save(voucherOrder);  
    return Result.ok("恭喜你，抢到了优惠卷!");  
}
```

## 7.分布式锁
分布式锁：<mark style="background: #BBFABBA6;">满足再分布式系统或集群模式下多进程可见并且互斥的锁</mark>
#### 要求
（1）多进程可见
（2）互斥
（3）高性能
（4）高可用
（5）安全性
#### 7-1 使用Redis的setnx并设置过期时间
##### 7-1-1 简单版
存在问题：当业务中阻塞时，阻塞时间超过Redis设置的过期时间，锁提前释放，导致其他线程也能拿到锁
```java
@Component  
public class SimpleRedisLock implements ILock{  
  
    @Resource  
    private StringRedisTemplate stringRedisTemplate;  
  
    private String name = "lock:";  
    @Override  
    public boolean tryLock(String keyPrefix,long expireSecond) {  
        long id = Thread.currentThread().getId();  
        String key =  name + keyPrefix;  
        Boolean isLock = stringRedisTemplate  
                .opsForValue()  
                .setIfAbsent(key, Long.toString(id), expireSecond, TimeUnit.SECONDS);  
        return Boolean.TRUE.equals(isLock);  
    }  
  
    @Override  
    public void unlock(String keyPrefix) {  
        String key = name + keyPrefix;  
        stringRedisTemplate.delete(key);  
    }  
}
```

##### 7-1-2 解决上一版本误删锁问题
<mark style="background: #BBFABBA6;">问题出现原因</mark>：当任务阻塞时，阻塞时间可能超过锁的过期时间，此时别的线程可以拿到锁并处理业务，这时当前线程任务执行完毕并且销毁锁，就将别的线程的锁销毁了，此时其他的线程又可以拿到锁并执行任务，这时就出现了线程安全问题
<mark style="background: #BBFABBA6;">解决方案</mark>：获取锁时将锁的值设为<mark style="background: #BBFABBA6;">UUID加当前线程ID</mark>，释放锁时判断是不是当前线程的锁，是就释放锁，不是就不释放锁
```java
@Component  
public class SimpleRedisLock implements ILock{  
  
    @Resource  
    private StringRedisTemplate stringRedisTemplate;  
  
    private static final String ID_PREFIX = UUID.fastUUID().toString(true) + "-";  
    private long currentThreadID = Thread.currentThread().getId();  
    private String name = "lock:";  
    @Override  
    public boolean tryLock(String keyPrefix,long expireSecond) {  
        String key =  name + keyPrefix;  
        String value = ID_PREFIX + currentThreadID;  
        Boolean isLock = stringRedisTemplate  
                .opsForValue()  
                .setIfAbsent(key, value, expireSecond, TimeUnit.SECONDS);  
        return Boolean.TRUE.equals(isLock);  
    }  
  
    @Override  
    public void unlock(String keyPrefix) {  
        String key = name + keyPrefix;  
        String value = stringRedisTemplate.opsForValue().get(key);  
        if (value.equals(ID_PREFIX + currentThreadID)){  
            stringRedisTemplate.delete(key);  
        }  
    }  
}
```

##### 7-1-3 解决多条Redis命令的原子性问题
多条Redis命令存在原子性问题
Redis提供了Lua脚本功能，可在一个脚本中编写多条Redis命令，确保多条Redis命令的原子性
lua脚本

存在问题：
（1）不可重入：通过个线程不可重复获取锁
（2）不可重试：一旦获取锁失败，就不重试
（2）超时释放
（4）主从一致性
```lua
-- 分布式锁的key  
local key = KEYS[1]  
-- 分布式锁的值  
local thredID = ARGV[1]  
  
local id = redis.call("get",key)  
  
if(id == thredID)  
then  
    return redis.call("del",key)  
end  
  
return 0
```
java业务
```java
@Component  
public class SimpleRedisLock implements ILock{  
  
    @Resource  
    private StringRedisTemplate stringRedisTemplate;  
  
    private static final String ID_PREFIX = UUID.fastUUID().toString(true) + "-";  
//    当前线程id  
    private long currentThreadID = Thread.currentThread().getId();  
    private String name = "lock:";  
  
//    释放锁脚本对象  
    private static final DefaultRedisScript<Long> UNLOCK_SCRIPT;  
    static {  
        UNLOCK_SCRIPT = new DefaultRedisScript<>();  
        UNLOCK_SCRIPT.setLocation(new ClassPathResource("unlock.lua"));  
        UNLOCK_SCRIPT.setResultType(Long.class);  
    }  
    @Override  
    public boolean tryLock(String keyPrefix,long expireSecond) {  
        String key =  name + keyPrefix;  
        String value = ID_PREFIX + currentThreadID;  
        Boolean isLock = stringRedisTemplate  
                .opsForValue()  
                .setIfAbsent(key, value, expireSecond, TimeUnit.SECONDS);  
        return Boolean.TRUE.equals(isLock);  
    }  
  
    @Override  
    public void unlock(String keyPrefix) {  
        String key =  name + keyPrefix;  
        String value = ID_PREFIX + currentThreadID;  
        stringRedisTemplate.execute(UNLOCK_SCRIPT, Collections.singletonList(key),value);  
    }
}
```
#### 7-2 使用Redisson
Redisson是一个在Redis基础上实现的Java驻内存数据网格。它不仅提供了一系列的Java常用对象，还提供了许多分布式服务，其中就包括了分布式锁
##### 7-2-1 Redisson 可重入锁
###### 7-2-1-1 基本使用
（1）导入Redisson坐标
```xml
<dependency>  
    <groupId>org.redisson</groupId>  
    <artifactId>redisson</artifactId>  
    <version>3.13.6</version>  
</dependency>
```
（2）创建RedissonClient对象
```java
    @Bean  
    public RedissonClient redissonClient(){  
//        配置信息  
        Config config = new Config();  
//        添加单体地址，也可用 useClusterServers 添加集群地址  
config.useSingleServer().setAddress("redis://192.168.100.11:6379").setPassword("123456");  
        return Redisson.create(config);  
    }
```
（3）使用
```java
@Resource
private RedissonClient redissonClient;
Rlock lock = redissonClient.getLock("anyLock");
// 获取锁，重试时间，过期时间，时间单位
boolean isLock = lock.tryLock(1,3,TimeUnit.SECONDS);
// 释放锁
lock.unlock();
```
###### 7-2-1-2 原理
利用Redis中的Hash结构，用field记录当前线程创建的锁的唯一标识，value记录获取锁的次数。
（1）每次获取锁时，判断锁是否为当前线程的锁，是则 <mark style="background: #BBFABBA6;">value + 1</mark>，并重置过期时间，否则为未获取到锁，结束；
（2）每次释放锁时，判断锁是否为当前线程的锁，是则 <mark style="background: #BBFABBA6;">value - 1</mark>，并重置过期时间，并判断当前 <mark style="background: #BBFABBA6;">value 是否未 0</mark>，是则删除锁。
lua脚本仿写 Redisson 可重入锁
（1）tryLock
```lua
-- 获取key  
local key = KEYS[1]  
-- 获取field  
local threadId = ARGV[1]  
-- 获取过期时间  
local releaseTime = ARGV[2]  
-- 判断是否存在，不存在创建锁
if (redis.call(EXISTS,key) == 0) then  
   redis.call("HSET",key,threadId,"1")  
   redis.call("EXPIRE",key,releaseTime)  
   return 1  
end  
--判断锁是否是自己的锁，是则 +1
if (redis.call("HEXISTS",key,threadId) == 1) then  
   redis.call("HINCRBY",key,threadId,"1")  
   redis.call("EXPIRE",key,releaseTime)  
   return 1
end  
return 0
```
（2）unlock
```lua
-- 分布式锁的key  
local key = KEYS[1]  
-- field  
local threadID = ARGV[1]  
--过期时间  
local releaseTime = ARGV[2]  
  
-- 不是我的锁  
if (redis.call("HEXISTS",key,threadID) == 0) then  
    return nil  
end  
  
local count = redis.call("HINCRBY",key,threadID,-1)  
  
if(count > 0) then  
    redis.call("EXPIRE",key,releaseTime)  
    return nil  
else  
    redis.call("DEL",key)  
    return nil  
end
```
学完并发编程重新看 67 - 68 集
## 8.秒杀优惠卷业务优化
#### 8-1.思路
（1）将优惠卷库存判断和购买资格判断用Redis实现
（2）没有购买资格的直接返回，有购买资格的，返回订单号，并开启一个线程执行订单构建业务
#### 8-2.实现
##### （1）在添加秒杀优惠卷时将优惠卷库存信息添加到Redis中
```java
@Override  
@Transactional  
public void addSeckillVoucher(Voucher voucher) {  
    // 保存优惠券  
    save(voucher);  
    // 保存秒杀信息  
    SeckillVoucher seckillVoucher = new SeckillVoucher();  
    seckillVoucher.setVoucherId(voucher.getId());  
    seckillVoucher.setStock(voucher.getStock());  
    seckillVoucher.setBeginTime(voucher.getBeginTime());  
    seckillVoucher.setEndTime(voucher.getEndTime());  
    seckillVoucherService.save(seckillVoucher);  
    //将优惠卷信息添加到Redis中
    stringRedisTemplate.opsForValue().set(SECKILL_STOCK_KEY+voucher.getId(),voucher.getStock().toString());  
}
```
##### （2）编写lua脚本，校验购买资格
```lua
-- 1.参数列表  
-- 1.1 voucherId  
local voucherId = ARGV[1]  
-- 1.2 userId  
local userId = ARGV[2]  
  
-- key  
local stockKey = "seckill:stock:" .. voucherId  
local orderKey = "seckill:order:" .. voucherId  
  
if (tonumber(redis.call("get",stockKey)) <= 0) then  
    -- 库存不足  
    return 1  
end  
  
if (redis.call("sismember",orderKey,userId) == 1) then  
    -- 重复购买  
    return 2  
end  
  
redis.call("incrby",stockKey,-1)  
redis.call("sadd",orderKey,userId)  
return 0
```
##### （3）构建订单
```java
// 阻塞队列
private BlockingQueue<VoucherOrder> SECKILL_ORDER_EXECUTOR = new ArrayBlockingQueue<VoucherOrder>(1024 * 1024);  

//线程池
private static final ExecutorService SECKILL_POOL = Executors.newSingleThreadExecutor();  

// 代理对象
private IVoucherOrderService proxy;  
/**  
 * 初始化方法  
 */  
@PostConstruct  
public void init() throws InterruptedException {  
    SECKILL_POOL.submit(new CreateOrder());  
}  

// 线程类 做订单构建
class CreateOrder implements Runnable{  
    @SneakyThrows  
    @Override    public void run() {  
        while (true){  
            VoucherOrder voucherOrder = SECKILL_ORDER_EXECUTOR.take();  
            RLock lock = redissonClient.getLock(LOCK_ORDER_KEY + voucherOrder.getUserId());  
            boolean isLock = lock.tryLock(1, 3, TimeUnit.SECONDS);  
            try {  
                proxy.createVoucherOrder(voucherOrder);  
            }catch (Exception e){  
                throw new RuntimeException(e);  
            }finally {  
                lock.unlock();  
            }  
        }  
    }  
}
// 加载脚本文件
private static final DefaultRedisScript<Long> SECKILL_CERIPT;  
static {  
    SECKILL_CERIPT = new DefaultRedisScript<>();  
    SECKILL_CERIPT.setLocation(new ClassPathResource("seckillVoucher.lua"));  
    SECKILL_CERIPT.setResultType(Long.class);  
}

@Override  
public Result seckillVoucher(Long voucherId){  
    Long userId = UserHolder.getUser().getId();  
    Long result = stringRedisTemplate.execute(SECKILL_CERIPT, Collections.emptyList(), voucherId.toString(), userId.toString());  
  
    // 不等于 0 订单构建失败  
    int i = result.intValue();  
    if (i != 0){  
        return Result.fail(i == 1 ? "优惠卷库存不足" : "优惠卷限购一张");  
    }  
	// 通过购买资格校验
    VoucherOrder voucherOrder = new VoucherOrder();  
    long orderId = redisIdWorker.nextId(ICR_ORDER_KEY);  
    voucherOrder.setId(orderId);  
    voucherOrder.setVoucherId(voucherId);  
    voucherOrder.setUserId(userId);  
	//将订单信息添加到阻塞队列
    SECKILL_ORDER_EXECUTOR.add(voucherOrder);  
    // 创建当前类的代理类，方便事务提交
    proxy = (IVoucherOrderService)AopContext.currentProxy();  
    // 返回订单id
    return Result.ok(orderId);  
}
/**
 * 创建订单
 * @param order  
 */
@Override  
@Transactional  
public void createVoucherOrder(VoucherOrder order){  
  
    boolean update = seckillVoucherService.update()  
            .setSql("stock = stock - 1")  
            .eq("voucher_id", order.getVoucherId())  
            .gt("stock", 0)  
            .update();  
    if (!update){  
        log.error("库存不足");  
        return;    }  
    VoucherOrder voucherOrder = new VoucherOrder();  
  
    voucherOrder.setId(order.getId());  
    voucherOrder.setVoucherId(order.getVoucherId());  
    voucherOrder.setUserId(order.getUserId());  
    voucherOrderService.save(voucherOrder);  
}
```
#### 8-3.存在问题
阻塞队列基于内存实现，存在内存限制问题和数据安全问题
## 9.Redis消息队列
#### 9-1.什么是消息队列
消息队列(Message Queue)，字面意思就是存放消息的队列，最简单的消息队列应包含三个角色
（1）消息队列：存储和管理消息，也被称为消息代理
（2）生产者：发送消息到消息队列
（3）消费者：从消息队列获取并处理消息
#### 9-2.Redis实现消息队列的三种方式
##### 9-2-1.List结构：基于List模拟消息队列
（1）通过<mark style="background: #BBFABBA6;">blpop</mark>或<mark style="background: #BBFABBA6;">brpop</mark>实现阻塞效果
（2）无法避免消息丢失
（3）只支持单消费者
##### 9-2-2.PubSub：基于点对点的消息模型
PubSub(发布订阅)时Redis2.0引入的消息传递模型，消费者可订阅多个channel，生产者向对应的channel发布消息，所有订阅者都能收到消息
###### 9-2-2-1.基本命令
<mark style="background: #BBFABBA6;">subscribe</mark> channel...：订阅一个或多个频道
<mark style="background: #BBFABBA6;">publish</mark> channel mes：向一个频道发送消息
<mark style="background: #BBFABBA6;">psubscribe</mark> pattern...：订阅与pattern格式匹配的所有频道
###### 9-2-2-2.缺点
（1）不支持数据持久化
（2）无法避免数据丢失，消息发布的频道无消费之订阅，消息直接丢失
（3）消息堆积有上线，超出数据丢失
##### 9-2-3.Stream：比较完善的消息队列模型
是Redis5.0引入的一个全新的数据类型
###### 9-2-3-1.特点
（1）消息可回溯，消息可持久化不会消失
（2）一个消息可被多个消费者读取
（3）可以阻塞读取
（4）有消息漏读风险
###### 9-2-4.消费者组
消费者组：将多个消费者划分到一个组中，监听同一个队列
![[消费者组的特点.png]]
###### 9-2-5.常用命令
```redis
ID:
	n:表示从第n个消息开始读
	$:表示从最新消息开始读
#添加消息到消息队列
XADD key [NOMKSTREAM] [<MAXLEN | MINID> [= | ~] threshold [LIMIT count]] <* | id> field value [field value ...]

#从消息队列中读取指定条数的消息
XREAD [COUNT count] [BLOCK milliseconds] STREAMS key [key ...] id [id ...]

#创建消费者组
XGROUP CREATE key groupname <id | $> [MKSTREAM]

#删除指定消费者组
XGROUP DESTROY key groupname

#向消费者组中添加消费者
XGROUP CREATECONSUMER key groupname consumername

#删除消费者组中指定的消费者
XGROUP DELCONSUMER key groupname consumername

#从消费者组读消息，ID:">" 表示从下一个未消费的消息开始读,id为数值就是取pending-list中的消息
XREADGROUP GROUP group consumer [COUNT count] [BLOCK milliseconds] [NOACK] STREAMS key [key ...] id [id ...]

#确认消息，从pending—list中移除
XACK key group id [id ...]

#获取pending-list中的消息，start、end可为 -|+:最小|最大
XPENDING key group [[IDLE min-idle-time] start end count [consumer]]
```
