# redis

> redis 可以用于缓存，中间件等。==部分支持事务==

**BSON**是一种计算机数据交换格式，主要被用作MongoDB数据库中的数据存储和网络传输格式。

类似于JSON

## redis聚合模型

- KV
- Bson
- 列族
- 图形

## 安装redis

- docker search redis

- docker pull redis

- docker run -d -p 6379:6379  ---requirepass “123456” --name=redis redis

## 基本命令

- `select 7`：切换数据库到第八个数据库
  ```pow
  > select 1
  # "OK"
  ```

- `keys *`：查询所有

    ```shell
    > keys * 
    # 1)  "key"
    ```

- `DBSIZE`：查看当前数据库大小

    ```shell
    > DBSIZE
    # 7
    ```

- `keys k?`:模糊查询，查询出所有以k开头的key

    - ? ：单个字符
    - \*：任意多个字符

    ```shell
    > keys lis*
    # 1)  "list"
    # 2)  "list1"
    > key list?
    # 1)  "list1"
    > key *is*
    # 1)  "list"
    # 2)  "mylist"
    # 3)  "list1"
    ```

- `flushdb`:清空当前库

- `flushall`：清除所有库

## 基本数据类型

- String（字符串）
- Hash（哈希，java中的map）
- List（列表）
- Set（集合）
- Zset（有序集合）

## key

- `Exists key`：判断当前key是否存在

  ```shell
  > exists key1
  # 0
  ```

- `move key db`：将jey移动到db库

  ```shell
  > move key1 2
  # 1
  ```

- `expire key seconds`：为当前时间设定过期时间

  ```shell
  > expire k2 2
  # 1
  ```

- `ttl key`：查看还有多少时间过期，-1永不过期，-2已过期

  ```shell
  > ttl k2
  # -1
  ```

- `type key`：查看当前key的类型

  ```shell
  > type k2
  # string
  ```

- `del key`：删除key

  ```shell
  > del k2
  # 1
  ```

## string

- `get msg`:**get**获取一个key的值

  ```shell 
  > get msg
  # out helloworld
  ```

- `set msg value`:set 设置一个key

  ```shell
  > set msg value
  #ok	
  ```

  

- `append msg hello`:append追加一个值到key上

  ```shell
  > append msg hello
  # out hello
  > append msg world
  # out helloworld
  ```

- `strlen msg`:获取字符串长度

  ```shell
  > strlen msg
  # 1
  ```

- `incr k2 `:自增

- `decr k2`:递减

- `incrby k2 3`: k2 + 3

- `decrby k2 5`: k2-5

  - 必须是数字

  ```shell
  > incr k2 
  # 3
  > decr k2 
  # 2
  > incrby k2 3
  # 5
  > decrby k2 5
  # 0
  ```

- `setrange k2 0 xxx`:给字符串前==三==位设值，原来的如果有值，则直接覆盖。

- `getrange k2 0 3`:获取字符串的前==四==位

  ```shell
  > setrange k2 0 5555
  # 5555xxx
  >getrange k2 0 3
  # 555
  ```

- `mset/mget`:设置多个或读取多个键值对

  ```shell
  > mset k1 v1 k2 v2 k3 v3
  # ok
  > mget k1 k2 k3
  # v1
  # v2
  # v3
  ```


## List

- `lpush/rpush key v1 v2 v3`:把多个元素 push 进入列表

  - 底层实现：队列

  ```shell
  > lpush mylist 1 2 3 4 5
  #out 5
  ```

  存储

  | key  | value |
  | :--: | :---: |
  |  1   |   5   |
  |  2   |   4   |
  |  3   |   3   |
  |  4   |   2   |
  |  5   |   1   |

- `lpop/rpop key`:移除并且返回 key 对应的 list 的第一个元素。

  ```shell
  > lpop mylist 
  #out 5
  > rpop mylist
  #out 1
  ```

- `Lrange key 0 -1`:取出列表所有值

  ```powershell
  > Lrange mylist 0 -1
  # 3
  # 2
  # 1
  ```

- `Lindex mylist 2`:取出指定索引的值

  ```powershell
  > Lindex mylist 2
  # null
  ```

- `Llen mylist`：获得list的长度

  ```powershell
  > llen mylist
  # 7
  ```

