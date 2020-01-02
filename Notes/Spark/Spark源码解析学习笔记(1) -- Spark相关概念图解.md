## 1、Scala解析
&emsp;&emsp;Ⅰ、Scala解析器

&emsp;&emsp;Scala解析器会快速编译Scala代码为字节码然后交给JVM运行；

		REPL -> Read（取值） -> Evaluation(求值) -> Print(打印) -> Lap(循环)

&emsp;&emsp;Ⅱ、默认情况下Scala不需要语句终结符，会默认将每一行作为一个语句，如果一行要写多条语句则必须要使用语句终结符 -- " ；",也可以用块表达式包含多条语句，最后一条语句的值就是这个块表达式的运算结果。

## 2、Spark体系概览 -- Spark的地位图解

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200101172744812.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

## 3、Spark vs MapReduce的计算模型图解

&emsp;&emsp;Spark相对于Hadoop最大的不同在于迭代式计算模型；

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200101172944270.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

## 4、Spark SQL和Hive的关系图解

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200101173710344.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

## 5、Spark Streaming和Storm的计算模型对比图解

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200101173945336.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

## 6、Wordcount程序原理深度剖析图解

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200101174218531.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

## 7、Spark架构原理图解
&emsp;&emsp;Spark会为每一个Partition启动一个Task进行处理操作。

&emsp;&emsp;一个RDD在逻辑上抽象的代表了一个HDFS文件，但实际上被切分为多个分区(块)散落在集群的不同节点上。

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020010117442471.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

## 8、transformation和action原理剖析图解

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200101175639804.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

## 9、不使用RDD持久化会带来的问题的图解

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200101181413627.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

## 10、使用RDD持久化的好处图解

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200101181847738.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

## 11、共享变量的工作原理

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200101182033961.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

&emsp;&emsp;广播变量会为每个节点拷贝一份变量，累加器则可以让多个task共同操作同一份变量进行累加计数；

&emsp;&emsp;广播变量是只读的；

&emsp;&emsp;累加器只提供了累加功能，只有Driver可以获取累加器的值；

## 12、Spark杂谈
&emsp;&emsp;Ⅰ、Spark自定义二次排序：

		    需要Javabean实现Ordered<T> 和 Serializable接口，然后在自定义的JavaBean里面定义需要进行排序的列，
		并为列属性提供构造方法\getter\setter\hashcode和equals方法，然后实现接口内部的比较方法和业务逻辑。

&emsp;&emsp;Ⅱ、RDD自动进行内存和磁盘之间的权衡和切换的机制，就是RDD弹性特点所在；

&emsp;&emsp;Ⅲ、SparkContext是Spark所有功能的入口，作用包括初始化和信心组件(DAGScheduler、TaskScheduler)和到Master节点注册等；

&emsp;&emsp;Ⅳ、RedueByKey首先会在本地进行聚合操作之后再进行shuffle操作；

## 13、Spark基本工作原理

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200101212022128.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

## 14、RDD以及其特性

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200101212214687.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

## 15、Spark核心编程原理

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200101212519180.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200101212637630.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)


