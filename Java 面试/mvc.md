## mvc运行流程

用户发起请求，被dispatcherservlet拦截，解析url，调用HandlerMapping,找到对应的handler

dispatcherservlet调用HandlerAdapter,找对应的controller,

controller完成业务逻辑，HandlerAdapter返回modelandview

dispatcherservlet调用viewresolver查找对应的视图

dispatcherservlet渲染并返回

## mvc重定向和转发

重定向:定向到一个新的页面,redirect:

转发:服务器内部转发,forward:

## 常用注解

1.@RequestMapping：用于处理请求 url 映射的注解，可用于类或方法上。用于类上，则表示类中的所有响应请求的方法都是以该地址作为父路径。

2.@RequestBody：注解实现接收http请求的json数据，将json转换为java对象。

3.@ResponseBody：注解实现将conreoller方法返回对象转化为json对象响应给客户。

## SpringMVC如何自定义拦截器并使用?

实现HandlerInterceptor接口，包含preHandle，postHandle，afterCompletion



spring：用于降低耦合，使程序员专注于核心业务的容器
springmvc：请求驱动类型的轻量级Web框架 
springboot：降低spring的难度，提供了多种启动器，自动配置，内置tomcat容器，简化xml配置
