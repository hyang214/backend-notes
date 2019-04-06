# JAVA面试应急


### Java线程的状态
[ava线程的6种状态及切换(透彻讲解)](https://blog.csdn.net/pange1991/article/details/53860651/)

### 进程和线程的区别，进程间如何通讯，线程间如何通讯
进场是资源的分配单位，线程是进程内部的调度单位，共享资源
进程间如何通讯
​	rpc、mq、socket、pipe、shared memory
线程间如何通讯
​	互斥锁、条件变量、读写锁 

### COW
子进程从父进程Fork出来后，暂时不进行内容的复制，而是等到某个内容进行修改的时候，复制这一块的内容，从而达到节约内存和加快相应的功效

### HashMap、HashTable、ConcurrentHashMap
[HashMap、HashTable、ConcurrentHashMap的区别](https://www.cnblogs.com/zq-boke/p/8654539.html)
[Java并发编程总结4——ConcurrentHashMap在jdk1.8中的改进](http://www.cnblogs.com/everSeeker/p/5601861.html)
HashTable:
+ 内部数据组来实现Hash，每个位置对应一个链表
+ 线程安全，存取都锁全表
HashMap: 
+ 内部数据组来实现Hash，每个位置对应一个链表
+ 线程不安全，存取无锁，性能更佳
ConcurrentHashMap:
+ 实现：
	+ 1.5 分成16个段，每个段是数据组来实现Hash，每个位置对应一个链表
	+ 1.8 不再使用分段，而是HashEntry数组元素作为锁，对每个hash位置单独加锁，进一步减少冲突，单个节点超过8，改用红黑树替换链表
+ 线程安全

### Cookie和Session的区别 
[Cookie和Session的区别](https://www.cnblogs.com/wswang/p/6062461.html)
需要这2种技术的原因是，http是无状态的，没法记录用户的信息，所以需要
+ Cookies是服务器在本地机器上存储的小段文本并随每一个请求发送至同一个服务器，是一种在客户端保持状态的方案。
+ Session是存在服务器的一种用来存放用户数据的类HashTable结构。

### 索引有什么用？如何建索引？
索引的作用，加快数据读取，提高读取性能
索引的分类：
+ primary 


















