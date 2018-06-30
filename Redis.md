
# Redis

## About Redis

[Redis官网](http://redis.io)

Redis is an open source (BSD licensed), in-memory data structure **store**, used as a **database**, **cache** and **message broker**. It supports data structures such as **strings, hashes, lists, sets, sorted sets with range queries, bitmaps, hyperloglogs and geospatial indexes with radius queries**. Redis has built-in **replication**, **Lua** scripting, **LRU** eviction, **transactions** and different levels of on-disk persistence, and provides **high availability** via Redis Sentinel and automatic partitioning with Redis **Cluster**.

### Download Redis

``` shell
# cd /usr/local/src
# wget http://download.redis.io/releases/redis-4.0.10.tar.gz
# tar zxvf /redis-4.0.10.tar.gz
# cd redis-4.0.10 或 redis-2.6.16
# make (32bit机器：# make 32bit)

注意：易碰到的问题，时间错误
原因：源码是官方 configure 过的，但官方 configure 时，生成的文件有时间戳信息
make 只能发生在 configure 之后
如果你的虚拟机的时间不对，比如说是 2012 年
解决：date -s 'yyyy-mm-dd hh:mm:ss' 重写时间
用 clock -w 写入cmos


# make test 测试编译
可能出现：need tcl > 8.4 情况 yum install tcl

安装时指定安装目录：
# make PREFIX=/usr/local/redis install
注意：PREFIX 必须大写

make install 之后得到如下几个文件：
- redis-benchmark 性能测试工具
- redis-check-aof 检查 aof 日志的工具（比如断电造成日志损坏，可以检测并修复）
- redis-check-dump 检查 rbd 日志的工具，快照文件检测工具，效果类上
- redis-cli 客户端
- redis-server 服务端服务程序

复制配置文件
# cd /usr/local/redis
# cp /usr/local/src/redis-4.0.10/redis.conf ./
# ls
  bin redis.conf

启动
# ./bin/redis-server ./redis.conf

后台运行服务：编辑 redis.conf 配置文件
# vim redis.conf
  daemonize yes 后台运行服务
  database 16 默认开启16个数据库(0-15个数据库)
# ./bin/redis-server ./redis.conf
# ps aux | redis

客户端连接服务器端服务
# ./bin/redis-cli
redis IP> set site www.wovert.com
redis IP> get site


```

## Redis 键值操作

``` redis
> select 2
> keys *
> select 0

> keys pattern 查询相应的 key
有3个通配符：*, ?, []
*: 通配任意字符
?: 统配单个字符

> flushdb

> keys *

> mset one 1 two 2 three 3
> keys o*

> keys ???

> keys on?

> set one yes

> keys on[eaw]

> randomkey 返回随机 key 名

> exists key 判断 key 是否存在，返回1|0

> type key 返回 存储的值的类型，有 sring, link, set, order, set, hash

> del key1 key2 ... keyn
> rename old_key new_key
作用: 给old_key 赋一个新的 new_key
注意：如果new_key已存在，则 new_key的原值被覆盖

> renamenx old_key new_key
作用: 把 old_key 改名为 new_key
返回： 发生修改返回 1, 违反生修改返回 0
注意：nx -> not exists, 即 new_key 不存在时，作改名动作


> select 1 选择第二个数据库
> keys *
(empty list or set)
> select 0
> keys *


> move sea 1
"sea" key 移动到 1号数据库
> select 1
> keys *


生命周期
> ttl key_name
(integer) -1
作用：查询 key 的生命周期
返回：秒数
注意：对于不存在的 key 或已过期的 key 不过期的都返回 -1
Redis 2.8 中，对于不存在的 key 返回 -2

> expire key 整型值
作用：设置 key 的生命周期以秒位单位

> pexpire key 毫秒数，设置生命周期
> pttl key，以毫秒返回生命周期

> persist key
作用: 把指定 key 置位永久有效
```

## Redis 数据类型

### string

## 事务

## 消息订阅

## 持久化

## 集群

## PHP-redis 客户端

## 运维

## 翻微博实战