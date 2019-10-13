# @SpringBootApplication

@SpringBootApplication// Spring Boot项目的核心注解，主要目的是开启自动配置

@SpringBootApplication 是 Spring Boot 的核心注解，它是一个组合注解，该注解组合了：@Configuration、@EnableAutoConfiguration、@ComponentScan； 若不是用 @SpringBootApplication 注解也可以使用这三个注解代替。

其中，@EnableAutoConfiguration 让 Spring Boot 根据类路径中的 jar 包依赖为当前项目进行自动配置，例如，添加了 spring-boot-starter-web 依赖，会自动添加 Tomcat 和 Spring MVC 的依赖，那么 Spring Boot 会对 Tomcat 和 Spring MVC 进行自动配置。
Spring Boot 还会自动扫描 @SpringBootApplication 所在类的同级包以及下级包里的 Bean ，所以入口类建议就配置在 grounpID + arctifactID 组合的包名下（这里为 cn.wmyskxz.springboot 包）

# 取消自动配置

当自动配置的东西一多了，就容易出问题，上篇中最后出现的"Cannot determine embedded database driver class for database type NONE"错误，就是因为springboot启动时会自动注入数据源和配置jpa，所以我们就需要取消Spring Boot的部分自动配置。至于取消自动配置的方式也相当简单，就是对@SpringBootApplication注解的exclude进行赋值即可，如：

```
@SpringBootApplication(exclude={DataSourceAutoConfiguration.class,HibernateJpaAutoConfiguration.class})
@Controller
public class HelloApplication {
    ...
}
```


# starter pom
Spring Boot为我们提供了简化企业级开发绝大多数场景的start pom（类似于组件），只要使用了对应的starter pom，Spring Boot就会为我们提供自动配置的Bean。

# 使用Xml配置文件
常规开发中，java配置方式已经可以完全取代xml配置方式了，但有时我们并不想使用java配置，而是继续沿用之前的xml配置方式，或者出于其他原因，一定要在xml中进行配置，那该怎么办呢，很简单，使用@ImportResource注解即可：

```
@ImportResource(value = {"classpath:xxx1.xml","classpath:xxx2.xml"})

```

# 全局配置文件
Spring Boot项目使用一个全局的配置文件application.properties或者是application.yml，在resources目录下或者类路径下的/config下，一般我们放到resources下。
我们知道Spring Boot会根据在pom.xml中依赖的jar包进行自动配置，当我们要对这些jar包对应的框架进行配置又该怎么办呢，没错，可以在全局配置文件（application.properties或者是application.yml）中进行配置，如tomcat的端口配置等。

## tomcat端口配置
server.port=8888

## 修改Spring MVC拦截路径规则
默认Spring MVC拦截路径规则是/，如果要修改成*.html的话，可以在全局配置文件中进行如下设置：

server.servlet-path=*.html

## 视图解析器配置
一样的，Spring Boot也可以通过全局配置文件对视图解析器进行配置：

spring.mvc.view.prefix=/WEB-INF/views/
spring.mvc.view.suffix=.jsp

## 日志输出
Spring Boot对各种日志框架都做了支持，我们可以通过配置来修改默认的日志的配置：

#设置日志级别
logging.level.org.springframework=DEBUG


# 参考

[SpringBoot详解（二）-Spring Boot的核心](https://juejin.im/post/59a6defb6fb9a0248e5cd3e5)