---
title: "fluent入门学习（1）：三维卡门涡街算例"
tags: [CFD]
date: 2020-08-13 12:33:10
categories: fluent入门学习
index_img: /img/fluent_1/index.png
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

# 1. 提要

从零开始学习fluent流体仿真的一些记录，此次学习建立效果较为明显且不需要实验验证的卡门涡街现象的仿真以熟悉大致流程，主要采用solidworks进行模型设计，ICEM进行网格划分。其中一些概念仅为笔者自己的理解，如有谬误欢迎各位提出讨论！

# 2. 基本流程
solidworks进行流场建模，spaceclaim进行几何处理，ICEM进行结构网格划分，最后在fluent中设定材料与边界条件得出计算结果

# 3. SpaceClaim的几何预处理
在solidworks中我们主要对固体部分的几何进行了建模，实际分析过程中则主要考察流体的特性，因此需要提取出流体域。这里几何比较简单，只需要使用体积抽取功能，选中能够和几何图形形成封闭空间的面，再选中空间内的任意一个面。（注意：几何图形目前来看需要是一个体而不是多个体合成的，因此本例中如果在solidworks中创建几何时使用了多次拉伸，需要在体积抽取前将多个体合并为一个part）
<p style="text-align: center;">
    <img src="https://img-blog.csdnimg.cn/20200819162016951.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5Mzk1MjU2,size_16,color_FFFFFF,t_70#pic_center" width=200>
</p>

本例中几何处理只需要进行这一步。另外由于这里是流固单向耦合，仅考虑固体对流体的单向作用，因此不需要对固体部分进行网格划分，可以选择在这一步就将固体部分的块抑制（supress）。

# 4. ICEM结构网格划分
ansys提供了独立的网格划分模块ICEM，我们可以利用它进行非结构或结构网格的划分。这里介绍一下结构网格与非结构网格的区别：
1）非结构网格：由提前设定的参数（网格大小、网格类型四面体或六面体等）自动生成的网格。
2）结构网格：首先对几何进行抽象，绘制出反应几何特征的块（block），由块到几何建立一一映射。由于块一般为较为规整的几何形状，因此由块生成的网格一般较为整齐，多为六面体网格
<p style="text-align: center;">
    <img src="https://img-blog.csdnimg.cn/20200819162944643.png#pic_center" width=500>
</p>

ICEM中的几何不存在体的概念，而只有点、曲线和面，最终形成的网格是在面围成的封闭空间内形成的。首先对所有面进行命名，右边栏part右键create part功能，选中需要的面进行命名。

<p style="text-align: center;">
    <img src="https://img-blog.csdnimg.cn/20200819163307528.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5Mzk1MjU2,size_16,color_FFFFFF,t_70#pic_center" width=300>
</p>

命名完成后建立整体的块，这个块是基于已有几何的外部框架搭建的，本例中为一个长方体，其边界与顶点已经一一和几何对应了，之后不用再重复定义。
<p style="text-align: center;">
    <img src="https://img-blog.csdnimg.cn/20200819163717805.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5Mzk1MjU2,size_16,color_FFFFFF,t_70#pic_center" width=600>
</p>

创建完成后对块进行划分。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200819163808356.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5Mzk1MjU2,size_16,color_FFFFFF,t_70#pic_center)
删去中间圆柱部分的块，建立圆柱周长和两组四条边的映射关系，设置全局网格大小后初步建立网格。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200819163945342.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5Mzk1MjU2,size_16,color_FFFFFF,t_70#pic_center)
针对产生畸变严重部分进行顶点的调整后将网格质量提升到0.85以上。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200819164123857.png#pic_center)

针对较稀疏的边使用edge params进行设置，增加节点个数：（1.同样能设定增长率，实现边界层效果的设计 2.进行参数复制可以将参数复制到平行的edge上）
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200819164150295.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5Mzk1MjU2,size_16,color_FFFFFF,t_70#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200819164200151.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5Mzk1MjU2,size_16,color_FFFFFF,t_70#pic_center)
针对圆柱周边可能出现的边界层进行加密。（网格在五位数数量级时计算速度+精度较为合理）

# 5. fluent求解器设置
general中能够进行网格质量的检查，以及是求解瞬态还是稳态情况。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200819164927594.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5Mzk1MjU2,size_16,color_FFFFFF,t_70#pic_center)
之后需要在models中开启需要模拟的物理方程，默认只有流体粘度模型，本例中也只需要开启这个。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200819165110520.png#pic_center)
之后进行材料设置和进出口条件设置，将流体域的材料改为液态水，进口速度根据卡门涡街产生的雷诺数范围粗略计算为20m/s，其余皆为默认。
method中选择迭代方法，这里介绍常用算法SIMPLE算法，其主要根据不可压缩流体的连续性方程以及N-S方程进行迭代，在上述两个方程中速度场与压强场相互耦合，通过初始化压强场，由NS方程得出对应速度场，再由连续性方程进行修正之后反复迭代。
迭代算法完成后在residual中设置残差要求后进行初始化，由于这个例子中为瞬态求解因此还需要在计算页中设定求解时间步长、步数、每个时间步长的迭代次数，最后进行计算。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200819165904277.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5Mzk1MjU2,size_16,color_FFFFFF,t_70#pic_center)
后处理可以使用fluent自带或者ansys中的cfd-post，如果需要展示动画需要在计算之前提前在calculation activities中添加需要观测的云图，这里得到的结果如下：

<p style="text-align: center;">
    <img src="https://img-blog.csdnimg.cn/20200819170156853.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5Mzk1MjU2,size_16,color_FFFFFF,t_70#pic_center" width=600>
</p>

<p style="text-align: center;">
    <img src="https://img-blog.csdnimg.cn/20200819170239399.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5Mzk1MjU2,size_16,color_FFFFFF,t_70#pic_center" width=630>
</p>

可以观测到涡旋的脱落。