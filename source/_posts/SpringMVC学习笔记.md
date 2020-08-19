---
title: SpringMVC学习笔记
date: 2020-08-16 17:30:32
tags: ['Spring', 'SpringMVC']
categories: ['SpringMVC']
typora-root-url: ..
---

# SpringMVC

ssm: MyBatis + Spring + SpringMVC **MVC三层架构**

JavaSE -> JavaWeb -> SSM框架



技术栈：SpringMVC +Vue +SpringBoot +SpringCloud +Linux



SSM = JavaWeb做项目

Spring: IOC 和 AOP

SpringMVC: SpringMVC的执行流程！

SpringMVC: SSM框架整合！

<!--more-->



# MVC

MVC：模型（dao, service） 视图(jsp) 控制器(Servlet)

## 什么是MVC

* MVC是模型（Model）、视图（View）、控制器（Controller）的简写，是一种软件设计规范
* 将业务逻辑、数据、显示分离的方法来组织代码
* MVC主要作用是降低子视图与业务逻辑间的双向耦合
* MVC不是一种设计模式，MVC是一种架构模式。当然不同的MVC存在差异

**模型（Model）：**数据模型，提供要展示的数据，因此包含数据和行为，可以认为是领域模型或JavaBean组件（包含数据和行为），不过现在一般都分离开来：Value Object（数据Dao）和服务层（行为Service）。也就是模型提供了数据查询和模型数据的状态更新等功能，包括数据和业务。

**视图（View）：**负责进行模型的展示，一般就是我们见到的用户界面，客户想看到的东西。

**控制器（Controller）：**接收用户请求，委托给模型进行处理（状态改变），处理完毕后把返回的模型数据返回给视图，由视图负责展示。也就是说控制器做了个调度员的工作。



## MVC框架要做的事情

1. 将url请求映射到java类或java类的方法
2. 封装用户提交的数据
3. 处理请求--调用相关的业务处理--封装相应数据
4. 将响应的数据进行渲染 .jsp/html 等表示层数据



## MVVM

M V VM(ViewModel)：双向绑定(前后端分离的核心)



# SpringMVC执行流程

1. DispatcherServlet表示前置控制器，是整个SpringMVC的控制中心，用户发出请求，DispatcherServlet接收请求并拦截请求。
   * 假设请求的url为：http://localhost:8080/SpringMVC/hello
   * http://localhost:8080为服务器域名
   * SpringMVC表示服务器上的web站点（通常为项目名）
   * hello表示控制器
   * 通过分析，如上url表示为：请求位于服务器localhost:8080上的SpringMVC站点的hello控制器
2. HandlerMapping为处理器映射。DispatcherServlet调用HandlerMapping，HandlerMapping根据请求url查找Handler。
3. HandlerExecution表示具体的Handler，其主要作用是根据url查找控制器，如上url被查找控制器为：hello。
4. HandlerExecution将解析后的信息传递给DispatcherServlet，如解析控制器映射等。
5. HandlerAdapter表示处理器适配器，其按照特定的规则去执行Handler。
6. Handler让具体的Controller执行
7. Controller将具体的执行信息返回给HandlerAdapter，如ModelAndView。
8. HandlerAdapter将视图逻辑名或模型传递给DispatcherServlet。
9. DispatcherServlet调用视图解析器（ViewResolver）来解析HandlerAdapter传递的逻辑视图名。
10. 视图解析器将解析的逻辑视图名传给DispatcherServlet。
11. DispatcherServlet根据视图解析器的视图结果，调用具体的视图。
12. 最终视图呈现给用户。

**流程图：**

![SpringMVC执行流程](/images/SpringMVC%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/SpringMVC%E6%89%A7%E8%A1%8C%E6%B5%81%E7%A8%8B.png)



# 搭建Web项目步骤

1. 新建一个Web项目
2. 导入相关jar包
3. 编写web.xml，注册DispatcherServlet
4. 编写springmvc配置文件
5. 接下来就是去创建相应的控制类，controller
6. 最后完善前端视图和controller之间的对应
7. 测试运行调试

