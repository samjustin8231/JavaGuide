
# RPC与本地调用的区别

* RPC远程调用：一般可以跨平台通讯，采用http协议。因为http协议底层使用socket技术，只要你的语言支持socket技术，就可以相互进行通讯。比如：java语言开发的接口，http协议，然后让别人可以使用C#语言也可以调用java写的接口(C#语言也支持socket技术)。开放平台大多都是http协议，因为能支持更多的语言通讯。

* 本地调用：只能支持java语言与java语言开发使用虚拟机与虚拟机之间的通讯，本地通讯使用rmi。


# 常见 RPC 框架
gRPC
Thrift
Dubbo


# 参考
- [基于Netty实现简易RPC框架
](https://juejin.im/post/5d98aebd51882560a8306915)