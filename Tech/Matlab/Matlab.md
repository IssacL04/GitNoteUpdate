### 前置：

#### 文献查找：

1.谷歌学术

https://scholar.google.com.hk/?hl=zh-CN

http://scholar.hedasudi.com (国内镜像)

2.Open Access Library

https://www.oalib.com

3.Z-library

https://zh.singlelogin.me/

4.国家统计局

https://data.stats.gov.cn

5.awesome-public-datasets

https://github.com/awesomedata/awesome-public-datasets

6.ESPDATA(七天免费)

https://www.espnet.com.cn

#### 数据处理：

##### 均值/众数插补：

适用于缺失数据数量较少，对个体数据精度要求不高的的情况。

定量数据：使用平均值；

定性数据：使用众数

##### Newton插值：

构造近似函数以补全缺失值；不适合对导数和变化率有要求的数据。

https://blog.csdn.net/weixin_45102840/article/details/105889606

https://zhuanlan.zhihu.com/p/337274059

##### 样条插值：

用分段光滑的曲线插值（曲线连续，曲率也连续），适用于对数据精度要求高且不允许突变的数据。

##### 正态分布判断异常值：

数据分布在(μ-3σ，μ+3σ)以外的均为异常值

#### 论文的大体结构：

##### 摘要：

包含研究的问题，使用的方法，得到的结果，大致步骤

不要加图和表格，字数控制在800-1100字。

摘要的结构大概为：开头-问题一概述-问题二概述-问题三概述-总结-关键词

关键词一般包含：使用的模型和算法/专业名词/问题关键词（**使用空格分开**）

##### 问题重述：

简述研究的问题

##### 模型假设，符号说明

##### 建立模型

##### 求解模型

*如果一道题有多个小问，则需要每个小问都建立一个模型/特殊情况：如果各小问只有约束条件不同，则可以对单一模型进行不同优化改进。

##### 模型的优缺点和改进方法

##### 参考文献（引用）：

使用GB/T引用格式。

##### 附录：

附录里要出现正文中使用的求解代码。

#### 论文排版

1.标题不超过三级

2.中文宋体，英文Times New Roman

3.正文排版

4.表使用三线表格式 

5.注意分段

#### 数学公式编辑器

1.Word自带（可使用Latex语法）

2.Mathpix 截图生成公式

3.AxMath 支持快捷键+latex+按钮输入

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151154461.png)

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151154462.png)

#### 三线表

1.三条线指：标题第一行的上下各一行，最后一行的下端设置横线。

2.表的标题写在表的正上方居中，带序号。

3.写表的标题时可采用插入题注，表的序号会自动更新

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151154463.png)

#### 作图软件

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151154464.png)

### Matlab

#### 字符（串）变量

使用双引号包含，当字符串本身含双引号时则需要使用双重双引号。

字符串如果包含算式，算式会原样输出而不计算结果。

字符串拼接：b = a + "123"

存储多个字符串变量：构造矩阵：A = ["a","b";"c","d"]

字符变量使用单引号包含，本质上是一个二维数组，包含多个变量：

A = '12345' 本质上是五个char类型变量。

取某一位：B =  A(i)

字符变量拼接:B = [A + 'zzzzz']

#### 矩阵

```matlab
a = [1 2 3 4 5] %创建矩阵
b = a + 3 %对矩阵中每个元素加3，等同于：[1 2 3 4 5] + [3 3 3 3 3]
plot(a) %作图函数
grid on %添加网格 
a = [1 2; 2 3 ;3 4] %多维矩阵
b = a' %求转置
[D,V] = eig(a) %特征值和特征向量
E = inv(a) %逆矩阵
A*B %矩阵乘法
A.*B %对应元素点乘

求解方程：A*x = B
则x = A\B
```

##### 矩阵的特征值和特征向量

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151154465.png)

https://zhuanlan.zhihu.com/p/165382601?utm_source=wechat_session

##### Matlab对于某些不合规范的矩阵运算方式的处理

![](https://cdn.jsdelivr.net/gh/IssacL04/IHS@img/img/202404151154466.png)