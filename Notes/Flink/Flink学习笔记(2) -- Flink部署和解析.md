<h1 id="1">1、Flink Local模式部署</h1>

Ⅰ、依赖环境：

&emsp;&emsp;linux机器

&emsp;&emsp;jdk1.8及以上【配置JAVA_HOME环境变量】

Ⅱ、下载地址

&emsp;&emsp;https://mirrors.tuna.tsinghua.edu.cn/apache/flink/flink-1.6.1/flink-1.6.1-bin-hadoop27-scala_2.11.tgz

Ⅲ、local模式快速安装启动

&emsp;&emsp;(1)解压：tar -zxvf flink-1.6.1-bin-hadoop27-scala_2.11.tgz 

&emsp;&emsp;(2)cd flink-1.6.1

&emsp;&emsp;(3)启动：./bin/start-cluster.sh  

&emsp;&emsp;(4)停止：./bin/stop-cluster.sh 

&emsp;&emsp;(5)访问web界面

&emsp;&emsp;&emsp;&emsp;http://hostname:8081

<h1 id="2">2、Flink StandAlone模式部署和解析</h1>

Ⅰ、依赖环境

&emsp;&emsp;jdk1.8及以上【配置JAVA_HOME环境变量】

&emsp;&emsp;ssh免密码登录【集群内节点之间免密登录】

Ⅱ、集群规划：

&emsp;&emsp;**master(JobManager)+slave/worker(TaskManager)**

&emsp;&emsp;hadoop100(master)  &emsp;&emsp;  hadoop101(slave) &emsp;&emsp;  hadoop102(slave)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231102221391.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70)

Ⅲ、集群安装

&emsp;&emsp;1：修改conf/flink-conf.yaml

&emsp;&emsp;&emsp;&emsp;jobmanager.rpc.address: hadoop100

&emsp;&emsp;2：修改conf/slaves

&emsp;&emsp;&emsp;&emsp;hadoop101

&emsp;&emsp;&emsp;&emsp;hadoop102

&emsp;&emsp;3：拷贝到其他节点

&emsp;&emsp;&emsp;&emsp;scp -rq /usr/local/flink-1.6.1 hadoop101:/usr/local

&emsp;&emsp;&emsp;&emsp;scp -rq /usr/local/flink-1.6.1 hadoop102:/usr/local

&emsp;&emsp;4：在hadoop100(master)节点启动

&emsp;&emsp;&emsp;&emsp;bin/start-cluster.sh

&emsp;&emsp;5：访问http://hadoop100:8081

Ⅳ、Flink配置文件重要参数解析

&emsp;&emsp;jobmanager.heap.mb：jobmanager节点可用的内存大小

&emsp;&emsp;taskmanager.heap.mb：taskmanager节点可用的内存大小

&emsp;&emsp;taskmanager.numberOfTaskSlots：每台机器可用的cpu数量

&emsp;&emsp;parallelism.default：默认情况下任务的并行度

&emsp;&emsp;taskmanager.tmp.dirs：taskmanager的临时数据存储目录

Ⅴ、slot和parallelism总结

&emsp;&emsp;1.slot是静态的概念，是指taskmanager具有的并发执行能力

&emsp;&emsp;2.parallelism是动态的概念，是指程序运行时实际使用的并发能力

&emsp;&emsp;3.设置合适的parallelism能提高运算效率，太多了和太少了都不行

Ⅵ、集群节点重启和扩容

&emsp;&emsp;启动jobmanager

&emsp;&emsp;&emsp;&emsp;如果集群中的jobmanager进程挂了，执行下面命令启动。

&emsp;&emsp;&emsp;&emsp;bin/jobmanager.sh start

&emsp;&emsp;&emsp;&emsp;bin/jobmanager.sh stop

&emsp;&emsp;启动taskmanager

&emsp;&emsp;&emsp;&emsp;添加新的taskmanager节点或者重启taskmanager节点

&emsp;&emsp;&emsp;&emsp;bin/taskmanager.sh start

&emsp;&emsp;&emsp;&emsp;bin/taskmanager.sh stop

