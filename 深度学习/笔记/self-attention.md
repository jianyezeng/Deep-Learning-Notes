## self-attention

在有些情况下，输入并不是一个向量。例如：文字处理，每一个单词都是一个向量，输入是一个向量的集合，下面的笔记就是关于这种问题需要如何处理。

输入：一个向量的集合（例如：音频、语句、图、分子等）

输出： 

-  case1：输入输出长度一样（例如文字处理过程中的词性标注）

- case2：只输出一个label

- case3： 不知道需要输出多少个label

### 为什么要使用self attention

对于case1，即输入输出长度一样的情况，又可以称为Sequence Labeling

如何解决这种问题？如果按照之前的，只使用全连接层（即每个向量经过各种Neuron得到最终的输出）得到的结果有着巨大的缺陷，因为这样在每个向量计算过程实际只考虑了一个向量，但是实际上，每个向量的输出与输入集合中其他向量都是存在关系的（例如，“I saw a saw”，这个句子中两个“saw”有着不同的词性，但只用全连接层输出可能相同），这种关系是我们需要考虑的。

![isawasaw](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305052113449.PNG)

如何才能考虑到这种关系的存在呢？在这里引入了self attention。self attention的输入和输出个数是相同的，每个输入向量都会对应一个输出向量（该输出向量是考虑了该向量与输入集合内其他向量之间关系的），之后将self attention得到的输出向量作为后续网络层的输入，就可以达到实现考虑输入集合内关系的效果。

### 如何实现self attention

#### 计算两个向量相关性的方法

dot product & additive

![计算attention](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305052113451.PNG)

#### 计算self attention输出的过程

![计算selfattention输出](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305052113452.PNG)

如上图所示，解释了如果得到其中一个输出（$b^1$）的过程,而得到其他输出的过程与之类似。

而整个过程的矩阵表达如下：

![selfattention矩阵](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305052113453.PNG)

在这个过程中，只有三个变换所需矩阵是要经过学习得到的（$W^q,W^k,W^v$）。

#### multi-head self-attention

![mutiself](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305052113454.PNG)

在有些情况下，输入集合内向量的关系并不是只有一种类型，因此我们可以使用multi-head self-attention，这样可以考虑多种关系。实现过程是在原有self-attention的基础上增加$W^q,W^k,W^v$的数量。

#### positional encoding

在某些情况下，输入集合内向量的位置也是很重要的（例如句子中第一个单词往往是主语），

因此在这种情况下，我们可以使用一组向量（$e^1,e^2,…，e^i)$存储位置，每个位置对应的向量是不同的，将其作为输入，这样在学习过程中就会考虑位置的因素。（如下图）

![position](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305052113455.PNG)



### self attention的应用

#### 语言识别

**truncated self-attention**

![](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305052113456.PNG)

由于音频的数据量是很庞大的，如果全都拿来做self attention，运算量较大，因此我们可以每次只考虑附近几个向量之间的关系，即truncated self-attention，如上图所示。

#### 影像处理

**self-attention VS CNN**

CNN是self attention的一个特例，之前说CNN中是把一个图片拼成一个向量作为输入，实际上，也可以将其作为一个向量的集合作为输入，使用self attention解决。

#### 图处理

在图中节点之间是否有连线实际上说明了节点之间的关系，所以我们可以只考虑之间有连线的节点之间的关系。

![GNN](https://zjyimage.oss-cn-beijing.aliyuncs.com/202305052113457.PNG)