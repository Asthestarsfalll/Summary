# **Digital Signal Processing**（DSP）

## Chapter 1 DSP的深度和广度

### DSP的应用

1. Space 
   - Space photograph enhancement(图像增强)
   - Data compression(数据压缩)
   - Intelligent sensory analysis by  remote space probes(遥感空间探测智能传感分析)
2. Medical
   - Diagnostic imaging(诊断成像：CT，MRL，超声等)
   - Electrocardiogram analysis(心电图分析)
   - Medical image storage/retrieval(医学图像存储/检索)
3. Commercial
   - Video conference calling(视频电话会议)
   - Image and sound compression for multimedia presentation
   - Movie special effects
4. Telephone
   - Voice and data  compression
   - Echo reduction
   - Signal mutiplexing(信号多路复用)
   - Filtering
5. Military
   - Radar(雷达)
   - Sonar(声呐)
   - Ordnance guidance(军械指导)
   - Secure communication(安全通信)
6. Industrial
   - Oil and mineral prospecting(石油矿产探勘)
   - Process monitoring & control(过程监控)
   - Nondestructive testing(无损检测)
   - CAD and design tools(CDA和设计工具)
7. Scientific
   - Earthquake recording & analysis
   - Data acquisition (数据采集)
   - Spectral analysis(光谱分析)
   - Simulation and modeling(模拟与建模)

### 领域重叠

- 通信理论

- 数值分析

- 概率和统计

- 模拟信号处理

- 决策理论

- 数字电子科技

- 模拟电子

  ***数字信号处理是面向电子信息学科的专业基础只是，也是多门新型学科的基础理论。在众多重要的科学和工程技术领域，信号处理都发挥着巨大的作用。可以说，数字信号处理是我们通过计算机洞察世界的基础工具。***

## Chapter 2 统计，概率和噪声

> 统计和概率在数字信号处理中被用于表征信号及其生成过程。
>
> 其允许对破坏性特征进行测量和分类，这是开发消除有害组件策略的第一步。

### 信号与图

> 信号是一个参数如何与另一个参数相关联的描述

- 声音可以被理解为一维空间内随时间变化的信号。
- 图像可以被理解为二维空间内变化的信号。
- 视频可以被理解为二维空间内随时间变化的信号。

### 域

横轴上的参数类型是信号的域

- 横轴上的参数类型是信号的域
- 例如，一个以时间为自变量(即横轴上的参数)的信号被称为在时域内。DSP中另一个常见的信号用频率作为自变量，从而产生了“频域”这个术语。同样，使用距离作为独立参数的信号被称为空间域(距离是空间的度量)。

### 均值和标准差

> 在某些情况下，平均值描述被测量的内容，而标准偏差代表噪音和其他干扰。
>
> 在这些情况下，标准差本身并不重要，而只是在与均值比较时才重要。这就产生了一个术语:信噪比(SNR)，它等于均值除以标准偏差。另一个术语也被使用，变异系数(CV)。也就是标准差除以均值，再乘以100%例如，CV为2%的信号(或其他测量值组)的信噪比为50。更好的数据意味着更高的信噪比值和更低的CV值

信噪比：即信息与噪声的比值。

标准差：计算过程中，若我们用样本均值代替总体均值，自由度要损失1，因此除以（n-1）；若总体均值已知，则除以n。

使用运行统计计算标准差：

- 当均值比标准差大的多的时候，会有更大的误差，将在第四章详细讨论；
- 在获取新样本并将其添加到信号中时，通常需要重新计算均值和标准偏差。我们将这种类型的计算称为:运行统计。

### 信号和潜在过程



### 直方图，概率密度函数，概率质量函数和累积分布函数

PDF：概率密度函数，**连续型随机变量**的概率密度函数是一个描述这个随机变量的输出值，在某个确定的取值点附近的可能性的函数。

若X为连续型随机变   量，定义概率密度函数为
$$
f_X(x)
$$
则有
$$
Pr(a\leq x\leq b)=\int_{a}^{b}f_X(x)dx
$$
PMF：概率质量函数，**离散随机变量**在各特定取值上的概率。
$$
f_X(x)=Pr(X=x)
$$
CDF：累积分布函数，**概率密度函数的积分**，能完整描述一个实随机变量X的概率分布。

对于连续型随机变量有：
$$
F_X(x)=Pr(X\leq x)=\int_{-\infty}^{x}f_X(t)dt
$$
对于离散型随机变量，为分段函数。

到此为止，学概率论去了。