# SSM 整合

&emsp;&emsp;整合的思路是：

&emsp;&emsp;&emsp;先创建spring框架

&emsp;&emsp;&emsp;通过spring整合spring mvc

&emsp;&emsp;&emsp;通过spring整合mybatis

## 工程创建
&emsp;&emsp;&emsp;创建Maven工程-->create for archtype-->webapp
## 创建项目结构
&emsp;&emsp;![image-20191125112943515](../images/image-20191125112943515.png)

在recourses目录下创建	`dbconfig.properties`,`log4j.properties`,`mysqlConfig.xml`,`springmvc.xml`,`applicationContext.xml`

## 工作流程
&emsp;&emsp;![b9055e11.png](../images/e9441f7f-7078-46c9-b8fd-b162dcbcba4e/b9055e11.png)
## 在pom.xml添加相关依赖 --- mybatis
```xml
<dependencies>
        <!--junit4-->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
        <!--日志-->
        <dependency>
            <groupId>commons-logging</groupId>
            <artifactId>commons-logging</artifactId>
            <version>1.2</version>
        </dependency>
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
        </dependency>
        <!-- mysql数据库驱动依赖 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.41</version>
        </dependency>
        <!--c3p0  数据连接池 jar-->
        <dependency>
            <groupId>com.mchange</groupId>
            <artifactId>c3p0</artifactId>
            <version>0.9.5.1</version>
        </dependency>
        <!--C3P0额外依赖的一个jar包-->
        <dependency>
            <groupId>com.mchange</groupId>
            <artifactId>mchange-commons-java</artifactId>
            <version>0.2.10</version>
        </dependency>
        <!-- Mybatis框架：-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.4.2</version>
        </dependency>
        <!-- MyBatis整合Spring的适配包 -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>1.3.1</version>
        </dependency>
        <!--EL表达式-->
        <dependency>
            <groupId>taglibs</groupId>
            <artifactId>standard</artifactId>
            <version>1.1.2</version>
            <type>jar</type>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
            <type>jar</type>
        </dependency>

        <dependency>
            <groupId>javax.servlet.jsp</groupId>
            <artifactId>javax.servlet.jsp-api</artifactId>
            <version>2.3.3</version>
            <scope>provided</scope>
        </dependency>

        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>4.0.1</version>
            <scope>provided</scope>
        </dependency>

        <!--json jar包-->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.62</version>
        </dependency>
        <!-- Spring依赖 -->
        <!--1. Spring核心依赖,上面已经有日志了 -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>4.3.7.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-beans</artifactId>
            <version>4.3.7.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>4.3.7.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-expression</artifactId>
            <version>4.3.7.RELEASE</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/commons-io/commons-io -->
        <dependency>
            <groupId>commons-io</groupId>
            <artifactId>commons-io</artifactId>
            <version>2.6</version>
        </dependency>

        <!--spring的aop jar包-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aop</artifactId>
            <version>4.3.7.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aspects</artifactId>
            <version>4.3.7.RELEASE</version>
        </dependency>
        <!--这两个jar包是阿帕奇的-->
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjrt</artifactId>
            <version>1.8.13</version>
        </dependency>
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.8.13</version>
        </dependency>
        <dependency>
            <groupId>aopalliance</groupId>
            <artifactId>aopalliance</artifactId>
            <version>1.0</version>
        </dependency>
        <!--2. SpringDAO层依赖 -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>4.3.7.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-tx</artifactId>
            <version>4.3.7.RELEASE</version>
        </dependency>
        <!--3. Spring WEB依赖 -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-web</artifactId>
            <version>4.3.7.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>4.3.7.RELEASE</version>
        </dependency>
        <!--4.Spring-test相关依赖 -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>4.3.7.RELEASE</version>
        </dependency>
    </dependencies>
```
## 基本思路
### 建立spring框架
1.创建bean

```java
public class user implements Serializable {
    private Integer id;
    private String name;
    private String gender;
    private String email;
    @Override
    public String toString() {
        return "user{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", gender='" + gender + '\'' +
                ", email='" + email + '\'' +
                '}';
    }
    
    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getGender() {
        return gender;
    }

    public void setGender(String gender) {
        this.gender = gender;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }
}
```
2.创建bean的dao
```java
public interface userDao {
    public List<user> findAll();
}
```
3.创建bean的userService的接口

```java
public interface userService {
    public List<user> findAll();
}
```
4.实现userServiceImpl,并给userServiceImpl 起别名userService

