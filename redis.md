# redis

#### redis安装

###### linux 目录结构

* /bin&/sbin ;kernel 二进制可执行程序
* /usr/bin&/usr/sbin&/usr/lib ;(unix software resource) ubuntu/BSD/centOS... 等系统默认自带二进制 可执行程序/动态库 或 通过包管理工具(apt-get/yum)安装的二进制 可执行程序/动态库
* /usr/local/bin&/usr/local/sbin&urs/local/lib ;预留的安装目录, 用于存放用户手动编译安装的二进制 可执行程序/动态库
* /opt ;预留的安装目录, 用于存放用户通过软件安装包安装的大型软件

###### redis 手动编译安装

```makefile
make MALLOC=libc

make PREFIX=/usr/local install

mkdir rundir/redis

cp redis.conf rundir/redis
```

#### redis 使用

###### redis 配置

```shell
#设置是否为守护进程
daemonize yes
#配置文件默认绑定到127.0.0.1 注释后默认绑定到0.0.0.0
bind 127.0.0.1
#配置连接密码
requirepass xxxx

# redis-server 根据配置文件启动
redis-server ./redis.conf
```

###### redis 内存上限时的8种处理策略

```shell
# 从设置了存活时间的key-value中, 删除最不常使用或使用频率最低(lru和lfu两种判定算法)的key-value
volatile-lru 
volatile-lfu 
# 从所有key-value中,删除最不常使用或使用频率最低(lru和lfu两种判定算法)的key-value
allkey-lru
allkey-lfu
# 从设置了存活时间的key-value中, 随机删除key-value
volatile-random
# 从所有key-value中, 随机删除key-value
allkey-random
# 从设置了存活时间的key-value中, 删除最快要过期的key-value
volatile-ttl
# redis默认处理策略, 不会主动删除数据, 无法再进行写入操作,但仍然可以进行读取操作
Noeviction
```



###### redis client参数

redis-cli [-h host] [-p port] [-a passwd]

###### redis 基本命令

```shell
# 登陆
auth passwd

# 清空控制台
clear
# 选择使用数据库
select db_number
# 清空当前数据库
flushdb
# 清空所有数据库
flushall
# 将当前数据库中的key-value移动到另一个数据库
move key db_number

# 删除一个或多个指定key-value
del key1 key2 ... 

# 获取key对应value在内存中储存的数据(已被压缩过)
dump key

# 查看一个或多个指定key-value是否存在, 返回存在key-value的数量
exists key1 key2 ...

# 为一个指定的key-value设定存活时间(秒)
expire key second

# 为一个指定的key-value设定存活时间(毫秒)
pexpire key millisecond

# 查看key-value的剩余存活时间(秒), 返回剩余时间, -1永久, -2无效不存在的key-valueu
ttl key

# 查看key-value的剩余存活时间(毫秒), 返回剩余时间, -1永久, -2无效不存在的key-valueu
pttl key

# 移除key-value的过期时间
persist key

# 获取所有与pattern匹配的key (* 匹配所有, ? 匹配单个字符)
keys pattern

# 选择数据库, 一个redis-server默认开启16个数据库(0-15)
select index

# 从当前数据库中随机获取一个key
randomkey

# 重命名key
rename key newkeyname

# 将当前数据库key-value, 移动到其他数据库
mov key dbindex

# 获取key对应value的类型信息, key不存在返回n5rone
type key
```

###### redis string命令