**注意：**

使用SpringMVC必须配置的三大件：

**处理器映射器、处理器适配器、视图解析器**

通常，我们只需要手动配置**视图解析器**，而**处理器映射器**和**处理器适配器**只需要开启**注解驱动**即可，这省去了大段的xml配置。



**Spring中使用的注解：**

```xml
@Component   组件
@Service     Service层
@Controller  Controller层
@Repository  Dao层
```

上面四种注解是等效的，只是用于区分是不同层的注解



# 控制器Controller

* 控制器负责提供访问应用程序的行为，通常通过接口定义或注解定义两种方法实现。
* 控制器负责解析用户的请求并将其转换为一个模型

## 实现方式

* 实现Controller接口

* 使用注解@Controller

  * @Controller注解类用于声明Spring类的实例是一个控制器（在讲IOC时还提到了另外3个注解：@Component、@Service、@Repository）
  * Spring可以使用扫描机制来找到应用程序中所有基于注解的控制器类，为了保证Spring能找到控制器，需要在配置文件中声明组件扫描：

  ```xml
  <!--扫描指定包，用于识别该包下代码中的注解-->
  <context:component-scan base-package="com.yan.controller"/>
  ```

  * 增加一个ControllerTest2类，使用注解实现：

  ```java
  package com.yan.controller;
  
  import org.springframework.stereotype.Controller;
  import org.springframework.ui.Model;
  import org.springframework.web.bind.annotation.RequestMapping;
  
  // 代表这个类会被Spring接管，这个被注解的类中的所有方法，如果返回值是String，并且有具体页面可以跳转，那么就会被视图解析器解析
  @Controller
  public class ControllerTest2 {
  
      @RequestMapping("/t2")
      public String test1(Model model){
  
          model.addAttribute("msg", "ControllerTest2");
          return "test2";
      }
  }
  ```

  **注意：**视图可以被复用，控制器和视图之间是弱耦合关系



## @RequestMapping

* @RequestMapping注解用于映射url到控制器类或一个特定的处理程序方法。
* 可用于类或方法上。用于类上，表示类中的所有响应请求的方法都是以该地址作为父路径

```java
@Controller
@RequestMapping("/c3")
public class ControllerTest3 {

    @RequestMapping("t1")
    public String test1(Model model){

        model.addAttribute("msg", "ControllerTest3");

        return "test";
    }
}
```

访问路径：http://localhost:8080/项目名/c3/t1



* 用于方法上，每个方法对应一个@RequestMapping定义的子路径

```java
@Controller
public class ControllerTest3 {

    @RequestMapping("t1")
    public String test1(Model model){

        model.addAttribute("msg", "ControllerTest3");

        return "test";
    }
}
```

访问路径：http://localhost:8080/项目名/t1



# RestFul风格

## 概念

RestFul就是一个资源定位及资源操作的风格。不是标准也不是协议，只是一种风格。基于这种风格设计的软件可以更简洁，更有层次，更易于实现缓存等机制。



## 功能

* 资源：互联网所有的事物都可以被抽象为资源
* 资源操作：使用POST、DELET、PUT、GET、使用不同方法对资源进行操作
* 分别对应：添加、删除、修改、查询



## 传统方式操作资源

通过不同的参数来实现不同的效果，方法单一，post和get

* http://127.0.0.1/item/queryItem.action?id=1 查询GET
* http://127.0.0.1/item/saveItem.action 新增POST
* http://127.0.0.1/item/updateItem.action 更新POST
* http://127.0.0.1/item/deleteItem.action?id=1 删除，GET或POST



## 使用RestFul操作资源

可以通过不同的请求方式来实现不用的效果！如下：请求地址一样，但是功能可以不同！一般使用/来分割url。

* http://127.0.0.0/item/1 查询GET
* http://127.0.0.0/item 新增POST
* http://127.0.0.0/item/1 更新PUT
* http://127.0.0.0/item/1 删除DELETE



