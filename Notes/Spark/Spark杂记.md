## Spark杂记

#### 1、Spark作业调度方法：

&emsp;&emsp;Ⅰ、 `local` 测试或者实验性质的本地运行模式

&emsp;&emsp;&emsp;local[n]是用单机模式的多个线程模拟Spark分布式运算，通常用来验证开发出来的应用程序逻辑上没有问题，其中n代表启动几个线程，每个线程默认占用一个cpu core。

&emsp;&emsp;Ⅱ、 `standAlone` Spark自己维护集群的运行

&emsp;&emsp;&emsp;使用Sparksubmit提交任务的时候，Driver运行在Client；

&emsp;&emsp;&emsp;Spark-submit --master spark:mini:6066 -deploy-mode cluster

&emsp;&emsp;Ⅲ、 `Yarn`

&emsp;&emsp;&emsp;(1)yarn client 测试常用

&emsp;&emsp;&emsp;(2)yarn cluster 生产环境使用

#### 2、Shared Variables(共享变量)

&emsp;&emsp;Ⅰ、Broadcast Variables(广播变量)  var xxx = sc.broadcast(y);

>> Ⅱ、Accumulators(累加器) var xxx = sc.longAccumulator(0);


