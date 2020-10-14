---

typora-root-url: ..\..\..\Pic
typora-copy-images-to: ..\..\..\Pic
---

# 环境搭建

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

![image-20201007205415342](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201008145126.png)

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

![image-20201007205638062](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201008145127.png)

## 4.异常映射

### 4.1.目标

* 使用异常映射机制讲整个项目的异常和错误提示进行统一管理

### 4.2.异常的两种配置

Springmvc提供了xml异常映射和基于注解的异常映射，不是二选一，有区别，原因在下面

#### 1.思路

![image-20201008135836032](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201008135839.png)

![image-20201008140756724](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201008145236.png)

#### 2.基于xml的异常映射(SpringMvc.xml)

**注意：当你没有写注解映射，这个简单异常处理器会帮助你处理异常**

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

#### 3.基于注解的异常映射

注意：一般情况下，在你的后端，你会接收到两种需求

![image-20201008135836032](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201008135839.png)

你需要判断需求类型，所以你需要写一个**工具类**

##### 判断请求类型的工具方法

思路：通过请求头的一个属性值`X-Requested-With` 来判断请求是否是普通请求还是ajax请求，当你的`X-Requested-With` 通过request.getHeader("`X-Requested-With`")取不到值，说明你的请求普通请求，如果可以去到 **XMLHttpRequest** 说明这个请求时ajax请求

![image-20201008171524777](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201009162657.png)

判断请求的工具



```java
package Util;

import sun.misc.Request;

import javax.servlet.http.HttpServletRequest;

public class CrowdUtil {

//    通过request判断
    public static boolean  judgeRequestType(HttpServletRequest request){

        /**通过请求头的X-Requested-With属性值判断，null为普通请求 XMLHttpRequest为ajax请求 */
        String X_Requested_With = request.getHeader("X-Requested-With");

        System.out.println(X_Requested_With);
//        返回为true表示ajax，false表示普通请求
        return "XMLHttpRequest".equals(X_Requested_With);
    }
}

```



##### CrowedExceptionHandler

```java
package MVC.ExceptionHandler;

import Util.CrowdUtil;
import Util.ResultEntity;
import com.google.gson.Gson;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;

/**@ControllerAdvice是一个基于注解的异常处理类*/
@ControllerAdvice
public class CrowedExceptionHandler {

/*  @ExceptionHandler可以将一个具体的异常类型和一个方法关联起来*/
    @ExceptionHandler(value = {NullPointerException.class})
//  注解可以从参数中获取，异常

    public ModelAndView ResolveNullPointerException(
            //实际捕获的异常
            NullPointerException nullPointerException ,
            //当前请求对象
            HttpServletRequest request,
            HttpServletResponse response) throws IOException {

         //首先获得当前请求时什么请求


        boolean b = CrowdUtil.judgeRequestType(request);

        if(b){
            //说明是ajax
            //返回ResultEntity对象,传入异常信息
            ResultEntity<Object> failed = ResultEntity.Failed(nullPointerException.getMessage());

//            将ResultEntity转换为json字符串
//            1.创建Gson对象
            Gson gson=new Gson();
//           2.进行转换
            String s = gson.toJson(failed);

//            3.将json数据传入到网页
            response.getWriter().write(s);

        }

        ModelAndView mv=new ModelAndView("system-error");
        mv.addObject("exception", nullPointerException);

        return mv;
    }
}

```



虽然异常处理器完成了，但是我们遇到了一个问题，一个异常就要一个方法？，可大部分代码是一样的，所以，我们要复用

船新版本：