**查询GET:**

```java
// RestFul风格
// 请求地址：http://localhost:8080/add/1/2
// 返回结果：结果为：3
@GetMapping("/add/{a}/{b}")
public String test4(@PathVariable int a, @PathVariable int b, Model model){

    int res = a + b;
    model.addAttribute("msg", "GET结果为：" + res);

    return "test";
}
```



**新增POST:**

```java
// 通过HTTP POST方法访问
// 请求地址：http://localhost:8080/add/1/2
// 返回结果：结果为：3
@PostMapping("/add/{a}/{b}")
public String test5(@PathVariable int a, @PathVariable int b, Model model){

    int res = a + b;
    model.addAttribute("msg", "POST结果为：" + res);

    return "test";
}
```



## 使用路径变量的好处

* 使路径变得更加简洁
* 获得参数更加方便，框架会自动进行类型转换
* 通过路径变量的类型可以约束访问参数，如果类型不一样，则访问不到对应的请求方法，例如：如果这里访问的路径是/add/1/a，则路径与方法会不匹配
* 安全



# 重定向和转发

## ModelAndView

设置ModelAndView对象，根据view的名称，和视图解析器跳到指定页面

页面：{视图解析器前缀} + viewName + {视图解析器后缀}

**springmvc-servlet.xml：**

```xml
<!--视图解析器：模板引擎ThymeLeaf Freemarker...-->
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"
      id="internalResourceViewResolver">
    <!--前缀-->
    <property name="prefix" value="/WEB-INF/jsp/"/>
    <!--后缀-->
    <property name="suffix" value=".jsp"/>
</bean>
```

**对应的controller类：**

```java
// 只要实现了 Controller 接口的类，说明这就是一个控制器了
// 这种方式需要去springmvc-servlet.xml文件中注册该bean
// 可以不在springmvc-servlet.xml中配置处理器映射器和处理器适配器，这样会使用默认配置
public class ControllerTest1 implements Controller {

    public ModelAndView handleRequest(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception {
        ModelAndView mv = new ModelAndView();

        // 添加数据
        mv.addObject("msg", "ControllerTest1");
        // 添加要跳转的视图名
        mv.setViewName("test");

        return mv;
    }
}
```



## ServletAPI

通过设置ServletAPI，不需要视图解析器

1. 通过HttpServletResponse进行输出 rsp.getWriter().println("Hello, Spring BY servlet API");
2. 通过HttpServletResponse实现重定向  rsp.sendRedict("/index.jsp")
3. 通过HttpServletRequest实现转发  

```java
@Controller
public class ResultGo {
    
    @RequestMapping("/result/t1")
    public void test1(HttpServletRequest req, HttpServletResponse rsp) throws IOException {
        rsp.getWriter().println("Hello, Spring BY servlet API");
    }
    
    @RequestMapping("/result/t2")
    public void test2(HttpServletRequest req, HttpServletResponse rsp) throws IOException {
        // 重定向
        rsp.sendRedict("/index.jsp");
    }
    
    @RequestMapping("/result/t3")
    public void test2(HttpServletRequest req, HttpServletResponse rsp) throws IOException {
        // 转发
        req.setAttribute("msg", "/result/t3");
        req.getRequstDispatcher("/WEB-INF/jsp/test.jsp").forward(req, rsp);
    }
}
```



## SpringMVC

**通过SpringMVC来实现转发和重定向-无需视图解析器；**

测试前，需要将视图解析器注释掉

```java
@Controller
public class ResultSpringMVC {
    @RequestMapping("/rsm/t1")
    public String test1(){
        //转发
        return "/index.jsp";
    }
    
    @RequestMapping("/rsm/t2")
    public String test2(){
        //转发二
        return "forward:/index.jsp";
    }
    
    @RequestMapping("/rsm/t3")
    public String test3(){
        //重定向
        return "redirect:/index.jsp";
    }
}
```



**通过SpringMVC来实现转发和重定向-有视图解析器；**

