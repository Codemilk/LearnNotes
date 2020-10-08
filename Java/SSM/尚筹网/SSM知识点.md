---
typora-copy-images-to: ..\..\..\..\MarkDownPic
---

## Maven知识点补充

* Maven聚合工程：父模块的依赖写完子模块要想使用，还得自己去写入依赖
* Scope:provided，因为provided表明该包只在编译和测试的时候用，所以，当启动tomcat的时候，就不会冲突了，
* Scope:test，表示当前依赖不传递，只能在当前工程的test目录下使用

## 1.日志系统:

### 介绍

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200927201051376.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTc4Nzk5OA==,size_16,color_FFFFFF,t_70#pic_center)

###不同日志系统的整合

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200927202339861.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTc4Nzk5OA==,size_16,color_FFFFFF,t_70#pic_center)日志的转换：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200927210045512.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTc4Nzk5OA==,size_16,color_FFFFFF,t_70#pic_center)
日志优点：
使用日志打印信息和使用sysout 打印信息的区别：sysout 如果不删除，那么执行到这里必然会打印；如果使用日志方式打印，可以通过志级别控制日志输出等级来控制，是否打印。

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:SpringPersist.xml")
public class TestConfig {

    @Autowired
    private DataSource dataSource;

    @Autowired
    private AdminMapper adminMapper;
    @Test
    public void test1() throws SQLException {

        System.out.println(dataSource.getConnection());


//        adminMapper.insert(new Admin(null, "tom", "123123","tom", "tom@qq.com", null));
        /**
         * 若在实际开发中，所有想查看数值的地方都使用sysout方式打印，都会给项目上线运行带来问题，因为他本身是一种IO操作通常IO的操作都比较耗费性能
         * 即使你为了节省性能专门花时间删除代码sysout也可能有遗漏，而且麻烦
         * 而如果使用日志系统，那么就可以通过日志级别批量控制信息打印
         * 具体原因：https://ask.csdn.net/questions/1079638
         *
         * */
        System.out.println(adminMapper.selectByPrimaryKey(1));
    }

    @Test
    public void testLog(){
//         1.获取Logger对象,这里传入Class对象就是当前打印日志的类
        Logger logger = LoggerFactory.getLogger(TestConfig.class);

//        2.根据不同日志级别的打印日志，当我们指定打印info，info一下就不会再打印了，以此类推
        logger.debug("Hello DEBUG");

        logger.info("Hello INFO");

        logger.warn("Hello WARN");


    }
```

### logback.xml配置文件（必须是logback.xml）

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">
    <!-- 指定日志输出的位置-->
    <appender name="STDOUT"
              class="ch.qos.logback.core.ConsoleAppender">
        <!--appender:追加器
                 class：在哪里打印,ConsoleAppender类表示在控制台打印
                 name：追加器name，便于引用
        -->
        <encoder>
            <!-- 日志输出的格式-->
            <!-- 按照顺序分别是：时间、日志级别、线程名称、打印日志的类、日志主体
            内容、换行-->
            <pattern>[%d{HH:mm:ss.SSS}] [%-5level] [%thread] [%logger]
                [%msg]%n</pattern>
        </encoder>
    </appender>
    <!-- 设置全局日志级别。日志级别按顺序分别是：DEBUG、INFO、WARN、ERROR -->
    <!-- 指定任何一个日志级别都只打印当前级别和后面级别的日志。-->
<!--     root表示全局日志输出级别-->
    <root level="INFO">
        <!-- 指定打印日志的appender，这里通过“STDOUT”引用了前面配置的appender -->
        <appender-ref ref="STDOUT" />
    </root>
    <!-- 根据特殊需求指定局部日志级别-->
    <logger name="Mapper" level="DEBUG"/>
<!--    -->
</configuration>
```
### 声明式事务

事务概念图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200928210733310.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTc4Nzk5OA==,size_16,color_FFFFFF,t_70#pic_center)

思路：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200928210758352.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTc4Nzk5OA==,size_16,color_FFFFFF,t_70#pic_center)
注意：我们的聚合工程，秉承一个原则，聚合且独立。所以我们选择在为事务专门建立一个Spring配置文件
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200928210925682.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTc4Nzk5OA==,size_16,color_FFFFFF,t_70#pic_center)
xml配置方式：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:jdbc="http://www.springframework.org/schema/jdbc" xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/jdbc http://www.springframework.org/schema/jdbc/spring-jdbc.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd http://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop.xsd">

<!--配置自动扫描包-->
    <context:component-scan base-package="Service"></context:component-scan>

