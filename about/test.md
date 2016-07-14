title: lsy
speaker: lsy
url: https://github.com/ksky521/nodePPT
transition: slide
theme: dark
files: /js/demo.js,/css/demo.css
date: 2016/07/14

[slide]

# lsy
## 演讲者：刘善永

[slide]

# 目录 {:&.flexbox.vleft}
## 上线时出现的问题
## 进程的介绍
## 信号的介绍
## 现象分析
## 解决措施及原理

[slide]

## 上线时出现的问题

[slide]

## 
##

[slide]

## 什么时信号
信号是很短的消息，可以发送给一个进程或者一组进程。
## 信号的目的
1. 让进程知道已经发生了一个特定的事件
2. 强迫进程执行它自己代码中的信号处理程序
## 信号的产生
内核更新目标进程的数据结构以表示一个新信号已被发送
## 信号的传递
内核强迫目标进程通过以下方式对信号作出反应：或改变目标进程的执行状态，或开始执行信号处理函数，或者两者都是。
## 挂起信号
已经产生还没有传递的信号被称为挂起信号。

![信号处理相关的数据结构图](https://raw.githubusercontent.com/buptlsy/images/master/redis-hash.png)

[slide]

![信号处理流程图](https://raw.githubusercontent.com/buptlsy/images/master/redis-hash.png)

[slide]


