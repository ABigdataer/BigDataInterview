#### 1、Hadoop发行版 ： Apche Hadoop 、 CDH 、 HDP 、 MapR 、 EMR ......

#### 2、CDH是Hadoop众多发行版中的一种，由Cloudera负责维护，基于稳定版本的Apche Hadoop构建；

#### 3、集群搭建过程中遇到的问题：

&emsp;&emsp;Ⅰ、在集群安装前需要考虑Jdk的版本兼容问题，且需要将jdk安装到cdh的默认读取目录 : /usr/java/jdk*/... ,如果确认版本无误，不用理会安装过程中
出现的版本不可用的提示，直接选择下一步并完成安装。

&emsp;&emsp;Ⅱ、CDH有默认的安装路径，如果需要自定义，则需要在安装界面修改配置信息；

&emsp;&emsp;Ⅲ、由于CDH内部并未包含Kafka模块，所以如果需要集成Kafka服务，需要单独下载Kafka的pero文件和pelcels文件等，如果Kafka启动报错，则可能是由于
默认的堆内存较小，将Java heap size 调节为1G及以上试试。

