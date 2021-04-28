# Octave  Convolution

## 摘要

- 自然图像中，信息主要以不同的频率传递，**高频通常使用细节编码，低频通常使用全局结构编码**，而卷积的特征输出也可看做是不同频率信息的混合。

  ![image-20210428125639823](images\image-20210428125639823.png)

- OctConv可以减少通道的冗余和降低计算量，并且提高一定的性能。

## 介绍

- OctConv将特征图输出分为高频和低频，分别对这两个部分进行处理，在降低计算量的同时保持参数与普通卷积相同；

- 其中，低频被储存在低分辨率张量（大小为原图的四分之一）中，以减少空间冗余，同时能够扩大感受野（相较于原图大小）；

  ![image-20210428125705098](images\image-20210428125705098.png)

- 设计了高低频之间的信息交换方式。

## 基本结构和实现细节

![image-20210428125731159](images\image-20210428125731159.png)

### 超参数：

$\alpha_{in}$和$\alpha_{out}$分别表示输入图片与输出图片中低频部分所占通道的比例

在第一层OctConv中，需要将$\alpha_{in}$设置为0，$\alpha_{out}$设置为$\alpha$

在最后一层OctConv中，需要将$\alpha_{in}$设置为$\alpha$，$\alpha_{out}$设置为0

在中间层的OctConv中，$\alpha_{in}=\alpha_{out}=\alpha$

### 高低频之间的信息交换

- 高频到低频：将高频特征下采样两倍，卷积得到$Y^{H\rightarrow L}$
- 低频到高频：将低频特征上采样两倍，卷积得到$Y^{L\rightarrow H}$

### 参数分配

![image-20210428131945132](images\image-20210428131945132.png)

-    $W^{H\rightarrow H}$参数量为$(1-\alpha_{in})(1-\alpha_{out})K^2C_{in}C_{out}$
-    $W^{H\rightarrow L}$参数量为$(1-\alpha_{in})\alpha_{out}K^2C_{in}C_{out}$
-    $W^{L\rightarrow L}$参数量为$\alpha_{in}(1-\alpha_{out})K^2C_{in}C_{out}$
-    $W^{L\rightarrow H}$参数量为$\alpha_{in}\alpha_{out}K^2C_{in}C_{out}$

其总和即为$K^2C_{in}C_{out}$，与普通卷积相同。

### 其他

- Octave Convolution对分组卷积，深度可分卷积等同样适用。

- ![image-20210428134026635](images\image-20210428134026635.png)

  可以看到随着$\alpha$的增加，计算量不断减小，但是$\alpha$过大会导致精度的下降。

## 复现代码

```python
class Frist_octaveconv(nn.Module):
    def __init__(self, in_ch=3, out_ch=3, ker_size=3, alpha=0.5, padding=0, stride=1, group=1, dilation=1, bias=True):
        super(Frist_octaveconv, self).__init__()
        # self.alpha = alpha

        self.l_out = int(alpha*out_ch)
        self.h_out = out_ch-self.l_out

        self.downsample2x = nn.AvgPool2d(
            kernel_size=2, stride=2, ceil_mode=True)
        # self.gauss = Guass_filter(2)
        self.hh = nn.Conv2d(in_ch, self.h_out, ker_size,
                            stride, padding, dilation)
        self.hl = nn.Conv2d(in_ch, self.l_out, ker_size,
                            stride, padding, dilation)

    def forward(self, x):
        xh = x
        yh = self.hh(xh)
        yl = self.hl(self.downsample2x(xh))

        return (yh, yl)


class Last_octaveconv(nn.Module):
    def __init__(self, in_ch=3, out_ch=3, ker_size=3, stride=1, alpha_in=0.25, padding=0, group=1, dilation=1, bias=True):
        super(Last_octaveconv, self).__init__()
        self.alpha = alpha_in
        self.l_in = int(self.alpha*in_ch)

        self.h_in = in_ch-self.l_in
        self.hh = nn.Conv2d(self.h_in, out_ch, ker_size,
                            stride, padding, dilation)
        self.lh = nn.Conv2d(self.l_in, out_ch, ker_size,
                            stride, padding, dilation)

    def forward(self, x):
        xh, xl = x
        yh = self.hh(xh)
        t = self.lh(xl)
        yl = F.interpolate(t, size=xh.shape[2:], mode='nearest')

        return yh+yl


class Octave_conv(nn.Module):
    def __init__(self, in_ch=3, out_ch=3, ker_size=3, padding=0, alpha_in=0.25, alpha_out=0.25, stride=1, group=1, dilation=1, bias=True):
        super(Octave_conv, self).__init__()
        self.alpha = alpha_in

        self.downsample2x = nn.AvgPool2d(
            kernel_size=2, stride=2, ceil_mode=True)
        self.l_in = int(self.alpha*in_ch)
        self.h_in = in_ch-self.l_in

        self.l_out = int(self.alpha*out_ch)
        self.h_out = out_ch-self.l_out
        # self.l=dilation  if dilation==1 else int(dilation/2)
        # self.dilation = dilation
        # self.l_dilation = dilation if dilation == 1 else dilation/2

        # self.gauss = Guass_filter(0.5, 2)
        # self.dilation = padding
        # if odd:
        #     self.padding = self.padding+1

        self.hh = nn.Conv2d(self.h_in, self.h_out, ker_size,
                            stride, padding,  dilation, group, bias)

        self.hl = nn.Conv2d(self.h_in, self.l_out, ker_size,
                            stride, padding,  dilation, group, bias)
        self.ll = nn.Conv2d(self.l_in, self.l_out, ker_size,
                            stride, padding,  dilation, group, bias)
        self.lh = nn.Conv2d(self.l_in, self.h_out, ker_size,
                            stride, padding,  dilation, group, bias)

    def forward(self, x):
        xh, xl = x
        # xh, xl = self.gauss(x)

        xhh = self.hh(xh)
        t = self.downsample2x(xh)
        xhl = self.hl(t)

        xll = self.ll(xl)
        xlh = self.lh(xl)
        xlh = F.interpolate(xlh, size=xhh.shape[2:], mode='nearest')
        yh = xhh+xlh
        yl = xll+xhl
        return (yh, yl)

```

除了卷积需要进行改动之外，各种池化和上采样等都需要进行改动，以适应分组的特征图。