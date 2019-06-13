# 前言
Spring版本: Spring5-0-3

Spring容器就相当于一个大的水桶，里面装着很多水——bean对象。bean对象就是一个普通的pojo对象。这里有一个很重要的概念，就是IOC——Invertion of Control，即反转控制。通俗点将就是将创建并且绑定数据bean的权利赋予给了Spring容器（或Spring IOC容器，下文Spring容器代指Spring IOC容器），在bean生成或初始化的时候，Spring容器就会将数据注入到bean中，又或者通过将对象的引用注入到对象数据域中的方式来注入对方法调用的依赖。



# 参考文献

- [深入Spring源码系列（二）——深入Spring容器，通过源码阅读和时序图来彻底弄懂Spring容器（上）](https://blog.csdn.net/coderbruis/article/details/85940756)