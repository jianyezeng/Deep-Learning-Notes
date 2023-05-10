## 卷积神经网络(CNN)

解决：**image classification（图片分类）**

输入：图片（默认大小是一致的）

输出：独热码（每个独热码代表其中一种分类）

### 1. 卷积神经网络的第一种表达

#### 1.1 如何把一张影响变成输入？

每张图片实际上是一个三维的tensor，三个维度：长、宽、层数（例如：RGB三个channels），将其拉直成一个向量，即可实现影像输入（如下图所示）

![将图片作为输入](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305052114518.PNG)

在拉直得到的向量中，每个元素的数值即代表了某一个位置某一种颜色的强度。

#### 1.2 简化

如果安装原来的Fully Connected Network对输入的向量进行处理，太过复杂，会增大overfitting的风险，那么该如此简化呢？

##### 1.2.1 每个Neuron只负责图片的一小部分

每个Neuron的图片的部分称为一个receptive field，不同Neuron的receptive field可以重叠，多个Neuron可以负责一个receptive field（因为一些图片的特征可能不只有一个）。

**最经典的receptive field安排方式**

每个Neuron负责所有channels ，长和宽一般3x3，一个receptive field移动stride（自定义）即可得到另一个receptive field（stride自定义），移动时超出范围，可补0。

##### 1.2.2 共享参数

一个特征可能同时出现一个图片的不同位置；因为我们可以让负责这一特征的Neuron共享参数

![共享参数](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305052114519.PNG)

每个receive field都有一组neuron在负责，不同receive field的对应neuron共享参数，我们将每一个neuron的参数称为一个fitler（卷积核）

![如何共享参数](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305052114520.PNG)

在经过2.1和2.2后的网络层就是**卷积层**，使用卷积层的神经网络即为**卷积神经网络**。

### 2. 卷积神经网络的另一种表达

**feature map**

![卷积层](笔记\image\卷积层.PNG)

用n个filter对图片的特征进行抓取，每个filter负责一个特征。在一个filter的抓取全部完成后可以得到一个一层的tensor（如下图）。

![filter1](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305052114522.PNG)

而n个filter全部进行一遍后既可得到一个n层的tensor，即为一个feature map。而feature map又可以看出一个拥有n个channels的图片，作为下一层的输入，而下一层的filter也要是一个n个channels的tensor（如下图）。

![多层卷积](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305052114523.PNG)

如果使用3*3的filter是否可以抓取原图像中更大部分才能体现的特征？答案是可以的，因为使用了多层卷积之后得到的feature map中可以一个元素就能体现出原来很大部分的特征。

### 3. pooling

将一个比较大图片做一个subsampling（如把偶数的行、奇数列去除），图片变为原来的1/4，但不会影响图片特征的提取

![pool](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305052114524.PNG)

如何做pooling？

将每个filter经过提取后得到的tensor中元素分成几个小组，从组中选出有代表性的元素留下来，其他元素去除（如下图）

![pooling](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305052114525.PNG)

max pooling：选择最大的元素作为代表。

pooling会对精确度造成损失，因此也**可以不用**。

### 4. CNN图片识别流程

![THE WHOLE CNN](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305052114526.PNG)

经过卷积和池化（pooling，可以不用）后的输出经过flatten可以得到一个向量，再经过全连接层和softmax即可得到最后的输出