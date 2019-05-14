# redo日志是什么

redo日志格式
通过上边的内容我们知道，redo日志本质上只是记录了一下事务对数据库做了哪些修改。 设计InnoDB的大叔们针对事务对数据库的不同修改场景定义了多种类型的redo日志，但是绝大部分类型的redo日志都有下边这种通用的结构：

![结构]()

各个部分的详细释义如下：

* type：该条redo日志的类型。

在MySQL 5.7.21这个版本中，设计InnoDB的大叔一共为redo日志设计了53种不同的类型，稍后会详细介绍不同类型的redo日志。

* space ID：表空间ID。

* page number：页号。

* data：该条redo日志的具体内容。



# 参考文献

* [redo日志（上）](https://juejin.im/book/5bffcbc9f265da614b11b731/section/5c238f0f518825741e7c3315)

