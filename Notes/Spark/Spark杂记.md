## Spark杂记

#### 1、Spark作业调度方法：

&emsp;&emsp;Ⅰ、 `local` 测试或者实验性质的本地运行模式

&emsp;&emsp;&emsp;Local[n]是用单机模式的多个线程模拟Spark分布式运算，通常用来验证开发出来的应用程序逻辑上没有问题，其中n代表启动几个线程，每个线程默认占用一个cpu core。

&emsp;&emsp;Ⅱ、 `standAlone` Spark自己维护集群的运行

&emsp;&emsp;&emsp;使用Sparksubmit提交任务的时候，Driver运行在Client；

&emsp;&emsp;&emsp;Spark-submit --master spark:mini:6066 -deploy-mode cluster

&emsp;&emsp;Ⅲ、 `Yarn`

&emsp;&emsp;&emsp;(1)yarn client 测试常用

&emsp;&emsp;&emsp;(2)yarn cluster 生产环境使用

#### 2、Shared Variables(共享变量)

&emsp;&emsp;Ⅰ、Broadcast Variables(广播变量)  var xxx = sc.broadcast(y);

&emsp;&emsp;Ⅱ、Accumulators(累加器) var xxx = sc.longAccumulator(0);

#### 3、数据倾斜问题

&emsp;&emsp;Ⅰ、多数Task执行较快，少数Task执行时间非常长，或者等待很长时间之后提示内存不足，执行失败；

&emsp;&emsp;Ⅱ、数据倾斜原因：

&emsp;&emsp;&emsp;(1)常见于各种Shuffle操作，例如reduceByKey、GroupByKey...

&emsp;&emsp;&emsp;(2)数据本身问题：Key本身分布不均匀(包括大量的Key为空)、Key的设计不合理...

&emsp;&emsp;&emsp;(3)Spark API的使用问题：shuffle操作的并行度不合理、计算方式有误...
