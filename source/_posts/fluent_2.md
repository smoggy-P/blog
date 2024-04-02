---
title: "fluent入门学习（2）：热电单向耦合分析"
tags: [CFD]
categories: fluent入门学习
date: 2020-09-24 13:35:21
index_img: /img/fluent_2/index.png
math: true
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
使用fluent和thermoelectric模块进行热电模拟，其中热电部分为单向耦合，即仅通过塞贝克效应考虑温度对电的影响。其中一些概念仅为笔者自己的理解，如有谬误欢迎各位提出讨论！

# 2. 基本概念
## 2.1 塞贝克效应
通过半导体材料两边稳定的温差能够形成电势差，又称为热电第一效应，基本公式为：

$$
V_{ab} = \int_{T_1}^{T_2} (S_B-S_A)(T_2-T_1)dT
$$

对单个材料定义绝对塞贝克系数：

$$
S=\lim\limits_{\Delta T\to0}\frac{V}{\Delta T}
$$

## 2.2 综合热电效应总结
![](https://img-blog.csdnimg.cn/20200924110431136.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5Mzk1MjU2,size_16,color_FFFFFF,t_70#pic_center)
# 3. 几何建模和网格划分
# 3.1 几何建模
![](https://img-blog.csdnimg.cn/20200924110711650.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5Mzk1MjU2,size_10,color_FFFFFF,t_70#pic_center)
几何部分设计两组pn结，外电路部分设计为一个闭环。设定上方为冷源和散热片，下方为热源。

# 3.2 网格划分
![](https://img-blog.csdnimg.cn/20200924111122922.png#pic_center)
对于流场部分设定inflation边界层。
![](https://img-blog.csdnimg.cn/20200924111252313.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5Mzk1MjU2,size_10,color_FFFFFF,t_70#pic_center)
对于几何较为规整的pn结、电阻等部分使用扫略的网格生成方式，但是底部连接部分扫略网格出现畸变，目前还没有解决方法。
![](https://img-blog.csdnimg.cn/20200924111411686.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5Mzk1MjU2,size_16,color_FFFFFF,t_70#pic_center)
# 4.Fluent流场热部分求解
打开能量方程-添加材料性质-设定边界条件-设置求解方法-初始化-计算求解
接触面温度场：
![](https://img-blog.csdnimg.cn/20200924114640608.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5Mzk1MjU2,size_16,color_FFFFFF,t_70#pic_center)
# 5.热电耦合设置
## 5.1材料设置
添加单独的热电模块之后在engineering data中添加所需材料，其中pn结与电阻材料需要自定义，添加新材料后增加所需要的性质。（电阻材料除了电导率外需要再添加其他性质，否则最后计算时会出现licsence权限问题，无法计算纯电学问题）
![](https://img-blog.csdnimg.cn/20200924115153674.png#pic_center)
## 5.2几何和网格处理
在workbench中导入和之前模型一致的几何，在model中添加材料的对应并生成自动网格（这里网格不需要与之前的网格一致，并且不需要考虑流场，可以将流体域部分以及散热片部分的几何抑制）
![](https://img-blog.csdnimg.cn/20200924115452469.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5Mzk1MjU2,size_16,color_FFFFFF,t_70#pic_center)
## 5.3边界条件设置与结果查看
1）在imported load中新增temperature边界条件，选中与散热片的连接面。
2）在总边界条件中增加下表面的温度条件。
3）选中一个交界面作为电势0点。
![](https://img-blog.csdnimg.cn/20200924115736570.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5Mzk1MjU2,size_16,color_FFFFFF,t_70#pic_center)
设置好边界条件后进行求解。
温度场：
![](https://img-blog.csdnimg.cn/20200924115837928.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5Mzk1MjU2,size_16,color_FFFFFF,t_70#pic_center)
电流密度：
![](https://img-blog.csdnimg.cn/20200924115858693.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5Mzk1MjU2,size_16,color_FFFFFF,t_70#pic_center)
电势场：
![](https://img-blog.csdnimg.cn/20200924115914591.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5Mzk1MjU2,size_16,color_FFFFFF,t_70#pic_center)
