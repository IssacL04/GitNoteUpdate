# 一.机器学习基础

## **参数与超参数**

**参数**是模型内部的配置变量，其值可以根据数据进行估计。

- 模型在进行预测时需要它们。
- 它们的值定义了可使用的模型。
- 他们是从数据估计或获悉的。
- 它们通常不由编程者手动设置。
- 他们通常被保存为学习模型的一部分。

举例：神经网络中的权重；支持向量机中的支持向量；线性回归或逻辑回归中的系数。

**超参数**是模型外部的配置，其值无法从数据中估计。

- 它们通常用于帮助估计模型参数。
- 它们通常由人工指定。
- 他们通常可以使用启发式设置。
- 他们经常被调整为给定的预测建模问题。

举例：训练神经网络的学习速率；用于支持向量机的C和sigma超参数。

超参数是在算法运行之前手动设置的参数，用于控制模型的行为和性能；而参数是在模型训练过程中自动学习的参数，用于调整模型的预测能力。超参数的选择对模型性能有重要影响，而参数的学习是通过优化算法自动进行的。参数的学习是模型训练的过程，通过将训练数据输入模型中，根据损失函数计算预测值与真实值之间的差异，并使用优化算法（如梯度下降）来更新参数，使模型逐渐收敛于最优解。参数学习的目标是找到最佳的参数配置，使得模型能够对新的未见过的数据进行准确的预测。

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310011745804.png)

## CNN

### CNN中的超参数

1.conv(卷积层)和dense layer(全连接层)数量

2.每个卷积层中的卷积核的数量，大小，采样间隔

3.全连接层宽度（输出的向量大小）

https://towardsdatascience.com/types-of-convolutions-in-deep-learning-717013397f4d

https://blog.csdn.net/m0_54487331/article/details/112846015

在制作一个神经网络的时候，需要手动设置超参数，并且进行每个卷积层的单独设置，每个卷积层是先后连接的：

![image-20231001180441938](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310011804982.png)

Neural Architecture Search: Find the architecture that leads to the best validation accuracy.

![image-20231001184255439](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310011842506.png)

4,3,2分别对应卷积核数量，大小，采样间隔的候选方案数量，20代表卷积层数量。

![image-20231001184421039](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310011844076.png)

## Random Search

通过随机采样超参数并代入模型中训练，计算准确率；通过多次采样后将准确率最高的超参数作为最终的结果。

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310011852424.png)

**问题**：

训练次数过多，成本过高(具体时间取决于训练设备和数据集大小)

搜索空间过大(如NAS所述)

## Differentiable NAS

1.选取一定数量q的候选模块，每个模块中卷积层的超参数都不同

![image-20231002155236384](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310021552475.png)

2.指定神经网络的层数p（把每个模块视作一个层）

3.从候选模块中依次选取模块做为每一层(**残差连接**)

https://zhuanlan.zhihu.com/p/449792026

4.那么所有的神经网络结构的数量总和即为q^p.为了提升准确率，引入**super-net**.

### Super-Net

super-net的每一层都由q个候选模块并联而成，输入的向量会被这些模块处理后得到q个输出向量。输出向量进行加权平均之后得到最终的输出向量Z，其中权重α总和为1.α值通过softmax函数计算.

![image-20231002155912446](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310021559491.png)

![image-20231002160135004](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310021601062.png)

https://zhuanlan.zhihu.com/p/628492966

super-net不同层的参数都是独立且不同的。通过对每一层进行训练，可以最终让每个层只保留一个模块。

![image-20231002160248466](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310021602516.png)

![image-20231002160423925](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310021604964.png)

输入训练集x，并通过计算由x计算出的预测结果与y的Loss值判定某个模块的准确度.

![image-20231002160549857](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310021605893.png)

由于super-net的输出p关于Θ可微，梯度可以通过Loss函数传递到P，所以可以用反向传播计算梯度关于Θ的关系。通过该方法计算出Θ后可以计算权重α。在super-net的每一层中我们选取权重最高的模块。

### Computational Efficient Design

**SUPER-NET**准确率高但同时计算量也更大，在移动设备等环境中，计算量的优先级一般比准确率更高.