- `LREM MYLIST 2 3`：删除mylist中两个相同的3元素

  ```powershell
  > lrem mylist 2 3
  # 5
  # 4
  # 2
  # 1
  ```

- `LTRIM MYLIST 1 3 `:截取指定key的指定范围后的结果再付值给key

  ```powershell
  > ltrim mylist 1 3
  # 1)  "4"
  # 2)  "3"
  # 3)  "2" 
  ```

- `rpoplpush key2 key1`:将源list的最后一个值压入目标list的第一个位置

   ```powershell
  docker_redis:2>rpoplpush mylist2 mylist
  # "7"
  docker_redis:2>lrange mylist2 0 -1
  # 1)  "9"
  # 2)  "8"
  docker_redis:2>lrange mylist 0 -1
  # 1)  "7"
  # 2)  "4"
  # 3)  "3"
  # 4)  "2"
  ```

- `lset key index value`：指定设置list中第index个的value

  ```powershell
  docker_redis:2>lset mylist 2 xxx
  # "OK"
  docker_redis:2>lrange mylist 0 -1
  # 1)  "7"
  # 2)  "4"
  # 3)  "xxx"
  # 4)  "2"
  ```

- `linsert key before/after value1 value2`:在value1之前或之后插入value2

  ```powershell
  docker_redis:2>linsert mylist before xxx jjj
  # "5"
  docker_redis:2>lrange mylist 0 -1
  # 1)  "7"
  # 2)  "4"
  # 3)  "jjj"
  # 4)  "xxx"
  # 5)  "2"
  docker_redis:2>linsert mylist after jjj ttt
  # "6"
  docker_redis:2>lrange mylist 0 -1
  # 1)  "7"
  # 2)  "4"
  # 3)  "jjj"
  # 4)  "ttt"
  # 5)  "xxx"
  # 6)  "2"
  ```

## set(所有元素均不相同)

- **`sadd key 1 1 2 2 3 3`**集合中添加元素

  ```shell
  > sadd mysets comi dml
  #out 2
  > sadd key 1 1 2 2 3 3
  # 3
  ```

-  `smembers key`：查看集合所有元素

  ```shell
  > smembers mysets
  # 1)  "dml"
  # 2)  "comi"
  ```

- `spop key 1`：从存储在`key`的集合中移除并返回一个或多个随机元素。

  ```shell
  > spop mysets 1
  # out 1
  ```

- `sismember key value`：判断给定的**值**是不是一个集合里面的成员

  ```shell
  > sismember mysets comi
  # 1 
  ```

- `scard key`：获取集合中元素的个数

  ```powershell
  docker_redis:2>scard set
  # "4"
  ```

- `srem key value`：删除集合中指定值的元素

  ```powershell
  docker_redis:2>srem set 1
  # "1"
  ```

- `srandmember key value`：从set中随机取出value个值

  ```powershell
  docker_redis:2>srandmember set 3
  # 1)  "3"
  # 2)  "2"
  # 3)  "4"
  ```

- `smove key1 key2 value`：将key1中的值移动到key2中去

  ```powershell
  docker_redis:1>smove set set1 5
  # "1"
  docker_redis:1>smembers set
  # 1)  "2"
  # 2)  "3"
  # 3)  "4"
  docker_redis:1>smembers set1
  # 1)  "3"
  # 2)  "5"
  # 3)  "12"
  # 4)  "43"
  ```

### 数学集合类

 1. 差集 `sdiff`：取出在第一个集合中的值而不再第二个集合中的值

    - 数学上意为：set1 - set2

    ```powershell
    docker_redis:1>sadd set1  1 2  3 4 5
    # "5"
    docker_redis:1>sadd set2 a b c 3 2
    # "5"
    docker_redis:1>sdiff set1 set2
    # 1)  "1"
    # 2)  "4"
    # 3)  "5"
    ```

 2. 交集`sinter`：取出两个集合相同的元素

    ```powershell
    docker_redis:1>sinter set1 set2
    # 1)  "2"
    # 2)  "3"
    ```

 3. 并集`sunion`：两个集合的所有元素

    ```powershell
    docker_redis:1>sunion set1 set2
    # 1)  "1"
    # 2)  "c"
    # 3)  "3"
    # 4)  "5"
    # 5)  "b"
    # 6)  "2"
    # 7)  "4"
    # 8)  "a"
    ```

## hash

