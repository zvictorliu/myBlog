---
title: IO管理基础
---

# IO管理基础

## 1 IO设备

IO设备根据信息交换的单位可分为：

- 块设备：以数据块为单位，比如磁盘，可寻址
- 字符设备：以字符串为单位，比如键盘，不可寻址

### 1.1 IO接口

设备控制器，即是IO接口，它既要能和CPU通信，还要和设备通信，并能按照CPU指令控制设备

<img src="https://cdn.jsdelivr.net/gh/zvictorliu/typoraPics@main/img/image-20230518130408698.png" alt="image-20230518130408698" style="zoom:80%;" />

有与CPU的接口，也有与设备的接口

IO逻辑能够对CPU发出的控制何地址信息译码，从而进行控制

所以，其功能包括了：

接收何识别CPU命令、数据交换、标识和报告设备状态、地址识别、数据缓冲、差错控制

### 1.2 IO端口

指的其实是<u>寄存器</u>，也就是IO接口中可以被CPU直接访问的那部分寄存器，包括：

- 数据寄存器：数据缓冲用
- 状态寄存器：记录状态，知道是否准备好
- 控制寄存器：命令

编址方式（很微机原理）

- 独立编址
- 统一编址（内存映射）

<img src="https://cdn.jsdelivr.net/gh/zvictorliu/typoraPics@main/img/image-20230518130651715.png" alt="image-20230518130651715" style="zoom:67%;" />

## 2 IO控制方式

设备和内存之间的输入输出控制方式

### 2.1 程序直接控制

即轮询方式，CPU读取外部设备的每一个字

CPU都需要不断检查外设状态，直到被放入设备管理器的寄存器中，更改了外设状态

### 2.2 中断驱动

数据读入后，通过控制线发起中断信号，CPU请求之后再将数据放到总线上传给CPU

比轮询好，但每个字也需要用一次CPU

### 2.3 DMA

在内存和设备之间建立一条之间的通路，以数据块为单位传输，直接送入内存，只有在开始和结束时才需要CPU

<img src="https://cdn.jsdelivr.net/gh/zvictorliu/typoraPics@main/img/image-20230518131706986.png" alt="image-20230518131706986" style="zoom:67%;" />

有专门的DMA控制器：

<img src="https://cdn.jsdelivr.net/gh/zvictorliu/typoraPics@main/img/image-20230518131808545.png" alt="image-20230518131808545" style="zoom:67%;" />

CR: 命令/状态寄存器

MAR: 内存地址寄存器，输出时是内存中的源地址，输入时是内存中的目标地址

DR: 数据寄存器

DC: 数据计数器

CPU接收到设备发出的DMA请求，给DMA控制器发一条命令启动后继续做自己的事，DMA自己进行传输（一个块，多个字），传输完成后再发起中断

## 3 IO软件层次

IO软件，分层结构

<img src="https://cdn.jsdelivr.net/gh/zvictorliu/typoraPics@main/img/image-20230518132532587.png" alt="image-20230518132532587" style="zoom:67%;" />

设备独立性软件屏蔽了设备的差异

## 4 应用程序的IO接口

字符设备接口

块设备接口

网络设备接口

阻塞/非阻塞IO