# 图像分类中的图像显著性区域

## 一、问题描述

图像分类问题中，检测图像显著性区域，图像显著性区域指的是，图像中的这些区域对于图像分类的权重最高，例如，如果要识别下图中的青蛙，那么很显然，图像中的前景部分，即紫色的青蛙是图像中的显著性区域，是对于该图片分类到“青蛙”类别的权重最高。而图像的背景部分，即蓝色的河水则不是显著性区域。换句话说，图像分类中的显著性区域检测就是要寻找图像中对于分类任务权重大的区域。图像分类示例图像如图1。

![图像分类示例图像](/others/pictures/frog.png)

图1、图像分类示例图像

本文使用的数据集是CIFAR-10数据集，该数据集有50000张图片，每张图片均为分辨率为32*32的彩色图片（分为3个信道）。分类任务需要分成青蛙、卡车、飞机等10个类别。本文设计一种卷积神经网络用于处理图像分类任务，接下来首先介绍基于卷积神经网络的分类模型。为了训练该网络，首先将数据分为训练集、验证集和测试集3个数据集，训练集样本个数为45000，验证集样本个数为5000，测试集样本个数为10000。

## 二、分类模型

### 模型一——基本卷积网络

模型的输入数据是网络的输入是一个4维tensor，尺寸为(128, 32, 32, 3)，分别表示一批图片的个数128、图片的宽的像素点个数32、高的像素点个数32和信道个数3。首先使用多个卷积神经网络层进行图像的特征提取，卷积神经网络层的计算过程如下步骤：

1. 卷积层1：卷积核大小5\*5，卷积核个数64，池化大小2\*2，激活函数ReLU。
2. 卷积层2：卷积核大小5\*5，卷积核个数128，池化大小2\*2，激活函数ReLU。
3. 卷积层3：卷积核大小5\*5，卷积核个数256，池化大小2\*2，激活函数ReLU。
4. 全连接层：隐藏层个数1024，dropout概率0.5，激活函数ReLU。
5. 分类层：softmax分类器。

训练1000轮，观察到loss变化曲线如图2。验证集准确率最高达到60%左右，而且在训练后期会发散。

![model1](/others/pictures/cifar10-v1.png)

图2、模型1的loss收敛曲线图和验证集准确率图

### 模型二——批正则化技术

模型2与模型1不同在于，在模型的每一层中加入batch normalization技术，在卷积层卷积操作后，激活函数激活之前，进行batch normalization的变换，在全连接层隐状态计算之后，激活函数激活之前，进行batch normalization的变换，在分类层计隐状态计算之后，softmax函数之前，进行batch normalization的变换。

加入batch normalization技术之后，训练1000轮，观察到loss变化曲线如图3。验证集准确率最后稳定在0.7982，loss最后收敛于0.0889。

![model2](/others/pictures/cifar10-v2.png) 

图3、模型2的loss收敛曲线图和验证集准确率图

### 模型三——更深层的卷积网络

模型3的改进在使用更深层的网络结构，该网络共15层，具体如下：

1. 卷积层1-5：卷积核大小3\*3，卷积核个数16，使用batch normalization技术，激活函数ReLU。
2. 卷积层6-9：卷积核大小3\*3，卷积核个数32，使用batch normalization技术，激活函数ReLU。
3. 卷积层10-13：卷积核大小3\*3，卷积核个数64，使用batch normalization技术，激活函数ReLU。
4. 池化层14：池化大小为2\*2，使用最大池化。
5. 分类层15：softmax分类，使用batch normalization技术，不使用dropout层。

训练1000轮，观察到loss变化曲线如图4。验证集准确率最高稳定在0.8019，loss最后收敛于0.1128。

![model3](/others/pictures/cifar10-v3.png)

图4、模型3的loss收敛曲线图和验证集准确率图