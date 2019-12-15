# Bean
在配置类上打个一个 @Configuration 注解，表示声明该类为 Spring 的配置类。在创建一个方法，方法返回对象即我们要创建的对象 Person ， 返回该对象的实例。在方法上打上注解 @Bean即表示声明该方法返回的实例是受 Spring 管理的 Bean。

# @Bean 注解的属性解析
## value,name
name 和 value 两个属性是相同的含义的， 在代码中定义了别名。 为 bean 起一个名字，如果默认没有写该属性，那么就使用方法的名称为该 bean 的名称

## autowire
装配方式 有三个选项
Autowire.NO (默认设置)
Autowire.BY_NAME
Autowire.BY_TYPE
指定 bean 的装配方式， 根据名称 和 根据类型 装配， 一般不设置，采用默认即可

## initMethod
bean 的初始化方法， 直接指定方法名称即可，不用带括号
```
@Configuration
public class BeanConfig {

    @Bean(initMethod = "init")
    public Person person() {
        return new Person("SJL01", 20);
    }

}
```
## destroyMethod
bean 的销毁方法， 在调用 IoC 容器的 close() 方法时，会执行到该属性指定的方法。不过，只是单实例的 bean 才会调用该方法，如果是多实例的情况下，不会调用该方法

```
@Bean(destroyMethod = "destroy")
public Person person() {
    return new Person("SJL01", 20);
}
```


# 参考

- [@Bean 的用法](https://www.cnblogs.com/feiyu127/p/7700090.html)