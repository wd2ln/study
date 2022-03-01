## redis

## Redis 优势

- 性能极高 – Redis能读的速度是110000次/s,写的速度是81000次/s 。
- 丰富的数据类型 – Redis支持二进制案例的 Strings, Lists, Hashes, Sets 及 Ordered Sets 数据类型操作。
- 原子 – Redis的所有操作都是原子性的，意思就是要么成功执行要么失败完全不执行。单个操作是原子性的。多个操作也支持事务，即原子性，通过MULTI和EXEC指令包起来。
- 丰富的特性 – Redis还支持 publish/subscribe, 通知, key 过期等等特性。

#### linux下安装redis

```
# wget http://download.redis.io/releases/redis-6.0.8.tar.gz
# tar xzf redis-6.0.8.tar.gz
# cd redis-6.0.8
# make
```

#### linux下启动redis服务

```
# cd src
# ./redis-server
启动客户端
# cd src
# ./redis-cli
```

#### 五种数据类型

- String: 字符串
- Hash: 散列
- List: 列表
- Set: 集合
- Sorted Set: 有序集合

#### redis键(key)操作

###### 设置指定key值

```
set key value
```

###### 获取指定key值

```
get key value
```

###### 设置多个key值

```
mset key value key value
```

###### 获取多个key的值

```
mget key key
```

###### 删除指定key

```
删除指定key
```

### string扩展操作

##### 加减操作

自增（每次只能加一）

```redis
incr key
```

增加指定数字

```redis
incrby key num--Integer
```

自增可以指定小数

```redis
incrbyfloat key float
```

自减（每次只能减去一）

```
decr key
```

减去指定数字

```
decrby key num--Integer
```

注：decr/decrby只能操作Integer类型---整数

##### string 作为数值操作

string在redis内部存储默认就是一个字符串 ，当遇到增减类操作incr，decr时会转成数值型进行计算 。

redis所有的操作都是原子性的，采用单线程处理所有业务，命令是一 个一个执行的，因此无需考虑并发带来的数据影响 。

”注意 : 按数值进行操作的数据 ，如果原始数据不能转成数值 ，或超越了 redis 数值上限学围 ，将报错 。

<!--9223372036854775807 (java 中 long 型 数据 最 大 值 ，Long.MAX_ VALUE)-->

##### 生命周期操作

数据需要一定的时效性，例：验证码，微信投票---四小时投一次，等.....

指定存活秒数

```redis
setex key seconds value
```

指定存活毫秒数

```
psetex key milliseconds value
```

<!--注：当key有生命周期时，再次使用set声明该key，key会覆盖掉有声明周期的key，转变为无生命周期的key-->

```
127.0.0.1:6379> setex age 10 4
OK
127.0.0.1:6379> set age 5
OK
127.0.0.1:6379> get age
"5"
127.0.0.1:6379> get age
"5"
127.0.0.1:6379> get age
"5"
127.0.0.1:6379> 
```

##### 数据操作不成功的反馈与数据正常操作之间的差异

表示 运行 结果 是 否 成 功

```
(integer)0 一 false 失败
(integer)1 一 true 成功
```

表示 运行 结果 值

```
(integer)3 — 3个
(integer)1 — 1个
```

数据 未 获取 到

```
(nil) 等 同 于 null
```

数据 最 大 存储 量

```
512MB
```

数值 计算 最 大 范围 〈java 中 的 long 的 最 大 值 )

```
9223372036854775807
```

##### key命名规范

```
	    表名 ：主键名 : 主键值 ：字段名
eg1:   order：id    : 1234  :name
eg2:   user1：id    : 4234  :type
eg3:   equip：id    : 1534  :info
```

##### string类型应用场景

- 在redis中为大V用户设定用户信息 ,以用户主键和属性值作为key，后 台设定定时刷新策略即可

​		eg: userid:3506728370:fans — 12210947

​		eg: userid:3506728370:blogs | 6164

​		eg: userid:3506728370:focuss — 83

- 在 redis中以 json格式存储大V用户信息 ，定时刷新(也可以凌用hash类型 )

​		set user:id1:24  一

​		{name:amy,age:34,height:170cm}

