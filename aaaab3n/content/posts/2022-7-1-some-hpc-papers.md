---
layout: post
title: "一些 HPC Scheduler 的 Paper"
date: 2022-07-01  10:03:59 +0800
category: EDU
tags: 
    - paper
---

# 一些 HPC Scheduler 的 Paper
## Implementing and Evaluating Multi-Resource Scheduling in Slurm (Sirisha Cherala)

### 概括
- 有一篇论文提出了 BBSched, 大概是有一个 window, 然后用遗传算法在这个 window size 的任务中做寻找最优调度 (因为算法是 pareto set, NP问题, 所以用遗传算法)。然后这篇文章就说他们怎么实现以及做 evaluation。我们准备做一个 HPC Scheduler， 所以研究这种 SOP 会带来一些启发和树立标准。
- Dr.Zhiling Lan, 或许之后有机会可以去拜访
- 第二章讲了他们安装并使用的 Slurm. 我们用 Slurm 很久了所以这部分可以略过。这里还提到了 [Slurm Simulator](https://github.com/ubccr-slurm-simulator).
    - slurm 插件是用 C 写的
    - [slurm plugin api](https://slurm.schedmd.com/plugins.html)
    - 基本上分为两个组件: 一个对接 slurm api 的 wrapper 和调度算法的实现 (我们可以用 restful api 或者 rpc 来把 wrapper 放在外部)
- 第三章是实验部分
    - 测试 CPU Utilisation 和 Average Slow down