# RNN

## 参考资料

- [RNN梯度消失和爆炸的原因](https://zhuanlan.zhihu.com/p/28687529)
- [机器学习 —— 基础整理（八）循环神经网络的BPTT算法步骤整理；梯度消失与梯度爆炸](https://www.cnblogs.com/Determined22/p/6562555.html)
- [LSTM如何解决梯度消失问题](https://zhuanlan.zhihu.com/p/28749444)

## 知识点总结

1. RNN BPTT算法求导, 着重关注参考2.
2. 梯度消失问题疑惑，参考资料不对的地方
   - 在对隐藏层参数求导中，提到的RNN的梯度消失问题不是指梯度结果为0，是指t1时刻对梯度的影响为0而已。形象化理解为，t1时刻的输入对t时刻没有影响。
   - 隐藏层参数反向求导，T时刻对结果影响最大
   - 着重观众参考1中的问答部分。
3. 因RNN所谓的“梯度爆炸”，导致LSTM的引入
4. 初始化参数的影响很容易造成梯度爆炸，最好初始化为 *单位矩阵*。
   
## LSTM
### 关注参考资料1的 *为什么LSTM能解决梯度问题*

从上述中我们知道， RNN产生梯度消失与梯度爆炸的原因就在于

$$ \prod_{j=k+1}^t \frac{\delta S_{j}}{\delta S_{j-1}} $$ 

， 如果我们能够将这一坨东西去掉， 我们的不就解决掉梯度问题了吗。 LSTM通过门机制来解决了这个问题。
我们先从LSTM的三个门公式出发：  
遗忘门：

$$ f_t = \sigma(W_f \cdot [h_{t-1}, x_t] + b_f) $$

输入门： 

$$ f_t = \sigma(W_i \cdot [h_{t-1}, x_t] + b_f) $$

输出门：

$$ f_t = \sigma(W_o \cdot [h_{t-1}, x_t] + b_f) $$

当前单元状态

$$ c_t = f_t \cdot c_{t-1} + i_t \cdot tanh(W_c \cdot [h_{t-1}, x_t] + b_c) $$

当前时刻的隐层输出：

$$ h_t = o_t \cdot tanh(c_t) $$

后续内容参考资料3