```java
//更新版
    private ModelAndView CommonExceptionResolved(
            //不同的错误可能有不同的视图
            String view,
            //对应的异常,这里时向上转型
            Exception exception,
            HttpServletRequest request,
            HttpServletResponse response) throws IOException {
        //首先获得当前请求判定什么请求
        boolean b = CrowdUtil.judgeRequestType(request);

        if(b){
            //说明是ajax

            //LoggerFactory.getLogger(TestHandler.class).info("异常处理器");

            //返回ResultEntity对象
            ResultEntity<Object> failed = ResultEntity.Failed(exception.getMessage());

            // 将ResultEntity转换为json字符串
            //1.创建Gson对象
            Gson gson=new Gson();
            //2.进行转换
            String s = gson.toJson(failed);

            //3.将json数据传入到网页
            response.getWriter().write(s);

            return null;
        }

        ModelAndView mv=new ModelAndView(view);
        mv.addObject("exception", exception);

        return mv;

    }

    @ExceptionHandler(value = {NullPointerException.class})
    public ModelAndView NullPointerExceptionResolver(Exception e,
                                             HttpServletRequest request,
                                             HttpServletResponse response) throws IOException {

      return  CommonExceptionResolved("system-error.jsp", e,request,response);
    }
```

## 5.创建常量类

* 方便后期更改，统一规范

  ![image-20201008210302748](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201008210415.png)

代码：

```java
package Util;

public class CrowedConstant {

    public static final String MESSAGE_ACCESS_FORBIDEN="Sorry,Please log in";

    public static final String MESSAGE_LOAD_FAILED="Sorry,Account or Password is wrong";


    public static final String MESSAGE_LOAD_ALREADY_IN_USE="Sorry,Account is USED";


    public static final String ATTR_Name_exception="exception";

}

```

## 6.登录页面搭建

![image-20201008224507314](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201009162658.png)

### 登录跳转

* 因为跳转到登录界面只是一个浏览器的转发，没有其他的操作，所以直接视图转发`mvc:view-controller`就可以

![image-20201008224619575](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201008224619.png)

这样只需要在浏览器输入对应的url就可以了

![image-20201008224658749](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201008224658.png)



##   7.layer

### 1.引入运行环境

![image-20201009113420516](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201009162659.png)

### 2.插入script并测试

![image-20201009113842121](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201009162700.png)

### 修改system-error.jsp页面

```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<!DOCTYPE html>
<html lang="zh-CN">
<base href="http://${pageContext.request.serverName}:${pageContext.request.serverPort}${pageContext.request.contextPath}/">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <meta name="description" content="">
    <meta name="keys" content="">
    <meta name="author" content="">
    <link rel="stylesheet" href="static/bootstrap/css/bootstrap.min.css">
    <link rel="stylesheet" href="static/css/font-awesome.min.css">
    <link rel="stylesheet" href="static/css/login.css">
    <script src="static/Jquery/jquery-3.3.1.min.js"></script>
    <script src="static/bootstrap/js/bootstrap.min.js"></script>
    <style>

    </style>
</head>
<script>

     $(function () {
         $("#btn").click(function () {
             //相当于浏览器的后退按钮
             window.history.back();
         })
     })
</script>
<body>
<nav class="navbar navbar-inverse navbar-fixed-top" role="navigation">
    <div class="container">
        <div class="navbar-header">
            <div><a class="navbar-brand" href="index.html" style="font-size:32px;">尚筹网-创意产品众筹平台</a></div>
        </div>
    </div>
</nav>

<div class="container">

        <h2 class="form-signin-heading"><i class="glyphicon glyphicon-log-in" style="text-align: center"></i> 尚筹网系统消息</h2>
<%--        表示req.getattribute("ex").getmessage--%>
        <h3>${requestScope.exception.message}</h3>

        <button id="btn" class="btn btn-l btn-success btn-block">点我回到上一步 </button>
</div>

</body>
</html>
```



# 管理员登录

## 1.流程图

![image-20201009162620943](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201009162701.png)



## 2.MD5加密工具方法

在你的工具模块写

![image-20201009163001334](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201012203541.png)



```java

    /**
     * 明文字符串转进行Md5加密
     * @param source 传入明文字符串
     * @return  加密结果
     */
    public static String MD5(String source){

        // 1.判断字符穿是否合法
        if(source==null||source.length() == 0){
          // 2.不合法抛出运行异常
          throw new RuntimeException(CrowedConstant.MESSAGE_ILLEAGE);
        }


        try {
            // 3.创建MessageDigest，MessageDigest.getInstance(level)，level表示什么加密方式
            String level="md5";
            MessageDigest messageDigest=MessageDigest.getInstance(level);

            // 4.获取明文数组串对应的字节数组
            byte[] bytes = source.getBytes();
            // 5.执行加密
            byte[] digest = messageDigest.digest(bytes);

            // 6.方便传入数据库，创建BigInteger
            // singNum表示控制是正数还是负数 ，1表示整数
            int singNum=1;
            BigInteger big=new BigInteger(singNum,digest);
            //按照16进制将BigInteger转变为字符串
            int radix=16;

          String encode=big.toString(radix);

          return encode;
        } catch (NoSuchAlgorithmException e) {
            e.printStackTrace();
        }

        return null;
    }
```