    <!-- 配置事务管理器-->
    <bean class="org.springframework.jdbc.datasource.DataSourceTransactionManager" id="dataSourceTransactionManager">
        <!--                装配数据源-->
        <property name="dataSource" ref="DruidDataSourceFactory"></property>
    </bean>

    <!--配置事务通知-->
    <tx:advice transaction-manager="dataSourceTransactionManager" id="transactionInterceptor">
        <tx:attributes >
            <!--                指定某个方法上的事务的属性
                            read-only=true:只读属性，让数据库知道这是一个查询操作，能够进行优化
             -->
            <!--                service层,配置只读  -->
            <tx:method name="get*" read-only="true"/>
            <tx:method name="find*" read-only="true"></tx:method>
            <tx:method name="query*" read-only="true"></tx:method>
            <tx:method name="count*" read-only="true"></tx:method>

            <!--            Dao层 增删改，配置事务传播行为，回滚异常
                             REQUIRED:如果当前没有当前没有事务，那么创建新的事务，如果有了事务就沿用现在事务，缺点就是：指定方法没错，但是在同一事务下的其他犯法错误，则会影响到
                             REQUIRES_NEW：必须开启新的事务
                             rollback-for:表示当前那个异常事务回滚
             -->
            <tx:method name="save*" propagation="REQUIRES_NEW" rollback-for="java.lang.RuntimeException"></tx:method>
            <tx:method name="update*" propagation="REQUIRES_NEW" rollback-for="RuntimeException"></tx:method>
            <tx:method name="remove*" propagation="REQUIRES_NEW" rollback-for="RuntimeException"></tx:method>
            <tx:method name="batch*" propagation="REQUIRES_NEW" rollback-for="RuntimeException"></tx:method>

        </tx:attributes>
    </tx:advice>

    <aop:config>
        <!--这里按顺序表示  *：任意返回值和修饰符 *：任意包 ..：任意包下的任意层次 *service：表示任意方法一service后缀结尾的类  *：任意方法  ..：任意参数      -->
        <!--  考虑到后面我们整合SpringSecurity，避免把UserService加入事务控制 让切入点定位到Impl结尾的方法-->
        <aop:pointcut id="TxPoint" expression="execution(* *..*ServiceImpl.*(..))"/>

