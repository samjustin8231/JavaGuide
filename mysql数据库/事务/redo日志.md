# redo日志是什么

redo日志格式
通过上边的内容我们知道，redo日志本质上只是记录了一下事务对数据库做了哪些修改。 设计InnoDB的大叔们针对事务对数据库的不同修改场景定义了多种类型的redo日志，但是绝大部分类型的redo日志都有下边这种通用的结构：



各个部分的详细释义如下：

* type：该条redo日志的类型。

在MySQL 5.7.21这个版本中，设计InnoDB的大叔一共为redo日志设计了53种不同的类型，稍后会详细介绍不同类型的redo日志。

* space ID：表空间ID。

* page number：页号。

* data：该条redo日志的具体内容。

# Redo 的作用
Redo log的主要作用是用于数据库的崩溃恢复

# Redo 的组成
Redo log可以简单分为以下两个部分：

一是内存中重做日志缓冲 (redo log buffer),是易失的，在内存中
二是重做日志文件 (redo log file)，是持久的，保存在磁盘中




# 参考文献

* [redo日志（上）](https://juejin.im/book/5bffcbc9f265da614b11b731/section/5c238f0f518825741e7c3315)
* [浅析MySQL事务中的redo与undo](https://juejin.im/post/5c3c5c0451882525487c498d)
* [我所理解的MySQL中的DML语句和日志的关系](https://juejin.im/post/5dad657df265da5bb977dd00)

