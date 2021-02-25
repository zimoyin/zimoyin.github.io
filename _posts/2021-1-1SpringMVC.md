# SpringMVC

作者: 子墨

参考：狂神说SpringMVC、百度百科





# 一、MVC架构



> 简介

经典MVC模式中，M是指业务模型，V是指用户界面，C则是控制器，使用MVC的目的是将M和V的实现[代码](https://baike.baidu.com/item/代码/86048)分离，从而使同一个程序可以使用不同的表现形式。其中，View的定义比较清晰，就是用户界面。



> MVC含义

**V:** 即**View**视图是指用户看到并与之交互的界面。比如由html元素组成的网页界面，或者软件的客户端界面。MVC的好处之一在于它能为应用程序处理很多不同的视图。在视图中其实没有真正的处理发生，它只是作为一种输出数据并允许用户操纵的方式。

**M:** 即**model**模型代表一个存取数据的对象或 JAVA POJO。它也可以带有逻辑，在数据变化时更新控制器。

**C:** 即**controller**控制器是指控制器接受用户的输入并调用模型和视图去完成用户的需求，控制器本身不输出任何东西和做任何处理。它只是接收请求并决定调用哪个模型构件去处理请求，然后再确定用哪个视图来显示返回的数据。



> MVC框架做了什么

1. 将URL映射到java类或java类的方法
2. 封装用户提交的数据
3. 处理请求--调用相关的业务逻辑处理--封装相应的数据
4. 将响应的数据进行渲染 .jsp/html 等表示层数据





# 二、回顾Servlet

### 0. 创建一个maven项目

记得设置maven的下载源

### 1. 导入依赖

导入的文件不光是Servlet要用，后面的SpringMVC也要用

```xml
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
    <scope>test</scope>
</dependency>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.2.0.RELEASE</version>
</dependency>

<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>servlet-api</artifactId>
    <version>2.5</version>
</dependency>

<dependency>
    <groupId>javax.servlet.jsp</groupId>
    <artifactId>jsp-api</artifactId>
    <version>2.2</version>
</dependency>

<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>jstl</artifactId>
    <version>1.2</version>
</dependency>
```



### 2. 添加框架支持

右键项目--->	Add Framework Support...   --->	Web Application (4.0) 前面打钩	

![image-20210208143716435](SpringMVC.assets/image-20210208143716435.png)



![image-20210208143738817](SpringMVC.assets/image-20210208143738817.png)



### 4. 添加Tomcat

![image-20210208145955195](SpringMVC.assets/image-20210208145955195.png)



![image-20210208150132473](SpringMVC.assets/image-20210208150132473.png)



![image-20210208150241464](SpringMVC.assets/image-20210208150241464.png)



### 5. 代码

> index.jsp

```html
<%--from表单向/hello提交数据，/hello处理数据并转发给test.jsp进行展示--%>
<form action="/hello" method="post">
    <input type="text" name="method">
    <input type="submit">
</form>
```

> WEB-INF\jsp\test.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
     <%--接受数据并展示在页面上--%>
	${msg}
</body>
</html>
```



> com.zimo.servlet.HelloServlet

```java
public class HelloServlet extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // 1.获取前端参数
        String var = request.getParameter("method");
        if (var.equals("add"))  request.getSession().setAttribute("msg","执行了add方法");
        if (var.equals("delete"))  request.getSession().setAttribute("msg","执行了delete方法");
        // 2.调用业务层
        // 3.（返回）视图转发或者重定向
        request.getRequestDispatcher("/WEB-INF/jsp/test.jsp").forward(request,response);
    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        doPost(request,response);
    }
}
```



> web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <!--配置servlet-->
    <servlet>
        <servlet-name>hello</servlet-name>
        <servlet-class>com.zimo.servlet.HelloServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>hello</servlet-name>
        <url-pattern>/hello</url-pattern>
    </servlet-mapping>

    <!--超时时间-->
    <!--<session-config>-->
    <!--    <session-timeout>15</session-timeout>-->
    <!--</session-config>-->


    <!--欢迎页-->
    <!--<welcome-file-list>-->
    <!--    <welcome-file></welcome-file>-->
    <!--</welcome-file-list>-->

</web-app>
```



### 注意:

- **WEB-INF**包下面的内容用户不能直接访问,但是可以通过转发来间接访问
- 访问不到hello：检查URL路径是否正确 http://localhost:8080/项目名/hello
- 创建maven时选择webapp，然后删除这个项目另起一个新项目后一些莫名的bug消失了（大雾）



# 三、初识SpringMVC

 Spring Web MVC是一种基于Java的实现了Web MVC设计模式的请求驱动类型的轻量级Web框架。Spring MVC属于SpringFrameWork的后续产品，已经融合在Spring Web Flow里面。



> springMVC原理图

![image-20210208160023748](SpringMVC.assets/image-20210208160023748.png)







### 1. spring mvc的特点

1.他是基于组件技术的,全部是应用对象,无论是控制器或视图,还是业务对象的类都是java组件,并且和spring提供的其他基础结构紧密集成.

2.不依赖于servlet Api

3.可以使用任意一种视图技术,不仅仅是jsp.

4.支持各种请求资源的映射策略.

5.是易于扩展的.

### 2. spring mvc的工作流程

1.用户提交请求至前端控制器DispatcharServlet

