# 前言


我们从一个简单的用例开始。在 Java 8 之前，任何访问对象方法或属性的调用都可能导致 NullPointerException：

```
String isocode = user.getAddress().getCountry().getIsocode().toUpperCase();
```

在这个小示例中，如果我们需要确保不触发异常，就得在访问每一个值之前对其进行明确地检查：

```
if (user != null) {
    Address address = user.getAddress();
    if (address != null) {
        Country country = address.getCountry();
        if (country != null) {
            String isocode = country.getIsocode();
            if (isocode != null) {
                isocode = isocode.toUpperCase();
            }
        }
    }
}

```
你看到了，这很容易就变得冗长，难以维护。


# Optional API使用

以下为Optional<T>的正确使用方式：

- 存在即返回, 无则提供默认值

```
return user.orElse(null);  //而不是 return user.isPresent() ? user.get() : null;
return user.orElse(UNKNOWN_USER);
```


- 存在即返回, 无则由函数来产生

```
return user.orElseGet(() -> fetchAUserFromDatabase()); 

//而不要 return 
user.isPresent() ? user: fetchAUserFromDatabase();

```


- 存在才对它做点什么

```
user.ifPresent(System.out::println);
 
//而不要下边那样
if (user.isPresent()) {
  System.out.println(user.get());
}
```




# 参考文献
[aa](https://www.jianshu.com/p/c169ddd34903)
