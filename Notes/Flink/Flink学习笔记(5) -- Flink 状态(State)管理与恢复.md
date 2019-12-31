
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231120954230.png)

&emsp;&emsp;我们前面写的word count的例子，没有包含状态管理。如果一个task在处理过程中挂掉了，那么它在内存中的状态都会丢失，所有的数据都需要重新计算。从容错和消息处理的语义上(at least once, exactly once)，Flink引入了state和checkpoint。

&emsp;&emsp;首先区分一下两个概念，state一般指一个具体的task/operator的状态【state数据默认保存在java的堆内存中】，而checkpoint【可以理解为checkpoint是把state数据持久化存储了】则表示了一个Flink Job在一个特定时刻的一份全局状态快照，即包含了所有task/operator的状态。

&emsp;&emsp;注意：task是Flink中执行的基本单位。operator指算子(transformation)。

&emsp;&emsp;State可以被记录，在失败的情况下数据还可以恢复。

&emsp;&emsp;Flink中有两种基本类型的State：

&emsp;&emsp;&emsp;&emsp;Keyed State

&emsp;&emsp;&emsp;&emsp;Operator State

&emsp;&emsp;Keyed State和Operator State，可以以两种形式存在：

&emsp;&emsp;&emsp;&emsp;原始状态(raw state)

&emsp;&emsp;&emsp;&emsp;托管状态(managed state)

&emsp;&emsp;托管状态是由Flink框架管理的状态，而原始状态，由用户自行管理状态具体的数据结构，框架在做checkpoint的时候，使用byte[]来读写状态内容，对其内部数据结构一无所知。通常在DataStream上的状态推荐使用托管的状态，当实现一个用户自定义的operator时，会使用到原始状态。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231121553657.png)

&emsp;&emsp;顾名思义，就是基于KeyedStream上的状态。这个状态是跟特定的key绑定的，对KeyedStream流上的每一个key，都对应一个state。

&emsp;&emsp;stream.keyBy(…)

&emsp;&emsp;**保存state的数据结构：**

&emsp;&emsp;ValueState<<T>T>:即类型为T的单值状态。这个状态与对应的key绑定，是最简单的状态了。它可以通过update方法更新状态值，通过value()方法获取状态值。

&emsp;&emsp;ListState<<T>T>:即key上的状态值为一个列表。可以通过add方法往列表中附加值；也可以通过get()方法返回一个Iterable<<T>T>来遍历状态值。

&emsp;&emsp;ReducingState<<T>T>:这种状态通过用户传入的reduceFunction，每次调用add方法添加值的时候，会调用reduceFunction，最后合并到一个单一的状态值。

&emsp;&emsp;MapState<UK, UV>:即状态值为一个map。用户通过put或putAll方法添加元素。

&emsp;&emsp;需要注意的是，以上所述的State对象，仅仅用于与状态进行交互（更新、删除、清空等），而真正的状态值，有可能是存在内存、磁盘、或者其他分布式存储系统中。相当于我们只是持有了这个状态的句柄。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231122341285.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231122536563.png)

&emsp;&emsp;与Key无关的State，与Operator绑定的state，整个operator只对应一个state。

&emsp;&emsp;**保存state的数据结构：**

&emsp;&emsp;ListState<T>
&emsp;&emsp;举例来说，Flink中的Kafka Connector，就使用了operator state。它会在每个connector实例中，保存该实例中消费topic的所有(partition, offset)映射

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231122649639.png#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231122717756.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231122733509.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231122802175.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231122815815.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/2019123114020717.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231140217878.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231140344712.png)

&emsp;&emsp;为了保证state的容错性，Flink需要对state进行checkpoint。

&emsp;&emsp;Checkpoint是Flink实现容错机制最核心的功能，它能够根据配置周期性地基于Stream中各个Operator/task的状态来生成快照，从而将这些状态数据定期持久化存储下来，当Flink程序一旦意外崩溃时，重新运行程序时可以有选择地从这些快照进行恢复，从而修正因为故障带来的程序数据异常。

