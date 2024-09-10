# Interview Questions - Transformer

### 为什么Query和Key的点乘需要除以$\sqrt{d_k}$？（$\sqrt{d_k}$是key向量的维数）(2024.9.7)

在softmax函数中，较大的输入值会导致更加尖锐的概率分布，而较小的值会导致更加平坦的分布。如果没有缩放，当$\sqrt{d_k}$的值很大时，点积的结果也会很大，这可能导致`softmax`函数的梯度非常小（接近于0），从而使得模型难以学习

一句话概括：`softmax`参数值特别大可能会梯度消失，使梯度大小更加稳定