# Spring mvc 学习笔记
## 创建项目选择spring MVC 
## 相关配置
web.xml    :     设置为dispatcher处理
  ```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>/WEB-INF/applicationContext.xml</param-value>
    </context-param>
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
    <servlet>
        <servlet-name>dispatcher</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>dispatcher</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
  ```
## 两种方式实现访问
### 1.xml实现访问
#### dispatcher.xml：主要配置相关controller，包括路由，controller绑定等。
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--配置路由-->
    <bean id="simpleUrlHandlerMapping"
          class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
        <property name="mappings">
            <props>
                <prop key="/hello">hello</prop>
            </props>
        </property>
    </bean>
    <!--配置映射-->
    <bean id="hello" class="controller.helloController"/>
</beans>
```
#### helloController实现,且需要在applicationContext.xml 开启视图解析器
```Java
package controller;


import org.springframework.web.servlet.ModelAndView;
import org.springframework.web.servlet.mvc.Controller;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.lang.annotation.Annotation;

public class helloController implements Controller {

    public ModelAndView handleRequest(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception {
        ModelAndView modelAndView = new ModelAndView("success");
        return modelAndView;
    }
}

```
### 2.注解实现路由配置
#### 开启视图解析器和开启注解支持，包扫描
  ```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/mvc
       http://www.springframework.org/schema/mvc/spring-mvc.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <!--视图解析器-->
    <bean id="viewResolver"
          class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/pages/"/>    <!--前缀-->
        <property name="suffix" value=".jsp"/>    <!--后缀-->
    </bean>

    <!-- 开启SpringMVC 框架的注解支持-->
    <mvc:annotation-driven/>

    <!--开启包扫描，自动扫描包下所有注解-->
    <context:component-scan base-package="controller"/>

</beans>
  ```
#### controller标注注解，无需再配置dispatcher-servlet.xml文件,也可实现访问
```java
	package controller;

	import org.springframework.stereotype.Controller;
	import org.springframework.web.bind.annotation.RequestMapping;

	@Controller 			<<<<<-------
	public class helloController {
		
		@RequestMapping("/hello") 		<<<<<-------
		public String hello() {
			System.out.println("helloWorld");
			return "success";
		}
	}

```
## 多级路由（ http://localhost:8080/demo/hello ）
最开始我们的路由是 http://localhost:8080/hello 加上后：http://localhost:8080/demo/hello
实现：在类之前加上RequestMapping("/demo")

```java
  @org.springframework.stereotype.Controller
  @RequestMapping("/demo") 			<<<<<-------
  public class democontroller implements Controller {

		@RequestMapping("/hello") 		<<<<<-------
		public String hello() {
			System.out.println("helloWorld");
			return "success";
		}
  }
```
## controller获取参数：
### 1.通过HttpServletRequest，HttpServletResponse 获取数据（原生）
  ```java
    @RequestMapping("/ServletApi")
    public String ServletApi(HttpServletRequest request, HttpServletResponse response) {
        System.out.println(request.getParameter("name") + request.getParameter("password"));
        return "success";
    }
  ```
### 2.使用@RequestParam获取参数
  ```java
    @RequestMapping("/login_do")
    public String login_do(@RequestParam(name = "userName") String name, @RequestParam(name = "userPassword")String password) {
        System.out.println(name + password);
        return "success";
    }
  ```
### 3.POJO作为参数（bean对象）form表单的命名应该与bean的命名相同，且可以实现级联属性
级联属性（一个bean对象包含其他引用bean类型）实现：只需要在前端form表单中设置name属性,并且在bean中设置相关的toString方法和setter，getter方法）
jsp页面
```
    <form action="${pageContext.request.contextPath}/pojo" method="post">
        user:<input type="text" name="name">
        password:<input type="text" name="password">
        address:<input type="text" name="acc.address">		<<<<<---------
        money:<input type="text" name="acc.money"/>		<<<<<---------
        <input type="submit"/>
    </form>
```
controller实现
```java
    @RequestMapping("/pojo")
    public String pojo(User u) {		<<<<<---------//自动封装
        System.out.println(u);
        return "success";
    }
```
User bean 声明
```java
	package bean;

	public class User {
		private String name;
		private String password;
		private account acc;		<<<<<---------

		public String getName() {
			return name;
		}

		public void setName(String name) {
			this.name = name;
		}

		public String getPassword() {
			return password;
		}

		public void setPassword(String password) {
			this.password = password;
		}

		public account getAcc() {
			return acc;
		}

		public void setAcc(account acc) {
			this.acc = acc;
		}

		@Override
		public String toString() {
			return "User{" +
					"name='" + name + '\'' +
					", password='" + password + '\'' +
					", acc=" + acc +
					'}';
		}
	}
```
  java bean对象的引用 account 申明
```java
	package bean;

	public class account {
		private String money;
		private String address;

		public String getMoney() {
			return money;
		}

		public void setMoney(String money) {
			this.money = money;
		}

		public String getAddress() {
			return address;
		}

		public void setAddress(String address) {
			this.address = address;
		}