&emsp;&emsp;Flink的checkpoint机制是state的持久化存储的前提：

&emsp;&emsp;持久化state的存储系统需要支持在一定时间内重放事件。典型例子是持久化的消息队列（比如Apache Kafka，RabbitMQ等）或文件系统（比如HDFS，S3，GFS等）。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231142230908.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231142238288.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70)

```java
//默认checkpoint功能是disabled的，想要使用的时候需要先启用
StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();

// 每隔1000 ms进行启动一个检查点【设置checkpoint的周期】
env.enableCheckpointing(1000);

// 高级选项：
// 设置模式为exactly-once （这是默认值）
env.getCheckpointConfig().setCheckpointingMode(CheckpointingMode.EXACTLY_ONCE);

// 确保检查点之间有至少500 ms的间隔【checkpoint最小间隔】
env.getCheckpointConfig().setMinPauseBetweenCheckpoints(500);

// 检查点必须在一分钟内完成，或者被丢弃【checkpoint的超时时间】
env.getCheckpointConfig().setCheckpointTimeout(60000);

// 同一时间只允许进行一个检查点
env.getCheckpointConfig().setMaxConcurrentCheckpoints(1);

// 表示一旦Flink处理程序被cancel后，会保留Checkpoint数据，以便根据实际需要恢复到指定的Checkpoint
env.getCheckpointConfig().enableExternalizedCheckpoints(ExternalizedCheckpointCleanup.RETAIN_ON_CANCELLATION);
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/2019123114345362.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231143552467.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70)

**MemoryStateBackend**

&emsp;&emsp;state数据保存在java堆内存中，执行checkpoint的时候，会把state的快照数据保存到jobmanager的内存中,基于内存的state backend在生产环境下不建议使用。

**FsStateBackend**

&emsp;&emsp;state数据保存在taskmanager的内存中，执行checkpoint的时候，会把state的快照数据保存到配置的文件系统中，可以使用hdfs等分布式文件系统。

**RocksDBStateBackend**

&emsp;&emsp;RocksDB跟上面的都略有不同，它会在本地文件系统中维护状态，state会直接写入本地rocksdb中。同时它需要配置一个远端的filesystem uri（一般是HDFS），在做checkpoint的时候，会把本地的数据直接复制到filesystem中。fail over的时候从filesystem中恢复到本地，RocksDB克服了state受内存限制的缺点，同时又能够持久化到远端文件系统中，比较适合在生产中使用。

**修改State Backend的两种方式**

&emsp;&emsp;第一种：单任务调整

&emsp;&emsp;&emsp;&emsp;修改当前任务代码

&emsp;&emsp;&emsp;&emsp;env.setStateBackend(new FsStateBackend("hdfs://namenode:9000/flink/checkpoints"));

&emsp;&emsp;&emsp;&emsp;或者new MemoryStateBackend()

&emsp;&emsp;&emsp;&emsp;或者new RocksDBStateBackend(filebackend, true);【需要添加第三方依赖】

&emsp;&emsp;第二种：全局调整

&emsp;&emsp;修改flink-conf.yaml

&emsp;&emsp;state.backend: filesystem

&emsp;&emsp;state.checkpoints.dir: hdfs://namenode:9000/flink/checkpoints

&emsp;&emsp;注意：state.backend的值可以是下面几种：

&emsp;&emsp;&emsp;&emsp;jobmanager(MemoryStateBackend),filesystem(FsStateBackend), rocksdb(RocksDBStateBackend)

![在这里插入图片描述](https://img-blog.csdnimg.cn/2019123114455251.png)

&emsp;&emsp;Flink支持不同的重启策略，以在故障发生时控制作业如何重启。

&emsp;&emsp;集群在启动时会伴随一个默认的重启策略，在没有定义具体重启策略时会使用该默认策略。 如果在工作提交时指定了一个重启策略，该策略会覆盖集群的默认策略。默认的重启策略可以通过 Flink 的配置文件 flink-conf.yaml 指定。配置参数 restart-strategy 定义了哪个策略被使用。

&emsp;&emsp;**常用的重启策略：**

&emsp;&emsp;&emsp;&emsp;固定间隔 (Fixed delay)

&emsp;&emsp;&emsp;&emsp;失败率 (Failure rate)

&emsp;&emsp;&emsp;&emsp;无重启 (No restart)

&emsp;&emsp;如果没有启用 checkpointing，则使用无重启 (no restart) 策略。 

&emsp;&emsp;如果启用了 checkpointing，但没有配置重启策略，则使用固定间隔 (fixed-delay) 策略，其中 Integer.MAX_VALUE 参数是尝试重启次数。

&emsp;&emsp;重启策略可以在flink-conf.yaml中配置，表示全局的配置。也可以在应用代码中动态指定，会覆盖全局配置。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231144841158.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231144857837.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231144936632.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231144947842.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231145024338.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231145032226.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/2019123114505419.png)

&emsp;&emsp;默认情况下，如果设置了Checkpoint选项，则Flink只保留最近成功生成的1个Checkpoint，而当Flink程序失败时，可以从最近的这个Checkpoint来进行恢复。但是，如果我们希望保留多个Checkpoint，并能够根据实际需要选择其中一个进行恢复，这样会更加灵活，比如，我们发现最近4个小时数据记录处理有问题，希望将整个状态还原到4小时之前。

&emsp;&emsp;Flink可以支持保留多个Checkpoint，需要在Flink的配置文件conf/flink-conf.yaml中，添加如下配置，指定最多需要保存Checkpoint的个数；

		state.checkpoints.num-retained: 20

&emsp;&emsp;这样设置以后就查看对应的Checkpoint在HDFS上存储的文件目录；

		hdfs dfs -ls hdfs://namenode:9000/flink/checkpoints
		
如果希望回退到某个Checkpoint点，只需要指定对应的某个Checkpoint路径即可实现。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231145439275.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231145443295.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231145641851.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231145646751.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231145724459.png)

&emsp;&emsp;**CheckPoint：**

&emsp;&emsp;&emsp;&emsp;应用定时触发，用于保存状态，会过期

&emsp;&emsp;&emsp;&emsp;内部应用失败重启的时候使用

&emsp;&emsp;**SavePoint：**

&emsp;&emsp;&emsp;&emsp;用户手动执行，是指向Checkpoint的指针，不会过期

&emsp;&emsp;&emsp;&emsp;在升级的情况下使用

&emsp;&emsp;注意：为了能够在作业的不同版本之间以及 Flink 的不同版本之间顺利升级，强烈推荐程序员通过 uid(String) 方法手动的给算子赋予 ID，这些 ID 将用于确定每一个算子的状态范围。如果不手动给各算子指定 ID，则会由 Flink 自动给每个算子生成一个 ID。只要这些 ID 没有改变就能从保存点（savepoint）将程序恢复回来。而这些自动生成的 ID 依赖于程序的结构，并且对代码的更改是很敏感的。因此，强烈建议用户手动的设置 ID。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231145953317.png)

&emsp;&emsp;1：在flink-conf.yaml中配置Savepoint存储位置

&emsp;&emsp;不是必须设置，但是设置后，后面创建指定Job的Savepoint时，可以不用在手动执行命令时指定Savepoint的位置。

		state.savepoints.dir: hdfs://namenode:9000/flink/savepoints
&emsp;&emsp;2：触发一个savepoint【直接触发或者在cancel的时候触发】

		bin/flink savepoint jobId [targetDirectory] [-yid yarnAppId]【针对on yarn模式需要指定-yid参数】
		
		bin/flink cancel -s [targetDirectory] jobId [-yid yarnAppId]【针对on yarn模式需要指定-yid参数】
		
&emsp;&emsp;3：从指定的savepoint启动job

		bin/flink run -s savepointPath [runArgs]