## 3.创建自定义登陆失败异常

![image-20201009172134326](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201012203542.png)

```java
package Exception;

/**
 * @author lenovo
 */
public class LoginFailedException extends RuntimeException{

    static final long serialVersionUID = -7034897190745766939L;
    
    public LoginFailedException() {
    }

    public LoginFailedException(String message) {
        super(message);
    }

    public LoginFailedException(String message, Throwable cause) {
        super(message, cause);
    }

    public LoginFailedException(Throwable cause) {
        super(cause);
    }

    public LoginFailedException(String message, Throwable cause, boolean enableSuppression, boolean writableStackTrace) {
        super(message, cause, enableSuppression, writableStackTrace);
    }
}

```

## 4.登录代码

jsp

```html
<%@ taglib prefix="mvc" uri="http://www.springframework.org/tags/form" %>

<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<!DOCTYPE html>
<html lang="zh-CN">
<base href="http://${pageContext.request.serverName}:${pageContext.request.serverPort}${pageContext.request.contextPath}/">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <meta name="description" content="">
    <meta name="keys" content="">
    <meta name="author" content="">
    <link rel="stylesheet" href="static/bootstrap/css/bootstrap.min.css">
    <link rel="stylesheet" href="static/css/font-awesome.min.css">
    <link rel="stylesheet" href="static/css/login.css">
    <script src="static/Jquery/jquery-3.3.1.min.js"></script>
    <script src="static/bootstrap/js/bootstrap.min.js"></script>
<%--    <link rel="stylesheet" href=""> --%>
    <style>

    </style>
</head>
<body>
<nav class="navbar navbar-inverse navbar-fixed-top" role="navigation">
    <div class="container">
        <div class="navbar-header">
            <div><a class="navbar-brand" href="index.html" style="font-size:32px;">尚筹网-创意产品众筹平台</a></div>
        </div>
    </div>
</nav>

<div class="container">

    <form action="Admin/login.html" method="post" class="form-signin" role="form">
        <h2 class="form-signin-heading"><i class="glyphicon glyphicon-log-in"></i> 管理员登录</h2>
        <div class="form-group has-success has-feedback">
            <input type="text" name="loginAcct" class="form-control" id="userName" placeholder="请输入登录账号" autofocus>
            ${requestScope.exception.message}
            <span class="glyphicon glyphicon-user form-control-feedback"></span>
        </div>
        <div class="form-group has-success has-feedback">
            <input type="password" name="userPswd" class="form-control" id="userPass" placeholder="请输入登录密码" style="margin-top:10px;">
            <span class="glyphicon glyphicon-lock form-control-feedback"></span>
        </div>
        <button type="submit" class="btn btn-lg btn-success btn-block" > 登录</button>
    </form>
</div>

</body>
</html>
```

handler

```java
package MVC.Controller;

import CrowedFunding_Entity.Admin;
import Service.dao.AdminServie;
import Util.CrowedConstant;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.SessionAttribute;
import org.springframework.web.bind.annotation.SessionAttributes;

import javax.servlet.http.HttpSession;

/**
 * @author lenovo
 */
@Controller
@RequestMapping("/Admin")
public class AdminHandler {

    @Autowired
    private AdminServie adminServie;

    @RequestMapping("/login.html")
    public String doLogin(@RequestParam("loginAcct") String userName, @RequestParam("userPswd") String password, HttpSession session){

        //若返回admin对象说明登录成功，若果账号密码不正确则会抛出异常
        Admin adminByLogAct = adminServie.getAdminByLogAct(userName, password);


        session.setAttribute(CrowedConstant.ATTR_LOGIN_ADMIN, adminByLogAct);


        return "success";
    }

}

```

