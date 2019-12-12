### 1、开发Spark项目的经验准则

    (1)尽量少生成RDD；
    (2)尽量少对RDD进行算子操作，如果有可能，尽量在一个算子里面实现多个功能；
    (3)尽量少对算子进行Shuffle操作，如GroupByKey、ReduceByKey、SortByKey;
    (4)无论什么功能，性能第一；
    (5)对于Accumulator这种分布式累加计算的变量的使用，在从Accumulator中获取数据插入数据库时，一定要在action操作之后；

### 2、Spark项目开发流程：

    数据调研 --> 需求分析 --> 技术方案设计 --> 数据库设计 --> 编码实现 --> 单元测试 --> 本地测试 --> 性能调优 --> troubshoting --> 数据倾斜解决

### 3、常规性能调优：

    Ⅰ、分配更多资源，性能和速度的提升在一定范围内和运算资源成正比 
    
      (1)分配哪些资源？
      
       executor | cpu per executor | memory per executor | driver memory ......
       
      (2)在哪分配资源？
      
       - spark_submit
       - class wordCount
       - num-exector n            配置executor的数量(50 - 100)
       - driver-memory  100m      配置driver的内存大小(影响不大| 1-5G)
       - executor-memory 100m     配置每个executor能使用的内存大小(5 - nG)
       - exceutor-cores n         配置每个exceutor的cpu core数量
       /path/wordConut.jar
       
       分配更多executor和cup可以增大task并行度，增大运存对cache、shuffle和task任务执行的GC有益；
       
       通过sparkconf.set("spark.cores.max",n)可以限制每个作业能够使用的cpu core总数量，负责作业默认使用全部的CPU；
       
      (3)应该调节到多大？
      
           尽量调节到最大资源(可以使用的资源|exceutor数量、executor内存)
           
           假如拥有20台机器(每台2个CPU、4G内存)，则共有80G内存、40个CPU，如果使用StandAlone模式，则可以启动20个executor,
       每个executor分配2个CPU，4G内存;
       
           如果共有500G内存和100个CPU,则可以尝试启动50个exceutor,平均每个executor2个CPU、10G内存(Yarn调度模式)。
           
    Ⅱ、调节并行度
    
            并行度就是指Spark作业中，每个Stage的task数量，就是Spark作业在各个阶段(Stage)的并行度(Spark作业中每个action触发一个job,
        每个job内的shuffle操作会将job拆分成多个Stage)。
        
        (1)合理的设置并行度，可以充分利用集群资源，且减少每个task处理的数据量；
        
        (2)task数量至少设置成与Spark application的总CPU核数相同；
        
        (3)官方推荐task数量设置为cou core数量的2-3倍，原因是某些task运行的比较快，剩余的task运行过程中，已经运行完的task的cpu core
        便会空闲，但不会被Spark application释放，因此如果设置为cou core数量的2-3倍,可以让空闲出来的cpu继续执行任务，从而提升性能。
        
        (4)如何设置Spark application的并行度：
        
           sparkConf.set("spark.default.parallelism" , "500");
           
           这种并行度设置，只会在没有使用SparkSQL的那些Stage生效，如果想调节SparkSQL的并行度，则可以通过 `spark.sql.shuffle.partions`调节。
           
     Ⅲ、RDD重构以及RDD持久化
         
       (1)默认情况下，多次对一个RDD算子执行算子去获取不同的RDD,都会对这个RDD以及之前的父RDD全部重新计算一次；
       (2)尽量复用RDD,功能差不多的RDD，可以抽取为一个公共的RDD;
       (3)公共RDD一定要持久化，将RDD的数据缓存到内存/磁盘；
       (4)将持久化数据进行序列化，避免OOM;
       (5)考虑数据的高可靠性，如果内存充足，可以使用双副本机制进行持久化.
       
     Ⅳ、广播大变量
     
       如果task使用大变量(如存储大量数据的map集合)，将会导致性能受损，可以尝试将大变量广播出去；
       
       (1)默认的task执行算子过程中，每个task都会获取一份外部变量的副本，可能会增大网络传输开销(副本发送)、增大磁盘IO开销、序列化导致内存值不足
       、频繁GC导致Spark作业暂停运行(task创建对象，堆内存放不下)；
       (2)BlockManager   负责管理某个Executor对应的内存和磁盘上的数据，BlockManager也许会从Driver上获取变量副本，也可能从距离较近的executor
       的BlockManager获取；
       (3)广播变量在Driver上有一份初始副本，task在运行时如果需要使用变量副本，会首先在本地executor对应的BlockManager中获取，若本地没有，则从
       Driver或者其它executor拉取并保存到本地；
       (4)使用广播变量后，不会每一个task拥有一份变量副本，而是每一个executor一份副本；
       (5)广播变量默认使用的最大内存可以设置为 ExecutorMemeory*60%*90%*80%.
       
    Ⅴ、使用Kryo序列化
    
        (1)Spark内部默认使用java序列化机制，好处在于处理简单，但是效率不高，并且会占用更多空间、速度慢，Spark默认支持Kryo序列化，性能更好。
        (2)当Spark需要通过网络进行传输数据，或者将数据溢写到磁盘，Spark会将数据序列化，Kryo序列化机制启用后生效的几个地方：
           -- 算子函数中使用外部变量
           -- 持久化RDD时进行序列化
           -- Stage之间的数据Shuffle
        (3)使用Kryo序列化机制，需要注册自定义类
           sparkConf.registerKryoClasses(new Class[]{***.class});
           
    Ⅵ、使用 fastUtil 优化数据格式 
        
        fastutil是java标准化集合框架(Map,List,Set)的类库扩展以及替代品，可以减小内存占用并提供更快的查询速度，Spark使用FastUtil的场景：
        (1)如果算子函数中使用了外部变量，第一步可以广播变量，第二步可以使用Kryo序列化机制，第三步如果是较大的数据集合可以使用fastutil进行重写;
        (2)Task要执行的计算逻辑里，有较大的集合时可以使用fastUtil，在一定程度上可以减小内存占用，避免频繁GC;
    Ⅶ、调节数据本地化等待时长
    
        (1)本地化级别
        
           Process_local  Node_local  No_pref  rack_local   Any
           
        (2)参数调节
        
           spark.locality.wait.process
           spark.locality.wait   调节数据本地化等待时长   sparkConf.set("spark.locality.wait","10")
           spark.locality.node
           spark.locality.wait.rack
 
 ### 4、JVM调优：
 
     Ⅰ、调节Cache操作的内存占比
     
        (1)JVM内存不足会导致
        
            ①频繁minorGC,导致Spark作业频繁停止工作
            ②老年代囤积大量短生命周期1对象，导致频繁fullGC,Spark作业长时间停止工作
            ③严重影响Spark作业的性能和运行速度
            
        (2)Spark作业运行过程中，对内存被划分为两块，一块用来给RDD的Cache、Persist操作进行RDD数据缓存，另外一块用来存储Spark
        作业算子函数创建的对象；
        
        (3)默认60%内存给Cache操作，如果在某些情况下Cache对内存的要求不是很大，而task算子函数中创建的对象过多导致频繁GC(可以通
        过Spark UI查看Yarn界面，查看Spark作业的运行统计，从而找到每个Stage的运行情况，包括每个task的运行时间、gc时间等),可以
        通过降低Cache内存占比，给task更多的运算空间，从而避免频繁GC;
        
        (4)sparkConf.set("spark.storage.memoryFraction","0.4");
        
     Ⅱ、调节executor堆外内存与连接等待时长
     
        (1)有时候Spark作业处理时会报错如：shuffle file cannot find 、 executor lost 、 task lost 、 OOM ,则有可能是因为executor
        的堆外内存不太够用，导致内存溢出，也可能导致后续的Stage的task在运行时从别的executor拉取shffle map output文件，但因为executor
        以及挂掉了，关联的BlockManager也没有了，所以会出现shuffle file cannot find 、 executor lost 、 task lost等报错；
        
        (2)可以通过参数调节executor的堆外内存大小来解决上述问题
        
            -- conf spark.yarn.executor.memeroryOverhed=2048    针对基于yarn的提交模式
            
            在spark的启动指令中添加参数，默认情况下内外内存大小为三百多MB,可调节为1G\2G\4G...,可以避免某些JVM OOM问题，同时让Spark
            作业有较大性能提升；
            
        (3)调节连接等待时长
        
           当某个executor的task创建的对象特别大，频繁的让JVM内存溢满进行垃圾回收，作业将停止工作无法提供相应，当下游的executor尝试建立
           远程网络连接拉取数据，可能会因为超过默认的60s而失败，因此导致Spark作业崩溃，也可能导致DAGSecheduler反复提交几次stage,
           taskScheduler反复提交task,大大延长了作业时长；
           
           可以通过参数调节等待时长，从而避免文件拉取失败： --conf spark.core.connection.ack.wait.timeout = 300 ;
           
