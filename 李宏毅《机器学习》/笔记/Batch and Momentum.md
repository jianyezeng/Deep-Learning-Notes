## Batch and Momentum

### shuffle

将资料分成多个Batch的过程，常见的做法是在每一个Epoch之前分一次Batch，每一次的Batch都不一样。

### 为什么要采用Batch

时间？事实上，因为有平行运算的原因，large Batch（例如只分出1个Batch）在时间上往往要比small Batch更占优势（超大数据集除外）

答案是small Batch的Optimization做的更好，small Batch的update过程是更noisy的，但恰恰是更noisy更有利于optimization的。为什么呢？因为如果是large batch，在update过程中遇到一个local minima或者saddle point就停下来了，而对于small batch，由于每次训练采用的数据集不同，因此function也不同，当一个function遇到local minima或者saddle point停下来了，却不会影响另一个function。

![batch](C:\Users\zjy\Desktop\机器学习\李宏毅《机器学习》\image\batch.PNG)

除此之外，small Batch也更有利于预测（或测试），其原因尚有争论，有一个观点是small Batch更有利于遇到flat minima,而large Batch更倾向于进入sharp minima。（flat minima指周围梯度绝对值较小的点，而sharp minima则与之相反，由于测试的数据集与训练的数据集可能存在差别，flat minima显然更有包容性，而small Batch在遇到sharp minima时，更有可能跳出去，因此测试结果更好）。

![good-bad-local](C:\Users\zjy\Desktop\机器学习\李宏毅《机器学习》\image\good-bad-local.PNG)

**small Batch 与 large Batch对比**：

![s-b对比](C:\Users\zjy\Desktop\机器学习\李宏毅《机器学习》\image\s-b对比.PNG)

### momentum

每次$\theta$的移动并不是只考虑gradient ，而是考虑过去所有gradient的总和。

即每次移动的方向为梯度反方向+原来移动方向

![mumentum](C:\Users\zjy\Desktop\机器学习\李宏毅《机器学习》\image\mumentum.PNG)

 这种方法的好处是可以有效避免local minima和saddle point（可以类比一下惯性)。![mu](C:\Users\zjy\Desktop\机器学习\李宏毅《机器学习》\image\mu.PNG)