需要加上前缀：forward(转发)、redirect(重定向)

**默认情况是转发：**return “test”; // 会将请求转发到/WEB-INF/jsp/test.jsp页面(视图解析器做了拼接)

**注意：**重定向不能访问WEB-INF目录下的内容

```java
@Controller
public class ModelTest1 {

    @RequestMapping("/m1/t2")
    public String test2(Model model){

        // 转发：url不发生变化
        model.addAttribute("msg", "ModelTest1");
        return "forward:/WEB-INF/jsp/test.jsp";
    }

    @RequestMapping("/m1/t3")
    public String test3(Model model){

        // 重定向：url会发生变化: http://localhost:8080/index.jsp?msg=ModelTest1
        model.addAttribute("msg", "ModelTest1");
        return "redirect:/index.jsp";
    }
}
```



# SpringMVC：数据处理

## 处理提交的数据

1. 域名中的字段名和处理方法的参数名一致

   url: http://localhost:8080/hello?name=kuangshen

   处理方法：

   ```java
   @RequestMapping("/hello")
   public String hello(String name){
       System.out.println(name);
       return "hello";
   }
   ```

   后台输出：kuangshen

2. 域名中的字段名和处理方法的参数名不一致

   url: http://localhost:8080/hello?username=kuangshen

   处理方法：

   ```java
   //@RequestParam("username"): username为域名中的字段名
   @RequestMapping("/hello")
   public String hello(@RequestParam("username") String name){
       System.out.println(name);
       return "hello";
   }
   ```

   后台输出：kuangshen

   **建议：**无论前端参数与方法参数是否一致，都加上@RequestParam("username")

3. 提交的是一个对象

   前端参数与对象中的属性名一致，则自动封装进对象中，不一致的属性值为null或0

   1. 实体类：

   ```java
   public class User {
       private int id;
       private String name;
       private int age;
       // 构造
       //get/set
       //toString()
   }
   ```

   2. url: http://localhost:8080/mvc04/user?name=kuangshen&id=1&age=15

   3. 处理方法：

   ```java
   @RequestMapping("/user")
   public String user(User user){
       System.out.println(user);
       return "hello";
   }
   ```

   后台输出：User{id=1, name='kuangshen',age=15}

   url: http://localhost:8080/user/t3?id=1&name1=xxx&age1=15
   结果：User(id=1, name=null, age=0)



## 数据传递到前端

### 第一种：通过ModelAndView

```java
// 只要实现了 Controller 接口的类，说明这就是一个控制器了
// 这种方式需要去springmvc-servlet.xml文件中注册该bean
// 可以不在springmvc-servlet.xml中配置处理器映射器和处理器适配器，这样会使用默认配置
public class ControllerTest1 implements Controller {

    public ModelAndView handleRequest(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception {
        // 返回一个模型视图对象
        ModelAndView mv = new ModelAndView();

        // 添加数据
        mv.addObject("msg", "ControllerTest1");
        // 添加要跳转的视图名
        mv.setViewName("test");

        return mv;
    }
}
```



### 第二种：通过Model

Model

```java
// url: localhost:8080/user/t1?username=xxx;
@GetMapping("/t1")
public String test1(@RequstParam("username") String name, Model model){
    // 1.接收前端参数
    System.out.println("接收到前端的参数为：" + name);

    // 2.将前端参数传递个前端: Model
    model.addAttribute("msg", name);

    // 3.转发视图
    return "test";
}
```



### 第三种：通过ModelMap

ModelMap

```java
@GetMapping("/t4")
public String test4(@RequestParam("username") String name, ModelMap map){

    // 封装要显示到视图中的数据
    // 相当于req.setAttribute("msg", name);
    map.addAttribute("msg", name);
    System.out.println("接收到的url的参数：" + name);

    return "test";
}
```



### 对比

Model: 只有很少的方法，只适合存储数据，简化了对Model对象的操作和理解

ModelMap: 继承了LinkedHashMap，除了实现了自身的一些方法，同样拥有LinkedHashMap的方法和特性