```
127.0.0.1:6379> set user:id1:24 {name:amy,age:34,height:170cm}
OK
127.0.0.1:6379> get user:id1:24
"{name:amy,age:34,height:170cm}"
```

Tips3:

redis应用于各种结构型和非结构型高热度数据访问加速

### hash类型

##### 基本操作

###### 添加/修改数据

- hset key field value	(设置key的属性值)

```
127.0.0.1:6379> hset user name tom
(integer) 1
127.0.0.1:6379> hset user age 18
(integer) 1
127.0.0.1:6379> hset user info girl
```

###### 获取数据

- hget key field	(获取指定key的属性值)
- hgetall key	(获取指定key的所有属性值)

```
127.0.0.1:6379> hgetall user
1) "name"
2) "tom"
3) "age"
4) "18"
5) "info"
6) "girl"
=========================================================
127.0.0.1:6379> hget user age
"18"
```

###### 删除数据

- hdel key field [field.....]

```
127.0.0.1:6379> hdel user age name
(integer) 2
127.0.0.1:6379> hgetall user
1) "info"
2) "girl"
127.0.0.1:6379> 
```

###### 添加/修改多个数据

- hmset key field value field value

```
127.0.0.1:6379> hmset ad name tom age 29
OK
```

###### 获取多个数据

- hmget key field field

```
127.0.0.1:6379> hmget ad name age
1) "tom"
2) "29"
```

###### 获取哈希表中字段的数量

- hlen key

```
127.0.0.1:6379> hlen ad
(integer) 2
```

###### 获取哈希表中是否存在指定的字段

- hexists key field

```
127.0.0.1:6379> hexists ad name
(integer) 1
```

##### 扩展操作

###### 获取哈希表中所有字段名/值

- hkeys key

```
127.0.0.1:6379> hkeys ad
1) "name"
2) "age"
3) "info"
```

- hvals key

- ```
  127.0.0.1:6379> hvals ad
  1) "tom"
  2) "23"
  3) "isnotstudy"
  ```

###### 设置指定字段的数值数据增加指定范围的值

hincrby key field increment

```
127.0.0.1:6379> hincrby ad age 4
(integer) 27
```

hincrbyfloat key field increment

```
127.0.0.1:6379> hincrbyfloat ad age 4.59
"31.59"
```

###### 特殊添加---有该属性不添加，没有则添加

hsetnx key field value

```
127.0.0.1:6379> hsetnx bb name tom1
(integer) 0
127.0.0.1:6379> hsetnx bb sex gitl
(integer) 1
127.0.0.1:6379> hgetall bb
1) "name"
2) "tom"
3) "age"
4) "23"
5) "info"
6) "{....}"
7) "sex"
8) "gitl"
```

###### 注意事项

1. hash类型下的value只能存储字符串，不允许存储其他数据类型，不存在谋套现象。如果数据未获取到，对应的值为(nil)
2. 每个hash可以存储232-1个键值对
3. hash类型十分贴近对象的数据存储形式，并且可以灵活添加删除对象属性。但hash设计初圳不是为了存储大量对象而设计的，切记不可滥用，更不可以将hash作为对象列表使用
4. hgetall操作可以获取全部属性，如果内部field过多，饥历整体数据效率就很会低，有可能成为数据访问瓶颈

### list类型

应用于具有操作先后顺序的数据控制

##### 基本操作

left------l

right----r

###### 添加/修改数据

- lpush key value [value] ...

  ```
  127.0.0.1:6379> lpush add a b c
  (integer) 3
  ```

- rpush key value [value] ...

  ```
  127.0.0.1:6379> rpush ac1 a b c
  (integer) 3
  ```

###### 获取数据

- lrange key start end		end为负数表示倒数第几个

  ```
  127.0.0.1:6379> lrange add 0 2
  1) "c"
  2) "b"
  3) "a"
  ===================================================
  127.0.0.1:6379> lrange add 0 -1
  1) "c"
  2) "b"
  3) "a"
  ```

- lindex key index

  ```
  127.0.0.1:6379> lindex add 0
  "c"
  ```

- llen key

  ```
  127.0.0.1:6379> llen add
  (integer) 3
  ```

###### 移除指定数据

- lrem key count value

  ```
  127.0.0.1:6379> lrem add 1 c
  (integer) 1
  ```

