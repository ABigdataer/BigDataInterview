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

     Ⅰ、控制shuffle reduce端缓冲区大小以避免OOM
     
        (1) reduce端task会拉取map端的一部分数据放在缓冲区，再在executor分配的内存中进行聚合操作，reduce端的缓冲区默认大小为48MB;
        
        (2) 有时map端数据量非常大，从而导致写出数据较多，reduce端的缓冲区被填满，函数拉取缓冲区内数据进行处理，创建的大量对象来不及回收会导致
        OOM,所以可以适当减小缓冲区大小，从而使内存可以被及时回收；
        
        (3) 如果整个Spark application的内存支援比较充足，而且map端的输出数据量不是很大，则可以考虑加大缓冲区大小，以减少reduce task的拉取
        数据的次数，从而减少网络性能消耗和reduce端聚合操作执行次数；
        
        (4) sparkConf.set("spark.reducer.maxIInFlight","48"); 
        
     Ⅱ、解决JVM GC导致的shuffle文件拉取失败的问题
     
        (1) spark.shuffle.io.maxRetres 默认值为3   shuffle文件拉取不到，最多重试次数
            spark.shuffle.io.retrywait 默认值5s    每次拉取的等待时长，如果重试次数为3，则3*5秒之后会报错--shuffle file not find
            
        (2)可以通过对两个参数进行预调节，从而尽量保证第二个stage的task能一定拉取到上一个stage的输出文件，最多可以接收一个小时，因为full gc
        不可能一个小时都没结束；   
     
     Ⅲ、Yarn队列资源不足导致application直接失败
     
        (1)当基于Yarn提交作业，可能会存在两个同样的任务导致内存资源不足，从而可能会导致两种情况：
           ①Yarn发现资源不足，直接Fail;
           ②Yarn发现资源不足，后来的作业一直等待第一个作业运行完成后执行；
           
        (2)解决方案：
           ①在J2EE系统中限制Spark作业的提交个数；
           ②分两个调度队列分别运行，避免小作业被大作业阻塞；
           ③无论如何都只同时运行一个作业并给与最大内存资源；
           ④在J2EE系统中使用线程池对作业进行调度管理，一个线程池对应一个资源队列，线程池的容量设为1;
           
      Ⅳ、解决各种序列化导致的报错
      
          (1) 算子函数中，如果使用到了外部的自定义类型的变量，则自定义的变量必须是可序列化的；
          
          (2) 如果要将自定义的类型作业RDD的元素类型，那么自定义类型也需要是可序列化的；
          
          (3) 不能在上述情况下，使用一些第三方的不支持序列化的类型，如数据库的链接类Connection conn;
          
      Ⅴ、解决算子函数返回NULL导致的问题
      
            有些算子函数需要有一个返回值，但有时候我们可能不需要返回值，如果直接返回null则可能会报错 -- scala.Math(null) exception,
         解决办法为：返回一些特殊值，比如 “-999”，然后算子获取RDD后将"-999"数据过滤掉，最后使用coalesce算子压缩partition数目，以提升
         性能；
         
      Ⅵ、解决Yarn-client模式导致的网卡流量激增问题
      
           spark在yarn-client模式下，Application的注册和task的调度是分离开的，driver启动在本地，需要频繁的和yarn集群上运行的的多个
         executor的每个task进行网络通讯，如果task较多，则可能导致本地机器网卡流量激增，yarn-client一般用于测试工作，便于查看log和
         trouble shooting,生产环境中应采用yarn-cluster模式，该模式driver运行在集群上，所以网卡流量激增问题也不会发生；
         
      Ⅶ、解决yarn--cluster模式的JVM内存溢出无法执行问题
      
         有时运行作业会出现本地client模式测试成功，但是cluster模式报出JVM 永久代(Permgen)溢出的错误，是因为本地client模式默认内存大小为128MB,但是cluster模式默认为82MB,可以在提交Spark作业时设置永久代内存大小：
         -- conf spark.driver.extraJavaOptions = "-Xx:PermSize=128M
                                                  -Xx:MaxPermSize=256MB"
         如果是栈内存溢出，则可能是因为SparkSQL使用 "or" 过多，需要将SQL语句进行优化拆分；
         
      Ⅷ、错误的使用持久化和checkPoint 
      
        (1) 正确的使用持久化的方式
        
            var usersRDD;
            ①usersRDD = usersRDD.cache();
            ②val cacheUsersRDD =  usersRDD.cache();
            如果直接用usersRDD调用cache算子，而不用对象接收，则会报file not find的错误；
            
        (2)checkPoint的使用方式：
           第一步：设置checkPoint目录  sc.setCheckPointDir("path");
           第二步：对RDD执行checkPoint算子  rdd.checkPoint();
           
        (3)checkPoint是将RDD的数据持久化一份到容错系统(HDFS),如果cache失效，则checkPoint作为数据备份；
        
