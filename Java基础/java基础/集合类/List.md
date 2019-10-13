# List


# 线程安全问题

```
List<String> list = new Vector<>();//Vector线程安全
List<String> list = Collections.synchronizedList(new ArrayList<>());//使用辅助类
List<String> list = new CopyOnWriteArrayList<>();//写时复制，读写分离

```