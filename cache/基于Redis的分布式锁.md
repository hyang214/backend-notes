# 基于Redis的分布式锁


## 传统方案
### 加锁
```
SET resource_name unique_value NX PX 30000
```

### 解锁

#### 直接解锁
```
public static void unLock(Jedis jedis, String lockKey, String requestId) { 
   
    // 判断加锁与解锁是不是同一个线程 
    if (requestId.equals(jedis.get(lockKey))) { 
        // lockkey锁失效，下一步删除的就是别人的锁 
        jedis.del(lockKey); 
    } 
   
}  　
```
不具备原子性

#### Lua脚本
+ 前提：Redis 使用单个 Lua 解释器去运行所有脚本，并且， Redis 也保证脚本会以原子性(atomic)的方式执行
```
- 释放锁（lua脚本中，一定要比较value，防止误解锁）
if redis.call("get",KEYS[1]) == ARGV[1] then
    return redis.call("del",KEYS[1])
else
    return 0
end
```

### 问题
事实上这类琐最大的缺点就是它加锁时只作用在一个Redis节点上，即使Redis通过sentinel保证高可用，如果这个master节点由于某些原因发生了主从切换，那么就会出现锁丢失的情况：
+ 1. 在Redis的master节点上拿到了锁；
+ 2. 但是这个加锁的key还没有同步到slave节点；
+ 3. master故障，发生故障转移，slave节点升级为master节点；
+ 4. 导致锁丢失。

## 多实例加锁

### 原理
对于 2N + 1 个实例，保证在其中 N + 1 个实例上加锁成功，

### 步骤
+ 获取当前Unix时间，以毫秒为单位。
+ 依次尝试从5个实例，使用相同的key和具有唯一性的value（例如UUID）获取锁。当向Redis请求获取锁时，客户端应该设置一个网络连接和响应超时时间，这个超时时间应该小于锁的失效时间。例如你的锁自动失效时间为10秒，则超时时间应该在5-50毫秒之间。这样可以避免服务器端Redis已经挂掉的情况下，客户端还在死死地等待响应结果。如果服务器端没有在规定时间内响应，客户端应该尽快尝试去另外一个Redis实例请求获取锁。
+ 客户端使用当前时间减去开始获取锁时间（步骤1记录的时间）就得到获取锁使用的时间。当且仅当从大多数（N/2+1，这里是3个节点）的Redis节点都取到锁，并且使用的时间小于锁失效时间时，锁才算获取成功。
+ 如果取到了锁，key的真正有效时间等于有效时间减去获取锁所使用的时间（步骤3计算的结果）。
+ 如果因为某些原因，获取锁失败（没有在至少N/2+1个Redis实例取到锁或者取锁时间已经超过了有效时间），客户端应该在所有的Redis实例上进行解锁（即便某些Redis实例根本就没有加锁成功，防止某些节点获取到锁但是客户端没有得到响应而导致接下来的一段时间不能被重新获取锁）。


## Reference
[Redlock：Redis分布式锁最牛逼的实现](https://zhuanlan.zhihu.com/p/59256821)
[redis分布式锁和lua脚本](http://www.cnblogs.com/number7/p/8320259.html)