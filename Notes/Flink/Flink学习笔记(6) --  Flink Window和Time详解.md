## 一、Window(窗口)

&emsp;&emsp;聚合事件（比如计数、求和）在流上的工作方式与批处理不同。比如，对流中的所有元素进行计数是不可能的，因为通常流是无限的（无界的）。所以，流上的聚合需要由 window 来划定范围，比如 “计算过去的5分钟” ，或者 “最后100个元素的和” 。

&emsp;window是一种可以把无限数据切割为有限数据块的手段。

&emsp;窗口可以是 时间驱动的 **【Time Window】**（比如：每30秒）或者 数据驱动的 **【Count Window】** （比如：每100个元素）。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231150900115.png#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231150946759.png)

&emsp;&emsp;tumbling windows：滚动窗口 【没有重叠】 

&emsp;&emsp;sliding windows：滑动窗口 【有重叠】

&emsp;&emsp;session windows：会话窗口 

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231151022164.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231151034902.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231151218322.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231151228763.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231151429315.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231151446662.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231151534323.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231151544488.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231151656324.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231151703926.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231151902603.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231151928135.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231151942521.png#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231151953469.png#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/2019123115210344.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231152120208.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231152148616.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231152203799.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231152231401.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231152235775.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231152331606.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/2019123115234431.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/2019123115245753.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231152623143.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231152709735.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231152719488.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/2019123115275816.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231152807302.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231152834125.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231152839547.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231153034539.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231153037133.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231153153871.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

## 二、Time
&emsp;&emsp;针对stream数据中的时间，可以分为以下三种:

&emsp;&emsp;&emsp;&emsp;**Event Time**：事件产生的时间，它通常由事件中的时间戳描述。

&emsp;&emsp;&emsp;&emsp;**Ingestion time**：事件进入Flink的时间。

&emsp;&emsp;&emsp;&emsp;**Processing Time**：事件被处理时当前系统的时间。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231153634396.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231153714978.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231153728611.png)

&emsp;&emsp;如果我们想要统计每分钟内接口调用失败的错误日志个数，使用哪个时间才有意义？

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231154005703.png)

&emsp;&emsp;Flink中，默认Time类似是ProcessingTime，可以在代码中设置；

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231154053623.png#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231154144930.png)

&emsp;&emsp;**在使用eventTime的时候如何处理乱序数据？**

&emsp;&emsp;我们知道，流处理从事件产生，到流经source，再到operator，中间是有一个过程和时间的。虽然大部分情况下，流到operator的数据都是按照事件产生的时间顺序来的，但是也不排除由于网络延迟等原因，导致乱序的产生，特别是使用kafka的话，多个分区的数据无法保证有序。所以在进行window计算的时候，我们又不能无限期的等下去，必须要有个机制来保证一个特定的时间后，必须触发window去进行计算了。这个特别的机制，就是watermark，watermark是用于处理乱序事件的。watermark可以翻译为水位线。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231154337342.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231154344386.png#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231154425168.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231154434125.png#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231154515799.png)
&emsp;&emsp;**注意：多并行度的情况下，watermark对齐会取所有channel最小的watermark**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231154647247.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231154928943.png)

&emsp;&emsp;通常，在接收到source的数据后，应该立刻生成watermark；但是，也可以在source后，应用简单的map或者filter操作后，再生成watermark。

&emsp;&emsp;**注意：如果指定多次watermark，后面指定的会覆盖前面的值。**

**生成方式：**

&emsp;&emsp;**With Periodic Watermarks**

&emsp;&emsp;&emsp;&emsp;周期性的触发watermark的生成和发送，默认是100ms；

&emsp;&emsp;&emsp;&emsp;每隔N秒自动向流里注入一个WATERMARK 时间间隔由ExecutionConfig.setAutoWatermarkInterval 决定. 每次调用getCurrentWatermark 方法, 如果得到的WATERMARK 不为空并且比之前的大就注入流中 ；

&emsp;&emsp;&emsp;&emsp;可以定义一个最大允许乱序的时间，这种比较常用；

&emsp;&emsp;&emsp;&emsp;实现AssignerWithPeriodicWatermarks接口；

&emsp;&emsp;**With Punctuated Watermarks**

&emsp;&emsp;&emsp;&emsp;基于某些事件触发watermark的生成和发送；

&emsp;&emsp;&emsp;&emsp;基于事件向流里注入一个WATERMARK，每一个元素都有机会判断是否生成一个WATERMARK. 如果得到的WATERMARK 不为空并且比之前的大就注入流中；

&emsp;&emsp;&emsp;&emsp;实现AssignerWithPunctuatedWatermarks接口

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231155500630.png)

&emsp;&emsp;**Flink应该如何设置最大乱序时间？**

&emsp;&emsp;这个要结合自己的业务以及数据情况去设置。如果maxOutOfOrderness设置的太小，而自身数据发送时由于网络等原因导致乱序或者late太多，那么最终的结果就是会有很多单条的数据在window中被触发，数据的正确性影响太大。对于严重乱序的数据，需要严格统计数据最大延迟时间，才能保证计算的数据准确，延时设置太小会影响数据准确性，延时设置太大不仅影响数据的实时性，更加会加重Flink作业的负担，**不是对eventTime要求特别严格的数据，尽量不要采用eventTime方式来处理，会有丢数据的风险**。