ModelAndView: 在存储数据的同时，可以设置返回的逻辑视图，进行控制展示层的跳转



# 乱码问题

## 测试步骤

1. 在首页编写一个提交的表单

```xml
<form action="/e/t1" method="post">
    <input type="text" name="name">
    <input type="submit">
</form>
```

2. 后台编写对应的处理类

```java
package com.yan.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.PostMapping;

@Controller
public class EncodingController {

    @PostMapping("/e/t1")
    public String test1(String name, Model model){
        model.addAttribute("msg", name);

        return "test";
    }
}
```

3. 访问：http://localhost:8080/form.jsp，输入中文测试，出现乱码：

![中文乱码](/images/SpringMVC%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E4%B8%AD%E6%96%87%E4%B9%B1%E7%A0%81.png)



## 解决方法

* 使用自定义过滤器：

编写过滤器EncodingFilter:

```java
package com.yan.filter;

import javax.servlet.*;
import java.io.IOException;

// 过滤器
public class EncodingFilter implements Filter {

    public void init(FilterConfig filterConfig) throws ServletException {

    }

    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        servletRequest.setCharacterEncoding("utf-8");
        servletResponse.setCharacterEncoding("utf-8");

        filterChain.doFilter(servletRequest, servletResponse);
    }

    public void destroy() {

    }
}
```

在web.xml注册过滤器:

```xml
<!-- / 匹配所有的请求：（不包括.jsp）-->
<!-- /* 匹配所有的请求：（包括.jsp）-->
<!--配置过滤器: 过滤所有请求：/-->
<filter>
    <filter-name>encoding</filter-name>
    <filter-class>com.yan.filter.EncodingFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>encoding</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```



* 使用SpringMVC自带的过滤器，解决乱码问题

```xml
<!--使用SpringMVC自带的过滤器：解决乱码问题-->
<filter>
    <filter-name>encoding</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>utf-8</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>encoding</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```



## Tomcat乱码问题

**数据传输过程产生的乱码：**修改Tomcat配置文件：server.xml，设置编码为utf-8

```xml
<Connector URIEncoding="utf-8" port="8080" protocol="HTTP/1.1"
           connectionTimeout="20000"
           redirectPort="8443" />
```

**控制台乱码：**修改**logging.properties**文件，设置控制台编码为GBK，因为**Windows的默认编码是GBK。**

```properties
java.util.logging.ConsoleHandler.encoding = GBK
```



# JSON

## 什么是JSON

* JSON（JavaScript Object Notation, JS 对象标记）是一种轻量级的数据交换格式，目前使用广泛。

* 采用完全独立于编程语言的**文本格式**来存储和表示数据。

* 简洁和清晰的层次结构使得JSON成为理想的数据交换语言。

* 易于阅读和编写，同时也易于机器解析和生成，并有效的提升网络传输效率。

  

**前后端分离：**

* 后端部署，提供接口，提供数据

* 前端独立部署，负责渲染后端的数据

* 前后端通过JSON的格式传输数据。

  

在JavaScript语言中，一切都是对象。因此，任何JavaScript支持的类型都可以通过JSON来表示，例如字符串、数字、对象、数组等。格式：

* 对象表示为键值对、数据由逗号分隔
* 花括号保存对象
* 方括号保存数组

```javascript
// JavaScript对象
var user = {
    name : "小强",
    age : 3,
    sex: "男"
};

// Json字符串
var json = '{"name":"小强","age":3,"sex":"男"}';

// 对象转JSON字符串
var json = JSON.stringify(user);
console.log(json); // 打印JSON字符串

// JSON字符串转对象
var Object = JSON.parse(json);
console.log(Object); // 打印对象
```



## 代码测试

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>

    <script type="text/javascript">
        // 编写一个JavaScript对象 ES6
        var user = {
            name : "小强",
            age : 3,
            sex: "男"
        };

        // 将js对象转换为JSON字符串
        var json = JSON.stringify(user);
        console.log(json);
        console.log(user);

        console.log("===========");

        // 将JSON字符串转换为JavaScript对象
        var Object = JSON.parse(json);
        console.log(Object);


    </script>

