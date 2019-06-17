# 3.服务网关 Spring Cloud Zuul

## 3.1.分析

通过前面的学习，使用Spring Cloud实现微服务的架构基本成型，大致是这样的：

![](zuul_微服务调用.png)

我们使用Spring Cloud Netflix中的Eureka实现了服务注册中心以及服务注册与发现；而服务间通过Ribbon或Feign实现服务的消费以及均衡负载；通过Spring Cloud Config实现了应用多环境的外部化配置以及版本管理。为了使得服务集群更为健壮，使用Hystrix的融断机制来避免在微服务架构中个别服务出现异常时引起的故障蔓延。

在该架构中，我们的服务集群包含：内部服务Service A和Service B，他们都会注册与订阅服务至Eureka Server，而Open Service是一个对外的服务，通过均衡负载公开至服务调用方。我们把焦点聚集在对外服务这块，这样的实现是否合理，或者是否有更好的实现方式呢？

先来说说这样架构需要做的一些事儿以及存在的不足：

- 首先，破坏了服务无状态特点。
	- 为了保证对外服务的安全性，我们需要实现对服务访问的权限控制，而开放服务的权限控制机制将会贯穿并污染整个开放服务的业务逻辑，这会带来的最直接问题是，破坏了服务集群中REST API无状态的特点。
	- 从具体开发和测试的角度来说，在工作中除了要考虑实际的业务逻辑之外，还需要额外可续对接口访问的控制处理。
- 其次，无法直接复用既有接口。
	- 当我们需要对一个即有的集群内访问接口，实现外部服务访问时，我们不得不通过在原有接口上增加校验逻辑，或增加一个代理调用来实现权限控制，无法直接复用原有的接口。

面对类似上面的问题，我们要如何解决呢？ 答案是：服务网关！

为了解决上面这些问题，我们需要将权限控制这样的东西从我们的服务单元中抽离出去，而最适合这些逻辑的地方就是处于对外访问最前端的地方，我们需要一个更强大一些的均衡负载器  服务网关。

服务网关是微服务架构中一个不可或缺的部分。通过服务网关统一向外系统提供REST API的过程中，除了具备服务路由、均衡负载功能之外，它还具备了权限控制等功能。Spring Cloud Netflix中的Zuul就担任了这样的一个角色，为微服务架构提供了前门保护的作用，同时将权限控制这些较重的非业务逻辑内容迁移到服务路由层面，使得服务集群主体能够具备更高的可复用性和可测试性。

## 3.2.Zuul的简介

![](zuul_介绍.png)

## 3.3.使用Zuul之后的架构

![](zuul_架构.png)

从架构图中可以看出，客户端请求微服务时，先经过Zuul之后再请求，这样就可以将一些类似于校验的业务逻辑放到zuul中完成。
而微服务自身只需要关注自己的业务逻辑即可。

## 3.4.快速入门

3.4.3.编写启动类ApiGatewayApplication

```
package cn.itcast.microservice;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.zuul.EnableZuulProxy;

@EnableZuulProxy
@SpringBootApplication
public class ApiGatewayApplication {

	public static void main(String[] args) {
		SpringApplication.run(ApiGatewayApplication.class, args);
	}

}
```

3.4.4.编写application.yml文件和路由规则


```
server:
  port: 6677 #服务端口

spring: 
  application:  
    name: itcasst-microservice-api-gateway #指定服务名
    
zuul: 
  routes: 
    item-service: #item-service这个名字是任意写的
      path: /item-service/** #配置请求URL的请求规则
      url: http://127.0.0.1:8081 #真正的微服务地址
```

启动测试

![](zuul_test.png)

可以看到，已经通过zuul访问到了商品微服务。

## 3.5.面向服务的路由
在快速入门中我们已经通过了URL映射，访问到了商品微服务。这样做会存在一个问题，就是，如果商品微服务的地址发生了改变怎么办？
很自然的能够想到，不应该配置具体的url而是走Eureka注册中心获取地址。

3.5.2.修改application.yml配置文件

```
server:
  port: 6677 #服务端口

spring: 
  application:  
    name: itcasst-microservice-api-gateway #指定服务名
    
zuul: 
  routes: 
    item-service: #item-service这个名字是任意写的
      path: /item-service/** #配置请求URL的请求规则
      #url: http://127.0.0.1:8081 #真正的微服务地址
      serviceId: itcast-microservice-item #指定Eureka注册中心中的服务id
      
eureka:
  client:
    registerWithEureka: true #是否将自己注册到Eureka服务中，默认为true
    fetchRegistry: true #是否从Eureka中获取注册信息，默认为true
    serviceUrl: #Eureka客户端与Eureka服务端进行交互的地址
      defaultZone: http://itcast:itcast123@127.0.0.1:6868/eureka/
  instance: 
    prefer-ip-address: true #将自己的ip地址注册到Eureka服务中
```



## 3.7.过滤器
过滤器是Zuul的重要组件。
3.7.1.过滤器ZuulFilter

![](zuul_filter_demo.png)


ZuulFilter是一个抽象类，其实现类需要实现4个方法：



1. shouldFilter：返回一个Boolean值，判断该过滤器是否需要执行。返回true执行，返回false不执行。
2. run：过滤器的具体业务逻辑。
3. filterType：返回字符串代表过滤器的类型

	- pre：请求在被路由之前执行
	- routing：在路由请求时调用
	- post：在routing和errror过滤器之后调用
	- error：处理请求时发生错误调用
4. filterOrder：通过返回的int值来定义过滤器的执行顺序，数字越小优先级越高。

3.7.2.执行流程

![](zuul_filter_执行流程.png)


![](zuul_filter_流程说明.png)

## 3.8.过滤器实战

- 需求：通过编写过滤器实现用户是否登录的检查。
- 实现：通过判断请求中是否有token，如果有认为就是已经登录的，如果没有就认为是非法请求，响应401.

3.8.1.编写UserLoginZuulFilter

```
package cn.itcast.microservice.filters;

import javax.servlet.http.HttpServletRequest;

import org.apache.commons.lang3.StringUtils;
import org.springframework.stereotype.Component;

import com.netflix.zuul.ZuulFilter;
import com.netflix.zuul.context.RequestContext;

@Component //加入到Spring容器
public class UserLoginZuulFilter extends ZuulFilter{

	@Override
	public boolean shouldFilter() {
		return true; // 该过滤器需要执行
	}

	@Override
	public Object run() { //编写业务逻辑
		RequestContext requestContext = RequestContext.getCurrentContext();
		HttpServletRequest request = requestContext.getRequest();
		String token = request.getParameter("token");
		if(StringUtils.isEmpty(token)){
			requestContext.setSendZuulResponse(false); // 过滤该请求，不对其进行路由
			requestContext.setResponseStatusCode(401); // 设置响应状态码
			return null;
		}
		return null;
	}

	@Override
	public String filterType() {
		return "pre"; // 设置过滤器类型为：pre
	}

	@Override
	public int filterOrder() {
		return 0;// 设置执行顺序
	}

}
```