与上面计算Loss的方式不同，为了减少计算量，我们计算Latency(即计算消耗的时间)。需要注意的是，这里的权重仍然与Latency有关，是一个简单函数。

 ![image-20231002165107104](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310021651166.png)

![image-20231002165350578](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310021653612.png)

可以将Loss函数与Lat函数结合，计算计算量和准确率的总开销。在下图的两种计算方式中，让λ(λ也是一个超参数)大一些，即可提升计算量的权重

![image-20231002165515060](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310021655096.png)

## Parallel Computing

总计算量并没有减少，只是利用多线程让计算耗时降低.

### Linear Predictor(线性回归)

https://zhuanlan.zhihu.com/p/461763459

![image-20231002171424992](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310021714055.png)

通过指标和权重的加权平均求和来构成向量.在机器学习中，可以通过学习过往的数据来推断w的值.通过损失函数来不断逼近正确的w值.(最小二乘回归)

![image-20231002171649325](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310021716357.png)

为了最小化损失函数，可以求L(w)的梯度，这里对向量求得的梯度仍然是向量.

![image-20231002171951900](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310021719939.png)

通过梯度下降，找出梯度最大的地方，也就是目标函数值变化率最大的地方。为了找到目标函数的最小值，可以以梯度相反方向下降一个步长(或称学习率)α后再次重复过程，当α足够小时重复足够多次该过程，则最后会收敛到需要求的最小值处

如何通过并行运算减少计算时间呢？

举例：

![image-20231002172235946](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310021722986.png)

简单来说就是将计算总额按照处理单元数量等分，让每个单元计算对应部分后再求和，平摊CPU/GPU时间，提升效率。

### Communication

多处理器间的通信一般有两种方式：

内存共享，所有的处理核心公用内存，可以随意访问其他单元的运算结果，缺点是拓展性差。

![image-20231002172624324](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310021726377.png)

消息传递，节点之间将数据打包后互相发送，但任意节点之间不能直接看见消息.

![image-20231002172958528](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310021729561.png)

为了解决消息传递的痛点，可以让某一个节点作为服务器负责传递信息，其他节点进行计算. (Client-Server架构)

![image-20231002173056418](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310021730489.png)

也可以使用P2P点对点网络，所有的节点都参与计算，结点的邻居之间可以通信。

![image-20231002173143574](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310021731635.png)

### MapReduce

MapReduce是谷歌的闭源算法，有一些开源算法是基于MapReduce的理念进行设计的，例如Apache Hadoop/Spark.

MapReduce架构仍然基于Client-Server架构。Server可以进行广播，将一些信息传递给所有worker node。

![image-20231002174045054](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310021740090.png)

每个worker会使用自己拥有的数据进行计算.

![image-20231002174135543](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310021741618.png)

Worker计算后将数据回传给Server，server会接受之后进行数据处理。

![image-20231002174204874](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310021742944.png)

下面对这一算法进行详细解释：

1.Broadcast:Server广播参数w给worker；将数据样本划分后发送给worker node

![image-20231002174452125](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310021744175.png)

2.Map:Worker将参数在本地进行计算

![image-20231002174604393](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310021746449.png)

3.Reduce:worker将自己计算出的所有g进行平均后得出新的g并回传给server，server将所有向量求和.

4.求和之后的g，再一次进行梯度下降，得到新的w.

![image-20231002174941751](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310021749788.png)

总体流程图：

![image-20231002175422458](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310021754514.png)

理想情况下时间会是单线程的1/m，但是考虑到通信时间等因素，实际的提升会偏低，且随着线程数量增加降低的幅度也增加，一般使用Sppedup Ratio参数来刻画这一变化：

![image-20231002180015611](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310021800660.png)

通信的时间成本以Communication Cost计算，该指标分为两部分：

communication complexity:通信复杂度，即server和worker之间需要传递的信息量，一般与参数数量和worker节点数量成正比

latency:延迟（网络延迟）

**Communication time : Complexity/Bandwidth + Latency**



另一方面，计算时不同的worker可能使用的计算时间不一样，只有等待所有worker都计算完毕后才能让server进行reduce操作

![image-20231002182956868](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310021829927.png)

考虑到最糟的情况，如果某个节点计算失败并且重启，则要让其他所有节点等待其计算完成，这被称为straggler.

