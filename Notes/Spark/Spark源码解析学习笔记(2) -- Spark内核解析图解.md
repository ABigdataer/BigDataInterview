## 1、Spark内核架构深度剖析图解

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200101195659203.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

## 2、宽依赖和窄依赖深度剖析图解

&emsp;&emsp;Spark的宽依赖和窄依赖是DAGScheduler将job划分为多个Stage的重要因素，每一个宽依赖都会划分一个Stage。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200101202546625.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

## 3、基于YARN的两种提交模式深度剖析图解(Yarn_Client、Yarn_Cluster)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200101203100159.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

&emsp;&emsp;Spark也支持StanAlone任务调度模式，所有任务调度都由Spark自己进行调度，但是相比较来说使用Yarn管理任务可以和其它的分布式任务一起被管理，比如Hadoop、Hive、Flink等，可以更加方便的管理集群的所有资源。

## 4、Spark任务调度剖析图解(DAGScheduler、TaskScheduler)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200101204026954.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

**SparkDeploySchedularBackend** : 

&emsp;&emsp;底层会通过SchedulerBackend方法针对不同种类的Cluster(StandAlone、Yarn、Mesos)调度Task,也可以通过将isLocal参数改为true并使用LocalBackend在本地模式下对任务进行调度运算；

&emsp;&emsp;负责处理一些通用逻辑，比如决定多个job的执行顺序、启动推测任务并执行；

&emsp;&emsp;客户端首先调用initialize和start方法然后通过runTasks方法提交TaskSets;

&emsp;&emsp;SparkDeploySchedularBackend的Start方法会封装一个ApplicationDescription对象用来初始化任务描述参数(name、需要的CPU数、需要的内存大小...),然作为参数之一传递给 new AppClient(...)作为参数之一创建出AppClient;

**AppClient** :

&emsp;&emsp;AppClient负责为Application与集群进行通信，会接受Spark Master的URL、ApplicationDescription和各种监视器；

**DAGScheduler** :

&emsp;&emsp;DAGScheduler实现了面向Stage的高层次调度机制，会为每个Job计算每一个Stage的DAG并追踪RDD和Satge的输出是否写入磁盘或内存等地方，并寻找一个最优调度机制运行job；

&emsp;&emsp;它会将Stage作为TaskSets提交到底层的TaskSchedulerImpl上，以在集群运行Job;

&emsp;&emsp;DAGScheduler还负责决定每个task的最佳运行位置，并基于当前缓存状态提交给TaskSchedulerImpl,当处理由于Shuffle输出文件丢失导致的失败，旧的Stage可能会被重新提交，如果一个Stage内部失败而非由于Shuffle文件丢失导致则会被TaskScheduler处理，TaskScheduler将会多次重试每一个task直到最终取消整个Stage。

## 5、Master解析(主备切换、注册机制、状态改变处理机制、资源调度机制)

### 5.1、Master的主备切换机制图解