2.DispatcharServlet控制器查询一个或多个handlerMaping,找到处理请求的controller

3.DispatcharServlet控制器将请求提交到controller

4.controller进行业务逻辑处理后,返回ModelAndView对象,该对象本身包含了视图对象的信息

5.DispatcherServlet控制器查询一个或多个ViewResoler视图解析器,找到ModelAndView对象指定的视图对象.

6.视图负责将结果返回到客户端.

![image-20210210152855178](SpringMVC.assets/image-20210210152855178.png)

执行流程再分析
1. DispatcherServlet表示前置控制器，是整个SpringMVC的控制中心。用户发出请求， DispatcherServlet接收请求并拦截请求。   我们假设请求的url为 : http://localhost:8080/SpringMVC/hello 如上url拆分成三部分： http://localhost:8080服务器域名 SpringMVC部署在服务器上的web站点 hello表示控制器 通过分析，如上url表示为：请求位于服务器localhost:8080上的SpringMVC站点的hello控制器。   
2.  HandlerMapping为处理器映射。DispatcherServlet调用HandlerMapping,HandlerMapping根据 请求url查找Handler。 
3. HandlerExecution表示具体的Handler,其主要作用是根据url查找控制器，如上url被查找控制器 为：hello。 
4.  HandlerExecution将解析后的信息传递给DispatcherServlet,如解析控制器映射等。
5. HandlerAdapter表示处理器适配器，其按照特定的规则去执行Handler。 
6.  Handler让具体的Controller执行。 
7.  Controller将具体的执行信息返回给HandlerAdapter,如ModelAndView。
8.  HandlerAdapter将视图逻辑名或模型传递给DispatcherServlet。
9. DispatcherServlet调用视图解析器(ViewResolver)来解析HandlerAdapter传递的逻辑视图名。
10. 视图解析器将解析的逻辑视图名传给DispatcherServlet。
11. DispatcherServlet根据视图解析器解析的视图结果，调用具体的视图。
12.  最终视图呈现给用户。
      在这里先听一遍原理，不理解没有关系，我们马上来写一个对应的代码实现大家就明白了，如果不明 白，那就写10遍，没有笨人，只有懒人！ 



>  DispatcharServlet  是什么？

他就是HttpServlet

![image-20210210152258254](SpringMVC.assets/image-20210210152258254.png)





> 为什么会出现控制器?

在SpringMVC 中，控制器Controller 负责处理由DispatcherServlet 分发的请求，它把用户请求的数据经过业务处理层处理之后封装成一个Model ，然后再把该Model 返回给对应的View 进行展示。

![image-20210208151929640](SpringMVC.assets/image-20210208151929640.png)



### 3. 代码

1. 记得创建maven项目
2. 添加web框架支持
3. 还有导包

> /WEB-INF/web.xml

#### 1. 配置web.xml 

注册DispatcherServlet

```xml
<!--1. 注册DispatcherServlet-->
<servlet>
    <servlet-name>springmvc</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <!--关联一个SpringMVC的配置文件: [servlet-name]-servlet.xml -->
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath*:springmvc-servlet.xml</param-value>
    </init-param>
    <!--启动级别-1-->
    <load-on-startup>1</load-on-startup>
</servlet>
<!--/ 匹配所有的请求;(不包括.jsp)-->
<!--/* 匹配所有的请求;(包括.jsp)-->
<!--所有的请求都要经过该servlet-->
<servlet-mapping>
    <servlet-name>springmvc</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>
```



> resources/springmvc-servlet.xml

#### 2. 编写SpringMVC 的 配置文件

名称：springmvc-servlet.xml : [servletname]-servlet.xml 说明，这里的名称要求是按照官方来的

```xml
<!--正常不用写这两个东西-->
<!--添加 处理映射器-->
<bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>
<!--添加 处理适配器-->
<bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>

<!--视图解析器：DispatcherServlet给他的ModelAndView-->
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="internalResourceViewResolver">
    <!--前缀-->
    <property name="prefix" value="/WEB-INF/jsp/"/>
    <!--后缀-->
    <property name="suffix" value=".jsp"/>
</bean>

```



> com.zimo.controller.HelloController

####  3. 编写Controller

编写我们要操作业务Controller ，要么实现Controller接口，要么增加注解；需要返回一个 ModelAndView，装数据，封视图；

```java
public class HelloController implements Controller {

    @Override
    public ModelAndView handleRequest(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception {
        //ModelAndView 模型和视图
        ModelAndView mv = new ModelAndView();

        //封装对象，放入ModelAndView中。
        mv.addObject("msg","HelloSpringMVC!!!!!!");
        //封装要跳转的视图，放入ModelAndView中
        mv.setViewName("hello");//他会被视图解析器拼成: /WEB-INF/jsp/hello.jsp


        return mv;
    }
}
```

**导包要注意一下!!!!!!**



![image-20210214170534095](SpringMVC.assets/image-20210214170534095.png)





> resources/springmvc-servlet.xml

#### 4. 注册bean

 将自己的类交给SpringIOC容器，注册bean

```xml
<!--Handler-->
<bean id="/hello" class="com.zimo.controller.HelloController"/>
```



> /WEB-INF/jsp/hello.jsp

#### 5. 写jsp