straggler说明了两点：第一，总时钟时间被最慢的节点所决定；第二，这时由同步操作所导致的问题.

### Parameter Server(异步梯度下降)

![image-20231002184516957](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310021845997.png)

与MapReduce不同，parameter server是异步的.**Ray**是一个很好的异步梯度下降的开源系统。由于不需要同步，在每一个参数计算结束之后，就可以把结果回传给server，大大提升了效率。

![image-20231002184921949](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310021849998.png)

server和worker的运行流程如下：

![image-20231002191248503](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310021912553.png)

没有了同步要求，显然效率更高，但是异步算法也有一点问题，当其中某个worker的计算速度非常慢，它可能会拿到还没有更新的参数，这可能导致计算结果的偏差：

![image-20231002191456485](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310021914532.png)

### Decentralized Network(P2P,去中心化网络)

特征：P2P网络（无中心服务器）；交流方式为信息传递；节点只与自己的邻居通信

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310031511238.png)

各个节点组成了一个图，当该图为完全图时，收敛速度最快；如果不是强连接图，则无法得到收敛的解。

### TensorFlow实现

```python
from tensorflow.python.client import device_lib
device_lib.list_local_device()
#查看所有可用的计算单元
from tensorflow import distribute
x = strategy.num_replicas_in_sync
strategy = distribute.MirroredStrategy(x)
#x可以指定进行运算的单元，留空默认使用所有计算单元

import tensorflow as tf
def scale(image,label):
    image=tf.cast(image,tf.float32)
    image /= 255
    return image,label
#图像预处理函数
import tensorflow_datasets as tfds
datasets,info = tfds.load(name='mnist',with_info=True,as_supervised=True)
#建立数据集
mnist_train = datasets['train'].map(scale).cache()
mnist_test = datasets['test'].map(scale)

#定义buffer和batch
BUFFER_SIZE = 10000
BATCH_SIZE_PER_REPLICA = 128
BATCH_SIZE = BATCH_SIZE_PER_REPLICA * x

data_train = mnist_train.shuffle(BUFFER_SIZE).batch(BATCH_SIZE)
data_test = mnist_test.batch(BATCH_SIZE)

#建立卷积网络
from tensorflow import keras
from tensorflow.keras.layers import Conv2D,MaxPooling2D,Flatten,Dense
with strategy.scope():
    model = keras.Sequential()
    model.add(Conv2D(32,3,activation='relu',input_shape=(28,28,1)))
    model.add(MaxPooling2D())
    model.add(Conv2D(64,3,activation='relu'))
    model.add(MaxPooling2D())
    model.add(Flatten())
    model.add(Dense(64,activation='relu'))
    model.add(Dense(10,activation='softmax'))
#使用并行计算并打包模型
with strategy.scope():
    model.compile(loss='sparse_categorical_crossentropy',optimizer=keras.optimizers.RMSprop(learning_rate=1E-3),metrics=['accuracy'])

    
#训练
model.fit(data_train,epochs=10)
#计算loss,acc
eval_loss,eval_acc = model.evaluate(data_test)

```

### All-reduce

并行计算同样也可以运用于上文提到的Reduce环节，相比于一般reduce只有server得到最终计算的结果，all-reduce会让所有节点都得到这一结果。

在server-client模式中，一般通过server广播来达到这一目的：

![image-20231003162638326](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310031626415.png)

在去中心化网络里，可以通过让所有节点都进行all-to-all通信让worker进行计算：

![image-20231003162707558](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310031627654.png)

除此之外，还可以通过环形方式，即Ring All-reduce.

先通过环形通信让最后一个计算单元得到G的值，再进行一次循环，让g传播到其他所有计算单元.

![image-20231003163234517](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310031632572.png)

但由于这种方式在每个时间点只有一个信道是繁忙的，效率很低。可以通过将信息均分并分组传输的方式提升效率：

![image-20231003164124749](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310031641806.png)

https://blog.csdn.net/SwankyOrc/article/details/83862554

# 二.联邦学习

## 概述

联邦学习本质上就是分布式学习.与普通分布式学习相比，联邦学习有以下特点：

1.用户对他们的设备和数据有控制权

2.用户节点可以随时退出

3.通信开销比计算开销要高

