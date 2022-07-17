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

## Hybrid Workload Scheduling on HPC Systems (Zhiling Lan)

### 概括

- 对任务进行分类: on-demand jobs, rigid jobs, and malleable jobs
  - Rigid Job: 一次 N 台机器，一次跑一天
    - 用户提交 Rigid Job 的时候需要提交使用 Nodes 和 **Estimated Time**. 这里比较 tricky 的是, 当 Estimated Time 大于了 Actual Time 的时候会被 kill 掉。虽然 slurm 本身在超过时间限制的时候也会 kill 掉，但是把预测运行时间和最大时间限制分开我认为是明智的选择。
  - On-demand job: 突发的高消耗的工作
    - estimated job arrival time, job size, and job duration runtime estimate.
    - ![](https://snz04pap002files.storage.live.com/y4mlAIQww3Kit3BkeclFMemSlGZlYt4Dm4aZdDsnep0ddxVIInMTnZzQ56xEHIlwbNK2uNwy-4LOSf6SS6NpoGnPv_e7uOHqrmJnclrsYQSkPQbqewVAYRkNYhZ5Ig8s3nUBPVnLgmTGJ_C-DqsYxd_yji9QPq7jIW3_HxabgItZWH_9_yf_tJJj8qgAF6-ynuH?width=256&height=133&cropmode=none)
    - 我们的 DASH 对于 on demand job 并不会给出一个更高的优先级，相反会给出一个可能很晚的 DDL. 未来的改进我觉得可以让 DASH 考虑用户的 DDL, 这样 on demand job 可以被放到 SLO guarantee cluster 上
  - Malleable job: 弹性任务
    - minimum job size nmin , maximum job size nmax , job estimate runtime when running at maximum job size testimate
    - [Amazon’s two minutes warning strategy on spot instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/spot-interruptions.html)
- 论文的目的就是对于三种任务同时进行调度
- 调度方法
    1. Do Nothing
    2. Collect-until-actual-arrival (CUA): 提前准备资源根据提前的通知
    3. Collect-until-predicted-arrival (CUP): 加入预测器以提前中断 rigid job (check point) ![](https://snz04pap002files.storage.live.com/y4m_JUdiEHUONZyxhPwx3dnL93LV5sheYO5PQBOwmW89Wj1bfczkmt36_5oYlBnmXVUMrVZ147D7cZfalDsKj6KOAA_zoyUhdqjDUPqVl3NaNZR3UeezuBC6rZTXTSpb2XWX6SJgUNtNNrp3t7yjcl5Gvc33-6VdVKeB2Y88Msg4L7mGaQoi03mlJgrzbm6ThIo?width=660&height=188&cropmode=none)
    4. Preempt-at-actual-arrival (PAA): on demand 硬抢其它 jobs
    5. Shrink-preempt-at-actual-arrival (SPAA): Shrink
    6. 如果预测的时间任务没来就释放。如果完成就释放。如果有任务因此 shrink, 再 expand 回来。
- 实验:![](https://snz04pap002files.storage.live.com/y4mhooHLGg440AYnKFB1H4Ktm3ZmcNUNhCkOIyqWFyJliaBnJ8BXGZWCSrvPzz_tnTSJcSbNCAg8hK5RGY27NhP5L6HDujO8Dc3JXu-cUHRNLu1QsOeeWSuNzChWpiAXSWNwqirbJwOOoPHnSqdKS2_fN5S3ctwyNsUPI3TdSetmXVLM106KMF4LjvxU_FwBSum?width=660&height=247&cropmode=none)
  - 指标:
        1. Job turnaround time
        2. On-demand jobs’ instant start rate
        3. Preemption ratio
        4. System utilization

  - 之后会有一些结论，比较了各种调度方法的特性。这部分可以看原文。
    - 预测越准确越好

### 优点

    - 总结了很多调度方法
    - 做了很多科学的测试，并且得出了很多结论
    - 对任务做了三种分类，这三种分类值得我们讨论

### 对我们的启示

    - 我们的调度策略可以再改进一下，这篇论文的一些关于预测的调度策略给了很多启示
    - 有一些边缘的案例，比如用户可以给出他的 DDL，来应付 on-demand jobs。刚性任务是我们之前所考虑的。我们目前应该还不支持可以自动放缩的程序。此外，检查点也可以被我们所利用
    - 这篇论文的预设需要用户给出很多东西，这在现实中无疑增加了很多心智负担。我们可以大大简化这些。

## HPC Scheduling Simulator (Daniel Rauch)
