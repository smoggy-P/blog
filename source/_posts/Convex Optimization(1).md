---
title: "Convex Optimization Note(1): Affine Set and Convex Set"
tags: [Math, English]
categories: Convex Optimization
math: true
mermaid: false
index_img: 
date: 2023-10-28 15:00:00
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
# 1. Convex Sets
## 1.1 Affine Set $C$
**Definition**: $\forall x_1,x_2\in C, \theta x_1+(1-\theta) x_2 \in C$

**Property**:

- Solution Set of Linear Equation $\iff$ Affine Set(线性方程的解集是affine set，同时affine set都能写成线性方程的解)

- Subspace: $V=C-x_0$ where $x_0\in C$(无论选择哪个$x_0$，subspace $V$始终能穿过原点)

## 1.2 Affine Hull $\textbf{aff }C$
**Definition**: All affine combinations of points in set $C$

**Property**: Smallest affine set that contains $C$

**Example**: Affine set of a circle $\left\{(x_1,x_2)\in\mathbb{R}^2|x_1^2+x_2^2=1\right\}$ is $\mathbb{R^2}$, the *affine dimesion* is 2.

## 1.3 Convex Set
**Definition:** Affine set with $\theta \in [0,1]$, we have similarly *Convex Hull*

**Property:**

- works for infinite sums and integral
  
- pass through expectation

## 1.4 Cone(non-negative homogeneous)
**Definition:** $\forall x\in C\text{ and } \theta > 0, \theta x\in C$

## 1.5 Simplex
**Definition:** Convex hull of $k+1$ affine independent vector

> affine independent: 直观理解与线性相关定义类似，数学表达上对于$v_0,v_1\dots v_{k}$，如果$(v_1-v_0,v_2-v_0\dots v_k-v_0)$线性无关，则这k+1个向量仿射无关。值得一提的是对于二维平面，要证明四个及以上向量仿射无关就需要证明三个及以上二维向量线性无关，而三个二维向量必定线性相关，因此不存在四个及以上仿射相关的二维向量，因此 $\mathbb{R}^2$ 中的simplex只可能是三角形（而不会是四边形或更多边形）。

## 1.6 Positive Semi-definite Cone
**Definition:** Set of Semi-definite matrix $S_+^n=\left\{X\in\mathbb{R}^{n\times n}|X^T=X,X\succeq0\right\}$

**Property:** This set is a convex cone. (任意两个半正定矩阵线性组合，系数为正的情况下，结果仍为半正定矩阵)

# 2. Operation that preserves convexity

- **Intersection** of convex sets is still convex
  
- **Affine Function** projects convex sets to convex sets
  
  > Affine Function: 等同于线性变换

- **Perspective Function** -> **Linear-Fractional Function**
  
  > $(x_1,x_2,x_3)$ to $(\frac{x_1}{x_3}, \frac{x_2}{x_3}, 1)$

# 3. General Inequality
## 3.1 Proper Cone
Convex; Closed; Solid(non-emplty interior); Pointed(不超过半平面)

## 3.2 Inequality
Let $K$ be a proper cone in vector space $V$, we say vector $x$ is greater than $y$ with respect to $K$ when we have $x-y\in K$. Denoted as:
$$x\succeq_Ky$$

# 4. Seperating and supporting hyperplane

<!-- # 4.1 Seperating -->
*Seperating hyperplane*: For two convex sets that are disjoint(no intersection), there exists a hyperplane that seperates these two sets.

*Converse theorm*: If at least one convex set is open, existing seperating hyperplane means disjoint sets.

*Supporting hyperplane:* Seperating hyperplane on a boundary point

# 5. Dual Cone
**Definition:** For a cone $K$, the dual cone is defined as:
$$K^*=\left\{y|x^Ty\geq0, \forall x\in K\right\}$$

> 几何上可以理解为与所有目标锥内的向量夹角都不超过90度的向量构成的锥

**Property:**

- $x$ is the minimum element of set $S$ with respect to $K$, it means that for $\lambda\succeq_{K^*}0$, $x$ is the minimizer of $\lambda^Tz$ over $z\in S$
