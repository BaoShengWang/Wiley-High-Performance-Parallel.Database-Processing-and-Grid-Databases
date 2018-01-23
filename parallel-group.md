# 3.1 并行聚合

group selectivity对算法性能的影响非常大。文献1指出，上述三种算法均不能适应所有group selectivity，每一个算法只有在某个笃定的group selectivity范围内才能取得最佳性能。当group selectivety较小时，Centralized Two Phase Algo和Two Phase Aggregation性能较好，当group selectivety增大到一定程度后，Repartitioning Algorithm算法性能最好，这里令人惊奇的是，尽管Repartitioing Algorithm会distribution原始数据，但是网络传输开销并没有占据一个主导地位，原因是，**在MPP系统中，总是假设使用一个高速互连网络传输数据。**

![](/assets/并行group算法性能.png)

## 3.1.1 Centralized Two Phase AlSgorithm

书中所介绍的并行聚合算法在Adaptive Parallel Aggregation Algorithms\[1995\]这篇论文中都有涉及。书中介绍了三种并行聚合算法。

第一种并行聚合算法是Centralized Two Phase Algo，该算法在Gamma中使用。该算法首先在在每一个processor上执行local aggregation，然后再将本地聚合后的计算结果发送到coordinator processor，coordinator processor再对计算进行一次global aggregation，然后将最终的计算结果返回给用户。

当group selectivity\(分组个数比较少时\)，该算法性能较好，但是当group selectivity增加时，coordinator processor将会成为计算瓶颈。

![](/assets/并行聚合之传统算法.png)

## 3.1.2 Two Phase Aggregation

该算法的一个改进版本时two phase aggregation，该算法也是分两步，首先在每一个processor上执行local aggregation，执行完local aggregation之后，然后使用hash或者range算法将本地聚合结果distribution到其他processor，然后在进行全局聚合。

two-phase算法分为两个阶段：local aggregation和global aggregation。在local aggregation阶段，每一个processor在本地按照group by key 列聚合，例如processor1生成\(Math,300\),\(Science,500\),processor 2生成（business,100）,\(science,100\)...。接下来在global aggregation阶段，每一个processor上的所有本地聚合结果按照hash或者range算法distribution到所有processor聚合生成全局计算结果。

![](/assets/并行聚合之-两阶段算法.png)

## 3.1.3 Repartitioning Algorithm\(Redistribution Method\)

该算法和前两种算法相比，没有local aggregation。该算法假设集群使用高速互联网络，算法的第一个阶段在每一个processor上执行repartition/distribution操作，将具有相同key的值聚合到同一个processor上。  
算法的第二个阶段对分区后的数据进行全局聚合操作。

![](/assets/并行聚合之分区算法.png)

## 3.1.4可适配并行聚合算法

可以参考Adaptive Parallel Aggregation Algorithms 论文。

# 3.2 并行排序



