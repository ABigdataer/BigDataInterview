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
       
       