写要跳转的jsp页面，显示ModelandView存放的数据，以及我们的正常页面；

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    ${msg}
</body>
</html>
```





### 4. 运行代码 （解决404）

#### 1. 添加jar包

为了防止idea不自动导包，导致访问不到页面404



![image-20210214171334751](SpringMVC.assets/image-20210214171334751.png)

![image-20210214171431450](SpringMVC.assets/image-20210214171431450.png)

![image-20210214171505914](SpringMVC.assets/image-20210214171505914.png)



#### 2. 配置Tomcat

![image-20210214171549866](SpringMVC.assets/image-20210214171549866.png)



#### 3. 运行

结果

![image-20210214171650379](SpringMVC.assets/image-20210214171650379.png)

==运行后注意URL地址==



# 四、SpringMVC注解

#### 1. 新建项目

新建一个maven项目，或者新建一个Moudle

#### 2. 引入依赖

在pom.xml文件引入相关的依赖：主要有Spring框架核心库、Spring MVC、servlet , JSTL等。我们在父依赖中已经引入了！ 也就是前面的回顾Servlet阶段

#### 3. 开启静态资源过滤

在pom.xml中写入(父、子文件都要写)

```xml
<!--maven的资源过滤问题-->
<build>
    <resources>
        <resource>
            <directory>src/main/resources</directory>
            <includes>
                <include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>true</filtering>
        </resource>
        <resource>
            <directory>src/main/java</directory>
            <includes>
                <include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>true</filtering>
        </resource>
    </resources>
</build>
```



#### 4. 添加框架支持

右键项目--->	Add Framework Support...   --->	Web Application (4.0) 前面打钩	

#### 5. 配置web.xml 

注意点： 

- 注意web.xml版本问题，要最新版！

-  注册DispatcherServlet 关联SpringMVC的配置文件 

- 启动级别为1 
- 映射路径为 / 【不要用/*，会404】



web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    
        <!--1. 注册DispatcherServlet-->
        <servlet>
            <servlet-name>springmvc</servlet-name>
            <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
            <!--通过初始化参数指定SpringMVC配置文件的位置，进行关联-->
            <init-param>
                <param-name>contextConfigLocation</param-name>
                <param-value>classpath*:springmvc-servlet.xml</param-value>
            </init-param>
            <!-- 启动顺序，数字越小，启动越早 -->
            <load-on-startup>1</load-on-startup>
        </servlet>
        <!--/ 匹配所有的请求;(不包括.jsp)-->
        <!--/* 匹配所有的请求;(包括.jsp)-->
        <!--所有的请求都要经过该servlet-->
        <servlet-mapping>
            <servlet-name>springmvc</servlet-name>
            <url-pattern>/</url-pattern>
        </servlet-mapping>
    
</web-app>
```



#### 6. 添加Spring MVC配置文件 

- 让IOC的注解生效 

- 静态资源过滤 ：HTML . JS . CSS . 图片 ， 视频 ..... 

- MVC的注解驱动 

- 配置视图解析器 



在resource目录下添加springmvc-servlet.xml配置文件，配置的形式与Spring容器配置基本类似， 为了支持基于注解的IOC，设置了自动扫描包的功能，具体配置信息如下

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans-4.2.xsd
        http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc-4.2.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context-4.2.xsd">

        <!--注意要导入约束-->

        <!-- 自动扫描包，让指定包下的注解生效,由IOC容器统一管理 -->
        <context:component-scan base-package="com.zimo.controller"/>

        <!-- 让Spring MVC不处理静态资源 -->
        <mvc:default-servlet-handler />

        <!--
        支持mvc注解驱动
        在spring中一般采用@RequestMapping注解来完成映射关系
        要想使@RequestMapping注解生效
        必须向上下文中注册DefaultAnnotationHandlerMapping
        和一个AnnotationMethodHandlerAdapter实例
        这两个实例分别在类级别和方法级别处理。
        而annotation-driven配置帮助我们自动完成上述两个实例的注入。
        -->
        <mvc:annotation-driven />


            <!-- 视图解析器 -->
        <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver "
              id="internalResourceViewResolver">
            <!-- 前缀 -->
            <property name="prefix" value="/WEB-INF/jsp/" />
            <!-- 后缀 -->
            <property name="suffix" value=".jsp" />
        </bean>
</beans>
```

在视图解析器中我们把所有的视图都存放在/WEB-INF/目录下，这样可以保证视图安全，因为这个 目录下的文件，客户端不能直接访问。 

#### 5. 代码

##### 1. 创建Controller

编写一个Java控制类： com.zimo.controller.HelloController , 注意编码规范

```java
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
//这个也可以不写这个注解，不写下面的地址就写: 项目名/hello
@RequestMapping("/HelloController")
public class HelloController {

    //真实访问地址 : 项目名/HelloController/hello
    @RequestMapping("/hello")
    public String hello(Model model){
        //向模型中添加属性msg与值，可以在JSP页面中取出并渲染
        model.addAttribute("msg","hello,SpringMVC");

        return "hello";//   WEB-INF/jsp/hello.jsp
    }
}
```



##### 2. hello.jsp

WEB-INF/jsp/hello.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
        ${msg}
</body>
</html>
```



#### 6. 运行代码

##### 1. 添加Tomcat

这里不过多解释，详情见回顾Servlet的添加Tomcat

##### 2. 配置Tomcat

这里不过多解释，详情见回顾Servlet的添加Tomcat

##### 3. 导入所需的包