		@Override
		public String toString() {
			return "account{" +
					"money='" + money + '\'' +
					", address='" + address + '\'' +
					'}';
		}
	}
```
输出结果
```java
 User{name='comi', password='123', acc=account{money='1000000', address='cs'}}
```
### 4.封装存为list或map的集合
form 表单 处理
a) list
  ```java
      phone:<input type="text" name="list[0].phone">
      address:<input type="text" name="list[0].address">
  ```
b) map
  ```java
      phone:<input type="text" name="map['one'].phone">
      address:<input type="text" name="map['one'].address">
  ```
## 中文字符乱码解决
  ```xml
  <filter>
      <filter-name>CharacterEncodingFilter</filter-name>
      <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
      <init-param>
          <param-name>encoding</param-name>
          <!-- 设置编码格式 -->
          <param-value>utf-8</param-value>
      </init-param>
  </filter>
  <filter-mapping>
      <filter-name>CharacterEncodingFilter</filter-name>
      <url-pattern>/*</url-pattern>
  </filter-mapping>
  ```

## Spring mvc 数据回显（向jsp传递数据）
### controller 设置相关数据
  ```java
    @RequestMapping("/mv")
    public ModelAndView mv() {
        ModelAndView mav = new ModelAndView("success");
        mav.addObject("message", "Hello Spring MVC");
        return mav;
    }
  ```
### jsp页面
  ```jsp
  <%@ page language="java" contentType="text/html; charset=UTF-8"
           pageEncoding="UTF-8" isELIgnored="false" %>

  <h1>${message}</h1>
  ```
## 使用@ModelAttribute设置属性，需要开启注解支持才能生效
### 作用：ModelAttribute标记的方法会在每一个方法执行之前被SpringMVC调用
  ```java
   //在启动时直接设置相应的属性，比如修改密码操作，其他信息不变，只需要更新密码，那么就可以先查出之前的数据，在修改密码并保存，无需new一个新的对象
   
    @ModelAttribute						<<<<------
    public void modelAttribute(Map<String,Object> map) {
		// 在map中存入的数据key为comi，则modelmadview中使用也需要相同的名字
        map.put("comi",new User("comi", "123456", new account("12000", "saijas")));			<<<<------
        System.out.println(map.get("comi"));
    }
	
    @RequestMapping("/mA") 				<<<<------
    public String testModelAttribute(User comi) {		<<<<------
        System.out.println(comi);
        return "success";
    }

  ```
## SpringMVC 常用注解
1.@RequestParam(将前端传递参数和控制器参数进行绑定)
用于前后端协调参数名不一致时进行处理

```jsp
    <%--前端参数名--%>
    user:<input type="text" name="myname">
    userPassword:<input type="text" name="mypassword">
```
后端处理
  ```java
      public void UserBean(@RequestParam(name = "myname") String name,@RequestParam(name = "mypassword") String password){
          System.out.println(myname + mypassword);
      }
  ```
2.@RequestBody(接收前端传递给后端的json数据)
获得整个请求体内容

  ```java
      public void UserBean(@RequestBody String body){
          System.out.println(body);
      }
  ```
3.@PathVriable(**PathVariable 映射 URL 绑定的占位符**，获取path的数据如：localhost：8080/user/{id}) 读出占位符id的数据

  ```java
      @RequestMapping("/UserBean/{id}")
      public void UserBean(@PathVariable(name = "id") String id){
          System.out.println(id);
      }
  ```
4.@RequestMapping(将http请求和controller类中的方法进行映射)

  ```java
      @RequestMapping("/UserBean/{id}",method = Request.GET/Request.POST)       <<<<-------
      public void UserBean(@PathVariable(name = "id") String id){
          System.out.println(id);
      }
  ```
5.@Controller(标记类为Controller对象,作用域：类)
6.@ModelAttribute(在方法执行前，执行一些操作)
7.@SessionAttributes(作用域：类)
&emsp;&emsp;a)  设置session属性
  ```java
  @SessionAttributes(value = {" msg "})    <<<<----
  public class controller  {
      //实现方法中使用model存储到request对象中去
      public void UserBean(Model model){
          model.addAttribute("msg","hello world");      <<<<----
      }
  }
    
  ```
&emsp;&emsp;前端页面可以按照EL标签的语法直接取值
```jsp
${msg}
```
&emsp;&emsp;b)  ModelMap 可以取出 之前设定的 session 
```java
    public void UserBean(ModelMap modelMap){
        modelMap.get("msg");
    }
```
&emsp;&emsp;c)  删除session 属性
```java
    public void UserBean(SessionStatus status){
        status.setComplete();
    }
```
## 后台响应请求
a)返回String 类型，常用Model 返回 String 数据
  ```java
      public void UserBean(Model model){
        model.addAttribute("msg","hello world");
    }
  ```
b)响应转发重定向(jsp的原生方式)
c)返回ModelAndView()
```java
ModelAndView mav = new ModelAndView();
//将user存储到mav对象和request对象中，
mav.addObject("user",new User("comi",18));
//页面跳转
mav.setViewName("index"); //在配置了视图解析器的情况下，使用index，否则使用index.jsp

```
d)转发和重定向
什么是转发？：转发是服务器内部的跳转，可以携带数据。
什么是重定向？：重定向是关闭掉上一次的请求，发起一次新的请求！
```java
  // 请求转发
  return "forword:/WEB-INF/pages/success.jsp";
  //  重定向
  return "redirect:/index.jsp";
```
## 客户端页面跳转
在controller下（无论该类是否实现Controller类）
  ```java
  @org.springframework.stereotype.Controller
  public class hellocontroller{

      @RequestMapping(path = "/jump")    <<<<----指定path，否者无法进入处理方法,和value 一样的效果
      public ModelAndView jump(){
          ModelAndView mav = new ModelAndView("redirect:/login");
          System.err.println("redirect!!"); 
          return mav;
      }
  }
  ```
## 设置静态资源可以被访问
在dispatcher-servelt.xml 下设置添加
```xml
<!--设置js目录下所有的资源都可以被访问-->
<mvc:resources mapping="/js/**" location="/js/**"/>
```





















