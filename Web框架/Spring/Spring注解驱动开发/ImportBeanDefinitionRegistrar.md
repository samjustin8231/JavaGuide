# ImportBeanDefinitionRegistrar

spring官方就是用这种方式，实现了@Component、@Service等注解的动态注入机制。定义一个ImportBeanDefinitionRegistrar的实现类，然后在有@Configuration注解的配置类上使用@Import导入

这里我们用一个简单的例子，我们需要实现类似@Componet的功能，添加了@Mapper注解的类会被自动加入到spring容器中。



#  参考

- [借助ImportBeanDefinitionRegistrar接口实现bean的动态注入](https://www.jianshu.com/p/2b993ced6a4c)
- [ImportBeanDefinitionRegistrar动态注册bean](https://blog.csdn.net/jiachunchun/article/details/94569246)