service

```java
package Service.Impl;

import CrowedFunding_Entity.Admin;
import CrowedFunding_Entity.AdminExample;
import Mapper.AdminMapper;
import Service.dao.AdminServie;
import Util.CrowdUtil;
import Util.CrowedConstant;
import exception.LoginFailedException;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import sun.security.jgss.LoginConfigImpl;

import java.util.List;
import java.util.Objects;

@Service
public class AdminServiceImpl implements AdminServie {

    @Autowired
    private AdminMapper adminMapper;

    //存入admin
    @Override
    public void saveAdmin(Admin admin) {

          adminMapper.insert(admin);


    }

    // 获得全体成员
    @Override
    public List<Admin> getALL() {
        return adminMapper.selectByExample(new AdminExample());
    }
    // 登入
    @Override
    public Admin getAdminByLogAct(String userName, String password) {

        // 1.根据账号查询Admin对象
        //创建adminExample对象，进行条件查询
        AdminExample adminExample=new AdminExample();
        AdminExample.Criteria criteria = adminExample.createCriteria();

        AdminExample.Criteria s1 = criteria.andLoginAcctEqualTo(userName);

        List<Admin> admins = adminMapper.selectByExample(adminExample);




        // 2.查询是否为空，空则抛出异常，反之从数据库取出作比较
        if(admins == null || admins.size() == 0){
            throw new LoginFailedException(CrowedConstant.MESSAGE_LOAD_FAILED);
        } else if(admins.size()>1){
            throw new  LoginFailedException(CrowedConstant.LOGIN_NOR_NNIQUE);
        }
//        list问题！！！！！！！！！！！
        Admin admin=admins.get(0);
        // 3.将表单密码进行加密
        String userPswdFromForm = CrowdUtil.MD5(password);
        // 4.和数据库取出密码进行比较
        String userPswdFromDB =  admin.getUserPswd();
        // 5.一致返回对象反之抛出异常，

        // 使用Objects.equals(userPswdFromForm,userPswdFromDB)的好处是：
        // public static boolean equals(Object a, Object b) {
        // 先判断是不是同一个对象或者判断是不是同一个属性值
        // return (a == b) || (a != null && a.equals(b));

        if(!Objects.equals(userPswdFromDB,userPswdFromForm)){
            throw new LoginFailedException(CrowedConstant.MESSAGE_LOAD_FAILED);
        }
        return admin;

    }
}

```

## 5.重定向到主页面

* 转发就是服务器再给浏览器一个路径让他去访问，但是我们的主界面在WEB-INF下不能直接访问，所以我们需要通过mvc的view-conrtoller来重定向



![image-20201009230530187](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201009230652.png)

修改为重定向

```java
package MVC.Controller;

import CrowedFunding_Entity.Admin;
import Service.dao.AdminServie;
import Util.CrowedConstant;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.SessionAttribute;
import org.springframework.web.bind.annotation.SessionAttributes;

import javax.servlet.http.HttpSession;

/**
 * @author lenovo
        */
@Controller
@RequestMapping("/Admin")
public class AdminHandler {

    @Autowired
    private AdminServie adminServie;

    @RequestMapping("/login.html")
    public String doLogin(@RequestParam("loginAcct") String userName, @RequestParam("userPswd") String password, HttpSession session){

        //若返回admin对象说明登录成功，若果账号密码不正确则会抛出异常
        Admin adminByLogAct = adminServie.getAdminByLogAct(userName, password);


        session.setAttribute(CrowedConstant.ATTR_LOGIN_ADMIN, adminByLogAct);


        /*要重定向，否则刷新就相当于重新提交表单，也即是在访问下handler方法，就再一次查找了数据库
        但是我们不能直接写访问这个jsp，因为是交给jsp访问的，所以要写对应的url,因为重定向是再给浏览器一个url
        这里的不可以直接转发web-inf下面的jsp因为被保护，所以得写入访问路径，这里我们直接写入新的url，让视图转换器跳转
        转发这里要加/
        */
        return "redirect:/Page.html";
    }

}

```

## 6.用户退出登录

