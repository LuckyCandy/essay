---
title: "校验码"
date: 2022-06-01T12:30:00+08:00
draft: false
share: true
slug: "checkcode"
author: "Damon"
tags: ["computer", "计算机"]
categories: ["computer"]
image: "/images/computer/jym.png"
---

# 校验码

> 信息保存在电容中，如果遇到电磁环境干扰，会导致电容的充放电或触发器的翻转，那么存在存储器的信息就可能会出错，因此必须存在相应的技术去校验信息是否准确，校验码的概念被提出。常见的校验方法有：**奇偶校验码、海明码、循环冗余校验码**。

## 什么是码距？

* 要给字母A，B编码，使用一位长度的二进制来编码，即：A = 0，B = 1 或者 A = 1，B = 0，此时因为只有一位长度，码距只能是1
* 如果使用二位长度的二进制来编码， 可选编码则有00,01,10,11四种
  1. 如果使用 A = 00， B = 01 或 A = 10，B = 11 来编码，存储的字符(AB)的编码即为{00,01}或{10,11}。此时如果遇到干扰，字符B由01变为了00，而00又是A的合法编码，就无法验证是否存储的内容发生过变化了
  2. 如果使用 A = 00，B = 11 或 A = 10，B = 01 来编码，存储的字符(AB)的编码即为{00,11}或{10,01}。此时如果遇到干扰，任何一个字符变化都会被标识为非法(两位都变化的则不能监测)
* 根据上面的分析，明显2的设计要优于1，不难发现采用2的编码方式，**两位长度的二进制都不一样**，所以我们可以说此时的**码距为2**，第一种方案只有一位不同，码距则为1。所以，**码距就是任意两个二进制编码之间不同bit（位）的最小个数**。


## 如何设置合适码距？

正如上面的（2）如果两位都发生变化**00**变为了**11**，而11也是合法编码，无法判定是否发生错误。此时码距(**d**)为2，可以检测误码位数(**e**)为1，故此：**在一个码组内要检测e个误码，则最小码距应该满足 d ≥ e + 1**

码距设为2时，可以检测一位误码，但是却无法进行修正。比如错误码 01，无法确定是00还是11发生一位变换而来，因此：**在一个码组内要纠正t个误码，要求最小的码距应该满足 d ≥ 2t + 1**

如要同时具备纠错检错能力，则码距需要满足：**d ≥ e + t + 1**



## 奇偶校验码

> 在原始码基础上增加一位校验位，校验位可以是0或者1，使其含有奇(偶)数个1

* 拿偶校验举例：假设原始码为11000011，增加一位校验位，由于采用偶校验，原始码中1的个数已经是偶数个，所以校验码补0，即<span style="color:red">0</span>11000011
* 假如传输是发生一位错误，变为<span style="color:red">0</span>11000010，1的个数变为奇数个，成功检测出错误
* 假如传输是发生二位错误，变为<span style="color:red">0</span>11000000，1的个数变还是偶数个，无法检测出错误

**因此，无论是奇校验还是偶校验，只能检测出奇数个位出错的情况**



## 海明码

> 原始码长度为**m**，在原始码的**2^n**(n ≥ 0)位置增加k个校验位，数据长度增长为**m + k**。将原始码分组，每组分配一个校验码，则工可以表示**2^k**个状态，其中1个表示所有组均没有错，**2^k - 1**个表示某些组出错情况；当满足**2^k - 1 ≥ m + k**时，就可以判断出具体是哪一位出错。

1. 假设要传输的原始码为：**1010**，求使用奇校验的海明校验码之后的传输内容？

   * 根据公式 2^k - 1 ≥ m +k，此时m = 4，可得最小k = 3，由于校验位在2^n位置，则原始码可写为：![:inline](/images/computer/海明码01.jpg)

   * 每组一个校验码，k=3则需要分为3组：

     1. 第一组序号第一位(从右向左)为1的：3（01<span style="color:red">1</span>）, 5（10<span style="color:red">1</span>）, 7（11<span style="color:red">1</span>），即<span style="color:red">0</span>100
     2. 第二组序号第二位(从右向左)为1的：3（0<span style="color:red">1</span>1）, 6（1<span style="color:red">1</span>0）, 7（1<span style="color:red">1</span>1），即<span style="color:red">1</span>110
     3. 第三组序号第三位(从右向左)为1的：5（<span style="color:red">1</span>01）, 6（1<span style="color:red">1</span>0）, 7（1<span style="color:red">1</span>1），即<span style="color:red">0</span>010

     于是可得，k1 = 0，k2 = 1，k3 = 0，带入上图位置，传输内容为：**0110010**

2. 接受结果为**0110110**是否正确？如果错误请指出具体的错误处。

   * 由（1）可知，接受结果错误，即数据在传输过程中发生了变化

   * 按照（1）中的分组进行校验：

     1. 一组：校验码为序号为1(2^0)，即0，序号「3,5,7」一组，则此组内容为0110，非奇数个1，校验结果为**1**(错误)
     2. 二组：校验码为序号为2(2^1)，即1，序号「3,6,7」一组，则此组内容为1110，奇数个1，校验结果为**0**(正确)
     3. 三组：校验码为序号为4(2^2)，即0，序号「5,6,7」一组，则此组内容为0110，非奇数个1，校验结果为**1**(错误)

     校验所得错误码为**101**（第一组校验结果|第二组校验结果|第三组校验结果），即序号5的内容发生了改变


## CRC循环冗余校验码

> 数据传输双方约定一个除数，被除数为原始信息 + R个校验位，需要保证被除数 **模二除** 除数，余数为0，如果非0则认为数据传输过程中出现错误。被除数通常已多项式方式表示，例如：G(x)=x^3 + x^2 + 1对用的二进制码为1101


**要传输的原始数据为101001，双方约定的除数(多项式)为G(x)=x^3 + x^2 + 1，求CRC码**

* 有多项式可知，除数的二进制表示为1101
* 多项式**最大次幂为3**，**则校验位有3位**，原始数据需要补0左移3位，即101001<span style="color:red">000</span>
* 进行**模2**除法(不进位加法)计算：


![](/images/computer/CRC.jpg)

所求CRC码为：**101001001**，验证即可使用101001001 **模二除** 1101，余数为0则验证成功。
