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

#### string扩展操作

###### 加减操作

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

###### string 作为数值操作

string在redis内部存储默认就是一个字符串 ，当遇到增减类操作incr，decr时会转成数值型进行计算 。

redis所有的操作都是原子性的，采用单线程处理所有业务，命令是一 个一个执行的，因此无需考虑并发带来的数据影响 。

”注意 : 按数值进行操作的数据 ，如果原始数据不能转成数值 ，或超越了 redis 数值上限学围 ，将报错 。

<!--9223372036854775807 (java 中 long 型 数据 最 大 值 ，Long.MAX_ VALUE)-->

###### 生命周期操作

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

###### 数据操作不成功的反馈与数据正常操作之间的差异

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

###### key命名规范

```
	    表名 ：主键名 : 主键值 ：字段名
eg1:   order：id    : 1234  :name
eg2:   user1：id    : 4234  :type
eg3:   equip：id    : 1534  :info
```

###### string类型应用场景

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

###### 基本操作

添加/修改数据

- hset key field value	(设置key的属性值)

```
127.0.0.1:6379> hset user name tom
(integer) 1
127.0.0.1:6379> hset user age 18
(integer) 1
127.0.0.1:6379> hset user info girl
```

获取数据

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

删除数据

- hdel key field [field.....]

```
127.0.0.1:6379> hdel user age name
(integer) 2
127.0.0.1:6379> hgetall user
1) "info"
2) "girl"
127.0.0.1:6379> 
```

添加/修改多个数据

- hmset key field value field value

```
127.0.0.1:6379> hmset ad name tom age 29
OK
```

获取多个数据

- hmget key field field

```
127.0.0.1:6379> hmget ad name age
1) "tom"
2) "29"
```

获取哈希表中字段的数量

- hlen key

```
127.0.0.1:6379> hlen ad
(integer) 2
```

获取哈希表中是否存在指定的字段

- hexists key field

```
127.0.0.1:6379> hexists ad name
(integer) 1
```