Ⅶ、Flink standalone集群中job的容错

&emsp;&emsp;jobmanager挂掉

&emsp;&emsp;&emsp;&emsp;正在执行的任务会失败

&emsp;&emsp;&emsp;&emsp;存在单点故障，(Flink支持HA)

&emsp;&emsp;taskmanager挂掉

&emsp;&emsp;&emsp;&emsp;如果有多余的taskmanager节点，flink会自动把任务调度到其它节点执行

<h1 id="3">3、Flink on Yarn模式部署和解析</h1>

Ⅰ、依赖环境

&emsp;&emsp;至少hadoop2.2

&emsp;&emsp;hdfs & yarn

Ⅱ、Flink on Yarn 的两种使用方式

![Flink on Yarn 的两种使用方式](https://img-blog.csdnimg.cn/20191231103356464.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70)

**Flink on Yarn的两种运行方式：**

&emsp;&emsp;第一种【yarn-session.sh(开辟资源)+flink run(提交任务)】

&emsp;&emsp;&emsp;&emsp;启动一个一直运行的flink集群

&emsp;&emsp;&emsp;&emsp;./bin/yarn-session.sh -n 2 -jm 1024 -tm 1024 [-d]

&emsp;&emsp;&emsp;&emsp;附着到一个已存在的flink yarn session

&emsp;&emsp;&emsp;&emsp;./bin/yarn-session.sh -id application_1463870264508_0029

&emsp;&emsp;&emsp;&emsp;执行任务

&emsp;&emsp;&emsp;&emsp;./bin/flink run ./examples/batch/WordCount.jar -input hdfs://hadoop100:9000/LICENSE -output 
&emsp;&emsp;&emsp;&emsp;hdfs://hadoop100:9000/wordcount-result.txt 

&emsp;&emsp;&emsp;&emsp;停止任务 【web界面或者命令行执行cancel命令】

&emsp;&emsp;第二种【flink run -m yarn-cluster(开辟资源+提交任务)】

&emsp;&emsp;&emsp;&emsp;启动集群，执行任务

&emsp;&emsp;&emsp;&emsp;./bin/flink run -m yarn-cluster -yn 2 -yjm 1024 -ytm 1024 ./examples/batch/WordCount.jar

&emsp;&emsp;&emsp;&emsp;注意：client端必须要设置YARN_CONF_DIR或者HADOOP_CONF_DIR或者HADOOP_HOME环境变量，通过这个环境变量来读取YARN和HDFS的配置信息，否则启动会失败

**./bin/yarn-session.sh 命令分析：**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231104407247.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70)

**./bin/flink run 命令分析：**

![在这里插入图片描述](https://img-blog.csdnimg.cn/2019123110451191.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70)

Ⅲ、Flink在Yarn上的分布：

&emsp;&emsp;ResourceManager

&emsp;&emsp;NodeManager

&emsp;&emsp;AppMater(jobmanager和它运行在一个Container中)

&emsp;&emsp;Container(taskmanager运行在上面)

Ⅳ、使用on-yarn的好处：

&emsp;&emsp;提高集群机器的利用率

&emsp;&emsp;一套集群，可以执行MR任务，spark任务，flink任务等...

Ⅴ、Flink on yarn内部实现：

![Flink on yarn内部实现](https://img-blog.csdnimg.cn/2019123110495792.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

<h1 id="4"/>4、Flink HA -- JobManager HA</h1>

&emsp;&emsp;jobManager协调每个flink任务部署。它负责任务调度和资源管理。

&emsp;&emsp;默认情况下，每个flink集群只有一个JobManager，这将导致一个单点故障(SPOF)：如果JobManager挂了，则不能提交新的任务，并且运行中的程序也会失败。

&emsp;&emsp;使用JobManager HA，集群可以从JobManager故障中恢复，从而避免SPOF(单点故障) 。 用户可以在standalone或 YARN集群 模式下，配置集群高可用。

![JobManager HA配置步骤](https://img-blog.csdnimg.cn/20191231105404853.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)
