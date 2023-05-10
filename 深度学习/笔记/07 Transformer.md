## Transformer

> 这是一个sequence-to-sequence（seq2seq）的模型

可以解决不知道输出长度的问题，例如

- 语音辨识

- multi-label classification

- object detection

- ……

### Transformer内部架构

![transformer](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305052113162.PNG)

Transformer主要由encoder和decoder两部分组成，encoder可以实现输出一排长度与输入相同的向量，decoder可以实现输出一排由模型自己决定的长度的向量。

#### encoder：

> 给一排向量，输出同样长度的一排向量

encoder包含了很多个block，

![encoder](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305052113163.PNG)

每个block包括的过程如下：

![encoderblock](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305052113164.PNG)

即输入的一排向量（b），先经过一次self-attention得到a,之后与原输入向量（b）相加（residual network）得到a+b，再经过一次layer normalization得到c，得到的结果c作为下一层的输入，在下一层将经过一次全连接层得到d，d会与c相加（residual network）得到c+d，经过一次layer normalization后得到这个block的输出。

#### decoder

##### Autoregressive（AT，常见的一种decoder）

AT 这种decoder的输出是一个一个产生的，在最开始时，decoder会有一个开始符（begin）和encoder的输出一起作为输入，得到第一个输出的向量（得到输出向量的过程是一个概率分布，例如经过decoder后，得到每个字对应分数，“机”字得分占总得分的0.8，“你”占0.1，“end”（结束符，输出此向量后，结束输出）占0.1，那么“机”字就有0.8的概率作为输出），之后此向量代替开始符和encoder的输出一起作为下一次的输入，依次类推，直至输出结束符（end）。

![at](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305052113165.PNG)

##### decoder的内部结构

> decoder的结构与encoder相似

**masked Multi-Head Attention**

decoder的Multi-Head Attention需要masked，那么什么是masked呢？

就是在做注意力机制时，不考虑右边的向量，即将右边的masked，如图所示

![masked](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305052113166.PNG)

在产生$b^1$时只能考虑$a^1$而不能考虑$a^1$之后的。

**cross attention**

![ ](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305052113167.PNG)

在decoder经过masked Multi-Head Attention后和encoder的输出一起做了下一个模块的输入，这个模块即为cross attention，其具体过程如下图所示：

![crossattention](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305052113168.PNG)

##### NAT

同时产生多个输出，decoder的输入一直都是begin和encoder的输出（即不会像AT那样将decoder的输出作为输入），实现平行化，速度更快，但是效果不好。

![nat](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305052113169.PNG)

NAT如何解决在合适时候停止的问题？

1. 另外训练一个模型，输入为encoder的输出，输出为一个数字（代表decoder应该输出的长度）
2. 给一个比较大数量的begin，这样就会输出一个很长的向量集合，再决定在哪里断开。

##### 训练过程（train）

**teacher forcing**

![traintransformer](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305052113170.PNG)

在decoder的训练过程中，会直接把正确的答案作为decoder的输入，这个过程称为teacher forcing，这个过程可能会导致mismatch。

### 训练transformer的一些tips

**chat-bot**

复制一些内容，例如聊天机器人、做摘要等。可以使用pointer network。

**guided attention**

要求在学习过程中的attention有一些属性是固定的。

**beam search**

可能每次都选最好的结果最后反而效果不好，可以在一些时候舍弃一些东西，加一些随机性。，例如下图中红色的路和绿色的路：

![beem search](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305052113171.PNG)

**Scheduled Sampling**

如何解决train中使用了正确答案导致可能出现mismatch的问题？

可以给一些错误的答案。
