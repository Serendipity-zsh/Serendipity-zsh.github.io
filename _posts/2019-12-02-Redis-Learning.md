---
layout: post
title: Redis-Learning
date: 2019-12-02
description: 
tag: "redis,logs,learning"
---

# Redis-Learning



## 安装使用

```shell
#安装
yay redis
#使用
#打开服务端
sudo redis-server
#客户端
sudo redis-cli

#在远程服务上执行命令
redis-cli -h host -p port -a password
#乱码解决
redis-cli --raw
```

## 数据类型

Redis 支持五种数据类型：

string（字符串），hash（哈希），list（列表），set（集合）及zset(sorted set：有序集合)

## 常用命令

### 基本命令

```shell
#主要使用的
127.0.0.1:6379> GET key_name
127.0.0.1:6379> SET key_name value
127.0.0.1:6379> DEL key_name
#检查给定key是否存在
127.0.0.1:6379> EXISTS key_name

#修改 key 的名称
127.0.0.1:6379> RENAME key_name new_key_name

#设置key的过期时间，单位为秒
127.0.0.1:6379> Expire key_name TIME_IN_SECONDS

#移除给定 key 的过期时间，使得 key 永不过期
127.0.0.1:6379> PERSIST key_name

#以秒为单位返回 key 的剩余过期时间
127.0.0.1:6379> TTL key_name

#查找所有符合给定模式 pattern 的 key 
127.0.0.1:6379> KEYS PATTERN
#查找以hello为开头的key
127.0.0.1:6379> KEYS hello*

#将当前数据库的 key 移动到给定的数据库 db 当中
127.0.0.1:6379> MOVE key_name DESTINATION_DATABASE

```

### 连接命令

```shell
#测试与服务器的连接是否仍然生效，或者用于测量延迟值
127.0.0.1:6379> PING
#正常返回PONG

#检测给定的密码和配置文件中的密码是否相符
127.0.0.1:6379> AUTH password

#用于关闭与当前客户端与redis服务的连接
127.0.0.1:6379> QUIT

#切换到指定的数据库，数据库索引号 index 用数字值指定，以 0 作为起始索引值
127.0.0.1:6379> SELECT index

#Redis服务器
127.0.0.1:6379> INFO
```

### 数据备份与恢复

```shell
#创建当前数据库的备份
127.0.0.1:6379> SAVE
#在redis安装目录中创建 dump.rdb 文件

# 恢复数据
#将备份文件 (dump.rdb) 移动到 redis 安装目录并启动服务即可
#获取 redis 目录
127.0.0.1:6379> CONFIG GET dir

#创建 redis 备份文件也可以使用命令 BGSAVE，该命令在后台执行
127.0.0.1:6379> BGSAVE

Background saving started
```



## Java Redis

```java
public class RedisUtil {

    //服务器IP地址
    private static String ADDR = "192.168.41.65";
    //端口
    private static int PORT = 6379;
    //密码
    private static String AUTH = "123456";
    //连接实例的最大连接数
    private static int MAX_ACTIVE = 1024;
    //控制一个pool最多有多少个状态为idle(空闲的)的jedis实例，默认值也是8。
    private static int MAX_IDLE = 200;
    //等待可用连接的最大时间，单位毫秒，默认值为-1，表示永不超时。如果超过等待时间，则直接抛出JedisConnectionException
    private static int MAX_WAIT = 10000;
    //连接超时的时间　　
    private static int TIMEOUT = 10000;
    // 在borrow一个jedis实例时，是否提前进行validate操作；如果为true，则得到的jedis实例均是可用的；
    private static boolean TEST_ON_BORROW = true;

    private static JedisPool jedisPool = null;
    //数据库模式是16个数据库 0~15 
    public static final int DEFAULT_DATABASE = 0;
    /**
     * 初始化Redis连接池
     */
    static {
        try {
            JedisPoolConfig config = new JedisPoolConfig();
            config.setMaxTotal(MAX_ACTIVE);
            config.setMaxIdle(MAX_IDLE);
            config.setMaxWaitMillis(MAX_WAIT);
            config.setTestOnBorrow(TEST_ON_BORROW);
            jedisPool = new JedisPool(config, ADDR, PORT, TIMEOUT,AUTH,DEFAULT_DATABASE);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    /**
     * 获取Jedis实例
     */
    public synchronized static Jedis getJedis() {

        try {
            if (jedisPool != null) {
                Jedis resource = jedisPool.getResource();
                System.out.println("redis--服务正在运行: "+resource.ping());
                return resource;
            } else {
                return null;
            }
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }

    /***
     * 
     * 释放资源
     */
    public static void returnResource(final Jedis jedis) {
            if(jedis != null) {
                jedisPool.returnResource(jedis);
            }
    }
}
```

