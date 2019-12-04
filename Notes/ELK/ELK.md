## ELK 日志采集、存储、可视化
#### 1、ElasticSearch &emsp; 易于部署、竖直、水平可伸缩、API使用简单、模块化、在线文档...
#### 2、Logstash &emsp; 类似于Flume,收集日志(日志数据源:Windows日志、Syslog、Bro、Silk...)

    Logstash支持动态的从各种数据源采集数据，并对数据进行过滤、分析、丰富、统一格式等操作，然后将数据存储到Es;
    
    一般通过Logstash收集服务器上的日志文件，并保存到ElastaticSearch，由ElastaticSearch存储日志、建立索引和搜索数据，然后最后由Kibana提供友好的web界面，从Es中读取数据并进行可视化展示。

#### 3、Kibana &emsp; 可视化平台，搜索展示存储在ElasticSearch中索引数据

    便捷的使用图表、表格等图形化工具展示和分析数据。

#### 4、ELK &emsp; 批量分析、数据集关联、生成图表、预警...
#### 5、集群架构 &emsp; ELK --> EFK

          Filebeat                                            ES DataNode
          Filebeat   --->   Kafka    --->    ES Master  --->  ES DataNode 
           ......        消息缓冲队列                             ......
           Filebeat是轻量级数据收集引擎，基于Logstash-fowarder