### 8、数据倾斜

      Ⅰ、如何定位数据倾斜问题？
      
         数据倾斜的发生基本是因为shuffle操作引起，可以通过查看代码中有哪些·1会触发shuffle的算子(如groupByKey、reduceByKey、
         countByKey、join),也可以看log日志文件，看哪些代码导致了OOM或者哪个Stage拖后腿。
         
      Ⅱ、聚合源数据(重剑无锋)
      
             (1)通过groupByKey和reduceByKey算子可以对数据进行聚合操作，也可以在hive etl中使用reduceBykey函数将values聚合；
         
             (2)数据倾斜即某个key对应大量的数据，可以直接对values进行预聚合从而减少数据量，比如按照key进行分组，将key对应的所有values
         全部用一种特殊的格式拼接到一个字符串里面(比如"key=sessionId,value : name=tom|age=22|sex=男")，处理之后每个key对应一
         条数据，在Spark程序中不再需要执行groupByKey+map操作，可以直接对每个key对应的value进行操作，从而也避免了shffle,所以一
         定程度上避免了数据倾斜。
         
             (3)在实际的业务场景中，可能没法对每条key对应的数据进行聚合，可以放粗粒度(比如数据内包含了几个城市、几天、几个地区的数据)，
         则可以按照城市粒度进行聚合，同样也可以减轻数据倾斜问题。
         
             (4)过滤导致数据倾斜的key: 如果可以接受某些数据在作业中被舍弃，则可以选择在hive中过滤掉某些导致数据倾斜的key，从而也不会影
         响业务需求。
         
       Ⅲ、提高shuffle操作并行度(如果前面的方案都不适用，则可以尝试这种方法)
       
           (1)将reduce task数量变多，就可以让每个reduce task分配更少的数据量，甚至解决数据倾斜问题；
           
           (2)可以在shuffle操作的算子中传入一个数字参数，从而提高并行度(如groupBeyKey、countBeyKey、reduceBeyKey),但是提高并行度并
           不能完全解决数据倾斜问题，治标不治本，只是尽可能的缓解和减轻shuffle中reduce task的压力。
           
       Ⅳ、使用随机key实现双重聚合
            
            (1)使用场景：
               groupBeyKey、reduceBeyKey
            (2)实现原理：
               先将key打散，将原来一样的key随机加上前缀从而将其分成多个组，然后针对多个组进行key的局部聚合操作，然后再去除key的前缀进行
             全局聚合；
             
       Ⅴ、将reduce joiin转换为map join
       
            (1)reduce join会发生shuffle操作，可以将原本两个RDD中的一个广播出来，然后进行map操作；
               适用于那种情况？
               如果两个RDD要进行join,其中一个RDD较小，可以将小的RDD广播出去，小的RDD便会在每个executor的block manager中驻留一份，从而
            避免了数据倾斜，如果两个RDd都比较大则不适合采用这种方案进行处理；
            (2)这种方法不止适用于数据倾斜问题，即使没有数据倾斜发生，也可以避免shuffle操作从而提升性能。
               
        Ⅵ、sample采样倾斜key进行两次join
        
           (1)技术方案思路：关键在于将发生数据倾斜的key单独拉出来放到一个RDD中，然后利用这个RDD和其它的RDD单独进行join操作，从而将key对
           应的数据分散到多个task中进行join操作，避免了所有keu混在一个RDD中导致只有一个RDD发生数据倾斜；
           
           (2)什么时候适用这个方案？
              在join操作时一个或少数几个key对应的数据特别多(可以用countBeKey计算每个key对应的数据个数)，如果导致数据倾斜的key过多，则不
           适用这种方案。
           
        Ⅶ、使用随机数以及扩容表进行join
            
            (1)实现步骤：
               ①选择一个RDD要用flatMap进行扩容，将每条数据映射为多条数据，每个映射出来的数据，都带有一个n以内的随机数作为key的前缀，一般情况下n取值10；
               ②将两外一个RDD做普通的map操作，每条数据都打上n以内的随机数作为前缀；
               ③最后将两个处理后的RDD进行join操作。
           (2)局限性：
              因为两个RDD都比较大，所以无法将某个RDD扩容到特别大，一般为10倍，而且数据倾斜只是减轻和缓解并不能彻底解决数据倾斜问题；
              
        Ⅷ、自定义Partioner将数据进行自定义分区      
               