这里不过多解释，详情见初识SpringMVC的添加jar包

##### 4. 启动Tomcat

##### 5. 访问网站

注意访问网站的网址为 ： 项目名/HelloController/hello



#### 7. / 和 /* 的区别：

 < url-pattern > / </ url-pattern > 不会匹配到.jsp， 只针对我们编写的请求； 即：.jsp 不会进入spring的 DispatcherServlet类 。 < url-pattern > /* </ url-pattern > 会匹配 *.jsp， 会出现返回 jsp视图 时再次进入spring的DispatcherServlet 类，导致找不到对应的 controller所以报404错。 



### 8. 总结

通过上面两章我们发现创建一个能玩的SpringMVC无外乎这几个步骤

1. 创建Maven项目添加web框架（创建web项目），导入所需的包
2. 配置web.xml , 注册DispatcherServlet 
3. 编写springmvc的配置文件 
4. 接下来就是去创建对应的控制类 , Controller 
5.  最后完善前端视图和controller之间的对应 
6. 测试运行调试. 使用springMVC必须配置的三大件： 处理器映射器、处理器适配器、视图解析器 通常，我们只需要手动配置视图解析器，而处理器映射器和处理器适配器只需要开启注解驱动即可，而 省去了大段的xml配置



# 五、关于SpringMVC的那些事

### 1. Controller 

- 控制器Controller 控制器复杂提供访问应用程序的行为，通常通过接口定义或注解定义两种方法实现。
- 控制器负责解析用户的请求并将其转换为一个模型。 
- 在Spring MVC中一个控制器类可以包含多个方法 
- 在Spring MVC中，对于Controller的配置方式有很多种



>  使用接口开发

Controller是一个接口，在org.springframework.web.servlet.mvc包下，接口中只有一个方法；

```java
//实现该接口的类获得控制器功能
public interface Controller {
//处理请求且返回一个模型与视图对象
ModelAndView handleRequest(HttpServletRequest var1, HttpServletResponse var2) throws Exception;
}
```

使用接口开发的话要配置Controller的实现的bean到配置文件中。并且还要有视图解析器，当然处理适配器、处理映射器这两个不用写

> 使用注解开发

```java
@Controller 在类上面有这个注解那就是控制器
@RequestMapping("/HelloController") 这个注解是用来指定访问路径的，通常放在类或方法上(方法上必须有，类可以没有)，用在类上面是是指该路径为父路径
```

使用注解开发不需要配置bean，但是也必须要配置视图解析器。还有**自动扫描包**、**静态资源过滤**、**注解驱动**



-----

### 2. RestFul风格

> 简介

RESTFUL是一种网络应用程序的设计风格和开发方式，基于[HTTP](https://baike.baidu.com/item/HTTP/243074)，可以使用[XML](https://baike.baidu.com/item/XML/86251)格式定义或[JSON](https://baike.baidu.com/item/JSON/2462549)格式定义。RESTFUL适用于移动互联网厂商作为业务接口的场景，实现第三方[OTT](https://baike.baidu.com/item/OTT/9960940)调用移动网络资源的功能，动作类型为新增、变更、删除所调用资源。

说人话就是他是一种URL的风格。比如：

```http
正常URL  			http：//www.baidu.com?admin=root&paw=123456
RestFul风格  		http：//www.baidu.com/root/123456
```

通过观察发现RestFul风格呢他更加简洁安全。



> 特点



> 对比

注意该项目是在第四章基础上进行的修改

####  1. 正常传参

- com.zimo.controller.HelloController

```java
@Controller
public class HelloController {
    // 接收前端的参数，并返回结果
    @RequestMapping("/add")
    public String hello(int a,int b,Model model){

        int r = a+b;

        //向模型中添加属性msg与值，可以在JSP页面中取出并渲染
        model.addAttribute("msg","结果为： "+r);

        return "hello";//   WEB-INF/jsp/hello.jsp
    }
}

```

前端访问： http://localhost:8080/add?a=1&b=2	成功输出



#### 2. RestFul风格

- com.zimo.controller.HelloController

```java
@Controller
public class HelloController {
	//RestFul风格
    // 接收前端的参数，并返回结果
    @RequestMapping("/add2/{a}/{b}")
    public String hello2(@PathVariable int a,@PathVariable int b, Model model){
        int r = a+b;
        //向模型中添加属性msg与值，可以在JSP页面中取出并渲染
        model.addAttribute("msg","结果为： "+r);
        return "hello";//   WEB-INF/jsp/hello.jsp
    }
}
```

```java
@PathVariable 注解，让方法参数的值对应绑定到一个URI模板变量上. 也就是add2/1/2   传来的值1给a，值2给b
```

前端访问http://localhost:8080/add2/1/2  成功输出



#### 3. 使用method属性指定请求类型 

用于约束请求的类型，可以收窄请求范围。指定请求谓词的类型如GET, POST, HEAD, OPTIONS, PUT, PATCH, DELETE, TRACE等

指定方法后只能通过对应的方法请求，否则会返回405。如：指定GET后只能用GET请求，指定POST后只能用POST请求，指定DELETE后只能用DELETE请求



例子：

当出现路径一样的时候，会根据HTTP方法来执行不同的java方法

```java
@Controller
@RequestMapping("/c2")
public class HelloController2 {
	//下面两种注解都可以
//    @RequestMapping(value = "/add/{a}/{b}",method = RequestMethod.GET)
    @GetMapping("/add/{a}/{b}")
    public String add(@PathVariable int a, @PathVariable int b, Model model){
        //向模型中添加属性msg与值，可以在JSP页面中取出并渲染
        model.addAttribute("msg","通过get方法访问成功!"+" 结果为: "+a+b);
        return "hello";
    }
    
    @PostMapping("/add/{a}/{b}")
    public String add2(@PathVariable int a, @PathVariable int b, Model model){
        //向模型中添加属性msg与值，可以在JSP页面中取出并渲染
        model.addAttribute("msg","通过post方法访问成功!"+" 结果为: "+a+b);
        return "hello";
    }
}
```

前端通过GET方法访问http://localhost:8080/c2/add/1/2   成功输出



所有的地址栏请求默认都会是 HTTP GET 类型的。

 方法级别的注解变体有如下几个： 

组合注解 

```java
@GetMapping
@PostMapping
@PutMapping
@DeleteMapping
@PatchMapping
```

@GetMapping 是一个组合注解 它所扮演的是 @RequestMapping(method =RequestMethod.GET) 的一个快捷方式。 平时使用的会比较多！



### 3. 结果(视图)跳转方式

这里介绍了三种跳转方式

1. ModelAndView 就是通过SpringMVC视图解析器的方式跳转
2. 通过Servlet的方式跳转
3. SpringMVC(不通过视图解析器)的方式跳转

在这个小节里我们代码是没有写的，所以这里就只有一些例子而已

> ModelAndView 

设置ModelAndView对象 , 根据view的名称 , 和视图解析器跳到指定的页面 . 页面 : {视图解析器前缀} + viewName +{视图解析器后缀}



```xml
<!-- 视图解析器 -->
<bean
class="org.springframework.web.servlet.view.InternalResourceViewResolver"
id="internalResourceViewResolver">
<!-- 前缀 -->
<property name="prefix" value="/WEB-INF/jsp/" />
<!-- 后缀 -->
<property name="suffix" value=".jsp" />
</bean>

```

对应的controller类

```java
public class ControllerTest1 implements Controller {
    public ModelAndView handleRequest(HttpServletRequest httpServletRequest,
    HttpServletResponse httpServletResponse) throws Exception {
    //返回一个模型视图对象
    ModelAndView mv = new ModelAndView();
    mv.addObject("msg","ControllerTest1");
    mv.setViewName("test");
    return mv;
    }
}
```



> ServletAPI 

通过设置ServletAPI , 不需要视图解析器 . 1. 通过HttpServletResponse进行输出 2. 通过HttpServletResponse实现重定向 3. 通过HttpServletResponse实现转发

```java
@Controller
public class ResultGo {
    @RequestMapping("/result/t1")
    public void test1(HttpServletRequest req, HttpServletResponse rsp)
    throws IOException {
    rsp.getWriter().println("Hello,Spring BY servlet API");
    }
    @RequestMapping("/result/t2")
    public void test2(HttpServletRequest req, HttpServletResponse rsp)
    throws IOException {
    rsp.sendRedirect("/index.jsp");
    }
    @RequestMapping("/result/t3")
    public void test3(HttpServletRequest req, HttpServletResponse rsp)
    throws Exception {
    //转发
    req.setAttribute("msg","/result/t3");
    req.getRequestDispatcher("/WEB-INF/jsp/test.jsp").forward(req,rsp);
    }
}
```



> SpringMVC 

**通过SpringMVC来实现转发和重定向 - 无需视图解析器；**

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

**通过SpringMVC来实现转发和重定向 - 有视图解析器；** 

重定向 , 不需要视图解析器 , 本质就是重新请求一个新地方嘛 , 所以注意路径问题. 可以重定向到另外一个请求实现 .



```java
@Controller
public class ResultSpringMVC2 {
    @RequestMapping("/rsm2/t1")
    public String test1(){
    //转发
    return "test";
    }
    @RequestMapping("/rsm2/t2")
    public String test2(){
    //重定向
    return "redirect:/index.jsp";
    //return "redirect:hello.do"; //hello.do为另一个请求/
    }
}
```



### 4. 数据处理

在这一小节中我们学习对传值的处理，防止前端传入非法的值，和怎么将前端传来的值封装成一个对象。以及将后端的数据传回前端(jsp)

> 处理提交数据

**1、提交的域名称和处理方法的参数名一致**

```java
@Controller
public class VarController {
    @RequestMapping("/name")
    public String name(String name, Model model){
        
        //向模型中添加属性msg与值，可以在JSP页面中取出并渲染
        model.addAttribute("msg",name);
        return "hello";//   WEB-INF/jsp/hello.jsp
    }
}

```
前端传值

```
提交数据 : http://localhost:8080/name?name=子墨
参数name为子墨

提交数据http://localhost:8080/name?sb=6666
方法也会被执行，但是方法参数name为null
```



**2、提交的域名称和处理方法的参数名不一致**

处理方法 :

```java
//@RequestParam("username") : username提交的域的名称 .
@RequestMapping("/name")
public String name(@RequestParam("username") String name, Model model){
    //向模型中添加属性msg与值，可以在JSP页面中取出并渲染
    model.addAttribute("msg",name);
    System.out.println(name);
    return "hello";//   WEB-INF/jsp/hello.jsp
}
```

前端传值

```
提交数据 : http://localhost:8080/name?username=子墨
前端返回正确的值

提交数据 : http://localhost:8080/name?name=子墨
服务器返回400

提交数据http://localhost:8080/name?sb=6666
服务器返回400
```

```
@RequestParam("参数名")	接收一个参数名的值，并将值交给被这个注解修饰的变量。
注意:
用了@RequestParam就不能传乱七八糟的值了，只能传RequestParam对应的参数名，否则会返回400
```



**3、提交(接收)的是一个对象**

要求提交的表单域和对象的属性名一致  , 参数使用对象即可

1、实体类

```java
public class User {
   private int id;
   private String name;
   private int age;
   //构造
   //get/set
   //tostring()
}
```

2、提交数据 : http://localhost:8080/mvc04/user?name=kuangshen&id=1&age=15

3、处理方法 :

```java
@RequestMapping("/user")
public String user(User user){
   System.out.println(user);
   return "hello";
}
```

后台输出 : User { id=1, name='kuangshen', age=15 }

说明：如果使用对象的话，前端传递的参数名和对象名必须一致，否则就是null。



>  数据显示到前端

**第一种 : 通过ModelAndView**

我们前面一直都是如此 (实现Controller接口). 就不过多解释

```java
public class ControllerTest1 implements Controller {

   public ModelAndView handleRequest(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception {
       //返回一个模型视图对象
       ModelAndView mv = new ModelAndView();
       mv.addObject("msg","ControllerTest1");
       mv.setViewName("test");
       return mv;
  }
}
```



**第二种 : 通过ModelMap**

ModelMap

```java
@RequestMapping("/hello")
public String hello(@RequestParam("username") String name, ModelMap model){
   //封装要显示到视图中的数据
   //相当于req.setAttribute("name",name);
   model.addAttribute("name",name);
   System.out.println(name);
   return "hello";
}
```



**第三种 : 通过Model**

Model

```java
@RequestMapping("/ct2/hello")
public String hello(@RequestParam("username") String name, Model model){
   //封装要显示到视图中的数据
   //相当于req.setAttribute("name",name);
   model.addAttribute("msg",name);
   System.out.println(name);
   return "test";
}
```





>  对比 数据显示到前端 的三种方式

就对于新手而言简单来说使用区别就是：

```
Model 只有寥寥几个方法只适合用于储存数据，简化了新手对于Model对象的操作和理解；

ModelMap 继承了 LinkedMap ，除了实现了自身的一些方法，同样的继承 LinkedMap 的方法和特性；

ModelAndView 可以在储存数据的同时，可以进行设置返回的逻辑视图，进行控制展示层的跳转。
```

当然更多的以后开发考虑的更多的是性能和优化，就不能单单仅限于此的了解。

**请使用80%的时间打好扎实的基础，剩下18%的时间研究框架，2%的时间去学点英文，框架的官方文档永远是最好的教程。**



### 5. 乱码问题

测试步骤：

1、我们可以在首页编写一个提交的表单

```jsp
<form action="/e/t" method="post">
 <input type="text" name="name">
 <input type="submit">
</form>
```

2、后台编写对应的处理类

```java
@Controller
public class Encoding {
   @RequestMapping("/e/t")
   public String test(Model model,String name){
       model.addAttribute("msg",name); //获取表单提交的值
       return "test"; //跳转到test页面显示输入的值
  }
}
```

3、输入中文测试，发现乱码

![image-20210220141744969](image/image-20210220141744969.png)

不得不说，乱码问题是在我们开发中十分常见的问题，也是让我们程序猿比较头大的问题！

以前乱码问题通过过滤器解决 , 而SpringMVC给我们提供了一个过滤器 , 可以在web.xml中配置 .

修改了xml文件需要重启服务器！

```xml
<filter>
   <filter-name>encoding</filter-name>
   <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
   <init-param>
       <param-name>encoding</param-name>
       <param-value>utf-8</param-value>
   </init-param>
</filter>
<!-- 这里要注意/和/*的区别 -->
<filter-mapping>
   <filter-name>encoding</filter-name>
   <url-pattern>/*</url-pattern>
</filter-mapping>
```

但是我们发现 , 有些极端情况下.这个过滤器对get的支持不好 .

处理方法 :

1、修改tomcat配置文件 ：设置编码！

```xml
<Connector URIEncoding="utf-8" port="8080" protocol="HTTP/1.1"
          connectionTimeout="20000"
          redirectPort="8443" />
```

2、自定义过滤器

```java
package com.kuang.filter;

import javax.servlet.*;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletRequestWrapper;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.UnsupportedEncodingException;
import java.util.Map;

/**
* 解决get和post请求 全部乱码的过滤器
*/
public class GenericEncodingFilter implements Filter {

