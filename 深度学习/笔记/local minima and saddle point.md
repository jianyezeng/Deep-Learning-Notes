## Optimization-local minima and saddle point

前面已经说过了，当模型训练结的Loss过大时，可能是模型本身的原因（model bias）和optimization过程没有做好，那么如何改良optimization过程呢？

首先我们要知道optimization出现问题很可能是遇到了critical point。

**critical point**( 临界点，gradient 为 0)

- local minima （梯度为0且是局部最低点）

- saddle point  （梯度为0但并不是局部最低点）

![捕获](image\捕获.JPG)

**如何去辨别是哪一种critical point？**

![捕获](image\捕获.PNG)

当位于临界点时，g（梯度）为0，可以认为
$$
L(\theta) = L(\theta^. )+1/2(\theta-\theta^.)^TH(\theta-\theta^.)
$$
之后可以根据H判断是哪种临界点；

![捕](image\捕.PNG)

当临界点为saddle point时的做法

我们可以根据H去更新$\theta$

![001](image\001.PNG)

Local minima 在更高级的维度中或许也是 一个saddle point