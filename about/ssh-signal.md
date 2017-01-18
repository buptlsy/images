title: lsy
speaker: lsy
url: https://github.com/ksky521/nodePPT
transition: move
theme: dark
files: /js/demo.js,/css/demo.css
date: 2016/07/14

[slide]

## 隐藏在“ssh关闭后，程序不再运行”的背后知识
## 刘善永

[slide]

# 目录 {:&.flexbox.vleft}
## 上线时出现的问题
## 用户态和内核态的介绍
## 进程的介绍
## 信号的介绍
## 现象分析
## 解决措施及原理

[slide]

## 上线时出现的问题

[slide]

# 用户态和内核态介绍 {:&.flexbox.vleft}
![linux架构图](https://raw.githubusercontent.com/buptlsy/images/gh-pages/linux%3Aunix%E6%9E%B6%E6%9E%84.png)

[slide]

# 用户态和内核态介绍 {:&.flexbox.vleft}
![linux内核态和用户态](https://raw.githubusercontent.com/buptlsy/images/gh-pages/linux-User_kernel.png)

[slide]

# 用户态和内核态介绍 {:&.flexbox.vleft}
## 用户态和内核态的转换
- 系统调用
- 异常。比如缺页异常
- 外围设备的中断。例如外围设备完成用户的请求操作后，会像cpu发出信号。

[slide]

# 进程介绍 {:&.flexbox.vleft}
## 进程、进程组、会话 

<font size=4>进程属于一个进程组，进程组属于一个会话，会话可以有也可以没有控制终端</font>

## 会话
### 会话首进程

<font size=4>创建会话的进程，该进程是会话的领导进程，其PID＝SID</font>

### 会话的构成

<font size =4>一个或者多个进程组的集合。包括一个前台进程组，多个后台进程组。当有控制终端输入输出时，会传递给该会话的前台进程组。</font>

[slide]

# 进程介绍 {:&.flexbox.vleft}
## 进程组

<font size=4>一个或多个进程的集合。（ps -ao pid,pgid,ppid,comm | cat) </font>

### 进程组组长

<font size=4>PID和PGID相等的进程。</font>

### 前台进程组

<font size=4>该进程组中的进程能够向终端设备进行读、写操作的进程组。</font>

### 后台进程组

<font size=4>该进程组中的进程能够向终端进行写，但是当试图读终端设备时，将会收到SIGTTIN信号，并停止。
后台进程组可以同时存在多个。</font>

[slide]

# 进程介绍 {:&.flexbox.vleft}
## 进程
### 僵尸进程

<font size=4>一个进程使用fork创建子进程，如果子进程退出，而父进程并没有调用wait或者waitpid获取子进程的状态信息，那么子进程的描述符仍然保存在系统中。</font>

### 孤儿进程

<font size=4>一个父进程退出，而它的一个或多个子进程还在运行，那这些子进程就成为孤儿进程。孤儿进程将有init进程收养，并由init进程完成状态收集工作。</font>

[slide]

# 信号介绍 {:&.flexbox.vleft}
## 什么是信号

<font size=4>信号是很短的消息，可以发送给一个进程或者一组进程。</font>

## 信号的目的

- <font size=4>
让进程知道已经发生了一个特定的事件
- 强迫进程执行它自己代码中的信号处理程序</font>

## 信号的产生

<font size=4>内核更新目标进程的数据结构以表示一个新信号已被发送</font>

## 信号的传递

<font size=4>内核强迫目标进程通过以下方式对信号作出反应：或改变目标进程的执行状态，或开始执行信号处理函数，或者两者都是。</font>

## 挂起信号

<font size=4>已经产生还没有传递的信号被称为挂起信号。</font>

[slide]

# 信号介绍 {:&.flexbox.vleft}
## 信号处理相关的数据结构图

![信号处理相关的数据结构图](https://raw.githubusercontent.com/buptlsy/images/master/signal-handle-struct.jpg)

[note]
信号描述符signal 字段，用来跟踪共享挂起信号
信号处理程序描述符，用来描述每个信号必须怎样被线程组处理
共享挂起信号队列，位于信号描述符的shared_pending字段，存放整个线程组的挂起信号
私有挂起信号队列，位于进程描述符的pending字段，存放特定进程的挂起信号
sigaction:
sa_handler:指定要执行操作的类型
sa_flags:是一个标志集，指定必须怎样处理
sa_mask:指定当前运行信号处理程序时要屏蔽的信号
[/note]

[slide]

# 信号介绍 {:&.flexbox.vleft}
## 信号捕获流程图

![信号处理流程图](https://raw.githubusercontent.com/buptlsy/images/master/signal-handle-proccess.jpg)

[note]
当中断或者异常发生时，进程切换到内核态。内核执行do_signal函数。这个函数又依次处理信号（通过调用handle_signal()）和建立用户态堆栈(通过调用setup_frame()或setup_rt_frame())。
当处理程序终止时，setup_frame()或者setup_rt_frame()函数放在用户态堆栈中的返回代码就执行。这个代码调用sigreturn()或rt_sigrenturn()系统调用，相应的服务例程把正常程序的用户态堆栈硬件上下文拷贝到内核堆栈，并把用户态堆栈恢复到它原来的状态(通过调用restore_sigcongtext()).当这个系统调用结束时，普通进程就因此能恢复自己的执行。
[/note]

[slide]

# 现象分析 {:&.flexbox.vleft}

<font size=4>一旦ssh关闭，程序就不再运行了。</font>

## 解释

<font size=4>当网络断开或者终端窗口关闭后，也即ssh断开后，会话的进程组会收到 sighup信号，导致会话期的所有进程退出。</font>

## 元凶 sighup信号

<font size=4>
If the process receiving SIGHUP is a Unix shell, then as part of job control it will often intercept the signal and ensure that all stopped processes are continued before sending the signal to child processes (more precisely, process groups, represented internally be the shell as a "job"), which by default terminates them.</font>

[slide]

# 解决措施 {:&.flexbox.vleft}
## 解决措施
### ctrl-c

- <font size=4>
原因：ctrl-c 后，会触发sigint信号，而sigint只会发给前台进程组，不会影响后台进程组的正常运行。
- 参考：https://www.cons.org/cracauer/sigint.html</font>

### nohup

- <font size=4>用法：在命令的前面加上 nohup 即可。如 nohup ping www.baidu.com
- 原因：nohup可以忽略sighup信号。</font>

[slide]

## thx