        <aop:advisor advice-ref="transactionInterceptor" pointcut-ref="TxPoint"></aop:advisor>
    </aop:config>

</beans>
```
## 2.SpringMVC

### 1.各个配置文件的关系

![image-20201007121113838](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201007121313.png)

####  1.1web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <!--    配置字符集拦截器-->
    <!--CharacterEncodingFilter这个filter一定要在所有的filter
             原因：request.setCharacterEncoding(encoding),一定一早在getAttributes（其他filter生效）前面
    -->
    <filter>
        <filter-name>CharacterEncodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
        <init-param>
            <param-name>forceRequestEncoding</param-name>
            <param-value>true</param-value>
        </init-param>
        <init-param>
            <param-name>forceResponseEncoding</param-name>
            <param-value>true</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>CharacterEncodingFilter</filter-name>
        <!--        拦截所有路径-->
        <url-pattern>/*</url-pattern>
    </filter-mapping>

    <!--配置监听器-->
    <!--    引入外部spring文件-->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:Spring_Tx.xml,classpath*:Spring_Mybatis.xml</param-value>
    </context-param>
    <!--在服务器创建的时候监听servletContext，创建ioc 把servlet传入IOC-->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <!--配置转发器dispatcherServlet
             一般servlet默认生命周期，创建对象在第一次接受请求的时候
             但在dispatcherServlet里面，有很多的框架初始化操作，不适合在请求的时候在来初始化
             设置<load-on-startup>1</load-on-startup>为了让servlet最早去创建
               但是加载顺序不会超过listener filter 加载顺序：listener->Filter>servlet
    -->
    <servlet>
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath*:Spring_Mvc.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <!--        配置url-servlet的方式：/表示拦截所有请求-->
        <!--        <url-pattern>/</url-pattern>-->
        <!--        配置url-servlet的方式：通过扩展名-->
        <!--        优点1：xxx.css，xxx.js，xxx.png等静态资源完全不经过SpringMvc，不需要特殊处理
                    优点2：可以实现伪静态效果，表面看起来是一个静态html文件，实际上是动态java运行的结果
                          给黑客入侵增加难度
                          利于SEO优化，SEO就是让百度，谷歌搜索引擎更容易找到我们的项目
                    缺点：不符合Restful风格，
                    -->
        <url-pattern>*.html</url-pattern>
        <!--   为什么要另外配置一个json扩展名呢

              如果一个Ajax请求扩展名是html，但是实际服务器返回的是*.json，就会报错406，所以我们的需要可以拦截*.json

        -->
        <!--
                                1. 1xx：服务器就收客户端消息，但没有接受完成，等待一段时间后，发送1xx多状态码
                                2. 2xx：成功。代表：200
                                3. 3xx：重定向。代表：302(重定向)，304(访问缓存)
                                4. 4xx：客户端错误。
                                    * 代表：
                                        * 400：BadRequest 一般是参数问题，例如类型转换，参数为找到
                                        * 404（请求路径没有对应的资源）
                                        * 405：请求方式没有对应的doXxx方法
                                5. 5xx：服务器端错误。代表：500(服务器内部出现异常)
          -->
        <url-pattern>*.json</url-pattern>

    </servlet-mapping>


</web-app>
```

#### 1.2Springmvc配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/mvc https://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <context:component-scan base-package="MVC"></context:component-scan>

<!--    mvc:annotation-driven:相当于打开了@RequestBody，@RequestBody，@ControllerAdvice-->
    <mvc:annotation-driven></mvc:annotation-driven>

<!--可以-->
<!--    <mvc:default-servlet-handler></mvc:default-servlet-handler>-->

<!--    配置视图解析器-->

    <bean id="internalResourceViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/"></property>
<!--        保护jsp，将jsp文件放入web-inf下面-->
         <property name="suffix" value=".jsp"></property>
    </bean>
</beans>
```
#### 1.3配置jsp

首先引入相应得api（servlet，jsp-api），注意这里的scope讲解一下：添加<scope>provided</scope> ，因为provided表明该包只在编译和测试的时候用，所以，当启动tomcat的时候，就不会冲突了，完整依赖如下-->

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200929200140496.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTc4Nzk5OA==,size_16,color_FFFFFF,t_70#pic_center)

##### 对应的jsp文件：在我们项目中，jsp文件一般设定到WEB-INF下面，保护文件，不让外界直接能访问到

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200929195913458.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTc4Nzk5OA==,size_16,color_FFFFFF,t_70#pic_center)



```html
<%--
  Created by IntelliJ IDEA.
  User: lenovo
  Date: 2020/9/29
  Time: 18:05
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<%--base标签：
<base> 标签为页面上的所有链接规定默认地址或默认目标。

通常情况下，浏览器会从当前文档的 URL 中提取相应的元素来填写相对 URL 中的空白

使用 <base> 标签可以改变这一点。浏览器随后将不再使用当前文档的 URL，而使用指定的基本 URL 来解析所有的相对 URL。这其中包括 <a>、<img>、<link>、<form> 标签中的 URL。--%>
<%--尽量这样写因为他是动态的，注意${pageContext.request.contextPath}本身带有/所以提前去掉，后面的/要留着--%>
<base href="http://${pageContext.request.serverName}:${pageContext.request.serverPort}${pageContext.request.contextPath}/">
<head>
    <title>Title</title>
</head>
<body>
<%--${pageContext.request.contextPath}/--%>
<%--如果不写拓展名，dispatchservlet不回去拦截，直接访问tomcat有没有此文件
    一般来说应该写的,但是我们的tomcat路径设置的就是http://localhost:8080/Crowdfunding/
--%>
<%--记住 请求的uil不可以有/开头，例如：/test/ssm.html, 这样就不参考base标签了--%>
<a href="test/ssm.html">TestSSM</a>
</body>
</html>
```
#### 1.4controller

```java
package MVC.Controller;

import CrowedFunding_Entity.Admin;
import Service.dao.AdminServie;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

import java.util.List;
import java.util.Map;

@Controller
//父路径
@RequestMapping("/test")
public class TestHandler {

