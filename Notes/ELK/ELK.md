## ELK 日志采集、存储、可视化
#### 1、ElasticSearch 易于部署、竖直、水平可伸缩、API使用简单、模块化、在线文档...
#### 2、Logstash 类似于Flume,收集日志(日志数据源:Windows日志、Syslog、Bro、Silk...)

&emsp;&emsp;一般通过Logstash收集服务器上的日志文件，并保存到ElastaticSearch，由ElastaticSearch存储日志、建立索引和搜索数据，然后最后由Kibana提供友好的web界面，从Es中读取数据并进行可视化展示。

#### 3、Kibana 可视化平台，搜索展示存储在ElasticSearch中索引数据
#### 4、ELK 批量分析、数据集关联、生成图表、预警...

