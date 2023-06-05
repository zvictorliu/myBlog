---
title: 进程概念
---

# 进程概念

## 1.1 进程的状态

进程具有多种运行状态

最基本的：

- 运行态：正在处理机上运行，是真正的运行，进程本身就是动态的也可以理解为”运行“，此处可以理解为CPU正在处理
- 就绪态：即将送入处理机运行，其它都已准备好，多个进程会排成就绪队列
- 阻塞态：进程等待某一事件（不包括等待处理机，那是就绪态）

另外还有细分的两种状态：

- 创建态：进程正在被创建，这个过程是多个步骤的显得比较漫长，所以有这个状态，即准备中
- 结束态：正在从系统中消失

<img src="https://cdn.jsdelivr.net/gh/zvictorliu/typoraPics@main/img/image-20230526194635294.png" alt="image-20230526194635294" style="zoom:67%;" />

## 1.2 进程的表示

一定要注意 *进程不是PCB*，进程实体是 <u>PCB + 程序段 + 数据段</u>

进程控制块 (Process Control Block, PCB) ：多少感觉像哈希值一样，记录了进程的信息

PCB是进程存在的唯一标志，创建了PCB，进程才算是”活的“

在linux中，它就是一个C语言的结构体：


{{< details "linux PCB"  >}}
```c
pid t pid; /* process identifier */ 
long state; /* state of the process */ 
unsigned int time slice /* scheduling information */
struct task_struct *parent; /* this process’s parent */ 
struct list_head children; /* this process’s children */ 
struct files_struct *files; /* list of open files */ 
struct mm_struct *mm; /* address space of this pro */
```

{{< /details >}}



PCB具体记录了哪些信息这个太多了，主要是这些：
{{< details "PCB通常包含的内容" >}}
<img src="https://cdn.jsdelivr.net/gh/zvictorliu/typoraPics@main/img/image-20230526195844759.png" alt="image-20230526195844759" style="zoom:67%;" />
{{< /details >}}



其中有处理机相关信息，也就是这个程序运行的上下文，它是被保存在PCB中的，可见PCB的意义

并且，程序和数据的内存基地址也在PCB当中，也要靠PCB来找到

这些信息会让每个进程自己认为自己独立使用CPU，看上去用于整个内存空间

## 1.3 进程控制

对于进程的控制我们不希望它被打断，所以设计了`原语`，执行期间不许中断

### 1.3.1 创建

一般来说是一个进程创建另一个进程

创建原语的工作：

- 为新进程分配一个唯一的进程标识号（返回给父进程），并申请一个空白的PCB（PCB总数是有限的）
  - 如果未能申请成功，创建失败
- 为新进程分配资源：可以从操作系统单独获得，也可以只从父进程那里获得
  - 如果资源不足<u>并不是创建失败</u>，而是处于创建态等待
- 初始化PCB
- 如果就绪队列能够接纳，就插入，等待被调度运行

### 1.3.2 进程终止

这里终止是删除，而不是停止暂停运行

终止可能是主动的（正常运行结束），也可能是被动的

终止原语的工作：

- 根据pid找到对应的PCB，读取状态
- 如果正处于运行态，就应该停止执行，处理机资源释放给其它进程
- 如果该进程有子孙进程则需要先去终止它们 (儿子永远是儿子，还是会被爸爸管着)
- 归还资源
- 从队列中删除PCB

`僵尸进程`：资源被释放后，PCB没有被删除，仍然占用着进程表的资源

### 1.3.3 进程阻塞和唤醒

正在运行态的进程需要等待某个响应，就会调用阻塞原语变为阻塞态

当响应出现时，对方进程（例如提供数据的进程）会调用唤醒原语进行唤醒

所以说，自己阻塞了，需要靠对方来将它唤醒，成对出现

这个切换的过程有`上下文切换`，会保存堆栈、寄存器、计数器值等信息



控制的过程是操作系统在内核态执行的