   @Override
   public void destroy() {
  }

   @Override
   public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
       //处理response的字符编码
       HttpServletResponse myResponse=(HttpServletResponse) response;
       myResponse.setContentType("text/html;charset=UTF-8");

       // 转型为与协议相关对象
       HttpServletRequest httpServletRequest = (HttpServletRequest) request;
       // 对request包装增强
       HttpServletRequest myrequest = new MyRequest(httpServletRequest);
       chain.doFilter(myrequest, response);
  }

   @Override
   public void init(FilterConfig filterConfig) throws ServletException {
  }

}

//自定义request对象，HttpServletRequest的包装类
class MyRequest extends HttpServletRequestWrapper {

   private HttpServletRequest request;
   //是否编码的标记
   private boolean hasEncode;
   //定义一个可以传入HttpServletRequest对象的构造函数，以便对其进行装饰
   public MyRequest(HttpServletRequest request) {
       super(request);// super必须写
       this.request = request;
  }

   // 对需要增强方法 进行覆盖
   @Override
   public Map getParameterMap() {
       // 先获得请求方式
       String method = request.getMethod();
       if (method.equalsIgnoreCase("post")) {
           // post请求
           try {
               // 处理post乱码
               request.setCharacterEncoding("utf-8");
               return request.getParameterMap();
          } catch (UnsupportedEncodingException e) {
               e.printStackTrace();
          }
      } else if (method.equalsIgnoreCase("get")) {
           // get请求
           Map<String, String[]> parameterMap = request.getParameterMap();
           if (!hasEncode) { // 确保get手动编码逻辑只运行一次
               for (String parameterName : parameterMap.keySet()) {
                   String[] values = parameterMap.get(parameterName);
                   if (values != null) {
                       for (int i = 0; i < values.length; i++) {
                           try {
                               // 处理get乱码
                               values[i] = new String(values[i]
                                      .getBytes("ISO-8859-1"), "utf-8");
                          } catch (UnsupportedEncodingException e) {
                               e.printStackTrace();
                          }
                      }
                  }
              }
               hasEncode = true;
          }
           return parameterMap;
      }
       return super.getParameterMap();
  }