###### 获取并移除数据

- lpop key

  ```
  127.0.0.1:6379> lpop add
  "c"
  127.0.0.1:6379> lpop add
  "b"
  127.0.0.1:6379> lpop add
  "a"
  ```

- rpop key

  ```
  127.0.0.1:6379> rpop ac1
  "c"
  127.0.0.1:6379> rpop ac1
  "b"
  127.0.0.1:6379> rpop ac1
  "a"
  ```

##### 扩展操作

规定时间内获取并移除数据		两个窗口测试

- blpop key1 [key2] timeout

  ```
  127.0.0.1:6379> blpop list1 10
  1) "list1"
  2) "d"
  (5.90s)
  ===================================================
  127.0.0.1:6379> lpush list1 d
  (integer) 1
  ```

- brpop key1 [key2] timeout

  ```
  127.0.0.1:6379> brpop list1 10
  1) "list1"
  2) "d"
  (5.90s)
  ===================================================
  127.0.0.1:6379> lpush list1 d
  (integer) 1
  ```

##### 注意事项

- list中具有索引概念，操作数据通常以队列进行入队出队操作，或者以栈的形式操作
- 获取全部数据，end用-1
- list中保存的数据都是string类型的，容量有限

### set类型

存储大量数据，查询效率高；与hash存储结构完全相同，仅存储key,不存储值(nil)，值不允许重复

##### 基本操作

###### 添加数据

- sadd key member [member]

  ```
  127.0.0.1:6379> sadd uu a11
  (integer) 1
  127.0.0.1:6379> sadd uu b88 c99
  (integer) 2
  ```

###### 获取全部数据

- smembers key

  ```
  127.0.0.1:6379> smembers uu
  1) "a11"
  2) "c99"
  3) "b88"
  ```

###### 删除数据

- srem key member [member]

  ```
  127.0.0.1:6379> srem uu b88
  (integer) 1
  ```

###### 获取集合数据总量

- scard key

  ```
  127.0.0.1:6379> scard uu
  (integer) 2
  ```

###### 判断集合是否包含指定数据

- sismember key member

  ```
  127.0.0.1:6379> sismember uu a11
  (integer) 1
  127.0.0.1:6379> sismember uu jjf
  (integer) 0
  ```

  

##### 扩展操作

###### 随机获取集合中指定数量的数据

- srandmember key [count]

  ```
  127.0.0.1:6379> smembers uu
  1) "a11"
  2) "c99"
  3) "333"
  4) "er4"
  ===================================================
  127.0.0.1:6379> srandmember uu 2
  1) "c99"
  2) "333"
  ```

###### 随机移除集合中指定数量的数据

- spop key  [count]

  ```
  127.0.0.1:6379> spop uu 2
  1) "c99"
  2) "333"
  ===================================================
  127.0.0.1:6379> smembers uu
  1) "a11"
  2) "er4"
  ```

###### 求两个集合的交、并、差集

sadd u1 aaa bbb ccc 		 sadd u2 aaa eee

- sinter key [key]...

  ```
  127.0.0.1:6379> sinter u1 u2
  1) "aaa"
  ```

- sunion key [key]...

  ```
  127.0.0.1:6379> sunion u1 u2
  1) "eee"
  2) "aaa"
  3) "ccc"
  4) "bbb"
  ```

- sdiff key [key]...

  ```
  127.0.0.1:6379> sdiff u1 u2
  1) "ccc"
  2) "bbb"
  127.0.0.1:6379> sdiff u2 u1
  1) "eee"
  ```

###### 将两个集合的交、并、差集存储到指定集合

- sinterstore destination key [key]...

  ```
  127.0.0.1:6379> sinterstore u3 u1 u2
  (integer) 1
  127.0.0.1:6379> smembers u3
  1) "aaa"
  ```

- sunionstore destination key [key]...

  ```
  127.0.0.1:6379> sunionstore u4 u1 u2
  (integer) 4
  127.0.0.1:6379> smembers u4
  1) "eee"
  2) "aaa"
  3) "ccc"
  4) "bbb"
  ```