### 5、Shuffle调优：

     Ⅰ、Spark的一些算子会触发shuffle，比如GroupByKey、ReduceByKey、CountByKey、Join等；
         GroupByKey会把分布在集群各节点上的数据中同一个key对应的values集中到同一个节点的一个executor的一个task中进行分析处理 --- <Key,Iterable<Value>>;
         ReduceByKey对values集合进行reduce操作，最终变为一个value;
         Join只需要两个RDD的Key相同，就会分发到同一个节点的executor中的task中；
         ......
         
     Ⅱ、合并map端输出文件(针对HashShuffle)(效果明显)
     
         (1)Shuffle中的写磁盘操作，基本就是shuffle中性能消耗最大的地方，Shuffle前半部分的task在写入磁盘文件之前，都会先写入一个内存
         缓冲，再溢写到磁盘文件，而且Shuffle的前半部分Stage的task，每个task都会创建下一个Stage的task数量的文件；
         
         (2)可以开启Hash Shuffle的文件合并机制，从而在map端就将文件进行合并，避免产生大量小文件，此机制只适用于HashShuffle,如果不需
         要SortShuffle的排序机制，除了SortShuffle的Bypass机制，也可以开启HashShuffle并启用文件合并机制；
            只有并行执行的task会创建下一批task个数的文件，下一批task个数相同的并行的task会复用已有的输出文件，下一批并行的task会复用已有的输出文件，但多批task同时
         执行，是不能复用输出文件的；
         
         (3)sparkConf.set.("spark.shuffle.consolidateFiles","true");
            合并输出文件对Spark作业有哪些影响？
            ①map task 减少，磁盘IO减少；
            ②网络传输性能消耗减少；
            
      Ⅲ、调节Map端内存缓冲大小和reduce端内存占比(效果不明显)   
      
         (1)spark.shuffle.file.buffer 默认32kb   map端内存缓冲可能会引起频繁Spill和磁盘IO消耗
            spark.shuffle.memoryFraction  默认0.2  reduce端聚合内存比例过小可能会导致频繁磁盘文件读写；
            
         (2)默认shuffle的map task，在将数据输出到磁盘文件之前，会统一先写入每个task关联的内存缓冲区，默认大小为32kb,当缓冲区满了
         才会进行spill操作，如果文件很大，将会导致多次磁盘写操作，如果reduce端内存不够用，也可能会导致频繁的spill；
         
         (3)查看Spark UI,如果每个task的shuffle write和shuffle read很大，则可以考虑进行相应调优；
            spark.shuffle.file.buffer 每次增加一倍  32kb -> 64kb -> 128kb ...
            spark.shuffle.memoryFraction 每次增加0.1  0.2 -> 0.3 -> 0.4 ...
            
      Ⅳ、HashShuffleManager和SortShuffleManager
      
         (1) "spark.shuffle.manager" -->  "hash" | "sort" | "tungsten-sort"
         
         (2) HashShuffleManager和SortShuffleManager的不同：
             ①SortShuffleManager会对reduce task要处理的数据进行排序；
             ②SortShuffleManager会避免像HashShuffleManager一样创建多份磁盘文件，一个task只会写入一个磁盘文件，不同的reduce task
             的数据用offset来划定界限；
             
         (3) tungsten-sort和sort机制差不多，但由于自己实现了内存管理机制，性能有较大提升，可以避免一些OOM、GC等内存相关的异常；
         
         (4)SortShuffle可以通过开启Bypass机制限制排序机制，即当输出文件个数小于某个设定值时不会触发排序机制；
         
