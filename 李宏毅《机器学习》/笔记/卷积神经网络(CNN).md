卷积神经网络(CNN)

专门用在影像上的

image classification

默认输入大小一样



简化



每个神经元负责图片的一小部分输入

receptive field 可以重叠



最经典的receptive field安排方式

看所有channels（rgb三层）

高和宽（kernel size 一般3x3）

一个receptive field移动stride即可得到另一个receptive field（stride自定义）

移动时超出范围，可补0



共享参数

filter



feature map



pooling



max pooling



棋盘



不能处理大小