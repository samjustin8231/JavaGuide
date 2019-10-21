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

# Optional常用api

## ifPresent

```
public void ifPresent(Consumer<? super T> consumer) {
        if (value != null)
            consumer.accept(value);
    }
```
如果 Optional 中有值，则对该值调用 consumer.accept，否则什么也不做。

所以对于引言上的例子，我们可以修改为：

```
Optional<User> user = Optional.ofNullable(getUserById(id));
user.ifPresent(u -> System.out.println("Username is: " + u.getUsername()));
```

## orElse
    
```
public T orElse(T other) {
        return value != null ? value : other;
    }
```
如果 Optional 中有值则将其返回，否则返回 orElse 方法传入的参数。

```

User user = Optional
        .ofNullable(getUserById(id))
        .orElse(new User(0, "Unknown"));
        
System.out.println("Username is: " + user.getUsername());
```

## orElseGet

```
public T orElseGet(Supplier<? extends T> other) {
        return value != null ? value : other.get();
    }
```
orElseGet 与 orElse 方法的区别在于，orElseGet 方法传入的参数为一个 Supplier接口的实现 —— 当 Optional 中有值的时候，返回值；当 Optional 中没有值的时候，返回从该 Supplier 获得的值。

```
User user = Optional
        .ofNullable(getUserById(id))
        .orElseGet(() -> new User(0, "Unknown"));
        
        System.out.println("Username is: " + user.getUsername());
```

## map
    
```
public<U> Optional<U> map(Function<? super T, ? extends U> mapper) {
    Objects.requireNonNull(mapper);
    if (!isPresent())
        return empty();
    else {
        return Optional.ofNullable(mapper.apply(value));
    }
}
```
    
如果当前 Optional 为 Optional.empty，则依旧返回 Optional.empty；否则返回一个新的 Optional，该 Optional 包含的是：函数 mapper 在以 value 作为输入时的输出值。
    

```
String username = Optional.ofNullable(getUserById(id))
                        .map(user -> user.getUsername())
                        .orElse("Unknown")
                        .ifPresent(name -> System.out.println("Username is: " + name));
```
而且我们可以多次使用 map 操作：

```

Optional<String> username = Optional.ofNullable(getUserById(id))
                                .map(user -> user.getUsername())
                                .map(name -> name.toLowerCase())
                                .map(name -> name.replace('_', ' '))
                                .orElse("Unknown")
                                .ifPresent(name -> System.out.println("Username is: " + name));
```


## filter
    
```
public Optional<T> filter(Predicate<? super T> predicate) {
        Objects.requireNonNull(predicate);
        if (!isPresent())
            return this;
        else
            return predicate.test(value) ? this : empty();
    }
```
    
filter 方法接受一个 Predicate 来对 Optional 中包含的值进行过滤，如果包含的值满足条件，那么还是返回这个 Optional；否则返回 Optional.empty。

```
Optional<String> username = Optional.ofNullable(getUserById(id))
                                .filter(user -> user.getId() < 10)
                                .map(user -> user.getUsername());
                                .orElse("Unknown")
                                .ifPresent(name -> System.out.println("Username is: " + name));
```

# 使用示例

Optional使用示例

当 user.isPresent() 为真, 获得它关联的 orders的映射集合, 为假则返回一个空集合时, 我们用上面的 orElse, orElseGet 方法都乏力时, 那原本就是 map 函数的责任, 我们可以这样一行：

```
return user.map(u -> u.getOrders()).orElse(Collections.emptyList())
```
 
//上面避免了我们类似 Java 8 之前的做法

```

if(user.isPresent()) {
  return user.get().getOrders();
} else {
  return Collections.emptyList();
}

```

map 是可能无限级联的, 比如再深一层, 获得用户名的大写形式：

```
return user.map(u -> u.getUsername())
           .map(name -> name.toUpperCase())
           .orElse(null);
```

以前的做法：

```
User user = .....
if(user != null) {
  String name = user.getUsername();
  if(name != null) {
    return name.toUpperCase();
  } else {
    return null;
  }
} else {
  return null;
}
```
filter() :如果有值并且满足条件返回包含该值的Optional，否则返回空Optional。

```
Optional<String> longName = name.filter((value) -> value.length() > 6);  
System.out.println(longName.orElse("The name is less than 6 characters")); 
```



# 参考文献
- [Java8之熟透Optional](https://juejin.im/post/5d834da15188250150110e82)