</head>
<body>

</body>
</html>
```



## Controller返回JSON数据：Jackson

* Jackson是目前比较好的json解析工具
* 还有阿里巴巴的fastjson等等
* 这里使用Jackson需要导入jar包：

```xml
<!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind -->
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.11.2</version>
</dependency>
```

* 配置SpringMVC需要的配置

**web.xml：**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <!--1.注册Servlet-->
    <servlet>
        <servlet-name>SpringMVC</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!--通过初始化参数指定SpingMVC配置文件的位置，进行关联-->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc-servlet.xml</param-value>
        </init-param>
        <!--启动顺序，数字越小，启动越早-->
        <load-on-startup>1</load-on-startup>
    </servlet>
    
    <!--所有请求都会被SpringMVC拦截-->
    <servlet-mapping>
        <servlet-name>SpringMVC</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

    <!--配置过滤器处理中文乱码问题-->
    <filter>
        <filter-name>encoding</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>utf-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>encoding</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
</web-app>
```



**springmvc-servlet.xml:**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

    <!--自动扫描指定的包，下面的注解类交给IOC容器管理-->
    <context:component-scan base-package="com.yan.controller"/>

    <!--视图解析器-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"
          id="internalResourceViewResolver">
        <!--前缀-->
        <property name="prefix" value="/WEB-INF/jsp/"/>
        <!--后缀-->
        <property name="suffix " value=".jsp"/>
    </bean>

</beans>
```



**User实体类，测试Controller：**

```java
package com.yan.pojo;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {
    private String name;
    private int age;
    private String sex;
}
```



**测试类，UserController：**

```java
package com.yan.controller;

import com.yan.pojo.User;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
public class UserController {

    @RequestMapping("j1")
    @ResponseBody // @ResponseBody: 不会走视图解析器，返回真实返回的东西，一般是字符串
    public String json1(){

        // 创建一个对象
        User user = new User("小强", 3, "男");

        return user.toString();
    }
}
```

**结果：**

![toString结果](/images/SpringMVC%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/toString%E7%BB%93%E6%9E%9C.png)



**使用Jackson将对象转换成JSON字符串：**

```java
@RequestMapping("/j2")
@ResponseBody // @ResponseBody: 不会走视图解析器，返回真实返回的东西，一般是字符串
public String json2() throws JsonProcessingException {

    //Jackson, ObjectMapper
    ObjectMapper mapper = new ObjectMapper();

    // 创建一个对象
    User user = new User("小强", 3, "男");

    String s = mapper.writeValueAsString(user);

    return s;
}
```

结果：

![Jackson转换json](/images/SpringMVC%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/Jackson%E8%BD%AC%E6%8D%A2json.png)



**乱码问题：**

方式一：通过@RequestMapping的produces属性来实现，修改下代码：

```java
// produces: 指定响应体返回类型和编码
@RequestMapping(value = "/json1", produces= "application/json;charset=utf-8")
```

结果：

![解决乱码1](/images/SpringMVC%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E8%A7%A3%E5%86%B3%E4%B9%B1%E7%A0%811.png)

方式二：由SpringMVC统一解决，在springmvc-servlet.xml配置文件中添加一段消息StringHttpMessageConverter转换配置！

```xml
<!--SpringMVC解决JSON乱码的问题-->
<mvc:annotation-driven>
    <mvc:message-converters register-defaults="true">
        <bean class="org.springframework.http.converter.StringHttpMessageConverter">
            <constructor-arg value="utf-8"/>
        </bean>
        <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
            <property name="objectMapper">
                <bean class="org.springframework.http.converter.json.Jackson2ObjectMapperFactoryBean">
                    <property name="failOnEmptyBeans" value="false"/>
                </bean>
            </property>
        </bean>
    </mvc:message-converters>
