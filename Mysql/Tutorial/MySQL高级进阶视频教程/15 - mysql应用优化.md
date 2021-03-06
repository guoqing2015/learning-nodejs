# mysql应用优化

## 访问数据库采用连接池

### 为何要使用数据库连接池

**数据库连接池的原理：**

数据库连接是一种关键的有限的昂贵的资源，这一点在多用户的网页应用程序中体现得尤为突出。

一个数据库连接对象均对应一个物理数据库连接，每次操作都打开一个物理连接，使用完都关闭连接，这样造成系统的性能低下。 数据库连接池的解决方案是在应用程序启动时建立足够的数据库连接，并将这些连接组成一个连接池(简单说：在一个“池”里放了好多半成品的数据库联接对象)，由应用程序动态地对池中的连接进行申请、使用和释放。对于多于连接池中连接数的并发请求，应该在请求队列中排队等待。并且应用程序可以根据池中连接的使用率，动态增加或减少池中的连接数。

数据库连接池在初始化时将创建一定数量的数据库连接放到连接池中, 这些数据库连接的数量是由最小数据库连接数来设定的.无论这些数据库连接是否被使用,连接池都将一直保证至少拥有这么多的连接数量.连接池的最大数据库连接数量限定了这个连接池能占有的最大连接数,当应用程序向连接池请求的连接数超过最大连接数量时,这些请求将被加入到等待队列中.


**数据库连接池的最小连接数和最大连接数的设置要考虑到以下几个因素:**

- 最小连接数:是连接池一直保持的数据库连接,所以如果应用程序对数据库连接的使用量不大,将会有大量的数据库连接资源被浪费.
- 最大连接数:是连接池能申请的最大连接数,如果数据库连接请求超过次数,后面的数据库连接请求将被加入到等待队列中,这会影响以后的数据库操作
- 如果最小连接数与最大连接数相差很大:那么最先连接请求将会获利,之后超过最小连接数量的连接请求等价于建立一个新的数据库连接.不过,这些大于最小连接数的数据库连接在使用完不会马上被释放,他将被放到连接池中等待重复使用或是空间超时后被释放.

### 传统的连接机制与数据库连接池的运行机制区别

使用了数据库连接池的机制：

- 程序初始化时创建连接池

- 使用时向连接池申请可用连接

- 使用完毕，将连接返还给连接池

- 程序退出时，断开所有连接，并释放资源

![数据库连接池的运行机制](https://images2018.cnblogs.com/blog/137084/201805/137084-20180524194122461-804315041.png)





## 采用缓存减少对于mysql的访问

1. 避免对同一数据做重复检索

2. 使用查询缓存

`query_cache_type` 这个系统变量控制着查询缓存工能的开启的关闭。

query_cache_type=`0`时表示关闭，`1`时表示打开，`2`表示只要select 中明确指定SQL_CACHE才缓存。

查询缓存相关的系统变量：

　　`have_query_cache`　　表示这个mysql版本是否支持查询缓存。

　　`query_cache_limit`　　 表示单个结果集所被允许缓存的最大值。

　　`query_cache_min_res_unit`　　每个被缓存的结果集要占用的最小内存。

　　`query_cache_size`　　用于查询缓存的内存大小。

 

如何监控查询缓存的命中率： 

　　`Qcache_free_memory`　　查询缓存目前剩余空间大小。

　　`Qcache_hits`　　　　　     查询缓存的命中次数。

　　`Qcache_inserts`　　　　　 查询缓存插入的次数。

　　也就是说缓存的命中率为 Qcache_hits/(Qcache_hits+Qcache_inserts)


## 读写分离
