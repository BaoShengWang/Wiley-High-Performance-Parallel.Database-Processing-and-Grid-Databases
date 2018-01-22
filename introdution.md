\# 在此处输入标题

标签（空格分隔）： 并行数据库-介绍

---

Wiley High Performance Parallel.Database Processing and Grid Databases读书笔记

## 1.介绍

### 1.1 度量指标

并行数据库系统的目标是提升数据计算性能。吞吐和响应时间是衡量并行数据库系统性能的两个重要指标。如何量化并行数据库系统的吞吐和响应时间呢，我们引入两个指标：speed up和scala up。

####  1.1.1 speed up

> aaa

spped up 说的是，给定一个数据库操作，增加一个processor之后，计算时间能减少多少。可以发现，speed up主要用于衡量系统的响应时间\(respond time\)

公式:

$$speed up=单个处理器计算时间/多个处理器计算时间$$

如果speed up等于分母，那么我们说这是linear speed up，如果小于，那么我们说这是sublinear speed up,否则，我们说这是superlinear speed up.

举一个例子，如果某个计算任务，用1个处理器计算它，用时100分钟，当使用5个处理器时，计算用了20分钟，那么speed up =100/20=5。speed up正好等于\*\*处理器个数\*\*，因此这是linear speed up。如果使用5个处理器计算了50分钟，那么speed up=100/50=2,该值小于处理器个数5，因此这是sublinear speed up。

#### 1.1.2 scala up

&gt;\* scala up,说的是同时增加事务个数和processor个数，事务处理时间是否维持不变。其衡量的是系统的吞吐能力\(thoughput\)

举一个例子，如果1个处理器10分钟内可以处理100个事务，现在如果将事务增加到300个，并同时将处理器个数也增加到3个，那么在事务和处理器增加前后，平均单个事务的计算时间是否发生了变化：

&gt;\* linear scala up,如果3个处理器计算300个事务时间仍然是10分钟，那么此时scala up=1，这说明平均单个事务处理时间没有发生变化，很好，这是linear scala up。

&gt;\* sublinear scala up,如果计算时间超过了10分钟，比如是15分钟，那么此时scala up=0.67，这说明原来平均处理一个事务是1分钟，现在降低到了0.67分钟，虽然吞吐增加了，但是事务相应时间取提高了。

&gt;\* superlinear scala up,很少见。

\#\#\# 1.2 瓶颈

有很多因此可能会降低系统的speed up和scala up。其中，start up和consolidation会影响系统的speed up。Interference and Communication则同时影响speed up和scala up。

\*\*start up\*\*,启动开销，启动多个processor是需要时间的，如果计算时间非常小，那么启动开销可能要超过计算时间。

\*\*Consolidation\*\*，固定开销，说的是对于一个给定的计算任务，这个计算任务中有些步骤是串行计算的\(计算时间为s\)，有些步骤是并行计算的（计算时间是p），那么整个计算任务所减少的时间会有一个上限，当处理器个数增加到一定个数后，整个计算任务的计算时间将受限于串行计算时间s。

举一个例子，某个计算任务有10个step，其中前8个step是并行的，最后2个step是串行的。假设每一个step计算时间需要1分钟，如果我们串行执行这个计算任务，那么将需要10分钟。

如果有8个processor同时处理前8个step，那么其计算时间是1分钟，而最后两个step需要串行执行，需要2分钟时间。整个计算任务执行时间是3分钟。

此时speed up=10/3=3.33，该值远远小于处理器个数9，因此这是sublinear speed up。也就是说尽管你增加了这么多processor，但是计算所需要的时间并没有减少太多。

Interference and Communication说的是，在并行数据库系统中，processor之间经常会协同工作，processor a的输入可能依赖processor b的输出，因此在processor b必须等待processor a执行完成之后才能执行。

