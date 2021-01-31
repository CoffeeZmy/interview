# 面试题——System Design

## 1.API Rate Limiting：给定一个公共API，限制每个用户每秒只能调用1000次，如何实现？



## 2.分布式id：如何设计一个分布式ID生成器(Distributed ID Generator)，并保证ID按时间粗略有序？

https://github.com/soulmachine/system-design/blob/master/cn/distributed-id-generator.md



## 3.设计一个key-value存储引擎

https://github.com/soulmachine/system-design/blob/master/cn/key-value-store.md



## 4.定时任务调度器

https://github.com/soulmachine/system-design/blob/master/cn/task-scheduler.md



## 5.实时输出最近一个小时内访问频率最高的10个IP

https://github.com/soulmachine/system-design/blob/master/cn/top-k-frequent-ip-in-one-hour.md



## 6.如何设计一个短网址服务(TinyURL)？

https://github.com/soulmachine/system-design/blob/master/cn/tinyurl.md



## 7.请实现一个搜索引擎(Design a search engine)



## 8.请设计一个信息流(news feed)。例如Facebook用户首页的信息流，微博用户的信息流



## 9.请设计一个load balancer



## 10.请实现PageRank算法(Implement PageRank)，注意要分布式



## 11.如何设计一个文件系统缓存

在 Linux 操作系统中，当应用程序需要读取文件中的数据时，操作系统先分配一些内存，将数据从磁盘读入到这些内存中，然后再将数据传给应用程序；当需要往文件中写数据时，操作系统先分配内存接收用户数据，然后再将数据从内存写到磁盘上。文件 Cache 管理指的就是对这些由操作系统分配，并用来存储文件数据的内存的管理



## 12.字节System Design

### 1.设计一个海量的评论系统



### 2.设计一个微信朋友圈系统，列出主要的表结构，只需要实现一些基础的功能，比如聊天列表等



### 3.设计一个长链接转短链接，需考虑高并发



### 4.设计一个海量日志写入系统