    @Autowired
    private AdminServie adminServie;

    @RequestMapping("/ssm.html")
    public String testSSM(Map map){
        List<Admin> all = adminServie.getALL();
        map.put("all",all);
       return "Target";
    }
}

```

#### 挂羊头卖狗肉：报错406

* 当你使用@responseBody返回json数据，但是你当前页面是*.html，就会报错406![image-20201007123003028](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201007123220.png)

**解决办法:**在你的`dispatcherServlet`添加扩展名json

```xml

    <!--配置转发器dispatcherServlet
             一般servlet默认生命周期，创建对象在第一次接受请求的时候
             但在dispatcherServlet里面，有很多的框架初始化操作，不适合在请求的时候在来初始化
             设置<load-on-startup>1</load-on-startup>为了让servlet最早去创建
               但是加载顺序不会超过listener filter 加载顺序：listener->Filter>servlet
    -->
    <servlet>
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath*:Spring_Mvc.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <!--        配置url-servlet的方式：/表示拦截所有请求-->
        <!--        <url-pattern>/</url-pattern>-->
        <!--        配置url-servlet的方式：通过扩展名-->
        <!--        优点1：xxx.css，xxx.js，xxx.png等静态资源完全不经过SpringMvc，不需要特殊处理
                    优点2：可以实现伪静态效果，表面看起来是一个静态html文件，实际上是动态java运行的结果
                          给黑客入侵增加难度
                          利于SEO优化，SEO就是让百度，谷歌搜索引擎更容易找到我们的项目
                    缺点：不符合Restful风格，
                    -->
        <url-pattern>*.html</url-pattern>
        <!--   为什么要另外配置一个json扩展名呢

              如果一个Ajax请求扩展名是html，但是实际服务器返回的是*.json，就会报错406，所以我们的需要可以拦截*.json

        -->
        <!--
                                1. 1xx：服务器就收客户端消息，但没有接受完成，等待一段时间后，发送1xx多状态码
                                2. 2xx：成功。代表：200
                                3. 3xx：重定向。代表：302(重定向)，304(访问缓存)
                                4. 4xx：客户端错误。
                                    * 代表：
                                        * 400：BadRequest 一般是参数问题，例如类型转换，参数为找到
                                        * 404（请求路径没有对应的资源）
                                        * 405：请求方式没有对应的doXxx方法
                                5. 5xx：服务器端错误。代表：500(服务器内部出现异常)
          -->
<!--        防止出现406-->
        <url-pattern>*.json</url-pattern>