4.节点数据并非独立同分布，即每个节点的数据概率分布并不同

5.数据量并不平衡，算法本身也不能以节点的数据量设置优先级

### Federated Averaging Algorithm

与并行计算的梯度下降不同，这里的梯度下降为满足以上的特性，将在本地进行梯度下降：

![image-20231005141922193](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310051419353.png)

通过这种重复，w就不会再和server端发送的w时刻保持一样.

![image-20231005142131119](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310051421202.png)

这里的FedAvg是同步的，FedAvg也有异步的版本：

https://zhuanlan.zhihu.com/p/539381658

### 分类

联邦学习按照数据分布的不同情况可大致分为三类：

横向联邦学习，纵向联邦学习，迁移联邦学习.

![image-20231005152605823](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310051526854.png)

**横向联邦学习**

在不同数据集之间数据特征重叠较多而多用户重叠较少的情况下，按照用户维度对数据集进行切分，并利用双方数据特征相同但对应用户不完全相同的数据部分进行训练。

从实现步骤来看，主要分为五步：

1)每个参与方从中央聚合器下载最新模型，然后在本地计算模型参数（又称“梯度”）；
2)每个参与方将经过加密的梯度信息发送到位于聚合器；
3)聚合器进行聚合操作（“安全聚合”，常见算法为FedAvg），更新模型；
4)聚合器把综合更新后的模型分发到各个参与方；
5)参与方更新自己的模型。
6)经过若干次迭代，直到模型精确度（“损失函数”）达到设定值，模型就大功告成。

（这里的FedAvg也就是之前提到的，联邦学习与分布式学习最大的区别）

![img](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310051528697.webp)

因为聚合器有可能被攻击（“DLG攻击”），所以客户端发给聚合器的梯度信息需要用同态加密或者差分隐私（例如加一个随机数）进行加密。

**纵向联邦学习**

在用户重叠较多而数据特征重叠较少的情况下（与横向相反），按照数据特征维度对数据集尽享切分，并去除双方针对相同用户而数据特征不完全相同的部分数据进行训练。

纵向联邦学习主要分为两步：

1) 对齐不同参与方的相同用户样本（“加密实体对齐”）；

2）对这些样本进行加密的模型训练（即下图中右侧部分）。

![img](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310051558889.webp)

训练步骤为：
1）协调方负责分发公钥，私钥不发，这样只有C可以解密（关于公私钥机制，以后也会单独写篇文章）。

2) A、B将对齐后的样本进行同态加密和交互，分别计算各自的梯度和损失值。

3）A、B算好以后，发给C（这时会加上掩码或者噪声，避免C泄密）。
4）C进行解密，再发回给A、B。A、B解除掩码，更新自己的模型。

通过非对称加密的信息传输，可以有效实现加密实体对齐的目的。

**迁移联邦学习**

在用户和特征数据重叠都很少的情况下，不对数据进行切分而利用迁移学习客服数据或标签不足的情况。

![img](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310051605261.webp)

### 联邦学习中的安全问题

在Server-Worker模式中，数据在传递过程中并非百分百安全，在之前提到的各类梯度在二者中传递的时候，由于梯度本质上只是对参数的变换，所以可以通过反演泄露数据.目前业界主要通过对梯度和参数添加噪声来抵御这一威胁。

除了模型反演之外，拜占庭错误和鲁棒性也是需要考虑的问题。拜占庭问题简单来说是，在多节点计算中，如果有一个节点出现故障但没有退出系统，则系统会因为该节点向其他节点发送的错误信息而崩溃。

**Data Poisoning:**即数据投毒，会让模型因为学习了错误的数据而出错

**Model Poisoning:**通过把错误的标签和正确的图片组合让梯度下降走向错误的方向

**对抗攻击：**对抗攻击是指 恶意构造输入样本导致模型以高置信度输出错误结果 。 这种通过在原始样本中添加扰动而产生的输人样本称为对抗样本.

## Practical Secure Aggregation for Privacy-Preserving Machine Learning

### 概述

对大量敏感信息的需求导致了现存的机器学习方案会对用户的隐私信息安全造成威胁，且这一趋势随着移动便携设备的普及不断加深.