   //取一个值
   @Override
   public String getParameter(String name) {
       Map<String, String[]> parameterMap = getParameterMap();
       String[] values = parameterMap.get(name);
       if (values == null) {
           return null;
      }
       return values[0]; // 取回参数的第一个值
  }

   //取所有值
   @Override
   public String[] getParameterValues(String name) {
       Map<String, String[]> parameterMap = getParameterMap();
       String[] values = parameterMap.get(name);
       return values;
  }
}
```

这个也是我在网上找的一些大神写的，一般情况下，SpringMVC默认的乱码处理就已经能够很好的解决了！

**然后在web.xml中配置这个过滤器即可！**

乱码问题，需要平时多注意，在尽可能能设置编码的地方，都设置为统一编码 UTF-8！



# 六、返回JSON

@RestController 将这个注解放在类或方法上面SpringMVC就知道你要返回一个JSON字符串

> Controller返回JSON数据

Jackson应该是目前比较好的json解析工具了

当然工具不止这一个，比如还有阿里巴巴的 fastjson 等等。

我们这里使用Jackson，使用它需要导入它的jar包；

### 1. 导包

```xml
<!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core -->
<dependency>
   <groupId>com.fasterxml.jackson.core</groupId>
   <artifactId>jackson-databind</artifactId>
   <version>2.9.8</version>