    </servlet-mapping>

```

#### base标签：

```html
base标签：
<base> 标签为页面上的所有链接规定默认地址或默认目标。

通常情况下，浏览器会从当前文档的 URL 中提取相应的元素来填写相对 URL 中的空白

使用 <base> 标签可以改变这一点。浏览器随后将不再使用当前文档的 URL，而使用指定的基本 URL 来解析所有的相对 URL。这其中包括 <a>、<img>、<link>、<form> 标签中的 URL。--%>
```

注意：

* 端口号前必须加：
* contextPath前面不用加上/
* contextPath后面必须加上/
* 页面上所有的参照base标签的标签都必须放在base标签的后面

![在这里插入图片描述](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201007130118.png)

## 3.ajax

### 3.1. 建立意识

![image-20201007131512948](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201007131513.png)

### 3.2. 常用注解：@RequestBody和@ResponseBody

@RequestBody和@ResponseBody要想正常使用，必须加入Jackson依赖，同时在mvc配置文件，配置mvc:annotion-driven 标签

![image-20201007133058619](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201007133058.png)



#### 知识巩固

1. 请求消息：客户端发送给服务器端的数据
		* 数据格式：
			1. 请求行
			2. 请求头
			3. 请求空行
			4. 请求体
	2. 响应消息：服务器端发送给客户端的数据
		* 数据格式：
			1. 响应行
				1. 组成：协议/版本 响应状态码 状态码描述
				2. 响应状态码：服务器告诉客户端浏览器本次请求和响应的一个状态。
					1. 状态码都是3位数字 
					2. 分类：
						1. 1xx：服务器就收客户端消息，但没有接受完成，等待一段时间后，发送1xx多状态码
						2. 2xx：成功。代表：200
						3. 3xx：重定向。代表：302(重定向)，304(访问缓存)
						4. 4xx：客户端错误。
							* 代表：
							    * 400：BadRequest 一般是参数问题，例如类型转换，参数为找到
								* 404（请求路径没有对应的资源） 
								* 405：请求方式没有对应的doXxx方法
						5. 5xx：服务器端错误。代表：500(服务器内部出现异常)
							
				
			2. 响应头：
				1. 格式：头名称： 值
				2. 常见的响应头：
					1. Content-Type：服务器告诉客户端本次响应体数据格式以及编码格式
					2. Content-disposition：服务器告诉客户端以什么格式打开响应体数据
						* 值：
							* in-line:默认值,在当前页面内打开
							* attachment;filename=xxx：以附件形式打开响应体。文件下载
			3. 响应空行
			4. 响应体:传输的数据

### 3.3@RequestBody的使用

#### 传入数组

##### 第一种方法

Jquery

![image-20201007145055183](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201007145056.png)

java:

```java
   @RequestMapping("/testJson.html")
    @ResponseBody
    public String testJson(@RequestParam("array[]") int [] arr){

        for(int i:arr){
            System.out.println(i);
        }

        return "Target";

    }
```

问题：传过来的数据json自动转换成特别的形式array [] :1 array[]:2...........array[]:n,需要@requestParam("")，在写入[].

##### 第二种方法（建议使用方法)

将json数据字符串化，提交json字符串对象

```html
<script>
    // 加载
    $(function () {

        $("#btn1").click(function () {
            <%--
                注意当你当然可以使用$.post() $.get()，但是$.post() $.get()这两个都是服务器成功处理之后，响应状态码必须是200才可以执行回调函数
                $.ajax()不管响应状态码是多少，都可处理
             --%>
            // 第二种方法
            // json数组
            var array=[6,9,5,4,1,8,7,4,6]

            // 将json数组转换为json字符串
            //注意无论是json数组还是对象都要像这样转换为字符串
            var requestBody = JSON.stringify(array);
            // json对象
            var obj={"name":"tom","age":12}
                             $.ajax({
                                 url:"test/testJson.html",      //请求地址
                                 type:"POST",                    //请求方式
                                 // 第一种方案：将@requestBody下入value="arrat[]"
                                 // data: {"array":[6,9,5,4,1,8,7,4,6]},      //要发送的请求参数
                                 // 第二种方案
                                 //必须设置contentType，告诉服务器发给我的数据类型化
                                 data: requestBody,      //要发送的请求参数
                                 contentType:"application/json;charset=UTF-8",
                                 datatype:"text",               //如何对待服务器返回的数据
                                 success:function (param) {      //服务器成功处理参数返后调用的回调函数，param是服务器返回参数
                                     alert(param)
                                 },
                                 error:function (param) {        //服务器失败处理参数返后调用的回调函数，param是服务器返回参数
                                     alert(param)
                                 }


                             })
                         })
                     })
                 </script>
```

当你的请求体为 Request Payload时候，你就可以直接使用@RequestBody获得json，但是必须在ajax请求的时候加入**contentType**属性

![image-20201007160737209](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201007160818.png)

对应的后盾端数据

```java