```java
    @RequestMapping("/logout.html")
    public String doLogout(HttpSession session){

        //强制刷新session
        session.invalidate();

//        这里也要重定向，防止重复提交表单，没意义

        return "redirect:/logout.html";

    }
```

![image-20201010183849094](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201010183900.png)

## 7.抽取后台页面

在我们实际开发的时候，有许多页面是所有页面公有的，方便我们开发，所以我们选择抽取后台页面

![image-20201010184341189](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201012203543.png)

方法

添加抽取的页面：

![image-20201010185214220](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201012203544.png)

include-head.jsp

```html
<%--
  Created by IntelliJ IDEA.
  User: lenovo
  Date: 2020/10/10
  Time: 18:44
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<base href="http://${pageContext.request.serverName}:${pageContext.request.serverPort}${pageContext.request.contextPath}/">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <meta name="description" content="">
    <meta name="author" content="">
    <link rel="stylesheet" href="static/bootstrap/css/bootstrap.min.css">
    <link rel="stylesheet" href="static/css/font-awesome.min.css">
    <link rel="stylesheet" href="static/css/main.css">
    <script src="static/Jquery/jquery-3.3.1.min.js"></script>
    <script src="static/bootstrap/js/bootstrap.min.js"></script>
    <script src="static/script/docs.min.js"></script>
    <script src="static/layer/layer.js"></script>
    <script type="text/javascript">
        $(function () {
            $(".list-group-item").click(function(){
                if ( $(this).find("ul") ) {
                    $(this).toggleClass("tree-closed");
                    if ( $(this).hasClass("tree-closed") ) {
                        $("ul", this).hide("fast");
                    } else {
                        $("ul", this).show("fast");
                    }
                }
            });
        });
    </script>
    <style>
        .tree li {
            list-style-type: none;
            cursor:pointer;
        }
        .tree-closed {
            height : 40px;
        }
        .tree-expanded {
            height : auto;
        }
    </style>
</head>

```

include-nav.jsp

```html
<%--
  Created by IntelliJ IDEA.
  User: lenovo
  Date: 2020/10/10
  Time: 18:47
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<nav class="navbar navbar-inverse navbar-fixed-top" role="navigation">
    <div class="container-fluid">
        <div class="navbar-header">
            <div><a class="navbar-brand" style="font-size:32px;" href="#">众筹平台 - 控制面板</a></div>
        </div>
        <div id="navbar" class="navbar-collapse collapse">
            <ul class="nav navbar-nav navbar-right">
                <li style="padding-top:8px;">
                    <div class="btn-group">
                        <button type="button" class="btn btn-default btn-success dropdown-toggle" data-toggle="dropdown">
                            <i class="glyphicon glyphicon-user"></i> ${sessionScope.loginAdmin.userName} <span class="caret"></span>
                        </button>
                        <ul class="dropdown-menu" role="menu">
                            <li><a href="#"><i class="glyphicon glyphicon-cog"></i> 个人设置</a></li>
                            <li><a href="#"><i class="glyphicon glyphicon-comment"></i> 消息</a></li>
                            <li class="divider"></li>
                            <li><a href="Admin/logout.html"><i class="glyphicon glyphicon-off"></i> 退出系统</a></li>
                        </ul>
                    </div>
                </li>
                <li style="margin-left:10px;padding-top:8px;">
                    <button type="button" class="btn btn-default btn-danger">
                        <span class="glyphicon glyphicon-question-sign"></span> 帮助
                    </button>
                </li>
            </ul>
            <form class="navbar-form navbar-right">
                <input type="text" class="form-control" placeholder="查询">
            </form>
        </div>
    </div>
</nav>

```

include-setbar.jsp

