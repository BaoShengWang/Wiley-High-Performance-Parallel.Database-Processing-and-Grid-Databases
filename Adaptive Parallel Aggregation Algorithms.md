# Adaptive Parallel Aggregation Algorithms 论文阅读笔记

# 背景

1995年， Ambuj Shatdal 和Jerey F. Naughton发表了Adaptive Parallel Aggregation Algorithms 论文，在这篇论文里面，作者对比了算法1，算法2，算法3的优缺点，并指出了各自算法的适应场景，然后作者提出了一个可适配的并行聚合算法。

我们知道，算法1，算法2，算法3都有其适应的场景，算法2在group number较小的时候性能较好，算法3在group number较大的时候性能较好。三种算法的性能对比如下：

![](/assets/并行group算法性能.png)

作者的目的是设计一种新的算法\(目前来看，这个算法的名字就叫做Adaptive Parallel Aggregation Algorithms \)，该算法能够在运行的时候根据group number的多少，动态的在算法1，算法2，算法3之间选择一个性能最佳的算法来实现并行聚合。

* **算法1： Centralized Two Phase AlSgorithm**，该算法的核心平均是coordinator processor是串行的，当group number较大时，coodinator processor将成为系统瓶颈。
* **算法2： Two Phase Aggregation**，该算法的核心开销是local aggregation阶段的磁盘io开销。
* **算法3： Repartitioning Algorithm\(Redistribution Method\)**。与算法2相比，该算法没有local aggregation。该算法也是分为两个阶段，第一个阶段是Repartitioning/Redistribution，每一个processor使用hash或者range算法将tuple分发到所有processor，第二个阶段，在每一个processor执行聚合操作。该算法的核心开销时网络开销。

算法2和算法3之间的核心矛盾是：算法2在local aggregation时，可能会有较高的磁盘IO开销，因为其可能使用hash或者sort算法来执行local aggregation，当数据非常大的时候，我们知道只能使用多路哈希或者多路sort来实现聚合，通常而言，这需要多次读写表，从而导致较大的磁盘开销。而算法3的没有local aggregation，其在distribution时只会从磁盘读取一遍数据，其核心开销时网络数据传输。如果我们假设使用的高速互联网络，比如10Gps，那么网络传输数据将非常快。算法2和磁盘IO开销和算法3的网络开销可能相互抵消。

> \* 核心：
>
> 1. group number的大小影响并行聚合算法的性能。
> 2. 算法2的核心开销是磁盘IO。
> 3. 算法3的核心开销是网络
> 4. 使用算法3的前提需要满足两个条件：group number非常大，processor之间的网络带宽和速度非常高。

# 实现方案

作者提出了三种实现方案：Sampling Based Approach，Adaptive Two Phase Algorithm，Adaptive Repartitioning Algorithm。

## 方案1：Sampling Based Approach

查询优化器在执行查询之前，先对数据表进行采样，如果样本中group number数据小于预设的一个阈值，那么将使用two phase算法，否则使用repartitioning 算法。

## 方案2：Adaptive Two Phase Algorithm

该算法的思想是以tow phase来执行，如果在运行的过程中发现group number数据变得很大，那么算法将切换到repartitioning算法来执行。现在的问题是如何检测group number变得很大呢？作者还了一个思路，如果local aggregation频繁的发生spill to disk操作，那么就说明group number太大了，因此可以切换到repartitioning算法来执行。具体实现步骤如下：

1. 如果某个节点在进行本地聚合时发现内存已经满了，那么就将聚合好的计算结果distribution，然后释放内存，接下来

将剩下的元祖直接distribution。

1. 在global aggregation阶段，它可能会收到两种类型的数据：一种时local aggregation result，一种是原始元祖。
   它必须对二者进行一个最终的合并处理。可以以local aggregation result来build hash table，然后以raw tuple作为probe来尽心工具盒。

![](/assets/并行可适配聚合算法.png)

## 方案3：Adaptive Repartitioning Algorithm

和算法2类似。只不过一开始使用repartitioning算法

