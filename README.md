# redis 介绍
## redis
- k/v cache and store
	+ in-memory
	+ 持久化
	+ 主从(借助于sentinel实现一定意义上的HA)
	+ Clustering(分布式)
- 数据结构服务器
	+ String,List, Hash, Set, Sorted Set, Bitmaps, Hyperloglogs

## redis 的发展史
> Redis, Remote Directory Server 远程服务器字典
意大利创业公司，使用mysql性能问题，C语言编写
数据缓存在内存中，普通电脑一秒读取10万个键值对儿
问题：断电、程序关闭，数据丢失
解决：数据持久化


## redis优势
- 内建replication及cluster
- 就地更新（in-place update）操作
- 支持持久化
	+ 避免雪崩效应
- Redis单线程，Memcached多线程

## mecached的优势
- 多线程
	+ 善用多核CPU
	+ 更少的阻塞操作
- 更少的内存开销
- 更少的内存分配压力
- 可能有更少的内存碎片

## 存储系统：
- RDBMS: Oracle, DB2, PostgreSQL, MySQL, SQL Server
- NoSQL: Cassandra, HBase, Memcached, MongoDB, Redis
	+ key-value NoSQL: Memcached, Redis, Tair
	+ Column family NoSQL: Cassandra, HBase
	+ Documentation NoSQL: MongoDB
	+ Graph NoSQL: Neo4j
- NewSQL: Aerospike, FoundationDB, RethinkDB

## redis　应用
- weibo
- zhihu
- stackoverflow
- Twitter
- Github
- Blizard
- flickr
- Pinterest
- Tumblr
- digg

## redis 版本
- 2015-4-1 Redis 3.0
	+ redis Cluster
	+ embedded string
	+ LRU演算法的改进


## redis 下载安装配置并启动服务程序
[redis官网](http://redis.io/download)

### 1. 下载
`# cd /usr/local/src && wget http://download.redis.io/releases/redis-3.2.10.tar.gz`

### 2. 解压安装
`# tar xvf redis-3.2.10.tar.gz && cd redis-3.2.10`
`# make && make install`

### 3. /usr/local/bin可执行程序
- redis-cli: 命令行客户端
- redis-server: Redis服务器端
- redis-benchmark: Redis性能测试工具
- redis-check-aof: AOF文件修复工具
	+ Corrupted RDB
- redis-check-dump: RDB文件检测工具
	+ AOF files utilties

### 4. 安装目录下修改Redis配置文件redis.conf并安装复制到/etc/目录下 
`# vi redis.conf`
> daemonize yes 以守护进程的方式使用

`# cp redis.conf /etc/`

###　5. 启动服务，指定配置文件
`# service redis start`
`# redis-server /etc/redis.conf`

###　6. 连接服务器
`# redis-cli`
`# redis-cli -h IP`
`>help append`
`>help @string`
`>help @server`
`>client list`
`ping`

### 7. 停止服务redis
- 客户端连接服务后
`> shutdown`

- 关闭进程
`# killall redis-server`

## Redis守护进程：
- 监听端口：`6379/tcp`
- backlog（等待队列）
- bind 127.0.0.1 172.16.100.6：监听地址

- 同一主机建议：
	+ unixsocket /tmp/redis.sock
	+ unixsocketperm 700
- timeout 0 超时功能
- loglevel notice
- logfile
- database 16：默认使用一个数据库，这里可使用16个数据库
	+ 集群只能使用一个

save 300 10 ： 300秒内发生10个记录修改，生成快照

## 打开数据库
`select 1`

## 数据结构
redis 是key-value的数据，所有每个数据都是一个键值对
- 键的类型是字符串
- 值的类型分为五种
	+ 字符串 string
	+ 哈希 hash
	+ 列表 list
	+ 集合 set
	+ 有序集合 zset

### String 
- 最大能存储512MB数据
- string类型是二进制安全的，既可以为任何数据，比如数字、图片、序列化对象等

- 连接 redis 服务器
$ redis-cli 
> ping

- 设置键值
set key value

- seconds: 过期时间，以秒为单位。没有过期时间就永久存储
setex key seconds value

- 设置多个值
MSET key value [key value ...]

> set 'name' 'cgw'

- 获取值
GET key
没有：(nil)

- mget key1 key2


- 运算
	+ 值是数字
	+ 将 key 对应的 value 加1

	+ INCR key 
	+ INCR key increment
	+ DECR key
	+ DECRBY key decrement

- 其他
	+ append key value 追加
	+ strlen key 字符长度

- 键的命令
	+ keys * 所有的键盘
	+ keys '*1*' 包含1的键名
	+ exists key [key ...] 是否存在，存在返回1，否则返回0
	+ 查看对应的 value 的类型
		* type key
	+ 删除键及对应的值
		* del key [key ...]
	+ 设置过期时间，以秒为单位
		* expires key seconds
		* 创建时没有设置过期时间，则一直存在，直到使用del 移除
	+ 查看有效时间
		* ttl key

### hash
> 用于存储对象，对象的格式为键值对
{name:'cg',age:20}

- 设置
	+ 设置单个属性
		* hset key field value
		* hset data name "零壹码"
		* hset data age 20
		* type data // hash


	+ 设置多个属性
		* hmset key field value [field value ...]
- 获取
	+ 获取一个属性的值
		* hget key field
		* hget name name 

	+ 获取多个属性的值
		* hmget key field [field ...]

	+ 获取所有的属性和值
		* hgetall key

	+ 获取所有的属性
		* hkeys key

	+ 返回包含属性的个数
		* hlen key

	+ 获取所有值
		* hvals key
- 其他
	+ 判断属性是否存在
		* hexists key field
	+ 删除属性及值
		* hdel key field [field ...]
	+ 返回值的字符串长度
		* hstrlen key field 	

### list
- 列表的元素类型为string
- 按照插入顺序排序
- 在列表的头部或者尾部添加元素


- 设置
	+ 在头部插入数据
		* lpush key value [value ...]
	+ 在尾部插入数据
		* rpush key value [value ...]

	+ 在一个元素的前后插入新元素
		* linsert key before|after pivoid value

	+ 设置指定索引的元素值
	+ 索引是基于0的下标
	+ 索引可以是负数，表示偏移量是从 list 尾部开始计算。

		* lset key index value

- 获取
	+ 移除并且返回 key 对应的 list　的第一个元素
		* lpop key

	+ 移除并返回存于 key 的 list 的最后一个元素
		* rpop key

	

`help set`
`set key value [EX seconds] [NX|XX]`
`SET disto fedora` 设置
`GET disto` 获取
`APPEND disto centos` 追加
`strlen disto` 字符大小
`SET count 0`
`incr count` 自增
`decr count` 自减
`SETNX disto gentoo NX` nx不存在时设置
`SETNX bar gentoo XX` xx存在时设置
`SETNX count 0`
`exists count`

### lists
- lpush
- rpush
- lpop
- rpop
- lindex
- lset

### sets
- sadd
- sinter
- sunion
- spop
- sismeb

### Sorted sets
- zadd
- zrange
- zcard
- zrank

### hashes
- hset
- hsetnx
- hget
- hkeys
- hvals
- hdel


