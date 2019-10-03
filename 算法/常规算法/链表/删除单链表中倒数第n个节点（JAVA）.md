# 解法一

思路

- 链表中有字段size是节点总个数
- 倒数第lastNum个节点就是正数第size-lastNum+1个节点，index=size-lastNum
删除操作需要拿到倒数第lastNum+1个节点来完成，即- 正数第size-lastNum个节点，index=size-lastNum-1
- 把该节点的next设置为该节点的next.next即可

# 解法二

- 需要找的节点和上述的一致，如果要删掉倒数第lastNum个节点，需要拿到倒数第lastNum+1个节点
- 网络上的快慢指针法就是在定位该节点时用了一种很巧妙的方法
- 先让快指针从header开始向后移动lastNum步，这时候快指针在index=lastNum位置
- 接着慢指针也从header开始，和快指针以相同步长1向后移动，循环条件是快指针的current.getNext() != null。之所以不使用current != null是因为这样会拿到被删除节点本身，而这里需要拿到被删除节点的前一个节点





# 参考文献

- [删除单链表中倒数第n个节点（JAVA）
](https://blog.csdn.net/cedarjo/article/details/88556280)