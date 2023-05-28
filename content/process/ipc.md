---
title: 进程通信
---

# 进程通信
进程之间需要通信：通知信息，数据传输，共享等待

`进程通信 (InterProcess Communication, IPC)` 机制就是用于进程间交互的一套规则

进程通信三种基本形式

- 共享内存，好比网盘传输
- 消息传递，好比文件传输助手
- 管道

## 1 共享内存

两个通信的进程之间存在一个可以直接进行访问的共享空间

{{< details "linux共享内存函数"  >}}
```c
#include <sys/shm.h>
// 创建或获取一个共享内存：成功返回共享内存ID，失败返回-1
int shmget(key_t key, size_t size, int flag);
// 连接共享内存到当前进程的地址空间：成功返回指向共享内存的指针，失败返回-1
void *shmat(int shm_id, const void *addr, int flag);
// 断开与共享内存的连接：成功返回0，失败返回-1
int shmdt(void *addr); 
// 控制共享内存的相关信息：成功返回0，失败返回-1
int shmctl(int shm_id, int cmd, struct shmid_ds *buf);
```
{{< /details >}}



{{< details "编程示例"  >}}

<img src="https://cdn.jsdelivr.net/gh/zvictorliu/typoraPics@main/img/image-20230527135203399.png" alt="image-20230527135203399" style="zoom:80%;" />

{{< /details >}}



虽然是共享，但对其读写也应是同步互斥的，需要同步互斥工具

需要特殊的系统调用才能让两个进程共享一片内存空间

<img src="https://cdn.jsdelivr.net/gh/zvictorliu/typoraPics@main/img/image-20230527140740181.png" alt="image-20230527140740181" style="zoom:67%;" />

## 2 消息传递

操作系统提供了发送和接收消息的两个原语，数据交换以格式化的`Message`为单位

![image-20230527141004702](https://cdn.jsdelivr.net/gh/zvictorliu/typoraPics@main/img/image-20230527141004702.png)

形象的例子：A要向B通信，需要把信息装在信封里寄给B

此时有：

- 直接：邮差直接给B，挂在B的一个消息缓冲队列里，B依次读取
- 间接：邮差放到B门口的邮箱里，B从里面拿

听起来感觉还是有点中间商



## 3 管道通信

管道是连接一个读进程和一个写进程的一个共享文件，即`pipe`文件

这种和共享内存的区别是，共享内存是完全互斥的，不能同时写，但管道是一种缓冲区，可以一边写，另一边同时读

![image-20230527141057424](https://cdn.jsdelivr.net/gh/zvictorliu/typoraPics@main/img/image-20230527141057424.png)

管道是半双工通信，即某一时刻只能单向传输，为了全双工需要定义两个管道



此外，OS课程还介绍了套接字

## 套接字 Socket

网络编程中常用
