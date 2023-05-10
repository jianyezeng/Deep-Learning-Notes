# 02 训练神经网络的一些tips

[TOC]

## 一、Optimization-local minima and saddle point

前面已经说过了，当模型训练结的Loss过大时，可能是模型本身的原因（model bias）和optimization过程没有做好，那么如何改良optimization过程呢？

首先我们要知道optimization出现问题很可能是遇到了critical point。

**critical point**( 临界点，gradient 为 0)

- local minima （梯度为0且是局部最低点）

- saddle point  （梯度为0但并不是局部最低点）

![捕获](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305052113225.JPG)

**如何去辨别是哪一种critical point？**

![捕获](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305052113226.PNG)

当位于临界点时，g（梯度）为0，可以认为
$$
L(\theta) = L(\theta^. )+1/2(\theta-\theta^.)^TH(\theta-\theta^.)
$$
之后可以根据H判断是哪种临界点；

![捕](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305052113227.PNG)

当临界点为saddle point时的做法

我们可以根据H去更新$\theta$

![001](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305052113228.PNG)

Local minima 在更高级的维度中或许也是 一个saddle point。

## 二、Batch and Momentum

### 1. shuffle

将资料分成多个Batch的过程，常见的做法是在每一个Epoch之前分一次Batch，每一次的Batch都不一样。

### 2. 为什么要采用Batch

时间？事实上，因为有平行运算的原因，large Batch（例如只分出1个Batch）在时间上往往要比small Batch更占优势（超大数据集除外）

答案是small Batch的Optimization做的更好，small Batch的update过程是更noisy的，但恰恰是更noisy更有利于optimization的。为什么呢？因为如果是large batch，在update过程中遇到一个local minima或者saddle point就停下来了，而对于small batch，由于每次训练采用的数据集不同，因此function也不同，当一个function遇到local minima或者saddle point停下来了，却不会影响另一个function。

![batch](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305101625355.png)

除此之外，small Batch也更有利于预测（或测试），其原因尚有争论，有一个观点是small Batch更有利于遇到flat minima,而large Batch更倾向于进入sharp minima。（flat minima指周围梯度绝对值较小的点，而sharp minima则与之相反，由于测试的数据集与训练的数据集可能存在差别，flat minima显然更有包容性，而small Batch在遇到sharp minima时，更有可能跳出去，因此测试结果更好）。

![good-bad-local](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305101625196.png)

**small Batch 与 large Batch对比**：

![s-b对比](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305101625295.png)

### 3. momentum

每次$\theta$的移动并不是只考虑gradient ，而是考虑过去所有gradient的总和。

即每次移动的方向为梯度反方向+原来移动方向

![mumentum](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305101625285.png)

 这种方法的好处是可以有效避免local minima和saddle point（可以类比一下惯性)。![mu](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305101625228.png)

## 三、自动调整学习速率

### 1. 不变的learning rate存在的问题：

1. 如果learning rate比较大，可能无论如何都无法到达最低点，而是在最低点旁左右横条

   ![rate large](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305101626991.png)

2. 如果learning rate比较小，问题1得到一定程度的解决，但是当grandient比较大时，移动的还比较快，当grandient比较小时，就会出现移动的很慢的情况。

![rate](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305101626202.png)

### 2. Root Mean Square与RMSProp

![02](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305101626112.png)

引入一个新的参数$\sigma_i^t$, 这样可以实现learning rate自动调整的目的，但这样存在一个问题，那就是最开始移动的g与最近移动的g对于$\sigma$的大小的影响是相同的，这样learning rate对于g变化的反应是比较缓慢的，那么做到让learning rate更快地对g变化做出反应呢？我们可以使用RMSProp

![sig](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305101626066.png)

### 3. learning Rate Scheduling

![yz](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305101626133.png)

在进入grandient较小的区域后，在纵轴方向累计了很多很小的$\sigma$,当累计到一定程度后，可能就会出现如图所示的情况。为了解决这一问题，可以使用learning Rate Scheduling

![？？](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305101626124.png)

即让$\eta$也随移动而变化，如何变化呢？有两种方案：

- learning rate decay：即从大到小，
- warm up：先从小增大到一定程度，再减小，原因：刚开始的$\sigma$是不准确的，因此先缓慢移动，等$\sigma$达到一定的准确程度，再继续快速移动.

### 4. 自动更新学习速率+Momentum

![s+m](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305101626524.png)

在$\sigma$的更新过程中，是与方向无关的（取了平方），因此与momentum不会抵消。

## 四、批次标准化（batch normalization）

当一个模型的多个输入差异比较大时（比如$x_1$在0-1，$x_2$在100-200之间，那么$x_1$如果发生改变对最后Loss的影响一定是远大于$x_2$的，因此我们希望**让不同维度的输入有一致的范围，让优化更简单**

### 1. feature Normalization（特征归一化）

![feature normalization](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305101627635.png)

我们将每个维度的输入都转化为同一范围的量的过程称为标准化，如上图所示即为标准化的过程，但是图中的标准化mean和$\sigma_i$的来源都是所有资料，但是我们知道，我们正常是要分成很多个batch，那么该怎么办呢？

### 2. batch Normalization

每个batch并不是同步进行的，为了完成feature Normalization的过程，我们可以将求所有资料的mean和$\sigma$变为这个batch的。

### 3. 多次标准化

![deep-num](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305101627581.png)

在深度学习中，是有多层layer的，上一个layer的输出也是下一个layer的输入，第一层输入做了feature Normalization，但后面的输出却没有做，这显然是不合理的，因此我们可以对每层的输出都做一个feature Normalization。至于这个feature Normalization放在sigmoid这类激化函数前后都是可以的（不过sigmoid一般建议放在前面，因为sigmoid函数在0附近grandient较大)。

在某些情况下，我们希望最后的输出并不是以0为mean的，因此在输出精确到一定程度后可以对其进行变换：

![afbt](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305101627440.png)

### 4. testing时的batch Normalization

在实际使用的测试过程中，可能一次输入并没有一个batch，这时batch Normalization可能会出现问题，pytorch给了解决方案，即在训练时每一个batch，都会将其mean和$\sigma$拿来做一个平均值，之后用来作为测试时的mean和标准差

![meanmean](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305101627436.png)