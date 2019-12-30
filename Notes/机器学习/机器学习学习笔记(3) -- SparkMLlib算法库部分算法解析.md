## 目录

* [1、线性回归(有监督值预测算法模型)](#1)
*  [2、贝叶斯(有监督的基于概率的分类算法模型)](#2)
*  [3、KNN最邻近算法(有监督的分类算法)](#3)
*  [4、K-Means算法(无监督聚类算法)](#4)
*  [5、逻辑回归算法(线性回归分析有监督分类模型)](#5)
*  [6、决策树和随机森林(非线性有监督分类模型)](#6)


<h2 id="1">1、线性回归(有监督值预测算法模型)</h2>

&emsp;&emsp;由点与点之间的关系反推出函数表达式的过程就是回归，回归在机器学习中解决的问题就是值预测问题；确定一条最好的直线来拟合所有的点，假设直线是y=W0+W1X，确定直线就是确定W0和W1的值；

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230092826737.png#pic_center)

&emsp;&emsp;一元线性回归公式：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230093037308.png#pic_center)

![Alt](https://img-blog.csdnimg.cn/20191230092019523.png#pic_center)

&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;Θ₀:截距 &emsp;&emsp;  Θ₁：权重 &emsp;&emsp; x:特征|维度 &emsp;&emsp;h₀(X):预测的值

&emsp;&emsp;二元线性回归公式：

![二元线性公式](https://img-blog.csdnimg.cn/20191230095507223.png#pic_center)


&emsp;&emsp;n元线性回归公式：

![n元线性回归公式](https://img-blog.csdnimg.cn/2019123009561017.png#pic_center)

&emsp;&emsp;即：

![n元线性回归公式](https://img-blog.csdnimg.cn/201912300957299.png#pic_center)

&emsp;&emsp;**线性回归预测的目的就是求使所有的点到这条线的误差最小的函数表达式；**

&emsp;&emsp;线性回归误差公式：

![线性回归误差公式](https://img-blog.csdnimg.cn/20191230094114477.png#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230094249886.png#pic_center)

&emsp;&emsp;根据极大似然估计法可得知，当误差最小，即y发生的概率最大，需要求得y最大值时的误差值ε，下面是标准正态分布函数图：

![标准正态分布](https://img-blog.csdnimg.cn/2019123010123728.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

&emsp;&emsp;误差推倒公式：

![误差推倒公式](https://img-blog.csdnimg.cn/20191230095203739.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

&emsp;&emsp;误差最小化：要求参数的最优解，使得所有点到这条线的误差最小，如果将y表示样本的真实值，h(x₀)表示样本的预测值，那么可得损失函数：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230101824966.png#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/2019123010191948.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

&emsp;&emsp;**误差优化方法有最小二乘法和梯度下降法；**

&emsp;&emsp;通过求导的方式可以确定Θ值，由于Θ是一组值，无法确定其中的Θ₀和Θ₁的值，即正向求导无法求得答案，则可以使用梯度下降的方法求Θ₀和Θ₁的值，梯度下降法又分为随机梯度下降法和批量梯度下降法；在训练线性回归模型时需要指定一个误差值J(Θ)，梯度下降法调节Θ₀和Θ₁的值时还要指定步长和学习率。下面是线性回归模型的训练流程图解：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230104623372.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

&emsp;&emsp;梯度下降停止的条件：
	
		Ⅰ、当计算得到的误差值小于指定的误差值
		Ⅱ、当迭代到指定次数

&emsp;&emsp;训练数据集一般分为两部分：

		Ⅰ、训练集
		Ⅱ、测试集
		一般训练集和测试集8|2分。

&emsp;&emsp;训练模型过程中的过拟合和欠拟合问题：

		过拟合即模型在训练集中表现优异，但是在测试集中表现极差，当训练集非常少，特征非常多时容易出现过拟合；
		欠拟合即训练集和测试集表现都不好。

<h2  id="2" />2、贝叶斯(有监督的基于概率的分类算法模型)</h2>

&emsp;&emsp;机器学习算法中，有种依据概率原则进行分类的朴素贝叶斯算法，正如气象学家预测天气一样，朴素贝叶斯算法就是应用先前事件的有关数据来估计未来某个事件发生的概率；

&emsp;&emsp;下面是基于贝叶斯定理的条件概率公式：

![基于贝叶斯定理的条件概率公式](https://img-blog.csdnimg.cn/20191230111433579.png#pic_center)


							P(A) 称为先验概率(已知) 			P(B)称为标准化常量(已知)
							P(B|A)称为似然度(已知)   	  	P(A|B)称为后验概率(未知)

 &emsp;&emsp;如何理解朴素贝叶斯算法？下面将从如何通过该算法进行垃圾分类进行分析：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230113300911.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

		P(spam) 所有邮件中是垃圾邮件的概率 		  	 P(Viagra|Spam) 是垃圾邮件且含有Viagra的概率
		P(Viagra) 所有邮件内含有Viagra的概率     P(spam|Viagra) 含有Viagra且是垃圾邮件的概率

 &emsp;&emsp;计算贝叶斯定理中每一个组成部分的概率，我们必须构造一个频率表：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230114732587.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

 &emsp;&emsp;由表可以计算 P(spam|Viagra)  =  P(Viagra|Spam)*P(spam) / P(Viagra) = （4/20）* (20/100) / (5/100) = 0.8,所以可以得知电子邮件中含有单词Viagra，那么该邮件是垃圾邮件的概率为80%,所以含有Viagra的消息都需要被过滤掉。

 &emsp;&emsp;当有更多额外特征时：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230115503814.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

 &emsp;&emsp;可以得到概率如下：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230115551473.png#pic_center)

 &emsp;&emsp;拉普拉斯估计：
 
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019123011301337.png#pic_center)

<h2 id="3"/>3、KNN最邻近算法(有监督的分类算法)</h2>

 &emsp;&emsp;该算法的思路是：如果一个样本空间中的K个最相似的样本中大多数属于某个类别，则该样本也属于这个类别。KNN算法由邻居推断类别，就是距离衡量样本之间的相似度。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230144540664.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230144619207.png#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/2019123014533061.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)
&emsp;&emsp;距离的度量常用的有欧式距离(两点之间的距离)、曼哈顿距离(出租车距离)、余弦夹角(余弦角度)等，一般使用欧式距离，对于文本分类问题则常用余弦距离；

 &emsp;&emsp;**该算法的三个基本要素是：K值的选择、距离度量和分类决策规则；**

 &emsp;&emsp;该算法在分类时有个不足是当样本数量不平衡时，如果一个类别的样本数量很大，而其它类别的样本数量很小时，有可能导致当输入一个新的样本时该样本的K个邻居中类别数量较多的样本占多数，从而导致该结果很大可能性是数量多的类别，解决该问题的方案是给样本给与不同的权重，根据和输入样本距离的远近给近邻进行加权，越近权值越大，权重一般为距离平方的倒数。

&emsp;&emsp;该算法还有一个不足是当某些值的数值大小特别大时可能会对距离的计算产生影响，为防止某一维度的数据对距离的计算有影响，保证多个维度的特征是等权重的，且最终结果不能被数据的大小影响，则可以将各个维度数据的数值进行归一化，把数据归一化到[0,1]区间内。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230154522647.png#pic_center)

<h2 id="4" /> 4、K-Means算法(无监督聚类算法)</h2>

&emsp;&emsp;**聚类的定义和思想：**

&emsp;&emsp;聚类是一种无监督学习，就是对大量未知标注的数据集按数据的内在相似性将数据集划分为多个类别，使类别内的数据相似度较大而类别间的相似度较小，一个类别也叫一个簇，类别内的相似性越大，类别之间的差别越大则聚类效果越好。

&emsp;&emsp;分类的目标是事先已知的，而聚类事先不知道目标变量是什么，类别没有像分类一样预先定义，聚类用于知识的发现，而不是预测。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230160221439.png#pic_center)

![K-Means算法实现结果示例](https://img-blog.csdnimg.cn/20191230160316459.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

&emsp;&emsp;K-Means算法实现步骤：

![K-Means算法实现步骤](https://img-blog.csdnimg.cn/20191230160400122.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230161525898.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)


&emsp;&emsp;K-Means算法训练过程：

![K-Means算法训练过程](https://img-blog.csdnimg.cn/20191230160508179.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

&emsp;&emsp;K值的选取一般可以根据问题的内容来确定，也可以根据肘部法确定。

![K-Means算法结束条件](https://img-blog.csdnimg.cn/20191230160625339.png)

&emsp;&emsp;**相似度计算方法：**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230161313420.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)


Ⅰ、欧式距离

![欧式距离](https://img-blog.csdnimg.cn/20191230161243109.png#pic_center)

Ⅱ、余弦距离

![余弦距离](https://img-blog.csdnimg.cn/20191230161354621.png#pic_center)

&emsp;&emsp;**K-Means算法的优劣：**

![K-Means算法的优劣](https://img-blog.csdnimg.cn/20191230161656828.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70)

&emsp;&emsp;**K-Means++算法：**

&emsp;&emsp;K-means算法可能存在随机选取的中心点位置非常近，导致迭代次数增加，K-Means++算法就是在K-Means算法的基础上使随机选取的中心点不会聚集的算法。

<h2 id="5" />5、逻辑回归算法(线性回归分析有监督分类模型)</h2>

&emsp;&emsp;线性回归是一种用于分类的算法，线性回归要求因变量必须是连续性的数据变量，逻辑回归要求因变量必须是分类变量，可以是二分类或者多分类(多分类可以归结为二分类问题)，逻辑回归的输出是0~1之间的概率。

&emsp;&emsp;逻辑回归公式：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230163213382.png#pic_center)

![线性回归公式](https://img-blog.csdnimg.cn/20191230162909193.png#pic_center)

![线性回归](https://img-blog.csdnimg.cn/20191230162954859.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

&emsp;&emsp;**S曲线的值域是在（0，1）之间，值域的中间值0.5就是作为分类的阈值；**

&emsp;&emsp;找到逻辑回归分界线：

![找到逻辑回归分界线](https://img-blog.csdnimg.cn/20191230163608725.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230163647537.png#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230163709380.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

由以上分析可得知逻辑回归的本质还是线性回归。

&emsp;&emsp;逻辑回归的优缺点：

![逻辑回归的优缺点](https://img-blog.csdnimg.cn/20191230164032749.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

&emsp;&emsp;**逻辑回归-梯度下降法：**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230164315330.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230164410366.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230164449987.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230164530908.png#pic_center)

现在问题就转换为求解损失函数最小值

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230164754619.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230164902535.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70)

&emsp;&emsp;**逻辑回归优化方案：**

Ⅰ、有无截距

Ⅱ、线性不可分问题

		解决办法：
		(1)重新选择算法
		(2)增加维度，映射至高维，增加新维度

Ⅲ、调整分类阈值

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230165432736.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70)

Ⅳ、鲁棒性调优

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230165515595.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70)
		
		正则化、增加权重


Ⅴ、归一化数据

![在这里插入图片描述](https://img-blog.csdnimg.cn/2019123016580628.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230165843562.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70)

Ⅵ、调整数据的正负值

Ⅶ、训练方法优化

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230170011650.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/2019123017005140.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70)

<h2 id="6" />6、决策树和随机森林(非线性有监督分类模型)</h2>

&emsp;&emsp;线性分类模型可能会存在线性不可分问题，但是非先行分类算法模型就不存在该问题；

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230171758547.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230171842891.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230171947478.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230172010735.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70)			

线性分裂纯粹度：

![在这里插入图片描述](https://img-blog.csdnimg.cn/2019123017222176.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230172305178.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230172348340.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230172448292.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230172614518.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230172657709.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230172726630.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230172836810.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230172929860.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230172951907.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230173027628.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230173057509.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230173120683.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230173223287.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191230173300358.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwNjQwMjI4,size_16,color_FFFFFF,t_70)
