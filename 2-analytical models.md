# 第二章 分析模型analytical models

并行数据库系统中有那么多查询算法，如何量化他们的执行时间呢？开销方程/公式cost equations/formulas用来计算查询算法的时间。

> \* 本章需要重点掌握开销方程中常用的几个参数意义及其符号，这些符号后面会经常用到，还要知道每一个参数用在什么地方，比如关系的元祖个数\|R\|用在什么地方，然后重点了解如何对数据倾斜进行建模。



开销方程\(cost equation\),在并行数据库系统中，一个开销方程是一组变量的函数，记为f\(x1,x2,x3,...,xn\)，在查询运行的时候，这些变量将被置换为实际的值，从而得出开销值，这个开销值的单位是时间。因此开销方程可以用于估计查询算法的计算时间。开销方程由以下几个变量组成：

*  ** data paramters** 

*  ** system paramters**

*  ** query paramters**

*  ** communication cost**

##  2.1 data parameters

关系R的元祖个数记为\|R\|,元祖大小记为R，单位为字节。

cpu 开销时基于记录个数的，而io开销时基于page的。因此，当实际到CPU开销时，将使用\|R\|，当涉及到IO开销时，将使用R.

关系R被划分到N个processor上并行执行，如果数据均匀分布，那么每个processor上有\|R\|/P个记录，实际上，数据可能呈现非均匀分布，这个时候有的processor 上记录多一些，有的少一些，我们并不关注每一个processor上的记录大小，我们只关注记录数最多的那个processor。因为整个关系R的计算时间由记录数最多的那个processor的计算时间所决定。

## 2.2 system parameters

* N,processor个数。

* Page size in bytes.

R/P时page总数。当涉及到IO开销时，会用到这个值。我们知道，关系R被划分到N个processor上，每一个processor的Ri /P就是processor i上的page个数。