```html
<%--
  Created by IntelliJ IDEA.
  User: lenovo
  Date: 2020/10/10
  Time: 18:50
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<div class="col-sm-3 col-md-2 sidebar">
    <div class="tree">
        <ul style="padding-left:0px;" class="list-group">
            <li class="list-group-item tree-closed" >
                <a href="main.html"><i class="glyphicon glyphicon-dashboard"></i> 控制面板</a>
            </li>
            <li class="list-group-item tree-closed">
                <span><i class="glyphicon glyphicon glyphicon-tasks"></i> 权限管理 <span class="badge" style="float:right">3</span></span>
                <ul style="margin-top:10px;display:none;">
                    <li style="height:30px;">
                        <a href="user.html"><i class="glyphicon glyphicon-user"></i> 用户维护</a>
                    </li>
                    <li style="height:30px;">
                        <a href="role.html"><i class="glyphicon glyphicon-king"></i> 角色维护</a>
                    </li>
                    <li style="height:30px;">
                        <a href="permission.html"><i class="glyphicon glyphicon-lock"></i> 菜单维护</a>
                    </li>
                </ul>
            </li>
            <li class="list-group-item tree-closed">
                <span><i class="glyphicon glyphicon-ok"></i> 业务审核 <span class="badge" style="float:right">3</span></span>
                <ul style="margin-top:10px;display:none;">
                    <li style="height:30px;">
                        <a href="auth_cert.html"><i class="glyphicon glyphicon-check"></i> 实名认证审核</a>
                    </li>
                    <li style="height:30px;">
                        <a href="auth_adv.html"><i class="glyphicon glyphicon-check"></i> 广告审核</a>
                    </li>
                    <li style="height:30px;">
                        <a href="auth_project.html"><i class="glyphicon glyphicon-check"></i> 项目审核</a>
                    </li>
                </ul>
            </li>
            <li class="list-group-item tree-closed">
                <span><i class="glyphicon glyphicon-th-large"></i> 业务管理 <span class="badge" style="float:right">7</span></span>
                <ul style="margin-top:10px;display:none;">
                    <li style="height:30px;">
                        <a href="cert.html"><i class="glyphicon glyphicon-picture"></i> 资质维护</a>
                    </li>
                    <li style="height:30px;">
                        <a href="type.html"><i class="glyphicon glyphicon-equalizer"></i> 分类管理</a>
                    </li>
                    <li style="height:30px;">
                        <a href="process.html"><i class="glyphicon glyphicon-random"></i> 流程管理</a>
                    </li>
                    <li style="height:30px;">
                        <a href="advertisement.html"><i class="glyphicon glyphicon-hdd"></i> 广告管理</a>
                    </li>
                    <li style="height:30px;">
                        <a href="message.html"><i class="glyphicon glyphicon-comment"></i> 消息模板</a>
                    </li>
                    <li style="height:30px;">
                        <a href="project_type.html"><i class="glyphicon glyphicon-list"></i> 项目分类</a>
                    </li>
                    <li style="height:30px;">
                        <a href="tag.html"><i class="glyphicon glyphicon-tags"></i> 项目标签</a>
                    </li>
                </ul>
            </li>
            <li class="list-group-item tree-closed" >
                <a href="param.html"><i class="glyphicon glyphicon-list-alt"></i> 参数管理</a>
            </li>
        </ul>
    </div>
</div>

```

# 登陆检查

## 1.目标

将部分资源保护起来，让没有登录的请求不能访问

## 2.思路

![image-20201011144318894](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201011144440.png)

## 3.代码

1. 创建自定义异常

   ![image-20201011153715656](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201012203545.png)

   ```java
   package exception;
   
   public class AccessForbiddenException extends RuntimeException {
   
       public AccessForbiddenException() {
           super();
       }
   
       public AccessForbiddenException(String message) {
           super(message);
       }
   
       public AccessForbiddenException(String message, Throwable cause) {
           super(message, cause);
       }
   
       public AccessForbiddenException(Throwable cause) {
           super(cause);
       }
   }
   
   ```

   

2. 创建拦截器类

​       ![image-20201011153755146](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201012203546.png)

```java
package MVC.Interceptor;

import CrowedFunding_Entity.Admin;
import Util.CrowedConstant;
import exception.AccessForbiddenException;
import org.springframework.web.servlet.HandlerInterceptor;

import javax.security.auth.login.LoginException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import java.nio.file.AccessDeniedException;

public class LoginInterceptor implements HandlerInterceptor {


    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {

        //1.获取Session对象
        HttpSession session = request.getSession();

        //2.尝试从session中取出数据Admin

        Admin attribute = (Admin) session.getAttribute(CrowedConstant.ATTR_LOGIN_ADMIN);

        //3.进入管理主界面之前，判断是否有用户信息

        if (attribute==null){

            throw new AccessForbiddenException();
        }
        return true;
    }
}

```