>  kv模式不变，但是v是一个键值对

- `hset/hget/hmset/hmget/hgetall/hdel`：添加/获取/添加多个/

  ```powershell
  docker_redis:2>hset h1 k1 v1
  # 1
  docker_redis:2>hget h1 k1
  # 1
  docker_redis:2>hmset user name comi age 18
  # ok
  docker_redis:2>hmget user name age
  #1)  "comi"
  #2)  "18"
  docker_redis:2>hgetall user
  #1)  "name"
  #2)  "comi"
  #3)  "age"
  #4)  "18"
  docker_redis:2>hdel user name
  # 1
  ```

- `hlen key`：获取hash的长度

  ```powershell
  docker_redis:2>hlen user
  # "2"
  ```

- `hexists key k1`:key中是否存在k1的键

   ```powershell
  docker_redis:2>hexists user name
  # "1"
  ```

- `hkeys/hvals key`：获取key中所有key/value

  ```powershell
  docker_redis:2>hkeys user
  # 1)  "age"
  # 2)  "name"
  docker_redis:2>hvals user
  # 1)  "18"
  # 2)  "comi"
  ```

- `hincrby/hincrbyfloat key k1 2`：增加整数或小数

  ```powershell
  docker_redis:2>hvals user
  # 1)  "18"
  # 2)  "comi"
  # 3)  "99.5"
  docker_redis:2>hincrby user age 2
  # "20"
  docker_redis:2>hincrbyfloat user score 0.5
  #"100"
  ```

- `hsetnx key k v`：如果属性不存在才去存储

  ```powershell
  docker_redis:2>hsetnx key email 111@qq.com
  # "1"
  ```

## set(有序集合)

-  `zadd key score v1 score v2` ：添加一个有序集合

-  `zrange key 0 -1 withscores`：获得有序集合的值

  ```powershell
  docker_redis:3>zadd rank 10 comi 20 dml
  # "2"
  docker_redis:3>zrange rank 0 -1
  # 1)  "comi"
  # 2)  "dml"
  docker_redis:3>zrange rank 0 -1 withscores
  # 1)  "comi"
  # 2)  "10"
  # 3)  "dml"
  # 4)  "20"
  ```

- `zrangebyscore key v1 v2`：获取符合v1-v2区间中的值

- `zrangebyscore key (v1 (v2 `：获取符合v1-v2区间中的值，但不包含v1，v2.

- `zrangebyscore key v1 v2 limit start step  `：获取符合v1-v2区间中的值，从值中的第start个元素开始，往下截取step个元素。

  ```powershell
  docker_redis:3>zrangebyscore rank 0 20
  # 1)  "comi"
  # 2)  "dml"
  docker_redis:3>zrangebyscore rank (0 (20
  # 1)  "comi"
  docker_redis:3>zrangebyscore rank 10 40
  # 1)  "comi"
  # 2)  "dml"
  # 3)  "siki"
  # 4)  "hdsajk"
  docker_redis:3>zrangebyscore rank 10 40 limit 1 2
  # 1)  "dml"
  # 2)  "siki"
  ```

-  `zrem key v1`：删除某个score下对应的value的值

    ```powershell
  docker_redis:3>zrem rank siki
  # "1"
  docker_redis:3>zrange rank 0 -1
  #1)  "comi"
  #2)  "dml"
  #3)  "hdsajk"
  ```

- `zcard key `：统计key下元素的个数

- `zcount key score1 score2 `：统计score1、score2区间下的元素的个数

- `zrank key v`:获取v对应的score对应的下标

- `zrevrank key v`:**逆序**获取v对应的score对应的下标

- `zscore key v`:获取v对应的score的值

   ```powershell
  docker_redis:3>zcard rank 
  # "3"
  docker_redis:3>zcount rank 10 40
  # "3"
  docker_redis:3>zrank rank comi
  # "0"
  docker_redis:3>zrevrank rank comi
  # "2"
  docker_redis:3>zscore rank comi
  #"10"
  ```

- `zrevrange key 0 -1`:根据score的值由大到小得到数据

  ```powershell
  docker_redis:3>zrevrange rank 0 -1
  # 1)  "hdsajk"
  # 2)  "dml"
  # 3)  "comi"
  docker_redis:3>zrange rank 0 -1
  # 1)  "comi"
  # 2)  "dml"
  # 3)  "hdsajk"
  ```

