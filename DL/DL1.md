# Interview Questions-Deep Learning



### 梯度消失和梯度爆炸(2024.9.2)

参考：

[深度学习之3——梯度爆炸与梯度消失 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/68579467)

[深度学习中梯度消失和梯度爆炸的根本原因及其缓解方法_什么是梯度消失-CSDN博客](https://blog.csdn.net/weixin_46470894/article/details/107145207)

[详解机器学习中的梯度消失、爆炸原因及其解决方法_梯度消失的原因及解决方法-CSDN博客](https://blog.csdn.net/qq_25737169/article/details/78847691)

#### 概念

对于深层神经网络$F(x) = f_n(...f_3(f_2(f_1(x)*w+b)*w+b)*w+b)$

$Loss = || g(x) - f(x) ||^2$

$\Delta{w_2}=\frac{\delta{f_n}}{\delta{f_{n-1}}}·\frac{\delta{f_{n-1}}}{\delta{f_{n-2}}}\dots\frac{\delta{f_2}}{\delta{w_2}}$

这里前n-2个项都可看作对激活函数求导，很多激活函数值域在(0,1)，当层数过多时多个导数相乘会趋于0，梯度消失；当激活函数梯度值全在1以上时会梯度爆炸

#### 梯度爆炸的问题

+ 值超出值域(infinity)

对于16位浮点数尤为严重（



+ 对学习率敏感



#### 解决方案

1. 梯度剪切

设置一个梯度剪切阈值；更新梯度时，超过或低于某范围，强制更新到该范围内



2. 权重正则化

$ Loss = (y-W^Tx)^2 +\alpha||W||^2 $



3. 采用其他激活函数

**Relu：**正数部分导数为1，连乘不会趋于0或无穷

**缺点：**

- 由于负数部分恒为0，会导致一些神经元无法激活（可通过设置小学习率部分解决）
- 输出不是以0为中心的



**LeakRelu:**
`LeakRelu`就是为了解决Relu的0区间带来的影响，其数学表达为：

$LeakRelu = max(k∗x, x) $

其中k是leak系数，一般选择0.01或者0.02，或者通过学习而来。



**elu:**

$elu(x) = x,if(x>0);else:f(x)= \alpha(e^x-1) $



# ##### 4，5，6待施工#####

4. **Batchnorm**









5. **残差网络**





6. **LSTM — 长短期记忆网络**





### L1正则化和L2正则化（2024.9.3）

**参考：**

[L1正则化与L2正则化的区别_l1正则化和l2正则化的区别是-CSDN博客](https://blog.csdn.net/ybdesire/article/details/84946128)

**L1正则化：**

损失函数增加所有权重绝对值之和(**$|w_1|+|w_2|+\dots+|w_n|$**)，也即L1范数



L1正则化对所有参数的惩罚力度一样（求导均为1），可以让一部分权重变为0，产生稀疏模型，能够使某些特征的权重降为0

**L2正则化（岭回归）：**

损失函数增加所有权重平方和的平方根(**$\sqrt{w_1^2+w_2^2+\dots+w_n^2}$**)，也即L2范数

L2正则化减少了权重的固定比例（求导为$w_i$的正比），使权重平滑。L2正则化不会使权重变为0（不会产生稀疏模型），所以选择了更多的特征。

#### 区别

- L1减少的是一个常量，L2减少的是权重的固定比例
- L1使权重稀疏，L2使权重平滑
- L1优点是能够获得sparse模型，对于large-scale的问题来说这一点很重要，因为可以减少存储空间
- L2优点是实现简单，能够起到正则化的作用。缺点就是L1的优点：无法获得sparse模型