# 真的理解Jmeter线程数、Ramp-Up、循环次数真的理解？

Jmeter平时做API接口的吞吐量测试，几个配置项很好上手没有去深入思考。
有几个疑问：
1 Ramp-Up配置时间实际作用？
2 循环是在哪个时间点开始执行的？与线程组的关系是怎样的？
3 配置10/s个并发如何实现？

有疑问就实践，读别人博客难免找不到自己想要的内容。
实践疑问1：
Ramp-UP time的实际作用，图一为例解释：有5个线程需要5秒发送完成，也就是1个/s请求。（如果线程数是10，ramp-up time是5，又是什么意思？）
注：线程数抽象实例化后就是用户数，Ramp-up time是规定所有用户在时间段内把请求发送完（前提条件循环次数是1），而且请求的时间间隔是固定的=Ramp-Up time/线程数。

![img](http://img.code520.net/blog/202206291806631.png)

看执行结果统计：从结果中可以看出，总计时耗时5s，请求数是5个，时间间隔是固定的1秒（这个总时耗和时间间隔就是Ramp-Up time实际作用）。

![img](http://img.code520.net/blog/202206291806424.png)

疑问二： 循环是在哪个时间点开始执行的？与线程组的关系是怎样的？
解答：循环开始时间几乎与启动时间并行，
如下图解释：a循环数为1的解释是1个/s请求发5次请求。
b循环数为2时解释是（1个/s请求发5次）执行2遍（循环数），循环开始执行时间几乎与第一遍执行时间并行。（仔细观察Thread Name的时间点）

![img](http://img.code520.net/blog/202206291806356.png)

![img](http://img.code520.net/blog/202206291807643.png)

疑问三：配置10/s个[并发](https://so.csdn.net/so/search?q=并发&spm=1001.2101.3001.7020)如何实现？是否可以通过（线程数，Ramp-Up time、循环数）配合来实现？
方法一： 通过（线程数，Ramp-Up time、循环数）配合来实现
组合有很多种，我列了几个

![实例](http://img.code520.net/blog/202206291807217.png)

方法二：用Jmeter自带Constant Throughput Timer插件实现，实际测试这样配置要达到10/s的效果不可行。（原因：评估这个配置限制TPS值的最小单位是minute，所有对数量小的请求统计测试不准是可以理解的。）具体是什么原因该以后工作留个疑问，多阅读多思考。
对上minute级别的大并发测试这个配置项应该是准的。

![img](http://img.code520.net/blog/202206291807341.png)

![img](http://img.code520.net/blog/202206291807437.png)

最后思考下：Ramp-Up time 为0 是什么意思？
用压测服务器最大的能力持续发送请求





![image-20220629180858681](http://img.code520.net/blog/202206291808803.png)