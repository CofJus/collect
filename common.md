1.【系统设计】[局部性原理——各类优化的基石](https://cloud.tencent.com/developer/article/1777697)

处理器在访问某些数据时短时间内存在重复访问，某些数据或者位置访问的概率极大，大多数时间只访问*局部*的数据。基于局部性原理，计算机处理器在设计时做了各种优化。

2.【MySQL】[预读和预读失效](https://juejin.cn/post/7211120847787655227) 

线性预读：如果顺序的访问了一个区里的多个数据页，访问的数据页的数量超过了这个阈值，此时就会触发预读机制，把下一个相邻区中的所有数据页都加载到缓存里去。

普通LRU，预读的数据可能会把热数据从缓冲池挤出去。

[一文读懂 InnoDB 缓冲池(buffer pool) 工作原理](https://segmentfault.com/a/1190000022754487)

局部性原理 -> 磁盘按页加载 -> 线性预读 -> 避免预读失效对LRU性能的影响 -> LRU分old和new区（类似新生/老年代）

缓冲池污染：大量数据（全表扫描）一次性加载到缓冲池，导致热数据被冲掉，性能严重下滑

缓冲池污染优化：old区停留时间。old区短期内频繁被读取的页不移动到new区

3.【MySQL】[插入缓冲 提高非聚簇索引插入效率](https://juejin.cn/post/7260499321983696957)

（非聚簇索引存储在独立的数据里，不影响聚簇索引的查询，数据插入和索引插入是两回事）

非聚簇索引不连续自增&不唯一&数据离散，插入效率低

用insert buffer（本质也是一棵B+树）缓存待插入的非聚簇索引，合适的时机合并插入到索引中

4.【MySQL】[两次写](https://juejin.cn/post/7062894835220348965)

如果发生了写失效的情况，可以通过重做日志进行恢复，重做日志是对页的物理操作，如果这个页本身发生了损坏，再对其进行重做是没有意义的。在应用重做日志前，用户需要一个页的副本，当发生写失效的时候，先通过页的副本来还原页，在进行重做，这就是 `double write`。

5.【MySQL】快照读和当前读

6.【MySQL】redolog和binlog

|          | redolog                                                      | binlog                                                |
| -------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| 作用     | crash-safe 掉电故障恢复                                      | 备份恢复 主从复制 ACID的Consistency                   |
| 实现     | innodb引擎层                                                 | server层                                              |
| 内容     | 物理日志 记录对页的操作                                      | 逻辑日志 记录语句的原始逻辑                           |
| 格式     | [redolog存储结构](https://juejin.cn/post/6895265596985114638) | raw statement mixed                                   |
| 存储     | 32个日志文件组 checkpoint覆盖循环写 容量固定                 | 追加写 默认全量永久保留                               |
| 写入方式 | 缓冲池公共redolog buffer->文件系统缓存->fsync刷盘            | 线程私有binlog cache->文件系统缓存->fsync刷盘         |
| 写入时机 | 事务prepare和commit之间不断写入                              | prepare->一次写binlog cache->commit->一次写binlog文件 |
|          |                                                              |                                                       |

7.【Spring Boot】[jar包和加载机制](https://juejin.cn/post/7353582927680208933)

- 通过maven插件把普通jar  repackage为fat-jar，包含了所有依赖和静态资源，可以直接通过java命令运行。

- Spring Boot的启动类加载器通过MANIFEST.MF文件中的依赖路径，加载所有依赖并执行主类
- Spring Boot内嵌tomcat，tomcat最终将jar文件解压到操作系统的临时目录，作为web根目录运行的。开箱即用

8.【Java并发】[锁](https://tech.meituan.com/2018/11/15/java-lock.html)

- 乐观锁和悲观锁
  - 乐观锁：认为自己在使用数据时不会有别的线程修改数据，所以不会添加锁，只是在更新数据的时候去判断之前有没有别的线程更新了这个数据。如果这个数据没有被更新，当前线程将自己修改的数据成功写入。如果数据已经被其他线程更新，则根据不同的实现方式执行不同的操作（例如报错或者自动重试）。Java中是通过使用无锁编程来实现，最常采用的是CAS算法，Java原子类中的递增操作就通过CAS自旋实现的。
  - 悲观锁：认为自己在使用数据的时候一定有别的线程来修改数据，因此在获取数据的时候会先加锁，确保数据不会被别的线程修改。Java中，synchronized关键字和Lock的实现类都是悲观锁。
- 

9.【Java并发】[AQS](https://tech.meituan.com/2019/12/05/aqs-theory-and-apply.html)

10.【Java并发】[volatile](https://juejin.cn/post/7052325968676913189)

- 保证可见性
- 禁止指令重排

11.【MyBatis】[MyBatis缓存](https://tech.meituan.com/2018/01/19/mybatis-cache.html)

- MyBatis一级缓存的生命周期和SqlSession一致。

- MyBatis一级缓存内部设计简单，只是一个没有容量限定的HashMap，在缓存的功能性上有所欠缺。

- MyBatis的一级缓存最大范围是SqlSession内部，有多个SqlSession或者分布式的环境下，数据库写操作会引起脏数据，建议设定缓存级别为Statement。

12.【系统设计】[分布式事务解决方案](https://cloud.tencent.com/developer/article/1806989)

13.【Java并发】[Happens-before原则](https://juejin.cn/post/6960128601249284110)和JMM

说到Happens-before原则，就要先理解as-if-serial语义，即单线程的情况下指令重排序不能影响程序执行的结果。

Happens-before原则

- 如果一个操作 Happens-before 另一个操作，那么第一个操作的执行结果将对第二个操作可见，而且第一个操作的执行顺序排在第二个操作之前。

- 两个操作之间存在 Happens-before 关系，并不意味着 Java 平台的具体实现必须要按照 Happens-before 关系指定的顺序来执行。如果重排序之后的执行结果，与按 Happens-before 关系来执行的结果一致，那么这种重排序并不非法（也就是说，JMM 允许这种重排序）

具体规则

- 前面的代码执行Happens-before后面的代码执行（线程内代码按序执行）
- 加锁Happens-before解锁
- A Happens-before B && B Happens-before C 则 A Happens-before C
- Thread.start() Happens-before 线程的任意动作
- 构造函数  Happens-before 析构函数

14.【Java并发】Java线程状态转换

![](https://cdn.tobebetterjavaer.com/stutymore/thread-state-and-method-20230829143200.png)

