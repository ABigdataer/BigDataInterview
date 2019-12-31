![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231165609662.png)

&emsp;&emsp;Kafka中的partition机制和Flink的并行度机制深度结合

&emsp;&emsp;Kafka可以作为Flink的source和sink

&emsp;&emsp;任务失败，通过设置kafka的offset来恢复应用

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231165642300.png)

&emsp;&emsp;**setStartFromGroupOffsets()【默认的消费策略】**

		默认读取上次保存的offset信息;
		如果是应用第一次启动，读取不到上次的offset信息，则会根据这个参数auto.offset.reset的值来进行消费数据;
		
		setStartFromSpecificOffsets(Map<KafkaTopicPartition, Long>)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231170105879.png#pic_center)
		
&emsp;&emsp;**setStartFromEarliest()**

		从最早的数据开始进行消费，忽略存储的offset信息

&emsp;&emsp;**setStartFromLatest()**

		从最新的数据进行消费，忽略存储的offset信息

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231170315294.png)

&emsp;&emsp;当checkpoint机制开启的时候，Kafka Consumer会定期把kafka的offset信息还有其他operator的状态信息一块保存起来。当job失败重启的时候，Flink会从最近一次的checkpoint中进行恢复数据，重新消费kafka中的数据。

&emsp;&emsp;为了能够使用支持容错的kafka Consumer，需要开启checkpoint

		env.enableCheckpointing(5000); // 每5s checkpoint一次

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231170441280.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231170450796.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231171217888.png)

&emsp;&emsp;针对job是否开启checkpoint来区分;

&emsp;&emsp;Checkpoint关闭时： 可以通过下面两个参数配置

		enable.auto.commit   设置是否开启自动提交Offset，默认为true
		
		auto.commit.interval.ms  设置自动提交的时间间隔，以毫秒为单位
		
&emsp;&emsp;Checkpoint开启时：当执行checkpoint的时候才会保存offset，这样保证了kafka的offset和checkpoint的状态偏移量保持一致。

		可以通过这个参数设置setCommitOffsetsOnCheckpoints(boolean)，这个参数默认就是true。表示在checkpoint的时候提交offset，
	此时，kafka中的自动提交机制就会被忽略

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231171728288.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231171730236.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231172326912.png)

&emsp;&emsp;如果Flink开启了checkpoint，针对FlinkKafkaProducer09 和FlinkKafkaProducer010 可以提供 at-least-once的语义，还需要配置下面两个参数:

	setLogFailuresOnly(false)
	setFlushOnCheckpoint(true)
	
	注意：建议修改kafka 生产者的重试次数
		  retries【这个参数的值默认是0】

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231172739286.png)

&emsp;&emsp;如果Flink开启了checkpoint，针对FlinkKafkaProducer011 就可以提供 exactly-once的语义,但是需要选择具体的语义:

		Semantic.NONE
		Semantic.AT_LEAST_ONCE【默认】
		Semantic.EXACTLY_ONCE

		注意：在这里我们使用的kafka是基于0.11这个版本，如果是低版本的话，有一些新特性是不支持的。具体的可以参考官方文档
		https://ci.apache.org/projects/flink/flink-docs-release-1.6/dev/connectors/kafka.html

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231173012739.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191231173033262.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)
