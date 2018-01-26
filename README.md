# My Awesome Book

High-Performance Parallel Database Processing and Grid Databases 读书笔记



本书介绍了并行数据库中的主要并行处理算法。例如并行join，并行group by等等。



并行数据库=并行计算+数据库。



# 并行计算

## 定义

并行计算系统：一个并行计算系统是一个processor集合，processor之间相互独立，每一个processor可以独立的执行计算。processor之间通过内部互联网络进行通信（可以是bus，network）。  



并行计算系统的输入是：计算任务t,输出是t的计算结果。给定一个计算任务t，并行计算系统从processor集合中随机选择一个processor作为**host processor\(coordinator processor\)**,host processor将任务t切分成多个子任务，然后将每一个子任务调度到processor上执行，host processor等待所有子任务都计算完成后，从子任务所在的processor上收集计算结果，并合，然后返回最终结果。



并行计算中几个核心的问题：processor之间如何通信，并行度，倾斜。

# 并行数据库

并行数据库就是以并行计算为支撑的数据库系统。例如，将并行计算系统中的task替换为sql查询语句，其中host processor负责解析，优化sql，并生成执行计划，然后将只执行计划切分成多个可以并行执行的任务，并调度到processor上执行并收集计算结果。



并行数据库的目标：linear speed up，linear scala up。所谓的linear speed up，就是给定一个查询，增加processor个数，可以线性的减少查询的计算时间。linear scala up是指，增加processor个数，同时处理的查询个数线性增加。



> **并行数据库系统的核心是：并行处理算法和并行查询优化器。其中并行处理算法主要是如何将传统关系数据库中的物理操作符并行化实现，算法的设计原则是最小化处理器时间，知道原则一般是最大化并行度，最小化网络通信开销。并行处理算法的难点是如何处理skew。并行查询优化器负责对sql执行逻辑优化和物理优化，并行查询优化器的挑战是，可选择性太多，可能导致枚举空间过大。**



并行数据库最大的挑战：skew。一旦出现skew，计算性能可能急剧下降。并行数据库中的skew模型一般假设为zipf分布。









