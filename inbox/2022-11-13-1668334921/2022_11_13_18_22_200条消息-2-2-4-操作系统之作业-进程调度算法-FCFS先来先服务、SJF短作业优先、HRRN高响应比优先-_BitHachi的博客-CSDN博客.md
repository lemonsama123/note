---
title: (200条消息) 2.2.4 操作系统之作业/进程调度算法（FCFS先来先服务、SJF短作业优先、HRRN高响应比优先）_BitHachi的博客-CSDN博客
url: https://blog.csdn.net/weixin_43914604/article/details/105328521
clipped_at: 2022-11-13 18:22:01
category: default
tags: 
 - 无
---


# (200条消息) 2.2.4 操作系统之作业/进程调度算法（FCFS先来先服务、SJF短作业优先、HRRN高响应比优先）_BitHachi的博客-CSDN博客

### 文章目录

*   *   *   *   [0.思维导图](#0_2)
            *   [1.先来先服务---FCFS](#1FCFS_5)
            *   [2.短作业优先---SJF](#2SJF_9)
            *   [3.高响应比优先---HRRN](#3HRRN_21)
            *   [4.三种算法的对比和总结](#4_29)

* * *

#### 0.思维导图

![在这里插入图片描述](assets/1668334921-da0ae8d7ae126f7784fcbcadd7a1b1a3.png)

#### 1.先来先服务—FCFS

*   First come first sever  
    ![在这里插入图片描述](assets/1668334921-7816d45b3f10df1fb24e373654a6323e.png)  
    ![在这里插入图片描述](assets/1668334921-36e30c5e08b4a4ee1b1129477615cced.png)

#### 2.短作业优先—SJF

*   Shortest Job First

![在这里插入图片描述](assets/1668334921-f1a013948ba7610db514b92ad2c0794a.png)

*   非抢占式—SJF  
    ![在这里插入图片描述](assets/1668334921-49b5b81e1f1c8e8d0afd845170c307ca.png)
*   抢占式—SJF(SRTN)  
    ![在这里插入图片描述](assets/1668334921-4775b2bb13140fec3f945eea55b7f893.png)  
    ![在这里插入图片描述](assets/1668334921-7be74b8d7df492fe60a24fd93e0cc1d5.png)
*   注意几个细节  
    ![在这里插入图片描述](assets/1668334921-122a8a929920a95522513d5e2553728d.png)

#### 3.高响应比优先—HRRN

*   Highest Response Ratio Next

![在这里插入图片描述](assets/1668334921-c49effacdcb9b87eea78134563d8e537.png)  
![在这里插入图片描述](assets/1668334921-f8e215cfbf55b805304c08d5408e5199.png)

![在这里插入图片描述](assets/1668334921-b25027a7f1a8aacf5afd6f8e32e11645.png)

#### 4.三种算法的对比和总结

![在这里插入图片描述](assets/1668334921-78d77897687dbe6d02c3b5ea3873d97c.png)

文章知识点与官方知识档案匹配，可进一步学习相关知识

[算法技能树](https://edu.csdn.net/skill/algorithm/)[首页](https://edu.csdn.net/skill/algorithm/)[概览](https://edu.csdn.net/skill/algorithm/)28643 人正在系统学习中