3. 注册拦截器类

   ![image-20201011190629938](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201012203547.png)
   
   

# 管理员维护

## 1. 任务清单

### 分页显示Admin数据

1. 分页显示

2. 带关键词分布

### 新增Admin

### 更新Admin

### 单挑删除Admin



## 4.代码

### 1.分页显示主题数据

#### 1.目标

将数据库中的Admin数据在页面以分页形式显示。在后端将“带关键词”和“不太关键词”的分页合并为同一套代码

#### 2 .思路

![image-20201011194541979](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201012203548.png)

  1.引入PageHelper

1. 加入对应的jar包

2. 在Spring和Mybatis整合的配置文件配置plugins

   ![image-20201011201120923](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201012203549.png)

3.  编写Mapper文件，因为我们会使用Mysql的Concat函数

​          concat函数：返回结果为连接参数产生的字符串。如有任何一个参数为NULL ，则返回值为 NULL。或许有一个或多个参数。 如果所有参数均为非二进制字符串，则结果为非二进制字符串。 如果自变量中含有任一二进制字符串，则结果为一个二进制字符串。

  

![image-20201012140802756](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201012203550.png)



4. 编写Service

```java
    @Override
    public PageInfo<Admin> getAdmins(String create_time,Integer pageNum,Integer pageSize) {

//        这里不可以直接PageHelper，el表达式只可识别这是一个list不是一个对象
        Page<Admin> page = PageHelper.startPage(1, 5);

        List<Admin> admins = adminMapper.selectByContact(create_time);

        PageInfo<Admin> pageInfo=new PageInfo<Admin>(admins);

        return pageInfo;
    }

```

5. 编写Handler

```java
    @RequestMapping("/getPageInfo")
//    @ResponseBody
    public String getPageInfo(@RequestParam(value = "create_time",defaultValue = "") String create_time, @RequestParam(value = "PageNum",defaultValue = "1")Integer PageNum, @RequestParam(value = "PageSize",defaultValue = "5") Integer PageSize, ModelMap modelMap){

//       获取info对象存入模型
        PageInfo<Admin> admins = adminServie.getAdmins(create_time, PageNum, PageSize);


        modelMap.addAttribute(CrowedConstant.ATTR_PAGE_INFO,admins);


        return  "admin-main";
    }
```



### 2. 分页栏

1. 通过`pagination_zh`来进行前端的分页插件

   1. 加入依赖环境，在指定页面加入依赖

      ![image-20201012142731758](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201012203551.png)

   2. 替换原有的分页栏源码

   ![image-20201012144802793](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201012203552.png)

   

   3. 写jQuery语句

      ![image-20201012162703583](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201012203553.png)

4. 依赖文件有bug，注销回调函数代码

   ![image-20201012171356757](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201012203554.png)

   ![image-20201012171252823](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201012203555.png)



### 3.关键词查询

3.1. 调整表单

![image-20201012172959883](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201012203556.png)

3.2.翻页的时候保持查询条件

防止点击分页栏的时候，提交的url没有参数，修改分页的超链接的url

![image-20201012180939454](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201012203557.png)

3.3 代码直接调用分页查询数据的代码的接口就行

### 4.单例删除

1. 目标

![image-20201012184820460](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201012203558.png)

2. 思路

3. 代码 

​     前端：

![image-20201012191826320](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201012203559.png)

​    后端：

​       Service

       ```java
 @Override
    public void deleteUser(Integer id) {

        adminMapper.deleteByPrimaryKey(id);
    
    }
    
       ```

​          注意：我们要是实现的目标是转发到原有的路径并带有`pageNum`和`ketWord`,直接返回会没有数据，转发到原来的页面，当你刷新还会执行删除操作，所以我们选择重定向

修改前端的删除超链接的href，这里直接进入后端Handler的查数据方法

![image-20201012193928846](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201012203600.png)

​       Handler

​         ![image-20201012200028808](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201012203601.png)



