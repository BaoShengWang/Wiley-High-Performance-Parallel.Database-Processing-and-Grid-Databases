如果一个查询中包含join和group，那么我们把这种查询叫做aggregate-join或者group-by-join查询。

在OLAP和数据仓库应用中，group-by-join是非常常见的操作，也是非常昂贵的操作，提高group-by-join计算性能是MPP数据库中的重中之重。

在传统关系数据库中，通常是先执行join，然后在执行groupby。但是有一些研究已经发现，如果能够先执行groupby，那么能够提高group-by-join的查询性能：这是因为先group by的计算结果通常要远远的小于原始表，join一个小表能够降低join中间结果的大小，从而提高join的查询性能。

我们把先执行groupby，然后在执行join这种优化技术叫做group-before-join.否则叫做group-after-join。

但是，并不是所有的group-by-join查询都能够以group-before-join方式执行，只有当join attribute和group attributei相同时，才能以group-before-join方式执行。

# 1. group-before-join

## 1.1 Early Distribution Schema

Eary Distribution Schema借鉴了并行join算法的思想：先将表R和S按照join attribute分区到N个processor上，然后在每一个processor上执行join。Early Distribution Schema具体执行步骤如下：

**1.distribution **因为group attribute和join attribute相同，所以可以选择其中一个作为partitioning attribute，然后按照partitionging attribute，将表R和S分区到N个processor，分区算法可以是hash或者range。

**2.group**，distribution之后，具有相同group attribute的元祖都存在同一个processor上，因此可以在每一个processor上执行local aggregation。

**3.join**，将上一步骤中产生的聚合结果和另外一个表进行join，最后返回结果。

因为该算法是先分发原始数据，所以可能具有非常高的通信开销，如果我们使用高速互连网络，那么可以使用该算法。

研究发现，当group number数目非常大，该算法具有非常好的性能。

## 1.2 Early GroupBy with partitioning scheme

1.local aggregation,在分发之前，先在每一个processor上执行local aggregation。

2.distribution，按照group attribute 将local aggregation分区到N个processor上。

3.global aggregation and join，在每一个processor上执行全局聚合，然后join，返回最终结果。



该算法因为先执行local aggregation，然后在distribution，因此当group number数据比较少时，可以显著的降低通信开销。

## 1.3 Early GroupBy with replication scheme

# 2.group-after-join



