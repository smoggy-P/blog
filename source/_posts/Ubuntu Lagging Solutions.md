---
title: "Ubuntu Lagging Solutions"
tags: []
categories: 
math: false
mermaid: false
index_img: 
date: 2023-08-04 15:00:00
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

最近解决了一下ubuntu卡顿的情况，梳理了一下造成卡顿的一些原因，便于之后排查。

# 1. 显卡驱动问题
ubuntu本身默认没有nvidia显卡驱动，需要手动安装，这里的过程不再赘述。判断显卡驱动有没有出问题可以通过以下几种渠道：

- 设置->关于 选项下查看显卡是否为独立显卡，如果驱动没有起作用，这里会显示核显。
<p style="text-align: center;">
    <img src="/blog/img/ubuntu/gpu.png" width=400>
</p>

- 在命令行输入 `nvidia-smi` 观察是否有输出
```
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 470.199.02   Driver Version: 470.199.02   CUDA Version: 11.4     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|                               |                      |               MIG M. |
|===============================+======================+======================|
|   0  NVIDIA GeForce ...  Off  | 00000000:01:00.0  On |                  N/A |
| N/A   59C    P8     3W /  N/A |    597MiB /  3911MiB |      6%      Default |
|                               |                      |                  N/A |
+-------------------------------+----------------------+----------------------+
                                                                               
+-----------------------------------------------------------------------------+
| Processes:                                                                  |
|  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
|        ID   ID                                                   Usage      |
|=============================================================================|
|    0   N/A  N/A      1584      G   /usr/lib/xorg/Xorg                 70MiB |
|    0   N/A  N/A      2420      G   /usr/lib/xorg/Xorg                222MiB |
|    0   N/A  N/A      2616      G   /usr/bin/gnome-shell               62MiB |
|    0   N/A  N/A      6695      G   ...RendererForSitePerProcess      206MiB |
|    0   N/A  N/A     13625      G   ...824728346665869974,262144       25MiB |
+-----------------------------------------------------------------------------+

```
---

# 2. 显卡使用模式
在确认显卡驱动成功起作用之后，我们仍然不能确认显卡在工作，这也是我这次遇到的情况。在通过 `apt upgrade` 自动升级显卡驱动后，我发现新安装的显卡驱动默认采用"on-demand"模式。一般情况下显卡都处于闲置状态。我们可以通过以下简单测试看看显卡是否正在工作。
- 在终端内输入 `watch nvidia-smi`，注意持续观察下半部分"Process"
- 打开firefox浏览器，观察Process部分有没有新增firefox的进程，如果没有就证明GPU处于on-demand模式
- 在终端输入`nvidia-settings`，将模式调整为Performance Mode，之后reboot
<p style="text-align: center;">
    <img src="/blog/img/ubuntu/gpu_set.png" width=400>
</p>

# 3. CPU使用模式
部分情况下CPU也会处于on demand模式，可以通过`cpufrequtils`进行调整：
```
# 安装cpufreq工具
sudo apt install cpufrequtils
# 检查CPU频率是否是最大状态
cpufreq-info
# 调整CPU模式
cpufreq-set -g performance
```
值得一提的是这个设置重启会被重置，之后再看看有没有保留设定的方法。