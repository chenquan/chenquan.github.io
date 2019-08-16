---
layout: post
title: spring-RestTemplate负载均衡调用
categories: [spring]
description: spring-RestTemplate负载均衡调用
---



# spring-RestTemplate负载均衡调用

在springcloud 的使用中如果使用RestTemplate来进行rpc远程调用的时候 ，在调用会员服务的时候有的会选择使用会员服务端在注册中心注册的名称来进行远程调用 也有的会直接使用域名进行调用，在这个过程中如果使用会员的注册名称的话在RestTemplate 那里开启 负载均衡 :  @LoadBalanced  如果是使用域名进行调用就不用开启负载均衡 如下例子：



```java
@RestController
public class OrderController {
  @Autowired
  private  RestTemplate restTemplate;
@RequestMapping("/getOrder")
  public  String getOrder(){
      //使用rpc远程技术调用服务 ,2种调用方法，一种是名称（要开启负载均衡），一个是域名（不用开启负载均衡）

     // String memberUrl = "http://app-itmayiedu-member/getMember"

   String memberUrl = "http://localhost:8000/getMember";

      String result=restTemplate.getForObject(memberUrl,String.class);

      System.out.println("会员服务调用订单服务，result："+result);

           return  result;

  }

}
```

```java
@EnableEurekaClient
@SpringBootApplication
public class ApringcloudEurekaOrderApplication {

	public static void main(String[] args) {
		SpringApplication.run(ApringcloudEurekaOrderApplication.class, args);
	}
	//配置bean
	@Bean
	//开启负载均衡
	//@LoadBalanced  //(如果是名称的远程要开启，不然要关闭)
	RestTemplate restTemplate(){
		return new RestTemplate();
	}
}
```