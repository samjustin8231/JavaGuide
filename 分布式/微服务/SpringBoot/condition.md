

Spring Boot 中的自动化配置确实够吸引人，甚至有人说 Spring Boot 让 Java 又一次焕发了生机，这话虽然听着有点夸张，但是不可否认的是，曾经臃肿繁琐的 Spring 配置确实让人感到头大，而 Spring Boot 带来的全新自动化配置，又确实缓解了这个问题。
你要是问这个自动化配置是怎么实现的，很多人会说不就是 starter 嘛！那么 starter 的原理又是什么呢？松哥以前写过一篇文章，介绍了自定义 starter：

[徒手撸一个 Spring Boot 中的 Starter ，解密自动化配置黑魔法！](https://juejin.im/post/5d88715a6fb9a06ace528e13)

这里边有一个非常关键的点，那就是条件注解，甚至可以说条件注解是整个 Spring Boot 的基石。
条件注解并非一个新事物，这是一个存在于 Spring 中的东西，我们在 Spring 中常用的 profile 实际上就是条件注解的一个特殊化。
想要把 Spring Boot 的原理搞清，条件注解必须要会用，因此今天松哥就来和大家聊一聊条件注解。

# 定义
Spring4 中提供了更加通用的条件注解，让我们可以在满足不同条件时创建不同的 Bean，这种配置方式在 Spring Boot 中得到了广泛的使用，大量的自动化配置都是通过条件注解来实现的，查看松哥之前的 Spring Boot 文章，凡是涉及到源码解读的文章，基本上都离不开条件注解：


todo


# 参考文献

- [Spring Boot2 系列教程(七)理解自动化配置的原理](https://juejin.im/post/5d88715a6fb9a06ace528e13)

