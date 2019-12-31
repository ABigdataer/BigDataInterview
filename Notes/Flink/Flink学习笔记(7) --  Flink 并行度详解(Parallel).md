![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231163152812.png)

&emsp;&emsp;Flink的每个TaskManager为集群提供slot。 slot的数量通常与每个TaskManager节点的可用CPU内核数成比例。一般情况下你的slot数是你每个节点的cpu的核数。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231163227843.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231163340931.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231163446880.png)

&emsp;&emsp;一个Flink程序由多个任务组成(source、transformation和 sink)。 一个任务由多个并行的实例(线程)来执行， 一个任务的并行实例(线程)数目就被称为该任务的并行度。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231164109738.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231164122868.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231164219435.png)

一个算子、数据源和sink的并行度可以通过调用 setParallelism()方法来指定

![在这里插入图片描述](https://img-blog.csdnimg.cn/2019123116430034.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231164402331.png)

&emsp;&emsp;执行环境(任务)的默认并行度可以通过调用setParallelism()方法指定。

&emsp;&emsp;执行环境的并行度可以通过显式设置算子的并行度而被重写。

&emsp;&emsp;为了以并行度3来执行所有的算子、数据源和data sink， 可以通过如下的方式设置执行环境的并行度：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231164556958.png#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/2019123116491031.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231164625522.png)

&emsp;&emsp;并行度可以在客户端将job提交到Flink时设定。

&emsp;&emsp;对于CLI客户端，可以通过-p参数指定并行度

		./bin/flink run -p 10 WordCount-java.jar

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231164713347.png)

&emsp;&emsp;在系统级可以通过设置flink-conf.yaml文件中的parallelism.default属性来指定所有执行环境的默认并行度

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231164755609.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)
