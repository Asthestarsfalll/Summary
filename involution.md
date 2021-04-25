# Involution（invert convolution）:

## Convolution:

1. 空间不变性：参数共享，平移等变；不能灵活改变参数，卷积核尺寸不能过大，只能通过堆叠来扩大感受野。
2. 通道特异性：在通道上进行编码；有冗余。

## Involution：

- 与convolution不同，involution拥有**完全相反**的性质：

  通道不变性和空间特异性。

### 通道不变性：

1. 将c个通道分为g个组，每个组内共享一个kernel
2. 

 