![Spark的主备切换机制](https://img-blog.csdnimg.cn/20200101213412915.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

&emsp;&emsp;Master对没有响应的Worker和Driver的处理包括将其从内存中移除、从相关组件内存缓存中移除和从持久化存储中移除。

### 5.2、Master注册机制原理剖析图解

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200101214139694.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

### 5.3、Master的状态改变处理机制剖析

**(1)DriverStateChanged** :

&emsp;&emsp;如果Driver的状态为错误、完成、被杀掉、失败，则会调用removeDriver方法，该方法内部使用了scala的find高阶函数找到对应的Driver，然后将Driver从内存缓存中清除并使用持久化引擎去除Driver的持久化信息，再重新设置Driver的State和Exception,然后遍历Driver所在的Worker并将Driver移除，最后调用Scheduler方法。

**(2)ExecutorStateChanged** :

&emsp;&emsp;首先会找到Executor对应的Application,然后再反过来通过Application内部的executors缓存获取executor信息，如果有值则设置executor当前状态，然后通过ExecutorUpdated方法向Driver发送同步信息，当Executor完成就从Application的缓存中移除excutor并从运行executor的Worker的缓存中移除Executor;如果Executor的退出状态是非正常的则判断当前Application重试次数是否达到最大值，若没有达到则重新调度，若达到则RemoveApplication。

### 5.4、Master资源调度机制解析

**Scheduler()方法解析** : 

&emsp;&emsp; **(1)Driver的调度机制：** 首先判断Master的状态，如果不是Active则直接return,如果是则取出之前注册的所有状态为Active的Worker并调用Random.shuffle方法随机打乱，然后遍历活着的Worker，如果遍历的当前Worker拥有的内存和CPU大于Driver所需，则将Driver任务从WatingDrivers队列中移除，如果资源不足，则将指针指向下一个Worker,直到足以启动Driver。

&emsp;&emsp; **(2)Application的调度机制：** 

&emsp;&emsp; Application的调度方法有SpreadOutApps(默认)和非SpreadOutApps两种：

&emsp;&emsp; **SpreadOutApps** ：

&emsp;&emsp; 首先会遍历WatingApps中AppInfo并过滤出需要调度的App，然后遍历并过滤出可以使用的Worker，然后再按照Worker剩余的CPU数量给Worker倒序排序，再将Worker的CPU分配给App,每分配一次便移动一次指针到下一个Worker并将资源分配给App,最后启动分配到CPU的App对应的Executor。

&emsp;&emsp; 通过这种算法会将每个App要启动的Executor平均分配到各个Worker上，但基于这种分配机制可能实际分配和Spark_submit指定的参数不一致。

&emsp;&emsp; **非SpreadOutApps** ：

&emsp;&emsp; 这种调度模式会将每一个App分配到尽可能少的Worker上，首先会遍历Worker并过滤出状态为Active的，然后遍历App过滤出需要分配CPU的，如果Worker可以使用则取Worker剩余CPU数和App要分配的CPU最小值，然后为App在Worker启动一个Executor。

## 6、Worker原理剖析图解

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200102095052380.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

&emsp;&emsp;Driver启动成功之后会调用DriverStateChaged发送信息给Master;

&emsp;&emsp;Executor启动成功并完成反向注册之后会调用ExecutorStateChanged发送信息给Master,如果Executor状态为Finshed则从缓存中清除该Executor。

## 7、Job触发流程解析

&emsp;&emsp;当程序执行到action算子，算子底层会去调用SparkContext的Runjob方法，然后会多次执行重载的runJob方法，最终会调用初始化时创建的DAGScheduler的runjob方法。

## 8、DAGScheduler的Stage划分剖析图解

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200102100706894.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

&emsp;&emsp;每个Stage分配一批Task,Stage有多少个Partition就有多少个Task,DAGScheduler会为每个Task计算最佳位置(Task会优先在CheckPoint/Cache所在的节点，然后如果父Stage都没有CheckPoint/Cache,那么最佳位置就是NIL)，除了finalStage之外的Stage都会创建ShuffleMapTask,finalStage会创建ResultTask,最后针对Stage的Task创建TaskSet对象并通过调用TaskScheduler的submitTask提交Task。

&emsp;&emsp;DAGScheduler  &emsp;  -- runjob-->  &emsp;  SubmitJob &emsp;  -- >  &emsp;  handleJobSubmit 使用触发Job的最后一个RDD创建finalStage并加入内存缓存，而且会将Job加入内存缓存，然后会通过 submitStage尝试提交finalStage，然后通过getMissingParentStages获取父Stage,然后通过Visit方法遍历RDD，Stage划分完之后会将RDD放入栈中，然后通过递归调用Submit提交到WatingStage,直到最终的Stage。

## 9、TaskScheduler解析

&emsp;&emsp;首先会通过SubmitTask方法给每一个TaskSet创建一个TasksetManager,由创建的TasksetManager负责这个TaskSet的任务执行状况的监视和管理，在TaskSchedulerImpl中对一个单独的TaskSet进行任务调度，TasksetManager负责追踪每一个Task，如果Task任务失败则重试运行Task(默认4次)，任务执行会通过makeOffers执行任务分配算法，从而为每个任务分配Executor,由Executor负责Task的执行。

## 10、Executor原理剖析图解

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200102110925843.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

## 11、Task原理剖析图解

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200102111029513.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

## 12、普通的Shuffle操作原理剖析图解

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200102111313782.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

## 13、优化后的Shuffle原理剖析图解(只对HashShuffle有用)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200102111636847.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

## 14、BlockManager原理剖析图解

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200102111942376.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

&emsp;&emsp;在Spark1.3.0版本之后将BlockManagerWorker更名为BlockTransferService;

&emsp;&emsp;由BlockmanageActor负责维护各个Executor的BlockManager的元数据、BlockManagerInfo和BlockStatus；

&emsp;&emsp;每一个BlockManager是通过一个节点上的ExecutorID来唯一标识的；

## 15、CacheManager原理剖析图解

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200102112840205.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

## 16、Checkpoint原理剖析图解

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200102113157904.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

## 17、对于多次操作的RDD进行持久化和Checkpoint操作图解

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200102113608527.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

## 18、Spark Streaming架构原理深度剖析图解

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200102153323985.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

## 19、Receiver数据接收原理剖析图解

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200102153745960.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

## 20、Receiver数据处理原理剖析图解

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200102153904852.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

## 21、可靠Receiver的原理图解

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200102153155984.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)
