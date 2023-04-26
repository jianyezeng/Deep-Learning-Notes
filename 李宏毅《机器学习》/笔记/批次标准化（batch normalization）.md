## 批次标准化（batch normalization）

当一个模型的多个输入差异比较大时（比如$x_1$在0-1，$x_2$在100-200之间，那么$x_1$如果发生改变对最后Loss的影响一定是远大于$x_2$的，因此我们希望**让不同维度的输入有一致的范围，让优化更简单**

### feature Normalization（特征归一化）

![feature normalization](C:\Users\zjy\Desktop\机器学习\李宏毅《机器学习》\image\feature normalization.PNG)

我们将每个维度的输入都转化为同一范围的量的过程称为标准化，如上图所示即为标准化的过程，但是图中的标准化mean和$\sigma_i$的来源都是所有资料，但是我们知道，我们正常是要分成很多个batch，那么该怎么办呢？

### batch Normalization

每个batch并不是同步进行的，为了完成feature Normalization的过程，我们可以将求所有资料的mean和$\sigma$变为这个batch的。

### 多次标准化

![deep-num](C:\Users\zjy\Desktop\机器学习\李宏毅《机器学习》\image\deep-num.PNG)

在深度学习中，是有多层layer的，上一个layer的输出也是下一个layer的输入，第一层输入做了feature Normalization，但后面的输出却没有做，这显然是不合理的，因此我们可以对每层的输出都做一个feature Normalization。至于这个feature Normalization放在sigmoid这类激化函数前后都是可以的（不过sigmoid一般建议放在前面，因为sigmoid函数在0附近grandient较大)。

在某些情况下，我们希望最后的输出并不是以0为mean的，因此在输出精确到一定程度后可以对其进行变换：

![afbt](C:\Users\zjy\Desktop\机器学习\李宏毅《机器学习》\image\afbt.PNG)

### testing时的batch Normalization

在实际使用的测试过程中，可能一次输入并没有一个batch，这时batch Normalization可能会出现问题，pytorch给了解决方案，即在训练时每一个batch，都会将其mean和$\sigma$拿来做一个平均值，之后用来作为测试时的mean和标准差

![meanmean](C:\Users\zjy\Desktop\机器学习\李宏毅《机器学习》\image\meanmean.PNG)