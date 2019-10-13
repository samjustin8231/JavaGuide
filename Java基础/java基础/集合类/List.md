# List


<<<<<<< HEAD
# 线程安全问题

```
List<String> list = new Vector<>();//Vector线程安全
List<String> list = Collections.synchronizedList(new ArrayList<>());//使用辅助类
List<String> list = new CopyOnWriteArrayList<>();//写时复制，读写分离

```
=======

# 参考文献

- [别再这样使用List了，会坑到你哭！](https://juejin.im/post/5d6a8ef8e51d4561d106cc2f)
>>>>>>> b85ff90be73f4ccd4d4dd19181955364e89fc73c
