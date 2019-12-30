## 1、线性回归(有监督值预测算法模型)

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

## 2、贝叶斯(有监督的基于概率的分类算法模型)
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



