---
title: (201条消息) 5.2.3 OS之I/O设备的分配与回收（DCT-COCT-CHCT-SDT）_BitHachi的博客-CSDN博客_dct chct
url: https://blog.csdn.net/weixin_43914604/article/details/106151353
clipped_at: 2022-11-14 22:21:26
category: default
tags: 
 - 无
---


# (201条消息) 5.2.3 OS之I/O设备的分配与回收（DCT-COCT-CHCT-SDT）_BitHachi的博客-CSDN博客_dct chct

### 文章目录

*   [0.思维导图](#0_3)
*   [1.设备分配时应该考虑的因素](#1_7)
*   *   [设备的固有属性](#_8)
    *   [设备的分配算法](#_10)
    *   [设备分配中的安全性](#_13)
*   [2.静态分配与动态分配](#2_16)
*   [3.设备分配管理中的数据结构](#3_18)
*   *   [设备控制表---DCT](#DCT_22)
    *   [控制器控制表---COCT](#COCT_25)
    *   [通道控制表---CHCT](#CHCT_27)
    *   [系统设备表---SDT](#SDT_29)
*   [4.设备分配的步骤](#4_33)
*   *   [设备分配的改进步骤](#_38)

* * *

# 0.思维导图

![在这里插入图片描述](assets/1668435686-c5260d1a77e7655cf7699608ef0deb7d.png)

# 1.设备分配时应该考虑的因素

## 设备的固有属性

![在这里插入图片描述](assets/1668435686-bd6650dad03fe4e593a6a0794567372d.png)

## 设备的分配算法

![在这里插入图片描述](assets/1668435686-2fdb2264765fa132db7dae1b1a30c651.png)

## 设备分配中的安全性

![在这里插入图片描述](assets/1668435686-42d5aa92bc30ca381fb17a8e27ba8281.png)

# 2.静态分配与动态分配

![在这里插入图片描述](assets/1668435686-ccae040fd90a90515d393258166395d7.png)

# 3.设备分配管理中的[数据结构](https://so.csdn.net/so/search?q=%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84&spm=1001.2101.3001.7020)

**设备、控制器、通道之间的关系：**  
![在这里插入图片描述](assets/1668435686-006f3a7d25cd43f35a6011697332798d.png)

## 设备控制表—DCT

![在这里插入图片描述](assets/1668435686-247a07929eca06e9d0ecdcbefd3c01c1.png)

## 控制器控制表—COCT

![在这里插入图片描述](assets/1668435686-62981dab6f751bc0b613e9a643d7636d.png)

## [通道](https://so.csdn.net/so/search?q=%E9%80%9A%E9%81%93&spm=1001.2101.3001.7020)控制表—CHCT

![在这里插入图片描述](assets/1668435686-efe49bda3c7937d80000805ffe9b1e57.png)

## 系统设备表—SDT

![在这里插入图片描述](assets/1668435686-816fac133bdf0b7fa3602e945819e0e8.png)

# 4.设备分配的步骤

![在这里插入图片描述](assets/1668435686-f8bf0d623c2d1faeee3bcb8c3dbe7e58.png)  
![在这里插入图片描述](assets/1668435686-525caeb5853529cdd983d37ee2d04761.png)  
![在这里插入图片描述](assets/1668435686-5d7f4fdece7f8c56aa0b3b1af816214a.png)  
![在这里插入图片描述](assets/1668435686-9ad5d6e3cdb1cd64dabdda85b1f4e9d5.png)

## 设备分配的改进步骤

![在这里插入图片描述](assets/1668435686-a47602f7eef9f0dd11ce14fd4582552f.png)  
![在这里插入图片描述](assets/1668435686-fba9dcaa1b3fd96bb6e3261c83c8b62c.png)  
![在这里插入图片描述](assets/1668435686-b305ae7a80697911532d218604b2e779.png)  
参考：《王道操作系统》