```java
@Service("userService")					<<<<-----------
public class userServiceImpl implements userService {
    
    @Override
    public List<user> findAll() {
        System.out.println("查询所有用户。。。");
        return null;//service.findAll();
    }
}
```
5.在resources文件下创建文件applicationContext.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.3.xsd
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.2.xsd
		http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.3.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd">


    <!--只处理Service和dao -->
    <context:component-scan base-package="club.twzw">
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>
	
</beans>
```
7.书写测试方法
```java
package test;

import club.twzw.bean.User;
import club.twzw.dao.UserDao;
import club.twzw.service.UserService;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import org.apache.ibatis.io.Resources;

import java.io.IOException;
import java.io.InputStream;
import java.util.List;


public class test {

    @Test
    public void testQueryUserList() {
        ApplicationContext ac = new ClassPathXmlApplicationContext("classpath:applicationContext.xml");
        UserService service = (UserService) ac.getBean("UserService"); // 因为给service起了别名，所以通过id的方式获取class
        service.findAll();
    }

}

```
输出 
![31a0456e.png](../images/e9441f7f-7078-46c9-b8fd-b162dcbcba4e/31a0456e.png)
至此spring框架已经创建

### 建立spring mvc
在web.xml文件中修改并配置过滤器，中文乱码过滤
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
  
    <servlet>
        <servlet-name>spring-dispatcher</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!--加载springmvc.xml配置文件-->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc.xml</param-value>
        </init-param>
        <!--启动服务器时，创建该servlet-->
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>spring-dispatcher</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

    <!--配置中文乱码过滤器-->
    <filter>
        <filter-name>characterEncodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>characterEncodingFilter</filter-name>
        <url-pattern>/</url-pattern>
    </filter-mapping>
</web-app>
```
在resources文件夹下创建springmvc.xml文件,开启注解扫描，视图解析器以及过滤静态资源和springmvc注解支持
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <!--开启注解扫描，只扫描controller-->
    <context:component-scan base-package="club.twzw">
        <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>

    <!--配置视图解析器-->
    <bean id="internalResourceViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="suffix" value=".jsp"/>
        <property name="prefix" value="/WEB-INF/views/"/>
    </bean>

    <!--过滤静态资源-->
    <!--    <mvc:resources mapping="/css/**" location="/css/"/>-->
    <!--    <mvc:resources mapping="/images/**" location="/images/"/>-->
    <!--    <mvc:resources mapping="/js/**" location="/js/"/>-->


    <!--开启springmvc注解支持-->
    <mvc:annotation-driven/>

</beans>
```
## 书写controller 
```java
package club.twzw.controller;

import club.twzw.bean.User;
import club.twzw.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;

import java.util.List;

@Controller
public class UserController {

    @Autowired
    private UserService service;

    @RequestMapping("/findAll")
    public String findAll(Model model){
        System.out.println("success");
        List<User> all = service.findAll();
        model.addAttribute("user",all);
        return "list";
    }

}
```
## index.jsp
```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
	<body>
		<a href="/findAll">查看所有用户</a>
	</body>
</html>
```
## success.jsp
```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
	<head>
    	<title>scuccess</title>
	</head>
	<body>
    	<h1>scuccess</h1>
	</body>