```shell
# 获取key对应value字符符串, 不存在返回nil
get key

# 获取key对应value字符串的子字符串
getrange key start end

# mget 同时获取多个key对应的value字符串
meget key1 key2 ...

# 设置key-value, 设置key-value,key不存在插入key-value,则存在则覆写value,无视value的类型一律作为字符串处理
set key value

# 设置key-value,并设置存活时间(秒)
set key value ex 1

# 设置key-value,并设置存活时间(毫秒)
set key value px 1000

# 同时设置多个key-value, 无视value的类型一律作为字符串处理 不支持设置存活时间
mset key1 value1 key2 value2 ...

# 如果key不存在,则设置key-value, 无视value的类型一律作为字符串处理,不支持设置存活时间
setnx key value

# 先获取key对应value字符串的值, 再设置value字符串的值,不支持设置存活时间
getset key value

# 获取key对应value字符串的长度
strlen key

# incr命令将key对应value integer字符串自增1,如果value不是integer字符串则报错, 如果key-value不存在,那么key的值会先被初始化为"0",然后再执行incr操作, 返回自增后的integer
incr key
# incr命令指定自增量
incrby key increment
# incr命令指定float自增量
incrbyfloat key increment

# decr命令将key对应value integer字符串自减1,如果value不是integer字符串则报错, 如果key-value不存在,那么key的值会先被初始化为"0",然后再执行decr操作, 返回自增后的integer
decr key
# decr命令指定自减量
decrby key decrement

# append命令为指定key对应的value字符串追加内容
append key tailstr
```

###### redis hash命令

```shell
# 为指定key对应value hash表, 设置field/field_value
hset key field field_value
# 为指定key对应value hash表, 同时设置多个field/field_value
hmset key field1 field_value1 field2 field_value2...

# 获取指定key对应value hash表, 指定field的field_value
hget key field
# 获取指定key对应value hash表, 多个field的field_value
hmget key field1 field2 ...

# 获取指定key对应value hash表, 所有field/field_value信息
hgetall

# 获取指定key对应value hash表, 所有field名信息
hkeys key
# 获取指定key对应value hash表, 所有field数量
hlen key

# 删除指定key对应value hash表, 指定一个或多个field/field_value, 当key对应的value hash表的所有字段被删除后, key-value也会被移除
hdel key field

# 查看指定key对应value hash表, 指定field/field_value是否存在
hexist key field

# 如果key-field不存在, 则设置field-value
hsetnx key field value

# 相当于对key-field执行incrby命令
hincrby key field increment
# 相当于对key-field执行incrbyfloat命令
hincrbyfloat key field increment
```

###### redis list命令

```shell
# 将一个或多个值从左到右依次插入队首
lpush key value1 value2 ...

# 将一个或多个值从左到右依次插入到队尾
rpush key value1 value2 ...

# 如果list存在则将一个值插入队首
lpushx key value

# 如果list存在则将一个值插入队尾
rpushx key value

# 从list的start元素开始遍历到stop元素
lrange key start stop #start开始索引, stop结束索引, stop为-1表示最后一个元素的索引, -2表示倒数第二个元素索引,依次类推

# 获取list长度, 如果key对应的list不存在则返回0
llen key

# 根据index获取list中指定元素-1表示最后一个元素索引, -2表示倒数第二个, 依次类推
lindex key index

# 获取并删除队首元素, list不存在则返回nil
lpop key

# 获取并删除队尾元素, list不存在返回nil
rpop key

# 获取并删除队首元素,该命令可以指定多个list,从任意一个list中lpop出一个元素即可,如果所有的list不存在则等待至超时,成功返回被lpop的list的key和lpop出的值,超时返回nil
blpop key1 key2 key3 timeout

# 获取并删除队尾元素,该命令可以指定多个list,从任意一个list中rpop出一个元素即可,如果所有的list不存在则等待至超时,成功返回被rpop的list的key和rpop出的值,超时返回nil
brpop key1 key2 key3 timeout

# 修剪list
ltrim key start stop #start开始索引, stop结束索引, stop为-1表示最后一个元素的索引, -2表示倒数第二个元素索引,依次类推

# 设置制定索引元素的值, key不存在或索引无效则报错
lset key index value

# 在与assign_value相等的第一个元素的前|后位置插入
linsert key before|after assign_value value

#删除与assign_value相等的元素
lrem key count assign_value # count=0表示删除全部, count>0表示删除从队首开始匹配的前count个元素, count<表示删除从队尾开始匹配的前count个元素

# pop list1的队尾元素,push到list2的队首元素, 并返回该元素,list1不存在则返回nil
rpoplpush key1 key2
```

