在OLAP应用中，经常会对大量的数据进行join，group操作。例如在星型模型中，可能对会多个维度表和一个事实表进行join，然后按照维度来groupby，最终得到各个维度的统计指标。

如果一个SQL查询中含有join，和groupby，那么我们管这个查询叫做aggregate-join查询。在并行数据库中，如何实现aggregate-join查询呢？一般而言，有这么两种思路：使用join attribute作为partitioning attribute，也就是先按照join attribute将两个表R和S分区到N个processor上，然后在每一个processor上执行join，然后在对join之后的结果再次按照groupby attribute进行partitioning，然后聚合得到结果，另外一种实现思路是先按照groupby attribute进行partitioning，然后在进行join。

> **作者提出三种实现方案，分别是Join Partitioning Method\(JPM\),Aggregate Partitioning Method\(APM\),Hrbrid Partitioning Method\(HPM\)。需要注意的是，作者并没有对比三种算法的性能。**







> \*QUERY 2
>
> Select PARTS.City, AVG\(Qty\)
>
> From PARTS, SHIPMENT
>
> Where PARTS.P\# = SHIPMENT.P\#
>
> Group By PARTS.City
>
> Having AVG\(Qty\)&gt;500 AND AVG\(Qty\)

# Join Partitioning Method

* **data partitioning** 按照join attribute对两个表进行分区，将数据分区到N个processor上。例如，按照P\# attribute,将PARTS和SHIPMENT分区到4个processor上，使得第一个processor 1到-processor4分别存储pI-p99, p100- p199, p200-p299, and p300-399数据。
* **join** 在每一个processor上执行join，可以使用任何传统的join算法实现join，例如nested-loop，grace hash，sort-merge。
* **local aggregation**:执行本地聚合
* **re-distribution** 对每一个processor上join之后的结果按照groupby attribute 分区到N个processor上。例如，我们对上一步骤中join之后的结果，按照city 分区到4个processor，使得processor 1-processor 4分别存储以 A-G, H-M, N-T, and U-Z的数据。
* ** global aggregation **在每一个processor上进行本地聚合
* **Consolidation** coordinator processor对每一个processor上的聚合结果做一个union返回。
* ![](/assets/JPM.png)

## Aggregate Partitioning Method

* 按照groupby attribute 对包含 groupby attribute那个表分区到N个processor。例如，按照city attribute 将PARRTS表分区到4个processor上。
* 将另外一个表复制到每一个processor上。例如，这里将SHITMENT表复制到Processor1，Processor2，Processor3，Processor4.
* 在每一个processor上先执行join，然后执行groupby聚合操作。
* Consolidation：coordinator processor对每一个processor上的聚合结果做一个union返回。

![](/assets/GPM.png)

