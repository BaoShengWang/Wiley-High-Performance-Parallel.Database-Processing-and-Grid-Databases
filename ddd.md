事务是用来简化用户编程的一种技术，手段。事务具有4个基本属性:ACID,其中最重要的两个是原子和隔离性。

原子操作可以简化用户在故障发生时对数据的处理。原子操作说的时，一组写操作，要么全不成功，要么全部失败。

其实现方式时redo/undo日志。

隔离性用于简化多用户对同一个数据进行操作时的处理，其核心内容是，给定一组事务集合，如何产生一个seria调度。

调度算法是优先图算法。隔离性的第二个核心内容是，锁。





关于并发，第一个要掌握的概念是，什么是schedule，什么是seria schedule，什么equivalence schedule。



transaction:读写序列。T={r,w,r,....}

schedule：一组事务T1,T2,T3...的schedule是一个读写序列，其中其中属于同一个事务中两个action顺序和原事务一样。

seria schedule\(序列化调度\)：按序执行事务T1,T2...

equivalence schedule：result equivalence，conflict equivalence，view equivalence，。

  conflict equivalence：事务S conflict equivalence S\`,

  

serializable schedule\(可序列化调度\)：如果一个schedule等价一个seria schedule。也就是说，这个schedule可以转换为seria schedule。二者效果等价。









关于mpp数据库中的分区。

分区复制，将分区存储到哪一个节点上。

DB2中使用如下测录：用于可以创建node group，然后在创建表的时候可以指定将数据存储到哪一个node group。不同的分区可以放到同一个node group，也可以放到不同的

node group。

一种高效的做法是colllocate join，将具有相同key的之放到一个节点上，比如，可以将两个表都放到同一个node group，然后使用相同的哈希函数，相同的分区数，就可以去报具有

相同key的值属于同一个节点，当进行join时，就不需要跨网络传输数据了。 



另外一种做法是复制数据，例如，小表可以复制到每一个节点上。或者使用c-store中的做法，为不同的查询模式。





在mpp数据库中，查询语句通常提交给一个叫做coordinator task，这个coordinator 负责解析sql语句，并调用查询优化器来生成查询计划，

然后交给调度器来将计算任务调度到底层的node分布执行，并监听任务是否执行成功，如果成功则收集结果返回用户，如果失败，那么可以重新执行。





mpp中的并行查询执行引擎，





join，最高效的方法是collocate join，不要任何数据传输。第二





分区策略：

 hash

 range

 list

 composite



并行join：

  collocate join

  direct join

  repartition join\(如何解决skew key\)

  broadcast join

并行filter/select....

  partitioned parallelism：说白了，就是有多少个partition，就有多少个task从partition上读取数据。

  并行度和分区数相关。添加两个并行操作符：merge和split。merge operator，可以将其input sprt的数据合并成一个output spot,添加split operator，用于将一个partition分裂成多个，比如在merge后添加一个

  spit operator。  在并行数据库中，每一个operator有一组input spot，一组output spot。然后使用merge和split来合并/切分数据。

  例如，关系R有三个分区，那么merge operator&lt;-scan of partition 1,scan of partition 2 ,scan of partition 3.

  pipe parallelism

  independent parallelism

  

数据传输抽象

 split table IN Gamma:查询一个split table，这个table记载了tuple应该去什么地方，每一个operator在输出数据时都会查询split table来决定数据到哪个位置。

 table queue IN DB2

 exchange IN volcano

 

vertica：所有节点时对等的，sql语句可以发送到任意一个节点，如果发送到了节点A，那么节点A就是initiator节点，他负责解析/查询优化然后将计算分发到其他节点，

其他节点执行完计算结果后会将结果发送给initiator，initiator对结果进行汇总，然后发送给用户。

greenplum：master+segment node组成，用于将sql发送到master，master负责解析/优化，然后将计划分发到segment node去执行。可以发现。greenplum不是对等架构。

greenplumn使用全局系统目录哦。



google deremal，是多级查询，而不是vertica和greenplum中的两层查询，在vertica和greenplumn中，都有一个类似coordinator task用于解析，优化，分发，收集计算结果。

尤其是收集计算结果这一步，可能会成为瓶颈。google dremal就是多级的。





mpp数据库对网络要求非常高，需要10G网络带宽，万兆。需要的内存也非常大，vertica中推荐的内存大小是96G以上，greenplum中推荐的内存大小是64G以上。