    @RequestMapping("/testJson.html")
    @ResponseBody
    public String testJson(@RequestBody() List<Integer>arr){

        //创建日志对象，通过日志数据输出方便后期管理
        Logger logger = LoggerFactory.getLogger(TestHandler.class);



        for(Integer i:arr){

            logger.info("number:"+i);
        }

        return "success";

    }

```



#### 传入复杂对象

建立复杂实体bean

![image-20201007162609071](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201007162609.png)

对应的ajxa方法：

```html
<script>
    // 加载
    $(function () {

        //传入json形式的复杂对象
        $("#btn2").click(function () {
            // 创建Json对象
            var student={
            // private Integer stuId;
            // private String strName;
            // private Address address;
            // private List<Subject> SubjectList;
            // private Map<String,String> map;
                "stuId":1,
                "strName":"LiSi",
                "address":{
                    // private String province;
                    // private String city;
                    // private String street;
                       "province":"Guangdong",
                       "city":"Wuhu",
                       "street":"facai"},
                "SubjectList":[{
                    // private String subjectName;
                    // private Integer subjectNumber;
                    "subjectName":"Math",
                    'subjectNumber':1
                },{
                    // private String subjectName;
                    // private Integer subjectNumber;
                    "subjectName":"Chinese",
                    'subjectNumber':0
                }],
                "map":{
                    "k1":"1k",
                    "k2":"2k"
                }

        }
        var student = JSON.stringify(student);
        $.ajax({
            url:"test/testConfuseJson.html",
            type:"POST",
            data:student,
            contentType:"application/json;charset=UTF-8",
            success:function (param) {
                alert(param)
            },
            error:function (param) {
                alert(param)
            }
        })
        })
   
                 </script>
```

对应的Handler：

```java
    @RequestMapping("/testConfuseJson.html")
    @ResponseBody
    public String testConfuseJson(@RequestBody Student student){

        logger.info("student:"+student);

        return "success";
    }

```

#### Ajax返回结果规范

* 统一整个项目Ajax的返回结果集，未来也可以用于分布式架构各个模块见得调用

代码

```java
package ResultEntity;

public class ResultEntity<T>{

    public static final String SUCCESS="SUCCESS";
    public static final String FAILED="FAILED";

/**用来封装当前结果成功还是失败*/
    private String result;
/**    请求处理失败，返回的错误消息*/
    private String message;
/**要返回的数据*/
    private T data;

    public ResultEntity() {
    }

    public ResultEntity(String result, String message, T data) {
        this.result = result;
        this.message = message;
        this.data = data;
    }
/**泛型方法:泛型在类上面就是泛型类，在方法上面就是泛型方法*/
/**请求成功但不需要返回类型*/
    public static <Type>ResultEntity<Type> successWithoutData(){
        return new ResultEntity<Type>(SUCCESS,null,null);
    }
/**请求成功但是需要返回数据
 * 注意:这里的泛型T就是泛型Type，准确的说是 先有Type再有T
 * */
    public static <Type>ResultEntity<Type> successWithData(Type data){
       return new ResultEntity<Type>(SUCCESS,null,data);
    }
/**处理消息失败*/
    public static <Type>ResultEntity<Type> Failed(String message){
        return new ResultEntity<Type>(FAILED,message,null);
    }

    public String getResult() {
        return result;
    }

    public void setResult(String result) {
        this.result = result;
    }

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }

    public T getData() {
        return data;
    }

    public void setData(T data) {
        this.data = data;
    }

    @Override
    public String toString() {
        return "ResultEntity{" +
                "result='" + result + '\'' +
                ", message='" + message + '\'' +
                ", data=" + data +
                '}';
    }
}

```

注意：这里使用的两个泛型，其实是一个泛型

![image-20201007201728113](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201007201834.png)

测试：

一定要修改你的ajax的url和handler@RequestMapping("/testConfuseJson.json")，之前讲过，返回json的时候你的url是*.html，会报错406   挂羊头卖狗肉

![image-20201007205415342](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20201007205415342.png)

解决办法：

Handler

```java
    @RequestMapping("/testConfuseJson.json")
    @ResponseBody
    public ResultEntity<Student> testConfuseJson(@RequestBody Student student){

        logger.info("student:"+student);
//        ResultEntity<Student> studentResultEntity=ResultEntity.successWithData(student);
//        直接这样返回就相当于上面一样
        return ResultEntity.successWithData(student);
    }
```

ajax

![image-20201007205638062](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20201007205638062.png)

## 异常映射

### 12.1目标

* 使用异常映射机制讲整个项目的异常和错误提示进行统一管理

### 12.2思路

![image-20201008135836032](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201008135839.png)

![image-20201008140756724](D:\MarkDownPic\image-20201008140756724.png)

基于xml的异常映射(SpringMvc.xml)

```xml

<!--配置基于Xml的异常映射-->
    <bean id="XmlExceptionResolver" class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
<!--        配置异常类型-->
        <property name="exceptionMappings" >
            <props>
<!--                key属性指定异常 标签体写入对应的视图，还去视图解析器那里拼接,不配置注解的异常处理器，这个简单处理器也会出来@requestMaping-->
                <prop key="java.lang.Exception">system-error</prop>
            </props>
        </property>
    </bean

```

