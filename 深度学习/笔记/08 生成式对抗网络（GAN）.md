## 生成式对抗网络（GAN）

### 基本概念

#### generator

>  可以输出一个分布的network

![捕获](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305092158143.PNG)

##### generator过程

network输入为x和一个从某一简单的（已知）分布中随机采样得到的一个random的z；network输出为是一个更复杂的分布。在获取z的过程中，不同方法差别不大。

##### 为什么我们有时需要可以输出一个分布的network？

希望能找到同样输入、但可以有多个不同输出，且这些输出都正确的函数。在这种情况下，我们希望network的输出是有几率的。例如：聊天机器人、绘画等。

#### discriminator

也是一个神经网络结构，可以采用CNN、transformer等架构。

**作用：**输入图片，输出数字，数字越大，越符合预期结果，即对generator产生的输出进行评估。

#### 算法过程

随机初始化gengeration和discriminate参数；

step1： 训练discriminate；

step2： 训练gengeration；

step3： 回到step1，循环往复。

### 理论部分

WGAN



GAN for sequence generation



评估

影像分类系统

mode collapse

mode dropping



diversity 与 quality



IS

FID



无标注资料

cycle GAN

两个generator

star GAN

