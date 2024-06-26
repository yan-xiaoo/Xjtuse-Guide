[TOC]

# 第十六章 微程序控制

> 微指令指什么
>
> 水平和垂直
>
> 微程序结构
>
> 任务

## 16.1 基本概念

### 微指令

**微程序控制语言microprogramming language**：指定微操作的语言，**每行描述一个时间内出现的一组微操作**，称为一条**微指令microinstruction**

**固件firmware**： 微指令序列，也称为微程序，只读存储中的程序指令、硬件和软件之间的中间环节

**控制字control word**：对任一微操作，控制器发出的每根控制线或开或关，每根控制线可以由一个二进制数字表示，<mark>和微操作一 一对应</mark>

可以将能同时进行的控制字归并成控制字段

**如何使用微程序概念来实现控制器呢:question:**

> 考虑到对于每个微操作，控制器所做的全部事情就是产生一组控制信号。对任一微操作，控制器发出的每根控制线或开或关。自然，对应这种情况，每根控制线可由一个二进制数字表示。这样，我们就构造了**一个控制字（control word），其中每位代表一根控制线，从而每个微操作能用控制字中的不同的0和1的式样来表示。**

**微指令类型**

垂直微指令：每个微指令指定要执行的单个（或几个）微操作 

> 宽度较窄 
> n 个编码为 log2 n 位的控制信号 
> 表达并行性的能力有限
> 很少使用
>
> ![image-20211201103646326](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img2/1695704551.png)

水平微指令：每个微指令指定要并行执行的许多不同的微操作

> 比较宽 
> 可实现高度并行操作 
> 控制信息的编码很少
>
> ![image-20211201103952812](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img2/1695704552.png)

### 微程序控制器

**微程序控制器的组成**

> **控制存储器（control memory）**存有一组微指令；
>
> **控制地址寄存器（control address register）**含有下面即将被读取的微指令地址；当一条微指令由控制存储器读出后，即被传送到控制缓冲寄存器
>
> **缓冲寄存器(control buffer register)。**此寄存器的左半部分与控制器发出的控制线相连接。于是，由控制存储器读一条微指令等同于执行这条微指令。
>
> 图中所示的第三个部件是**排序单元（sequencing unit），**它向控制地址寄存器装人地址并发出读命令。
>
> ![image-20211201104752774](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img2/1695704553.png)

**控制器的功能**

> :one: 为执行一条指令，定序逻辑发出一个读命令给控制存储器。
> :two: 控制地址寄存器指定的一个字读人到控制缓冲寄存器。
> :three: 控制缓冲寄存器的内容生成控制信号，并为定序逻辑提供下一条地址信息。
> :four: 定序逻辑根据这个地址信息和ALU标志，将一个新地址装人到控制地址寄存器。所有这些事情都发生在一个时钟周期内。

![image-20211201105540040](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img2/1695704554.png)

对于第四步，在每条微指令结束时，定序逻辑都要将一个新的地址装入控制地址寄存器，取决于ALU标志和控制缓冲寄存器的内容，要做如下决策：

> **取顺序下一条微指令**：加1到控制地址寄存器。
> **基于跳转微指令转移到一个新的例程**：将控制缓冲寄存器的地址字段装人控制地址寄
> 存器。
> **转移到一个机器指令例程**：根据IR中的操作码向控制地址寄存器装人机器指令例程的第一条微指令。

**微程序设计的优缺点**

> 简化控制单元的设计
> 便宜 不易出错 
> 比硬连线控制慢 
> 微程序设计是当代CISC中实现控制单元的主要技术 硬连线控制主要用于RISC

## 16.2 微指令排序

微指令排序的设计考虑

> :one: 微指令大小
>
> 减小微指令的大小就能节省控制存储器的成本
>
> :two: 地址生成时间
>
> 生成时间要尽可能短，这样才能满足尽快执行微指令的要求
>
> :three: 下一条微指令地址来源
>
> 由指令寄存器决定\下一个顺序地址\转移

下一微指令的地址必须根据当前微指令、条件标志、和IR的内容来生成

根据微指令中的地址信息的格式，可将转移控制逻辑分为：

> 双地址字段、单地址字段、可变格式

![image-20211227161944224](https://raw.githubusercontent.com/yijunquan-afk/img-bed-1/main/img2/1695704555.png)

## 16.3 微指令执行

取微指令

执行微指令

对于水平微指令而言，执行等同于读取、对于垂直微指令而言，意味着译码

### 微程序设计的应用

计算机的实现 

仿真 

> 在一台计算机上使用微程序来执行最初为另一台计算机编写的程序 

操作系统支持 

> 实现基元 

实现特殊用途设备 

高级语言支持 

> 固件中实现功能和数据类型 

微诊断 

> 监控、检测、隔离、修复系统错误 

用户定制 

> 一些机器提供可写存储和易于使用的微指令集，以允许用户编写微程序

### 优缺点

优点：简化控制器的设计任务、成本较低、不易出错

**缺点**：速度要慢于硬连接式控制器

> 微程序式CU包含的存储模块是时序电路，硬连线是组合电路