### 6、算子调优

     Ⅰ、使用mapPartitions提升map操作的性能
     
            Spark中每个Task处理一个RDD的Partition,如果是普通的map,加入partition内有100w条数据，那么task的map函数的fuction要执行和
         计算100w次，如果使用mapPatitons，则一个task仅执行一次fuction(一次接收整个partiton的所有数据)，效率比较高；
            相比较来说，map函数可以通过GC回收掉已经使用过的内存，但是mapPartitions因为一次传入大量数据，容易导致OOM,所以比较适用于数
         据量不是很大的场景，所以在实际开发中应估测一下Partition的数据量和每个executor的内存资源。
         
      Ⅱ、filte，r之后使用coalesce减少分区数量
       
              默认情况下经过filter之后，RDD的每个Partition的数据量将会变的不均匀，所以可能会有一些数据量较小的partition单独启动一个task
          进行处理，造成资源浪费，也可能会导致数据倾斜；
              coalesce算子主要就是在filter之后针对每个partition进行压缩，减少partiton数量从而让每个partion的数据量更加均匀；
              repartiton算子其实就是第二个参数为true的coalesce算子的实现；
       
      Ⅲ、使用repartition解决SparkSQL低并行度的性能问题
          
        并行度可以通过参数自定义：
           spark.default.parallelism   一般调节为application总CPU个数的2-3倍
           textFile                    传参时第二个参数可以指定partition数量
        当SparkSQL读取Hive表对应的HDFS文件的block,可能会因为block数量少而导致并行度较低，而spark.default.parallelism参数只能对除
        SparkSQL意外的算子生效，如果需要增加并行度，则可以使用repartiton算子进行重分区以提高并行度。
        
      Ⅳ、使用foreachPartition优化写数据库性能
      
             foreach对于每条数据都会建立和销毁数据库链接，并发送和执行多次SQL，对于性能消耗较大，在实际开发中，可以使用foreachPartion函数
         来进行数据库写操作，对于一个partion只会建立一次数据库连接，并且只需要向数据库发送一次SQL和多组参数，但因为一次对整个partiton
         进行操作，所以可能会引起OOM问题，需要估算一下数据量。
         
     Ⅴ、使用reduceByKey实现本地聚合
     
            reduceByKey相较于普通shuffle,会进行一次map端本地聚合，在map端给每个stage的每个task创建的文件输出数据之前，会进行本地聚合，所以
         使用reduceByKey算子后map端数据量会减少，从而也会减少磁盘IO和磁盘空间占用，也会降低网络传输消耗，reduc端数据缓存的内存占用也会降低；
            reduceByKey适用于要实现类似于wordCount程序一样的对每个key对应的value进行数据计算的场景，也适用于一些字符串拼接等较为复杂的使用
         场景;
         
### 7、troubleShooting


           

        
           
        
       
       
       
       
       
       
       
       
       
       
       
       
       
       
       
       
       
       
       
       
       
       
       
