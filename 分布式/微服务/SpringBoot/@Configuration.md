# @Configuration 和 @Bean
Spring的java配置方式是通过@Configuration 和 @Bean这两个注释实现的：

@Configuration 作用于类上，相当于一个xml配置文件
@Bean 作用于方法上，相当于xml配置中的 < bean > 标签

```
@Configuration// 通过注解来表明该类是一个Spring的配置，相当于一个xml文件
public class SpringConfig {
    @Bean// 这里要注意,方法名"getUserDao"将作为UserDao在容器中的id
    public UserDao getUserDao() {
        return new UserDao();
    }
}
```



# 参考文献

- [spring4.0之二：@Configuration的使用](https://www.cnblogs.com/duanxz/p/7493276.html)
- [SpringBoot详解（一）-快速入门](https://juejin.im/post/59a6dbf451882511264e93b6)