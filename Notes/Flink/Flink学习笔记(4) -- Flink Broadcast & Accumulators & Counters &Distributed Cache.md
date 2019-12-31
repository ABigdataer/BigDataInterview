![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231114347232.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231115005126.png)

&emsp;&emsp;广播变量允许编程人员在每台机器上保持1个只读的缓存变量，而不是传送变量的副本给tasks；

&emsp;&emsp;广播变量创建后，它可以运行在集群中的任何function上，而不需要多次传递给集群节点。另外需要记住，不应该修改广播变量，这样才能确保每个节点获取到的值都是一致的；

&emsp;&emsp;一句话解释，可以将广播变量理解为是一个公共的共享变量，我们可以把一个dataset 数据集广播出去，然后不同的task在节点上都能够获取到，这个数据在每个节点上只会存在一份。如果不使用broadcast，则在每个节点中的每个task中都需要拷贝一份dataset数据集，比较浪费内存(也就是一个节点中可能会存在多份dataset数据)。

**用法：**

&emsp;&emsp;1：初始化数据

&emsp;&emsp;&emsp;&emsp;DataSet<Integer> toBroadcast = env.fromElements(1, 2, 3)

&emsp;&emsp;2：广播数据

&emsp;&emsp;&emsp;&emsp;.withBroadcastSet(toBroadcast, "broadcastSetName");

&emsp;&emsp;3：获取数据

&emsp;&emsp;&emsp;&emsp;Collection<Integer> broadcastSet = getRuntimeContext().getBroadcastVariable("broadcastSetName");

**注意：**

&emsp;&emsp;1：广播出去的变量存在于每个节点的内存中，所以这个数据集不能太大。因为广播出去的数据，会常驻内存，除非程序执行结束；

&emsp;&emsp;2：广播变量在初始化广播出去以后不支持修改，这样才能保证每个节点的数据都是一致的。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231114941749.png)

&emsp;&emsp;Accumulator即累加器，与Mapreduce counter的应用场景差不多，都能很好地观察task在运行期间的数据变化，可以在Flink job任务中的算子函数中操作累加器，但是只能在任务执行结束之后才能获得累加器的最终结果。

&emsp;&emsp;Counter是一个具体的累加器(Accumulator)实现(IntCounter, LongCounter 和 DoubleCounter)。

**用法：**

&emsp;&emsp;1：创建累加器

&emsp;&emsp;&emsp;&emsp;private IntCounter numLines = new IntCounter(); 

&emsp;&emsp;2：注册累加器

&emsp;&emsp;&emsp;&emsp;getRuntimeContext().addAccumulator("num-lines", this.numLines);

&emsp;&emsp;3：使用累加器

&emsp;&emsp;&emsp;&emsp;this.numLines.add(1); 

&emsp;&emsp;4：获取累加器的结果

&emsp;&emsp;&emsp;&emsp;myJobExecutionResult.getAccumulatorResult("num-lines")

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231115259390.png)

&emsp;&emsp;Broadcast(广播变量)允许程序员将一个只读的变量缓存在每台机器上，而不用在任务之间传递变量。广播变量可以进行共享，但是不可以进行修改；

&emsp;&emsp;Accumulators(累加器)是可以在不同任务中对同一个变量进行累加操作。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231115402598.png)

&emsp;&emsp;Flink提供了一个分布式缓存，类似于hadoop，可以使用户在并行函数中很方便的读取本地文件，此缓存的工作机制如下：程序注册一个文件或者目录(本地或者远程文件系统，例如hdfs或者s3)，通过ExecutionEnvironment注册缓存文件并为它起一个名称。当程序执行，Flink自动将文件或者目录复制到所有taskmanager节点的本地文件系统，用户可以通过这个指定的名称查找文件或者目录，然后从taskmanager节点的本地文件系统访问它。

**用法：**

&emsp;&emsp;1：注册一个文件

&emsp;&emsp;&emsp;&emsp;env.registerCachedFile("hdfs:///path/to/your/file", "hdfsFile")  

&emsp;&emsp;2：访问数据

&emsp;&emsp;&emsp;&emsp;File myFile = getRuntimeContext().getDistributedCache().getFile("hdfsFile");