- `zrangebyscores key v2 v1`：取出由score从大到小排列，符合v2、v1区间的值

     ```powershell
  docker_redis:3>zrevrange rank 0 -1 withscores
  # 1)  "hdsajk"
  # 2)  "40"
  # 3)  "dml"
  # 4)  "20"
  # 5)  "comi"
  # 6)  "10"
  docker_redis:3>zrevrangebyscore rank 40 20
  # 1)  "hdsajk"
  # 2)  "dml"
  ```

## redis的配置文件

- 配置文件的单位大小写不敏感

- INCLUDES区域:可以包含多个配置文件文件

  - redis.conf: 作为主配置文件
  - include /path/to/local.conf

- general：通用模块

  - daemon ：守护进程

    - ```conf
      daemonize yes
      ```

  - tcp-keeplive：如果设置为0不会进行keeplive检测，建议设置为60

  - security：安全

    - config get  requirepass：查看当前设置的密码
    - config set requirepass “123456”：设置redis的访问密码
    - auth 123456：登录redis

  - limits：限制

    - 缓存过期策略

      ![image-20191218151257128](D:\notes\images\image-20191218151257128.png)

    - maxmemory-policy：noeviction

## redis持久化

- RDB（redis database）

  > 指定的时间间隔内将内存的数据集快照写入磁盘
  >
  > 保存为dump.rdb文件

  - 默认持久化策略
    - 修改：save seconds changes 
    - save 900 1：900秒内修改过一次
    - save 300 10：30秒内修改过10次
    - save 60 10000：1秒内修改过10000次
    - 满足以上条件就会进行存储
  - 在进行shutdown/flushdb/flushall命令时，都会快速进行备份生成rdb文件。
  - `save命令`：立即保存备份，其他全部诸塞
  - `bgsave命令`：后台异步快照，快照同时可以响应客户端请求

- AOF（append only file）

  > 以日志形式来记录每个写操作，而读操作则不记录。只允许追加文件，而不允许修改文件。
  >
  > 保存文件形式：appendonly.aof

  默认为关闭

  修改：appendonly yes：开启AOF模式

  默认为64mb就会启动日志压缩，高并发一般至少为3G。

  ==如果只是作为缓存，可以不使用任何形式的持久化方式==

  注:如果同时开启两种持久化方式，redis会优先载入==AOF==文件来恢复原始数据。

 ## redis的事务

> 事务：可以一次执行多个命令，本质是一组命令的集合，按顺序依次执行命令

- multi：标记一个事务块的开始-->开启事务
- discard：取消事务，放弃事务块内所有命令
- exec：执行事务块内的命令
- unwatch：取消watch命令对key的监视
- watch key：监视一个或多个key

**正常执行情况**

```powershell
docker_redis:0>multi
#"OK"
docker_redis:0>set k1 v2
#"QUEUED"
docker_redis:0>set k2 v2
#"QUEUED"
docker_redis:0>exec
# 1)  "OK"
# 2)  "OK"
```

**放弃事物**

```powershell
docker_redis:0>multi
#"OK"
docker_redis:0>set k1 v2
#"QUEUED"
docker_redis:0>set k2 v2
#"QUEUED"
docker_redis:0>discard
#"OK"
```

**在书写时命令错误，所有命令均无法执行**

```powershell
docker_redis:0>multi
#"OK"
docker_redis:0>set q1 q1
#"QUEUED"
docker_redis:0>set q2 q2
#"QUEUED"
docker_redis:0>setget 1
#"ERR unknown command `setget`, with args beginning with: `1`, "
docker_redis:0>set q3 q3
#"QUEUED"
docker_redis:0>exec
#"EXECABORT Transaction discarded because of previous errors."
```

 **某个命令执行时出现问题，而其他命令正常**

```powershell
docker_redis:0>multi
#"OK"
docker_redis:0>set  k1 v1
#"QUEUED"
docker_redis:0>set k2 aa
#"QUEUED"
docker_redis:0>incr k2
#"QUEUED"
docker_redis:0>exec
# 1)  "OK"
# 2)  "OK"
# 3)  "ERR value is not an integer or out of range"
```

**watch监控**