</dependency>
```

### 2. 配置  web.xml



```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
        version="4.0">

   <!--1.注册servlet-->
   <servlet>
       <servlet-name>SpringMVC</servlet-name>
       <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
       <!--通过初始化参数指定SpringMVC配置文件的位置，进行关联-->
       <init-param>
           <param-name>contextConfigLocation</param-name>
           <param-value>classpath:springmvc-servlet.xml</param-value>
       </init-param>
       <!-- 启动顺序，数字越小，启动越早 -->
       <load-on-startup>1</load-on-startup>
   </servlet>

   <!--所有请求都会被springmvc拦截 -->
   <servlet-mapping>
       <servlet-name>SpringMVC</servlet-name>
       <url-pattern>/</url-pattern>
   </servlet-mapping>

    <!-- 过滤所以请求并设置编码 -->
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
       <url-pattern>/</url-pattern>
   </filter-mapping>

</web-app>
```

### 3. 配置springmvc-servlet.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xmlns:context="http://www.springframework.org/schema/context"
      xmlns:mvc="http://www.springframework.org/schema/mvc"
      xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/mvc
       https://www.springframework.org/schema/mvc/spring-mvc.xsd">

   <!-- 自动扫描指定的包，下面所有注解类交给IOC容器管理 -->
   <context:component-scan base-package="com.zimo.controller"/>

   <!-- 视图解析器 -->
   <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"
         id="internalResourceViewResolver">
       <!-- 前缀 -->
       <property name="prefix" value="/WEB-INF/jsp/" />
       <!-- 后缀 -->
       <property name="suffix" value=".jsp" />
   </bean>

</beans>
```



### 3. 代码

#### 1. com.zimo.pojo.User

我们随便编写一个User的实体类，然后我们去编写我们的测试Controller；

```java
package com.zimo.pojo;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

//需要导入lombok。如果没有lombok就老老实实的写 get/set/有参构造/无参构造/toString 吧
@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {

   private String name;
   private int age;
   private String sex;
   
}
```



#### 2. com.zimo.controller.UserController

这里我们需要两个新东西，一个是@ResponseBody，一个是ObjectMapper对象，我们看下具体的用法

编写一个Controller；

```java
@Controller
public class UserController {

   @RequestMapping("/json1")
   @ResponseBody
   public String json1() throws JsonProcessingException {
       //创建一个jackson的对象映射器，用来解析数据
       ObjectMapper mapper = new ObjectMapper();
       //创建一个对象
       User user = new User("秦疆1号", 3, "男");
       //将我们的对象解析成为json格式
       String str = mapper.writeValueAsString(user);
       //由于@ResponseBody注解，这里会将str转成json格式返回；十分方便
       return str;
  }

}
```

