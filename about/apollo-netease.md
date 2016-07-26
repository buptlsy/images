title: apollo 产品介绍
speaker: lsy
transition: cards
files: /js/demo.js,/css/demo.css

[slide]

# apollo 产品介绍
## 演讲者：刘善永

[slide]

# 目录 {:&.flexbox.vleft}
## 产品初衷
## 产品架构
## 系统架构

[slide]

# 产品初衷

[slide]

## 最近训练出的几个模型，该如何选择？
## 如何证明机器学习模型优于目前人工设置的模型？
## 几个新设计的产品方案，该如何选择？
## 怎样衡量新上功能的效果？
## 这个功能先从5%开始观测，效果正向的话逐步全量
## 想测试一下西二旗附近用户对某功能的接受程度

[slide]

# 定向投放


[slide]

# 灰度发布
![灰度发布](https://raw.githubusercontent.com/buptlsy/images/gh-pages/apollo-huidu.png)
[slide]

# AB测试
![AB测试](https://raw.githubusercontent.com/buptlsy/images/gh-pages/apollo-AB.png)

[slide]

# 产品架构
![产品架构](https://raw.githubusercontent.com/buptlsy/images/gh-pages/apollo-chanpin-struct.png)

[slide]

# 系统架构
![系统架构](https://raw.githubusercontent.com/buptlsy/images/gh-pages/apollo-server-struct.png)

[slide]

# 流量分发机制

- 幂等：保证进入某组的用户不会进入其他组
- 均匀化：防止被灰的是相同的人群
- 并行实验：能够支持多组实验并行运行
- 互斥实验：能够支持多组实验互斥


[slide]

# 扩量

![扩量产品图](https://raw.githubusercontent.com/buptlsy/images/gh-pages/apollo-kuoliang.png)

[slide]