</mvc:annotation-driven>
```



## Controller返回JSON数据：FastJson

​	FastJson.jar是阿里巴巴开发的一款专门用于Java开发的包，方便用于实现Json字符串与JavaBean对象的转换。

**导入依赖：**

```xml
<!-- https://mvnrepository.com/artifact/com.alibaba/fastjson -->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.73</version>
</dependency>
```

**fastjson三个主要的类：**

* JSONObject代表json对象：实现了Map接口
* JSONArray代表json数组：内部有List接口中的方法来完成操作
* JSON代表JSONObject和JSONArray的转化

```java
package com.yan.controller;

import com.alibaba.fastjson.JSON;
import com.yan.pojo.User;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.ArrayList;

// 使用fastjson将对象转换为json字符串
@RestController
public class UserController3 {

    @RequestMapping("/f1")
    public String json1(){

        ArrayList<User> userList = new ArrayList<User>();
        User user1 = new User("小强1", 1, "男");
        User user2 = new User("小强2", 6, "女");
        User user3 = new User("小强3", 5, "男");
        User user4 = new User("小强4", 3, "男");

        userList.add(user1);
        userList.add(user2);
        userList.add(user3);
        userList.add(user4);
		// [{"age":1,"name":"小强1","sex":"男"},{"age":6,"name":"小强2","sex":"女"},{"age":5,"name":"小强3","sex":"男"},{"age":3,"name":"小强4","sex":"男"}]
        String s = JSON.toJSONString(userList);
        return s;
    }
}
```





## @RestController

使该类下定义的所有接口都不会走视图解析器，返回一般是字符串

```java
package com.yan.controller;

import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.yan.pojo.User;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController // 是该类下定义的所有接口都不走视图解析器，一般直接返回字符串
public class UserController2 {

    @RequestMapping("/u1")
    public String json1() throws JsonProcessingException {
        // Jackson, ObjectMapper
        ObjectMapper mapper = new ObjectMapper();

        // 创建一个对象
        User user = new User("小强", 3, "男");

        // 将对象转换为JSON字符串
        String s = mapper.writeValueAsString(user);

        return s;
    }
}
```



```xml
@Controller: 会走视图解析器，返回数据，以及要转发或重定向的页面或请求，加在类上
@RestController：不走视图解析器，一般返回字符串，加在类上
@ResponseBody ：和@Controller配合使用，加在方法上，表示该方法不走视图解析器，一般返回字符串
```



# 整合SSM

## 环境要求

* IDEA
* MySQL 8.0.21
* Tomcat 9
* Maven 3.6.3(idea自带)



## 数据库环境

创建一个存放书籍数据的数据库表



# SpringMVC: Ajax技术

* AJAX = Asynchronous JavaScript and XML（异步的JavaScript 和 XML）（异步无刷新请求）
* Ajax是一种在无需重新加载整个网页的情况下，能够更新部分网页的技术
* Ajax不是一种新的编程语言，而是一种用于创建更好更快以及交互性更强的Web应用程序的技术

* 传统的网页（即不用Ajax技术的网页），想要更新内容或者提交一个表单，都需要记载整个网页
* 使用ajax技术的网页，通过在后台服务器进行少量的数据交换，就可以实现异步局部更新
* 使用ajax技术，用户可以创建接近本地桌面应用的直接、高可用、更丰富、更动态的Web用户界面



## jQuery.ajax

* Ajax的核心是XMLHttpRequest对象（XHR）。XHR向服务器发送请求和解析服务器响应提供了接口。能够以异步的方式从服务器获取新数据。
* jQuery提供多个与AJAX有关的方法
* 通过jQuery AJAX方法，能够使用HTTP Get和HTTP Post从远程服务器上请求文本、HTML、XML或JSON-同时能够把这些外部数据直接加载入网页的被选元素中。
* jQuery Ajax本质就是XMLHttpRequest，对他进行了封装，方便调用！

![ajax](/images/SpringMVC%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/ajax.png)