</html>
```
![cb4ea08b.png](../images/e9441f7f-7078-46c9-b8fd-b162dcbcba4e/cb4ea08b.png)
## 页面如果做成显示，自此成功添加spring mvc 开始整合
在整合之前需要明白，我们需要在controller中调用service，最快捷的便是使用依赖注入，而至今使用Tomcat服务器只加载了springmvc.xml文件，并没有applicationContext.xml的加载（也就是spring并没有被加载），所以可以通过监听ServeltContext域对象，在创建时加载spring的配置文件（applicationContext.xml）
![2f485e53.png](../images/e9441f7f-7078-46c9-b8fd-b162dcbcba4e/2f485e53.png)

## 配置监听器
在web.xml文件下添加listener,context-param设置监听和applicationContext.xml的文件路径
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <!--加载Spring-->
    <!--配置监听器,默认只加载web-inf目录下的applicationContext.xml-->
    <!--但是我们的文件在resources文件夹下-->    					<<<<------
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
    <!--设置配置文件路径-->											<<<<------
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:applicationContext.xml</param-value>
    </context-param>

    <!--加载spring mvc-->
    <!--配置前端控制器-->
    <servlet>
        <servlet-name>spring-dispatcher</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!--加载springmvc.xml配置文件-->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc.xml</param-value>
        </init-param>
        <!--启动服务器时，创建该servlet-->
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>spring-dispatcher</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

    <!--配置中文乱码过滤器-->
    <filter>
        <filter-name>characterEncodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>characterEncodingFilter</filter-name>
        <url-pattern>/</url-pattern>
    </filter-mapping>
</web-app>
```
这样我们就可以使用依赖注入了
## 使用依赖注入并从新发布,如果正常，就可以在控制台看到两句话
```java
package club.twzw.controller;
import club.twzw.service.impl.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class UserController {

    @Autowired
    private UserService service;

    @RequestMapping("/findAll")
    public String findAll(){
        System.out.println("success");
        service.findAll();
        return "success";
    }
}
```
![357efc1b.png](../images/e9441f7f-7078-46c9-b8fd-b162dcbcba4e/357efc1b.png)
这样spring mvc 就已经整合完毕了！
## 建立mybatis环境
在UserDao中使用注解查询
```java
package club.twzw.dao;

import club.twzw.bean.User;
import org.apache.ibatis.annotations.Insert;
import org.apache.ibatis.annotations.Select;
import org.springframework.stereotype.Repository;

import java.util.List;
@Repository
public interface UserDao {

    @Select("select * from user")
    public List<User> findAll();

    @Insert("insert into user (name,gender,email) values(#{name},#{gender},#{email})")
    public boolean Save(User u);
}

```
创建mybatis的核心配置文件mysqlConfig.xml
```xml-dtd
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org.//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<!-- mybatis 的 主配置文件-->
<configuration>

    <environments default="mysql">
        <!--配置mysql-->
        <environment id="mysql">
            <!--配置事务类型-->
            <transactionManager type="JDBC"/>
            <!--配置数据源/连接池-->
            <dataSource type="POOLED">
                <!--配置连接数据库的基本信息-->
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://47.106.153.41:3306/blog"/>
                <property name="username" value="blog"/>
                <property name="password" value="14632579"/>
            </dataSource>
        </environment>
    </environments>

    <!--指定映射配置文件的位置，映射配置文件指的是每个dao独立的配置文件-->
    <mappers>
        <!--使用xml进行查询-->
        <!--<mapper resource="mapper/UserDao.xml"/>-->
        <!--使用注解-->
        <!--<mapper class="club.twzw.dao.UserDao"/>-->
        <!--扫描mapper下所有注解-->
        <package name="mapper"/>
    </mappers>

</configuration>
```
## 书写测试方法
```java
    @Test
    public void test() throws IOException {
        InputStream resourceAsStream = Resources.getResourceAsStream("mysqlConfig.xml");
        SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
        SqlSessionFactory factory = builder.build(resourceAsStream);
        SqlSession session = factory.openSession(true);

        UserDao mapper = session.getMapper(UserDao.class);
        List<User> all = mapper.findAll();
        for (User user : all) {
            System.out.println(user);
        }
    }
```
输出结果：
![28e6737f.png](../images/e9441f7f-7078-46c9-b8fd-b162dcbcba4e/28e6737f.png)
说明了mybatis可用，那么可以开始整合了

## 整理mybatis,思路相同，同样使用依赖注入，将mysqlConfig.xml添加到容器中，并自动注入
在spring的文件中整合mybatis,配置连接池，factory，dao所在的包,此时有无将mysqlConfig.xml都不重要！
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx" xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.3.xsd
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.2.xsd
		http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.3.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd">


    <!--只处理Service和dao -->
    <context:component-scan base-package="club.twzw">
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>

    <!--Spring 配置mybatis-->
    <!--配置连接池-->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="com.mysql.jdbc.Driver"/>
        <property name="jdbcUrl" value="jdbc:mysql://47.106.153.41:3306/learn?useSSL=false"/>
        <property name="user" value="learn"/>
        <property name="password" value="EhRGr6hfiifjtw4J"/>
    </bean>
    <!--配置sqlSessionFactory-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <!--配置dao所在的包-->
    <bean id="mapperScannerConfigurer" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="club.twzw.dao"/>
    </bean>
</beans>
```
## 使用依赖注入
在dao类上添加@Repository注解
```java
@Repository					<<<<<--------
public interface UserDao {

    @Select("select * from user")
    public List<User> findAll();
}
```
在serviceimpl中注入接口
```java
package club.twzw.service.impl;

import club.twzw.bean.User;
import club.twzw.dao.UserDao;
import club.twzw.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;
@Service("UserService")
public class UserServiceImpl implements UserService {


    @Autowired
    private UserDao dao;

    public List<User> findAll() {
        System.out.println("查询所有用户。。。");
        return dao.findAll();
    }
}

