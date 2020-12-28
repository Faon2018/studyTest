# Spring Cloud Gateway

配置解释

```yaml
spring:
  application:
    name: provider-gateway-service
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848
    gateway:
      discovery:
        locator:
          enabled: true #是否与服务发现组件进行结合，通过 serviceId(必须设置成大写) 转发到具体的服务实例。默认为false，设为true便开启通过服务中心的自动根据 serviceId 创建路由的功能。
      routes: #这些路由默认跨域
      - id: consum-blog-get
        #uri: http://localhost:80 # uri 不是 url
        uri: lb://consum-blog-service  #动态路由 lb负载均衡 consum-blog-service服务名
        predicates:
          - Path=/consum/blog/api/get/**
      - id: consum-blog-login
        #uri: http://localhost:80
        uri: lb://consum-blog-service  #动态路由
        predicates:
          - Path=/consum/blog/api/login/**
```