#### 3. 配置Tomcat ， 启动测试

http://localhost:8080/json1



![image-20210219170328536](image/image-20210219170328536.png)

### 4. 解决乱码

发现出现了乱码问题，我们需要设置一下他的编码格式为utf-8，以及它返回的类型；

通过@RequestMaping的produces属性来实现，修改下代码

```java
//produces:指定响应体返回类型和编码
@RequestMapping(value = "/json1",produces = "application/json;charset=utf-8")
```

再次测试， http://localhost:8080/json1 ， 乱码问题OK！

![image-20210219170502338](image/image-20210219170502338.png)



**【注意：使用json记得处理乱码问题】**

> 代码优化

**乱码统一解决**

上一种方法比较麻烦，如果项目中有许多请求则每一个都要添加，可以通过Spring配置统一指定，这样就不用每次都去处理了！

我们可以在springmvc的配置文件上添加一段消息StringHttpMessageConverter转换配置！

```xml
<mvc:annotation-driven>
   <mvc:message-converters register-defaults="true">
       <bean class="org.springframework.http.converter.StringHttpMessageConverter">
           <constructor-arg value="UTF-8"/>
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



### 5. 返回json字符串统一解决*

在类上直接使用 **@RestController** ，这样子，里面所有的方法都只会返回 json 字符串了，不用再每一个都添加@ResponseBody ！我们在前后端分离开发中，一般都使用 @RestController ，十分便捷！

```java
@RestController
public class UserController {

   //produces:指定响应体返回类型和编码
   @RequestMapping(value = "/json1")
   public String json1() throws JsonProcessingException {
       //创建一个jackson的对象映射器，用来解析数据
       ObjectMapper mapper = new ObjectMapper();
       //创建一个对象
       User user = new User("秦疆1号", 3, "男");
       //将我们的对象解析成为json格式
       String str = mapper.writeValueAsString(user);
       //由于@ResponseBody注解，这里会将str转成json格式返回；十分方便
       return str;
  }

}
```

启动tomcat测试，结果都正常输出！



### 6. 测试集合输出

增加一个新的方法

```java
@RequestMapping("/json2")
public String json2() throws JsonProcessingException {

   //创建一个jackson的对象映射器，用来解析数据
   ObjectMapper mapper = new ObjectMapper();
   //创建一个对象
   User user1 = new User("秦疆1号", 3, "男");
   User user2 = new User("秦疆2号", 3, "男");
   User user3 = new User("秦疆3号", 3, "男");
   User user4 = new User("秦疆4号", 3, "男");
   List<User> list = new ArrayList<User>();
   list.add(user1);
   list.add(user2);
   list.add(user3);
   list.add(user4);

   //将我们的对象解析成为json格式
   String str = mapper.writeValueAsString(list);
   return str;
}
```

运行结果 : 十分完美，没有任何问题！

![图片](image/640)

### 7. 输出时间对象

增加一个新的方法

```java
@RequestMapping("/json3")
public String json3() throws JsonProcessingException {

   ObjectMapper mapper = new ObjectMapper();

   //创建时间一个对象，java.util.Date
   Date date = new Date();
   //将我们的对象解析成为json格式
   String str = mapper.writeValueAsString(date);
   return str;
}
```

运行结果 :



![图片](image/640)

- 默认日期格式会变成一个数字，是1970年1月1日到当前日期的毫秒数！
- Jackson 默认是会把时间转成timestamps形式

**解决方案：取消timestamps形式 ， 自定义时间格式**

```java
@RequestMapping("/json4")
public String json4() throws JsonProcessingException {

   ObjectMapper mapper = new ObjectMapper();

   //不使用时间戳的方式
   mapper.configure(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS, false);
   //自定义日期格式对象
   SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
   //指定日期格式
   mapper.setDateFormat(sdf);

   Date date = new Date();
   String str = mapper.writeValueAsString(date);

   return str;
}
```

运行结果 : 成功的输出了时间！



![图片](image/640)

> 抽取为工具类

**如果要经常使用的话，这样是比较麻烦的，我们可以将这些代码封装到一个工具类中；我们去编写下**

```java
package com.kuang.utils;

import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.databind.SerializationFeature;

import java.text.SimpleDateFormat;

public class JsonUtils {
   
   public static String getJson(Object object) {
       return getJson(object,"yyyy-MM-dd HH:mm:ss");
  }

   public static String getJson(Object object,String dateFormat) {
       ObjectMapper mapper = new ObjectMapper();
       //不使用时间差的方式
       mapper.configure(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS, false);
       //自定义日期格式对象
       SimpleDateFormat sdf = new SimpleDateFormat(dateFormat);
       //指定日期格式
       mapper.setDateFormat(sdf);
       try {
           return mapper.writeValueAsString(object);
      } catch (JsonProcessingException e) {
           e.printStackTrace();
      }
       return null;
  }
}
```

我们使用工具类，代码就更加简洁了！

```java
@RequestMapping("/json5")
public String json5() throws JsonProcessingException {
   Date date = new Date();
   String json = JsonUtils.getJson(date);
   return json;
}
```

大功告成！完美！

# 七、ssm框架初步整合

# 八、Ajax

# END