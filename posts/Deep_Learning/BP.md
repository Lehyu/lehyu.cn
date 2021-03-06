## 基础

### 神经网络的表示

在[基于梯度的优化方法](http://blog.csdn.net/Lehyu/article/details/52225692)中, 我们提到一个具有 $nl+1$ 层的神经网络可以一般表示为 $y'=f(W,b,X)= f_{nl}(W^{nl},b^{nl},f_{nl-1}(W^{nl-1},b^{nl-1},...f_{1}(W^1,b^1,X)...))$，神经网络的训练就是调整 $W,b$ 使得 $L(y, f(W,X,b))$ 最小。
需要注意的是，当神经网络从第0层，即输入层为第0层与输入层为第1层开始的表述可能由稍许不同。本博文以输入层为第1层，输入层与下一层之间的权值与偏置值记为 $W^1,b^1$ 。

### 链式法则

链式法则（[chain rule](https://zh.wikipedia.org/wiki/%E9%93%BE%E5%BC%8F%E6%B3%95%E5%88%99)）是求复合函数导数的一个法则。假设$f,g$为两个关于x的可导函数，则复合函数 $y=f(g(x))$ 的导数为

$$\begin{equation}
\begin{array}{rcl}
\frac{dy}{dx} &=& \frac{df}{dg}\frac{dg}{dx}=f'(g(x))g'(x)
\end{array}
\end{equation}$$

### 求解 $L(y, f(W,X,b))$ 的最优值

首先损失函数 $L$ 是关于 $W,b$ 的函数，由基于梯度的优化方法，我们可以知道可以根据梯度来0更新搜索最优值。而 $L$ 是一个复合函数，因此我们可以采用链式法则来迭代调整每一层网络的权值与偏置值。假如我们采用梯度下降的方法来调整 $nl$ 层，则有：

$$\begin{equation}
\begin{array}{rcl}
\frac{\partial L}{\partial W^{nl}} &=& \frac{\partial L}{\partial f_{nl}}\frac{\partial f_{nl}}{\partial W^{nl}} \\
W_{i+1}^{nl} &=& W_{i}^{nl}-\epsilon \frac{\partial L}{\partial W^{nl}}
\end{array}
\end{equation}$$

如此我们可以迭代更新每次的 $W_i,b_i$ 。

## 神经网络的前馈算法

我们知道在神经网络中上一层的输出时下一层的输入，假设第 $j$ 层输出为 $a^{j}$ , 则第 $j+1$ 层的输出为 $a^{j+1} = f_{j}(W^{j}a^{j}+b^{j})$，其中 $f_{j}$ 为激活函数，当我们令 $z^{j+1}=W^{j}a^{j}+b^{j}$ 时，我们就可以得到如下算法：

1. 令 $j=1$ ，$a^{j}=a^{1} = X$
2. 计算 $z^{j+1} = W^{j}a^{j}+b^{j}$
3. 计算 $a^{j+1} = f_{j}(z^{j+1})$
4. 重复步骤2-3，直到输出层 $y' = a^{nl+1}=f_{nl}(z^{nl+1})=f_{nl}(W^{nl}a^{nl}+b^{nl})$


## BP算法

### 第$nl$ 层网络的更新

我们知道 $nl+1$ 层的输出为 $y' =a^{nl+1} = f_{nl}(z^{nl+1}), z^{nl+1} = W^{nl}a^{nl}+b^{nl}$，则损失函数为 $L(y,f_{nl}(W^{nl}a^{nl}+b^{nl}))$。则
对 $W^{nl}$ 更新

$$\begin{equation}
\begin{array}{rcl}
\frac{\partial L}{\partial W^{nl}} &=& \frac{\partial L}{\partial f_{nl}}\frac{\partial f_{nl}}{\partial W^{nl}}=\frac{\partial L}{\partial f_{nl}}a^{nl} \\
W_{i+1}^{nl} &=& W_{i}^{nl}-\epsilon \frac{\partial L}{\partial W^{nl}}
\end{array}
\end{equation}$$

对 $b^{nl}$ 更新

$$\begin{equation}
\begin{array}{rcl}
\frac{\partial L}{\partial b^{nl}} &=& \frac{\partial L}{\partial f_{nl}}\frac{\partial f_{nl}}{\partial b^{nl}}=\frac{\partial L}{\partial f_{nl}} \\
b_{i+1}^{nl} &=& b_{i}^{nl}-\epsilon \frac{\partial L}{\partial W^{nl}}
\end{array}
\end{equation}$$

### 第 $nl-1$ 层网络的更新

此时 $L=L(y,f_{nl}(W^{nl}f_{nl-1}(W^{nl-1}a^{nl-1}+b^{nl-1})+b^{nl}))$，则对 $nl-1$ 层更新为：
对 $W^{nl-1}$ 更新

$$\begin{equation}
\begin{array}{rcl}
\frac{\partial L}{\partial W^{nl-1}} &=& \frac{\partial L}{\partial f_{nl}} \frac{\partial f_{nl}}{\partial f_{nl-1}} \frac{\partial f_{nl-1}}{\partial W^{nl-1}}=\frac{\partial L}{\partial f_{nl}} W^{nl} a^{nl-1} \\
W_{i+1}^{nl} &=& W_{i}^{nl}-\epsilon \frac{\partial L}{\partial W^{nl-1}}
\end{array}
\end{equation}$$

对 $b^{nl-1}$ 更新

$$\begin{equation}
\begin{array}{rcl}
\frac{\partial L}{\partial b^{nl-1}} &=& \frac{\partial L}{\partial f_{nl}} \frac{\partial f_{nl}}{\partial f_{nl-1}} \frac{\partial f_{nl-1}}{\partial b^{nl-1}}=\frac{\partial L}{\partial f_{nl}} W^{nl} \\
b_{i+1}^{nl} &=& b_{i}^{nl}-\epsilon \frac{\partial L}{\partial b^{nl-1}}
\end{array}
\end{equation}$$

### 综合

明显第 $j(j\neq nl)$ 层来说，更新与第 $n-1$ 层类似。观察上述等式，我们知道每层对权值的导数十分相似，都是一个项乘以上一层的输出，即当前层的输入，我们将该项称为“error term”  $\delta^{j}$。

则第 $nl$ 层:

$$\begin{equation}
\begin{array}{rcl}
\delta^{nl} &=& \frac{\partial L}{\partial f_{nl}} \\
W_{i+1}^{nl} &=& W_{i}^{nl}-\epsilon \delta^{nl} a^{nl} \\
b_{i+1}^{nl} &=& b_{i}^{nl}-\epsilon \delta^{nl}
\end{array}
\end{equation}$$

第 $j(j\neq nl)$ 层：

$$\begin{equation}
\begin{array}{rcl}
\delta^{j} &=& \delta^{j+1} W^{j+1} \\
W_{i+1}^{j} &=& W_{i}^{j}-\epsilon \delta^{j} a^{j} \\
b_{i+1}^{j} &=& b_{i}^{j}-\epsilon \delta^{j}
\end{array}
\end{equation}$$

### 伪代码

1. 计算第 $nl$ 层的 error term： $\delta^{nl} = \frac{\partial L}{\partial f_{nl}}$
2. 计算第 $j(j = nl-1,nl-2,\dots,1)$ 层 error term： $\delta^{j} = \delta^{j+1} W^{j+1}$
3. 更新第 $j+1$ 层的权值与偏置值：
$W_{i+1}^{j+1}=W_{i}^{j+1}-\epsilon \delta^{j+1} a^{j+1}$
$b_{i+1}^{j+1}=b_{i}^{j+1}-\epsilon \delta^{j+1}$
4. 重复步骤2-3,知道 $j=1，同理更新第1层权值与偏置值