###### redis set命令

```shell
# 向set中插入一个或多个不重复的值, 插入成功返回插入元素的个数, 插入重复元素时不会报错但也不会插入
sadd key value1 value2 

# 获取set中元素的个数
scard key

# 获取set中所有元素
smembers key

# 判断指定的值是否在set中已经存在, 存在返回1,不存在返回0
sismembers key assign_value

# 从set中随机获取count个元素的值
srandmember key count

# 删除指定值的一个或多个元素,返回删除元素的个数
srem key assign_value1 assign_value2

# 随机删除count个元素
spop key count

# 将set1中的指定值的元素移动到set2中
smove key1 key2 assign_value

# 获取set1对set2, set3 ...的差积
sdiff key1 key2 key3
# 将set1对set2, set3 ...的差积保存在一个newset中
sdiffstore newkey key1 key2 key3

# 获取set1, set2, set3的交集
sinter key1 key2 key3
# 将set1, set2, set3的交集保存在一个newset中
sinterstroe newkey key1 key2 key3

# 获取set1, set2,set3的并集
sunion key1 key2 key3
# 将set1, set2, set3的并集保存在一个newset中
sunionstroe newkey key1 key2 key3
```

###### redis zset命令

```shell
# 向zset中插入一个或多个member不重复的(score member),或者修改zset中已经存在的(score member)元素的secore, 返回成功插入数据的个数
zadd key [nx|xx] score1 member1 score2 member2... #nx参数表示只进行插入不允许修改, xx表示只允许修改不允许插入

# 返回zset元素个数
zcard key

# 根据score的升序,遍历zset
zrange key start stop

# 根据score的降序,遍历zset
zrevrange key start stop

# 获取score符合条件的元素个数
zcount key minscore maxscore

# 获取指定member的rank
zrank key assign_member

# 删除指定一个或多个member
zrem key assign_member1 assign_member2

# 根据分数删除一个区间
zremrangebyscore key minscore maxscore

# 根据分数排行删除一个区间
zremrangebyrank key start stop
```

###### redis 订阅和发布命令

```shell
# 订阅一个或多个channel
subscribe channel1 channel2...
# 向一个频道发布消息
publish channel message
```

###### redis 事务

```shell
# 输入multi命令开始, 输入的命令都回依次进入命令队列中,但不会执行
# 直到输入exec后, redis会将队列中的命令依次执行
# 或直到输入discard后, redis会放弃命令队列
# 或直到出现指令语法解析错误, redis会放弃命令队列
multi
hset user:1:name "loop"
hset user:1:age "25"
hset user:1:salary "12000+"
exec 
multi
set title "hello,loop"
incr title
discard

# watch key1, key2... 监视一个或多个key, 如果事务执行之前这个(或这些)key被其它命令所改动,那么事务将被打断
watch title
multi
get title
set title "hell,loop"
# 此时title被其它客户端发送的命令修改 set title "hello,world"
exec # 事务被打断,exec不会执行命令队列,返回nil

# unwatch 取消watch命令对所有key的监视, 如果在执行watch命令之后,exec命令或discard命令先被执行的话, 那就不需要再执行unwatch了
unwatch 

# 事务 商品秒杀使用场景
watch produce:count #开启监视
get produce:count
#此时程序判断是还有剩余商品,如果有则开启事务
multi
decr produce:count
rpush users "loop"
exec
```

###### redis 持久化机制

* rdb 数据快照

  * 快照条件:

    * 服务器正常退出

      redis-cli shutdown

    * key满足一定条件

      在redis.conf文件中配置

      save 900 1

      save 300 10

      save 60 10000

* aof 持久化

  * 启用aof持久化
    * 配置redis.conf appendonly yes
  * aof 的三种方式
    * appendfsync always //受到写命令立即写入硬盘, 最慢, 但是保证完全的持久化
    * appendfsync everysec //每秒钟写入磁盘一次, 在性能和持久化方面做了很好的折中
    * appendfsync no