### 5.新增

#### 1.目标

* 将表单提交的Admin对象保存到数据库中

   1.loginAcct不能重复

   2.密码加密

#### 2.思路

![image-20201012203525133](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201012203602.png)

#### 3.实现

1. 要查重，将表中login_acct赋予 唯一约束，借此判断是否重复

```
ALTER TABLE `t_admin` ADD UNIQUE INDEX (`login_acct`)
```

2. 调整修改按钮，并准备表单页面admin-login

   ![image-20201012205500200](/image-20201012205500200.png)

```html
<%--
  Created by IntelliJ IDEA.
  User: lenovo
  Date: 2020/10/9
  Time: 18:14
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<!DOCTYPE html>
<html lang="UTF-8">
<%@include file="include-head.jsp" %>
<body>
<%@include file="include-nav.jsp" %>
<div class="container-fluid">
    <div class="row">
        <%@include file="include-setbar.jsp" %>
        <div class="col-sm-9 col-sm-offset-3 col-md-10 col-md-offset-2 main">
            <div class="col-sm-9 col-sm-offset-3 col-md-10 col-md-offset-2 main">
                <ol class="breadcrumb">
                    <li><a href="Admin/getPageInfo.html">首页</a></li>
                    <li><a href="Admin/login.html">数据列表</a></li>
                    <li class="active">新增</li>
                </ol>
                <div class="panel panel-default">
                    <div class="panel-heading">表单数据<div style="float:right;cursor:pointer;" data-toggle="modal" data-target="#myModal"><i class="glyphicon glyphicon-question-sign"></i></div></div>
                    <div class="panel-body">
                        <form href="Admin/Register.html" role="form">
                            <div class="form-group">
                                <label for="userName">登陆账号</label>
                                <input type="text" class="form-control" name="userName" id="userName" placeholder="请输入登陆账号">
                            </div>
                            <div class="form-group">
                                <label for="password">用户名称</label>
                                <input type="text" class="form-control" name="password" id="password" placeholder="请输入用户名称">
                            </div>
                            <div class="form-group">
                                <label for="email">邮箱地址</label>
                                <input type="email" class="form-control" name="email" id="email" placeholder="请输入邮箱地址">
                                <p class="help-block label label-warning">请输入合法的邮箱地址, 格式为： xxxx@xxxx.com</p>
                            </div>
                            <button type="button" name="submit" class="btn btn-success"><i class="glyphicon glyphicon-plus"></i> 新增</button>
                            <button type="button" name="reset" class="btn btn-danger"><i class="glyphicon glyphicon-refresh"></i> 重置</button>
                        </form>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>

</body>
</html>

```

3. 配置view-controller

   ![image-20201013175924411](/image-20201013175924411.png)

4. 配置Service

   ```java
   
       //存入admin
       @Override
       public void saveAdmin(Admin admin) {
           //密码加密
           String password=admin.getUserPswd();
           String MD5_password = CrowdUtil.MD5(password);
           admin.setUserPswd(MD5_password);
   
           //生成创建时间
           Date date=new Date();
           SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-mm-dd HH:mm:ss");
           String format = simpleDateFormat.format(date);
   
           adminMapper.insert(admin);
   
   
       }
   ```

   

5. 配置Handler

  ```java

    @RequestMapping(value = "/Register.html",method = RequestMethod.POST)
    public String  Register(Admin admin){

        System.out.println("来了");
        adminServie.saveAdmin(admin);

        return   "redirect:/Admin/getPageInfo.html?pageNum="+ Integer.MAX_VALUE;

    }
  ```

异常处理器知识补充：![image-20201013203108695](/image-20201013203108695.png)

![image-20201013203116429](/image-20201013203116429.png)

结论就是：这两个异常处理器选择一个就可了，且当你开启了mvc：annotion-drven 的时候默认开始异常处理器，DispatcherServlet也会默认转配HandlerExceptionResolver

![image-20201013204026444](/image-20201013204026444.png)

![image-20201013204500256](/image-20201013204500256.png)

#### 修改

由于mybais对于唯一主键异常在内部就应经抛出了，所以

![image-20201013215411972](/image-20201013215411972.png)