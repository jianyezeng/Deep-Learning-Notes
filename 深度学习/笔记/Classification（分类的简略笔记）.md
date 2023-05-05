## Classification

### 如何做分类

- 当作预测数值

  每个分类之间是无关的，但预测的数值之间却存在关系的远近，例如将预测结果0当作分类A，预测结果1当作分类B，预测结果100当作分类C，显然预测数值1和2之间关系要比1和100更接近，但分类A,B,C之间是不存在这种关系的，因此这种做法并不合适。

- 把每个分类结果当作独热码

### softmax 

![softmax](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305052113972.PNG)

Classification需要对结果进行一次softmax，softmax步骤如下：

![soft-max](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305052113973.PNG)

这样得到的结果，全都在0-1，且和为1

### Loss of Classification

在进行分类时对Loss的定义

1. Mean Square Error(MSE)
   $$
   e = \sum_i(\widehat{y}_i-\dot{y_i})^2
   $$

2. Cross-entropy 
   $$
   e = -\sum_i\widehat{y}_iln\dot{y_i}
   $$

**分类时cross-entroy更适用。**

![cross](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305052113974.PNG)

如上图所示，MSE的结果在Loss比较大时grandient比较小，更难update到loss小的地方，而cross-entropy的结果更有利于优化（optimization）。