- sdiffstore destination key [key]...

  ```
  127.0.0.1:6379> sdiffstore u5 u1 u2
  (integer) 2
  127.0.0.1:6379> smembers u5
  1) "ccc"
  2) "bbb"
  ===================================================
  127.0.0.1:6379> sdiffstore u6 u2 u1
  (integer) 1
  127.0.0.1:6379> smembers u6
  1) "eee"
  ```

  

###### 将指定数据从原始集合中移动到目标集合中

- smove source destination member

  ```redis
  127.0.0.1:6379> smove u2 u1 eee
  (integer) 1
  127.0.0.1:6379> smembers u1
  1) "eee"
  2) "aaa"
  3) "ccc"
  4) "bbb"
  ```

  

### Sorted Set类型

##### 基本操作

###### 添加数据

zadd key score1 member1 [score2 member2]

```
127.0.0.1:6379> zadd age 23 tom
(integer) 1
127.0.0.1:6379> zadd age 19 aini
(integer) 1
127.0.0.1:6379> zadd age 34 long_an
(integer) 1
```

###### 获取全部数据

zrange key start stop [withscores] 			升序（默认）

```
127.0.0.1:6379> zrange age 0 -1
1) "aini"
2) "tom"
3) "long_an"
===================================================
127.0.0.1:6379> zrange age 0 -1 withscores
1) "aini"
2) "19"
3) "tom"
4) "23"
5) "long_an"
6) "34"
```

zrevrange key start stop [withscores] 		降序

```
127.0.0.1:6379> zrevrange age 0 -1 withscores
1) "long_an"
2) "34"
3) "tom"
4) "23"
5) "aini"
6) "19"
```

###### 删除数据

zrem key member [member ...]

```
127.0.0.1:6379> zrem age tom
(integer) 1
127.0.0.1:6379> zrange age 0 -1
1) "aini"
2) "long_an"
```

###### 按条件获取数据

zrangebyscore key min max [withscores] [LIMIT offset count]

limit 与Mysql limit用法一致。分页0 1 表示两个数据

```
127.0.0.1:6379> zrangebyscore age 20 40 withscores
1) "tom"
2) "23"
3) "long_an"
4) "34"
```

zrevrangebyscore key max min [withscores] [LIMIT offset count]

```
127.0.0.1:6379> zrevrangebyscore age 70 30 withscores
1) "pzz"
2) "69"
3) "long_an"
4) "34"
```

###### 按条件删除数据

zremrangebyrank key start stop

```
127.0.0.1:6379> zremrangebyrank age 0 1
(integer) 2
删除默认排序下前两个数据
```

zremrangebyscore key min max

```
127.0.0.1:6379> zremrangebyscore age 20 50
(integer) 1
删除数值范围在20~50中的数据
```

###### 获取集合数据总量

zcard key

```
127.0.0.1:6379> zadd i1 32 a 45 b 70 c
(integer) 3
127.0.0.1:6379> zcard i1
(integer) 3
```

zcount key min max

```
127.0.0.1:6379> zcount i1 30 50
(integer) 2
```

###### 集合交、并集操作

help [命令]

zinterstore destination numkeys key [key ...] 

```
127.0.0.1:6379> zinterstore sss 3 i1 i2 i3
(integer) 2
```

zunionstore destination numkeys key [key ...] 

```
127.0.0.1:6379> zunionstore aaa 3 i1 i2 i3
(integer) 4
```

###### 获取数据对应的索引（排名）

zrank key member

```
127.0.0.1:6379> zadd i1 230 aa 129 bb 87 cc
(integer) 3
127.0.0.1:6379> zrank i1 aa
(integer) 2
```

zrevrank key member

```
127.0.0.1:6379> zrevrank i1 aa
(integer) 0
```

###### score值获取与修改

zscore key member

```
127.0.0.1:6379> zscore i1 bb
"129"
```

zincrby key increment member

```
127.0.0.1:6379> zincrby i1 3 cc
"90"
```

#### 注意事项

1. Score保存的数据存储空间是64位，如果是整数范围是-9007199254740992~9007199254740992

2. Score保存的数据也可以是一个双精度的double值，基于双精度浮点数的特征，可能会丢失精度，使用时候要慎重

3. Sorted set底层存储还是基于set结构的，因此数据不能重复，如果重复添加相同的数据，score值将被反
   复覆善，保留最后一次修改的结果