> **表锁**：将整张表进行锁定
>
> 行锁：将修改的行锁定
>
> **乐观锁**：读取出数据时，将此版本号一同读出，之后更新时，对此版本号加一。同时，将提交数据的版本数据与数据库表对应记录的当前版本信息进行比对，如果提交的数据版本号等于数据库表当前版本号，则予以更新，否则认为是过期数据。
>
> - 简而言之就是==提交的版本==必须**大于**记录==当前的版本==才能执行更新
>
> **悲观锁**：大多数情况下依靠数据库的锁机制实现，以保证操作最大程度的独占性。

`watch key`：在进行一些操作时，比如说**余额**增减，为防止在操作时余额发生变动，先监控余额，如果余额发生变动，则当前事务所有操作无效。

`unwatch key`:对key取消监控

## 消息订阅

- `psubscribe new* `：订阅频道为new开头的任意频道

- `publish new11 hello `: 推送消息

  - 客户端一

  ```powershell
  docker_redis:0>psubscribe new*
  #Switch to Pub/Sub mode. Close console tab to stop listen for messages.
  # 1)  "psubscribe"
  # 2)  "new*"
  # 3)  "1"
   
  # 1)  "pmessage"
  # 2)  "new*"
  # 3)  "new11"
  # 4)  "hello"
  ```

  - 客户端二

    ```powershell
    docker_redis:0>publish new11 hello
    #"1"
    ```

## 主从复制

作用：读写分离、容灾恢复

> 自动同步到配用机的master/slaver机制，master以写为主，slave以读为主

1. 配从库不配主库

2. `info replication` ：查看当前redis的信息

   ```powershell
   docker_redis:0>info replication
   # Replication
   role:master	<-----
   connected_slaves:0
   ....
   ```

### 一主二仆情况

   > 一台主机用于写数据，二台从机读数据

   从库配置： `slaveof 主库ip 主库端口`；每次断开连接后，需要重新连接主库，可以同通过配置文件来自动配置。

   **主机:6379--->从机1:6380、主机:6379--->从机2:6381**

   从机1:6380

   ```powershell
   docker_redis:0>slaveof 127.0.0.1 6379
   #OK
   docker_redis:0>info replication
   # Replication
   role:slave	<-----
   master_host:127.0.0.1
   master_port:6379
   ....
   ```

   **从机2**：6381

   ```powershell
   docker_redis:0>slaveof 127.0.0.1 6379
   #OK
   docker_redis:0>info replication
   # Replication
   role:slave	<-----
   master_host:127.0.0.1
   master_port:6379
   ....
   ```

### 薪火相传情况

> 去中心化，上一个slave可以是下一个的master，slave同样可以接受其他slave的连接和同步请求，那么该slave的作为了链条中下一个的master，可以有效减轻master的写压力。
>
> eg：三台机器，一台机器作为主机，另一台作为从机，另一台作为从机的从机

**主机:6379--->从机1:6380--->从机2:6381**

从机1:6380

```powershell
> slaveof 127.0.0.1 6379
# ok
```

从机2:6381

```powershell
> slaveof 127.0.0.1 6380
# ok
```

### 反客为主情况

> 如果主机意外挂了，从机可以作为主机。

**从机输入命令，让从机变为主机**；再让自己的从机`slaveof`自己

**~~主机:6379--->~~从机1:6380--->从机2:6381**

**从机1**：6380

```powershell
> slaveof no one
# ok
```

**从机2**：6381

```powershell
docker_redis:0>slaveof 127.0.0.1 6380
#OK
```

### 复制原理

![image-20191219085505457](D:\notes\images\image-20191219085505457.png)

###  哨兵模式

> 反客为主的自动版，如果主机挂了，将从剩余的从机中更具投票票数，自动将**从库转换为主库**。

步骤：

1. 在自定义的`/myredis/`目录下新建文件`sentinel.conf`（redis的运行目录）

2. 配置文件配置

   ```powershell
   > sentinel monitor was-detect-library-name ip-address port Votes
   eg: >sentinel monitor mainLibrary6379 127.0.0.1 6379 1
   ```

   如果主机挂掉以后，谁的投票大于1，谁就成为新的主机。

3. 启动哨兵：`Redis-sentinel /myredis/sentinel.conf`（自己存放sentinel.conf的位置）

4. 当原先主机回来后，就会被哨兵监控到，成为新的从机

==一组的sentinel可以监控多个master==



