联邦学习协议具有恒定的轮数、低通信开销、对故障的鲁棒性，并且只需要一个信任有限的服务器。服务器只按照MPC(安全多方计算)协议的标准要求，使用基于模拟的证明来学习用户的聚合输入。这有效解决了以前机器学习带来的隐私问题

**传统机器学习的局限性**

不同于传统机器学习，由于联邦学习的用户数据集状态不可预测，系统必须对退出的用户具有健壮性

移动设备无法和其他设备直接通信

大量数据的更新导致了很高的网络通信成本

移动设备与其数据集的身份认证难度大

![image-20231009165006585](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310091650754.png)

![image-20231009165035717](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310091650789.png)

### 数学前置知识

**Shamir Secret Sharing(门限秘密分享)**

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310091722894.png)

**Key Agreement(私钥分享)**

![image-20231009172244104](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310091722175.png)

![image-20231009172256263](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310091722367.png)

**Others![image-20231009172538132](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310091725215.png)**

![image-20231009172519866](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310091725920.png)

### 具体实现

![image-20231009172822402](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310091728477.png)

![image-20231009172841314](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310091728388.png)

改进：可以基于Diffle-Hellman算法的秘密共享将SEED共享给其他所有用户，这样可以解决中途用户退出的问题，并且对应用户的数据可以通过传递的SEED复原.但考虑另一种情况，某个节点因为计算速度过慢而被服务器认为已经掉线，这时服务器要求其他所有节点复原该结点的SEED与对应的数据进行后续处理。如果处理完之后该节点才发送数据，服务器为了重新接收只能把所有的掩码从提交的数据中剔除，原始数据暴露会导致安全问题。

![image-20231009172858710](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310091728774.png)

![image-20231009173003501](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310091730581.png)

使用了双掩码进行改进的协议是一个同步协议。服务器没有输入，但可以通过安全（私有和经过身份验证的）通道与用户通信。在任何时候，用户都可以退出协议（在这种情况下，他们完全停止发送消息），服务器将能够产生正确的输出，只要保证t个用户可以（这里的t取决于门限消息发送时定义的t）存活到最后一轮。当服务器经过了预设的一段时间仍然没有接收到足够的数据，或存活的用户数量少于门限定义的t时，服务器就会自动中止这一次的通信。总的来说，协议通过一个安全参数k将整个过程进行参数化，这个k可以调整以限制攻击者的成功概率。在该协议中，首先假设客户端的数量n在安全参数中是多项式有界的。（使系统满足2ODH假设成立的条件）理想状态下，因为参与协议的各方都遵守规则，因此对客户端的安全签名验证是不必要的，但是需要PKI使得各个验证和公私钥交换合法。

![image-20231009173318318](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310091733413.png)

每个用户使用Key.gen()生成密钥，用SIG.sign()对密钥对进行签名.每个用户都将生成的公钥发送给服务器，服务器对收到的数据数量进行检验（必须大于t）.每个用户都会收到由服务器发送的，其他所有用户公开的公钥。其中Cu用来加密传输的数据，Su加密模型参数.在用户收到公钥列表后，会进行签名验证并将其分为t份(SS.share())，即利用门限分享保证安全性后回传给服务器.

![image-20231009173328753](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310091733902.png)

从服务端收到其他用户发送给自己的秘密份额后，根据收到的用户列表，计算自掩码和互掩码的PRG，每个参数都需要哈希一次，保证每个位置上的参数加密的数都是随机的。计算完成后将加密后的参数上传给服务器。

![image-20231009173356885](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310091733010.png)

服务器将收到的模型参数进行聚合并将掉线用户列表发送给所有用户，用户通过该列表上传掉线用户的秘密份额和在线用户的秘密份额，服务器收到至少t个用户上传的份额后恢复对应参数，得到聚合结果。

**Demo**:https://github.com/IssacL04/SecureAggregationDemo

### Security

![image-20231009174615886](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310091746973.png)

![image-20231009174626983](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310091746068.png)

![image-20231009174640464](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310091746573.png)

![image-20231009174654788](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202310091746882.png)

# Demo

***Honest server & malicious clients**

Server通过标准数据集（小范围收集并确认安全性），通过计算余弦距离判定是否可信，小于等于0舍弃

**Question**

1.向量收敛方向错误

2.Dishonest Server

3.Aggregation authentication failure

