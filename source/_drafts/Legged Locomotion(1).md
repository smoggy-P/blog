---
title: "Legged Locomotion(1): Zero Moment Point"
tags: [Math, Chinese]
categories: Legged Locomotion
math: true
index_img: 
date: 2024-04-15
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

# 1. 什么是ZMP

个人认为ZMP是一种**描述足底合外力的简化模型**。这个概念由塞尔维亚学者Miomir Vukobratovic提出[1]。直观上来说，当我们需要描述一个人形机器人的运动状态时，从纯解析的角度上来看，我们需要了解机器人所收到的所有合外力的情况。对于人形机器人而言，这些合外力主要来自于地面对机器人的支撑力和机器人对地面的摩擦力。ZMP的提出就是为了简化这个问题，将所有合外力的影响简化为一个点的作用力。

<p style="text-align: center;">
    <img src="/blog/img/locomotion/zmp.png" width=300>
</p>

如图所示，脚底的合外力可以等效为作用于ZMP点的合外力，同时在这个点没有x和y方向的扭矩。此时机器人整体的受力情况可以由下图表示：

<p style="text-align: center;">
    <img src="/blog/img/locomotion/force.png" width=300>
</p>

其中$F_{G.R.}$表示地面对机器人的支撑在ZMP点的等效力，$Mg$为机器人的重力，$Ma$为机器人的惯性力（这里是将机器人质心的加速度等效为一个作用在质心的力，这样就能通过静力学的平衡关系来求解机器人的运动状态，这用到了**达朗贝尔原理**的思想）。

# 2. ZMP的应用

上面我们给出了围绕ZMP构建的受力分析，但到此为止这个模型并不能提供额外信息，这只是重新构建了一个平衡方程。这个平衡方程能够让我们通过机器人的运动状态$a$来求解ZMP点的位置。

[1]: https://www.worldscientific.com/doi/abs/10.1142/S0219843604000083 "Vukobratovic, M., & Borovac, B. (2004). Zero-moment point-thirty five years of its life. International Journal of Humanoid Robotics, 1(1), 157-173."