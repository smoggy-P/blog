---
title: "From Calculus of Variations to Largrangian Equation"
tags: [Math, Chinese]
categories: 
math: true
index_img: 
date: 2024-04-02
post:
  meta:
    author:  # 作者，优先根据 front-matter 里 author 字段，其次是 hexo 配置中 author 值
      enable: false
    date:  # 文章日期，优先根据 front-matter 里 date 字段，其次是 md 文件日期
      enable: true
      format: "dddd, MMMM Do YYYY, h:mm a"  # 格式参照 ISO-8601 日期格式化
    wordcount:  # 字数统计
      enable: true
      format: "{} 字"  # 显示的文本，{}是数字的占位符（必须包含)，下同
    min2read:  # 阅读时间
      enable: true
      format: "{} 分钟"
    views:  # 阅读次数
      enable: true
      source: "busuanzi"  # 统计数据来源，可选：leancloud | busuanzi   注意不蒜子会间歇抽风
      format: "{} 次"
---

最近复习了一下拉格朗日方程，重新思考了一下一些疑惑，这里记录一下从变分法到拉格朗日方程的推导过程以及这些疑惑的可能解答。

# 1. 变分法
考虑一个泛函：

$$
J[y] = \int_{x_1}^{x_2} F(x, y, y') \, dx
$$

其中 $y = y(x)$ 是未知函数，$y' = \frac{dy}{dx}$，$F$ 是关于 $x, y, y'$ 的函数。我们希望找到一个函数 $y(x)$ 使得 $J[y]$ 取得极值。这里的问题是如何找到这个函数 $y(x)$。

> Q1: 为什么这里的F是关于x, y, y'的函数，而不关于y的导数的高阶导数？
>> 很多说法表示对于自然系统，二阶导数是最高的导数，因此我们只需要考虑到二阶导数。（比如牛顿运动方程中的加速度）。 

解决这个问题的基本思想就是变分。变分可以理解为对函数的微小扰动，即对函数 $y(x)$ 做一个微小的变化 $\delta y(x)$，此时泛函 $J[y]$ 也会有一个微小的变化 $\delta J[y]$。与函数极值的概念类似，假如此时的 $y(x)$ 是 $J[y]$ 的极值点，那么对于任意的微小扰动 $\delta y(x)$，$\delta J[y]$ 都应该为0。这样我们就可以得到一个关于 $y(x)$ 的微分方程，即欧拉-拉格术方程。

$$
\delta J[y] = \int_{x_1}^{x_2} \delta F dx

=\int_{x_1}^{x_2} \left( \frac{\partial F}{\partial y} \delta y + \frac{\partial F}{\partial y'} \delta y' \right) \, dx = 0
$$

> 引理：变分的导数等于导数的变分：$\delta y' = \frac{d(\delta y)}{dx}$

根据引理我们可以进一步得到：

$$
\begin{align*}
\delta J[y] &= 
\int_{x_1}^{x_2} \left( \frac{\partial F}{\partial y} \delta y + \frac{\partial F}{\partial y'} \frac{d(\delta y)}{dx} \right) \, dx \\
&= \int_{x_1}^{x_2} \left( \frac{\partial F}{\partial y} \right) \delta y \, dx + \left[ \frac{\partial F}{\partial y'} \delta y \right]_{x_1}^{x_2} - \int_{x_1}^{x_2} \frac{d}{dx} \left( \frac{\partial F}{\partial y'} \right) \delta y \, dx && \text{分部积分} \\
&= \int_{x_1}^{x_2} \left( \frac{\partial F}{\partial y} - \frac{d}{dx} \left( \frac{\partial F}{\partial y'} \right) \right) \delta y \, dx + \left[ \frac{\partial F}{\partial y'} \delta y \right]_{x_1}^{x_2}
\end{align*}
$$

其中第一项是关于 $\delta y$ 的积分，第二项是边界项。

# 2. 欧拉-拉格朗日方程
在一些物理问题中，边界条件是被限定为固定的，即 $\delta y(x_1) = \delta y(x_2) = 0$， 此时边界项为0。为了使得变分恒为0，我们需要使得第一项中的系数恒为0，此时我们可以得到欧拉-拉格朗日方程：

$$
\frac{\partial F}{\partial y} - \frac{d}{dx} \left( \frac{\partial F}{\partial y'} \right) = 0
$$

通过求解这个方程，我们可以得到函数 $y(x)$，使得泛函 $J[y]$ 取得极值。

# 3. 拉格朗日力学(lagrangian mechanics)
在物理学中，我们可以利用欧拉-拉格朗日方程来求解物体的运动。在拉格朗日力学中，我们定义拉格朗日量 $L = E - K$，其中 $E$ 是系统的势能，$K$ 是系统的动能。

> Q2: 为什么拉格朗日量是势能减去动能？
>> 个人理解这里的拉格朗日量的定义是一种逆向工程。我们首先确定牛顿力学中，系统的运动可以通过求解牛顿第二定律$F = ma$来求解。这里我们希望找到一个拉格朗日量$L$，当系统运动满足牛顿第二定律时，拉格朗日量的变分为0。这样求解牛顿第二定律就和求解拉格朗日量的变分等价。而我们进一步发现当拉格朗日量定义为$E - K$时，求解拉格朗日方程就等价于求解牛顿第二定律。[1]

这里给出一个单摆的例子，单摆的广义坐标为 $\theta$，单摆的拉格朗日量为：

$$
L = T - V = \frac{1}{2} m l^2 \dot{\theta}^2 - mgl(1 - \cos \theta)
$$

代入欧拉-拉格朗日方程，我们可以得到单摆的运动方程：

$$
\frac{d}{dt} \left( \frac{\partial L}{\partial \dot{\theta}} \right) - \frac{\partial L}{\partial \theta} = 0
$$

$$
\frac{d}{dt} \left( m l^2 \dot{\theta} \right) + mgl \sin \theta = 0
$$

$$
m l^2 \ddot{\theta} + mgl \sin \theta = 0
$$

这就是单摆的运动方程。同样的结果也可以通过牛顿第二定律来求解，使用拉格朗日力学的好处在于我们可以通过定义拉格朗日量来简化问题的求解。


[1]: https://www.reddit.com/r/AskPhysics/comments/v9yqhs/comment/ibzlqfi/?utm_source=share&utm_medium=web3x&utm_name=web3xcss&utm_term=1&utm_content=share_button "understanding of lagrangian mechanics"




