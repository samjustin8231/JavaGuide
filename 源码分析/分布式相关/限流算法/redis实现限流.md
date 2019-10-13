# 自定义注解+拦截器的优势
类似用户权限或者接口限流的需求，但并不是所有操作或者接口需要。可以使用过滤器或者拦截器，但这样就必须在配置文件里加上所有方法或者使用通配符。所以可以采用一种比较简单灵活的方式：采用自定义注解加Spring拦截器来实现。

# 编写示例
例如我们现在要做个接口的计数器限流，只需要像下面这样，加上一个注解即可。定义为每seconds秒内，最大访问量maxCount。

```
@AccessLimit(seconds=5, maxCount=5)
@RequestMapping(value="/path", method=RequestMethod.GET)
@ResponseBody
public Result<String> getMiaoshaPath(...) {
    // 业务逻辑代码
}
```

注解定义如下：

```
@Retention(RUNTIME)
@Target(METHOD)
public @interface AccessLimit {
    int seconds();
    int maxCount();
}
```

配合拦截器使用，使注解生效：

```
@Service
public class AccessInterceptor  extends HandlerInterceptorAdapter{

    @Autowired
    RedisService redisService;

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
            throws Exception {
        if(handler instanceof HandlerMethod) {
            MiaoshaUser user = getUser(request, response);
            UserContext.setUser(user);
            HandlerMethod hm = (HandlerMethod)handler;
            AccessLimit accessLimit = hm.getMethodAnnotation(AccessLimit.class);
            if(accessLimit == null) {
                return true;
            }
            int seconds = accessLimit.seconds();
            int maxCount = accessLimit.maxCount();
            String key = request.getRequestURI();
            Integer count = redisService.get(key);
            if(count  == null) {
                // 定义一个seconds有效期的key
                redisService.set(key, 1, seconds);
            }else if(count < maxCount) {
                redisService.incr(key);
            }else {
                return false;
            }
        }
        return true;
    }
```




# 参考

- [自定义注解+拦截器优化项目代码](https://juejin.im/post/5d8aa2a4518825094d625c46)