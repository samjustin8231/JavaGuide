# 自定义拦截器
增加一个拦截器，需要通过继承WebMvcConfigurerAdapter然后重写父类中的方法进行扩展。

```
@Configuration
public class MySpringMvcConfig extends WebMvcConfigurerAdapter {

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        HandlerInterceptor handlerInterceptor = new HandlerInterceptor() {
            @Override
            public boolean preHandle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o) throws Exception {
                System.out.println("自定义拦截器。。。");
                return true;
            }

            @Override
            public void postHandle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, ModelAndView modelAndView) throws Exception {

            }

            @Override
            public void afterCompletion(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception e) throws Exception {

            }
        };
		// 添加拦截器并设置拦截规则
        registry.addInterceptor(handlerInterceptor).addPathPatterns("/**");
    }
}

```

# 自定义消息转化器

自定义消息转化器有两种实现方式，一种是@Bean方式，另一种是自定义拦截器。
## @Bean方式
只需要在@Configuration的类中添加消息转化器的@bean加入到Spring容器，就会被Spring Boot自动加入到容器中。
// spring boot默认就有消息转化器，其编码格式为utf-8

```
@Bean
public StringHttpMessageConverter stringHttpMessageConverter() {
    StringHttpMessageConverter stringHttpMessageConverter = new StringHttpMessageConverter(Charset.forName("UTF-8"));
    return stringHttpMessageConverter;
}
```

## 自定义拦截器方式
WebMvcConfigurerAdapter的功能很强大，除了可以配置拦截器外，还可以配置消息转换器。

```
@Configuration
public class MySpringMvcConfig extends WebMvcConfigurerAdapter {

    @Override
    public void configureMessageConverters(List<HttpMessageConverter<?>> converters) {
        StringHttpMessageConverter stringHttpMessageConverter = new StringHttpMessageConverter(Charset.forName("UTF-8"));
        converters.add(stringHttpMessageConverter);
    }
}
```

# 读取外部的配置文件

```
@Configuration
@PropertySource(value = { "classpath:jdbc.properties", "classpath:base.properties" }, ignoreResourceNotFound = true)
public class 任意类 {

}

```

# Druid DataSource的配置
Druid提供了一个高效、功能强大、可扩展性好的数据库连接池，常用于替换DBCP和C3P0。但在Spring Boot上配置比较“恶心”，这里就简单的贴出个人参照网上的配置代码，不必深究。
1）引入依赖
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.0.11</version>
</dependency>
复制代码
2）jdbc.properties
项目中一般会创建一个jdbc.properties文件来记录数据库的连接信息。

```
#MySQL
jdbc.url=jdbc:mysql://127.0.0.1:3306/dbxxx?useUnicode=true&characterEncoding=utf-8
jdbc.username=root
jdbc.password=123456
#Druid
jdbc.initialSize=0
jdbc.minIdle=0
jdbc.maxActive=150
```

3）配置Druid数据源
建议将配置Druid数据源的操作放在@SpringBootApplication注解的类中。

```
@SpringBootApplication
@Configuration
@PropertySource(value = {"classpath:jdbc.properties"})
public class MyWebApplication{

    @Value("${jdbc.url}")
    public String jdbcUrl;
    @Value("${jdbc.username}")
    public String jdbcUsername;
    @Value("${jdbc.password}")
    public String jdbcPassword;
    @Value("${jdbc.initialSize}")
    public int jdbcInitialSize;
    @Value("${jdbc.minIdle}")
    public int jdbcMinIdle;
    @Value("${jdbc.maxActive}")
    public int jdbcMaxActive;

    @Bean
    public ServletRegistrationBean druidServletRegistrationBean() {
        ServletRegistrationBean servletRegistrationBean = new ServletRegistrationBean();
        servletRegistrationBean.setServlet(new StatViewServlet());
        servletRegistrationBean.addUrlMappings("/druid/*");
        return servletRegistrationBean;
    }

    @Bean
    public FilterRegistrationBean duridFilterRegistrationBean() {
        FilterRegistrationBean filterRegistrationBean = new FilterRegistrationBean();
        filterRegistrationBean.setFilter(new WebStatFilter());
        HashMap<String, String> initParams = new HashMap<>();
        // 设置忽略请求
        initParams.put("exclusions", "*.js,*.gif,*.jpg,*.bmp,*.png,*.css,*.ico,/druid/*");
        filterRegistrationBean.setInitParameters(initParams);
        filterRegistrationBean.addUrlPatterns("/*");
        return filterRegistrationBean;
    }

    @Bean(initMethod = "init", destroyMethod = "close")
    public DruidDataSource druidDataSource() {
        DruidDataSource druidDataSource = new DruidDataSource();
        druidDataSource.setUrl(jdbcUrl);
        druidDataSource.setUsername(jdbcUsername);
        druidDataSource.setPassword(jdbcPassword);
        druidDataSource.setInitialSize(jdbcInitialSize);
        druidDataSource.setMinIdle(jdbcMinIdle);
        druidDataSource.setMaxActive(jdbcMaxActive);
        return druidDataSource;
    }
}

```
之后就可以通过@AutoWried注解得到数据源(druidDataSource)了。


# 参考

- [SpringBoot详解（三）-Spring Boot的web开发](https://juejin.im/post/59a6e0caf265da2473444c52)