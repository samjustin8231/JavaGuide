# happen-before


Happens-before 规则主要用来约束两个操作，两个操作之间具有 happens-before 关系, 并不意味着前一个操作必须要在后一个操作之前执行，happens-before 仅仅要求前一个操作(执行的结果)对后一个操作可见,   (the first is visible to and ordered before the second)

> 程序顺序性规则

一个线程中的每个操作, happens-before 于该线程中的任意后续操作
第一感觉这个原则是一个在理想状态下的"废话"，并且和上面提到的会出现重排序的情况是矛盾的，注意这里是一个线程中的操作，其实隐含了「as-if-serial」语义: 说白了就是只要执行结果不被改变，无论怎么"排序"，都是对的

这个规则是一个基础规则，happens-before 是多线程的规则，所以要和其他规则约束在一起才能体现出它的顺序性，别着急，继续向下看


> volatile变量规则

对一个 volatile 域的写, happens-before 于任意后续对这个 volatile 域的读

我将上面的程序添加两行代码作说明:


todo 

> 传递性规则


如果 A happens-before B, 且 B happens-before C, 那么 A happens-before C 直接上图说明一下上面的例子


todo 

# 参考文献

- [可见性有序性，Happens-before来搞定](https://juejin.im/post/5d80251d518825491b72419c)