```
修改controllerfindAll方法
```java
package club.twzw.controller;

import club.twzw.bean.User;
import club.twzw.service.impl.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;

import java.util.List;

@Controller
public class UserController {

    @Autowired
    private UserService service;


    @RequestMapping("/findAll")
    public String findAll(Model model){
        System.out.println("success");
        List<User> all = service.findAll();
        model.addAttribute("user",all);
        return "list";
    }
}

```
添加list.jsp
```jsp
<%--
  Created by IntelliJ IDEA.
  User: Administrator
  Date: 2019/10/29
  Time: 21:07
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>“”
<html>
<head>
    <title>Title</title>
</head>
<body>
    <c:forEach items="${user}" var="user">
        <tr>
            <td>${user.id}</td>
            <td>${user.name}</td>
            <td>${user.gender}</td>
            <td>${user.email}</td>
        </tr>
    </c:forEach>
</body>
</html>
```
## 配置spring 框架声明式事务管理,在spring的配置文件applicationContext.xml下添加声明
```jsp
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx" xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.3.xsd
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.2.xsd
		http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.3.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd">


    <!--只处理Service和dao -->
    <context:component-scan base-package="club.twzw">
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>

    <!--Spring 配置mybatis-->
    <!--配置连接池-->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="com.mysql.jdbc.Driver"/>
        <property name="jdbcUrl" value="jdbc:mysql://47.106.153.41:3306/learn?useSSL=false"/>
        <property name="user" value="learn"/>
        <property name="password" value="EhRGr6hfiifjtw4J"/>
    </bean>
    <!--配置sqlSessionFactory-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <!--配置dao所在的包-->
    <bean id="mapperScannerConfigurer" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="club.twzw.dao"/>
    </bean>

    <!--配置Spring 框架声明式事务管理-->
    <!--配置事务管理器-->
    <bean id="dataSourceTransactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>
    <!--配置事务通知-->
    <tx:advice id="txAdvice" transaction-manager="dataSourceTransactionManager">
        <tx:attributes>
            <tx:method name="find" read-only="true"/>
            <tx:method name="*" isolation="DEFAULT"/>
        </tx:attributes>
    </tx:advice>
    <!--配置Aop增强-->
    <aop:config>
        <aop:advisor advice-ref="txAdvice" pointcut="execution(* club.twzw.service.impl.*ServiceImpl.*(..))"/>
    </aop:config>
</beans>
```
实现访问
![21e09f30.png](../images/e9441f7f-7078-46c9-b8fd-b162dcbcba4e/21e09f30.png)
## 实现插入
controller
```java
    @RequestMapping("/save")
    public String save(Model model){
        System.out.println("success");
        User u = new User();
        u.setEmail("184611875@qq.com");
        u.setGender("男");
        u.setName("comi");
        boolean b = service.Save(u);
        System.out.println(b);
        return "success";
    }
```
UserService
```java
package club.twzw.service;

import club.twzw.bean.User;
import org.springframework.stereotype.Service;

import java.util.List;

public interface UserService {
    public List<User> findAll();

    public boolean Save(User u);
}

```
UserServiceImpl
```
package club.twzw.service.impl;

import club.twzw.bean.User;
import club.twzw.dao.UserDao;
import club.twzw.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;
@Service("UserService")
public class UserServiceImpl implements UserService {


    @Autowired
    private UserDao dao;

    public List<User> findAll() {
        System.out.println("查询所有用户。。。");
        return dao.findAll();
    }

    @Override
    public boolean Save(User u) {
        return dao.Save(u);
    }
}
```
dao
```
package club.twzw.dao;

import club.twzw.bean.User;
import org.apache.ibatis.annotations.Insert;
import org.apache.ibatis.annotations.Select;
import org.springframework.stereotype.Repository;

import java.util.List;
@Repository
public interface UserDao {

    @Select("select * from user")
    public List<User> findAll();

    @Insert("insert into user (name,gender,email) values(#{name},#{gender},#{email})")
    public boolean Save(User u);
}
```
访问路径(http://localhost:8080/ssmWork_war_exploded/save)
![6bacaaf3.png](../images/e9441f7f-7078-46c9-b8fd-b162dcbcba4e/6bacaaf3.png)
控制台输出
![c8cb4ee1.png](../images/e9441f7f-7078-46c9-b8fd-b162dcbcba4e/c8cb4ee1.png)
这样我们的ssm框架就完成整合了，可以去干大事了！！！！
码云开源库：[码云链接](https://gitee.com/comiiiii/SSM)