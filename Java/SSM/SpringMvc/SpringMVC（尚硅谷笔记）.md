### SpringMVC属于SpringFrameWork的后续产品，已经融合在SpringWebFlow里面。Spring框架提供了构建Web应用程序的全功能MVC模块。使用Spring可插入的MVC架构，可以选择是使用内置的SpringWeb框架还可以是Struts这样的Web框架。
#### 优点：Lifecycle for overriding binding, validation, etc，易于同其它View框架（Tiles等）无缝集成，采用IOC便于测试。它是一个典型的教科书式的mvc构架，而不像struts等都是变种或者不是完全基于mvc系统的框架，对于初学者或者想了解mvc的人来说我觉得 spring是最好的，它的实现就是教科书！第二它和 tapestry一样是一个纯正的servlet系统，这也是它和tapestry相比 struts所没有的优势。而且框架本身有代码，而且看起来容易理解
#### 必要Jar包（maven）：

```java
  <dependencies>
<!--SpringMVC必用jar包-->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>5.2.3.RELEASE</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-web</artifactId>
      <version>5.2.3.RELEASE</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-beans</artifactId>
      <version>5.2.3.RELEASE</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>5.2.3.RELEASE</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-core</artifactId>
      <version>5.2.3.RELEASE</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-expression</artifactId>
      <version>5.2.3.RELEASE</version>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-expression</artifactId>
      <version>5.2.3.RELEASE</version>
    </dependency>

      <dependency>
          <groupId>org.springframework</groupId>
          <artifactId>spring-aop</artifactId>
          <version>5.2.3.RELEASE</version>
      </dependency>
<!--    junit-->
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
    </dependency>
<!--    servlet-->
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>4.0.0</version>
      <scope>provided</scope>
    </dependency>
<!--Spring日志-->
      <dependency>
          <groupId>commons-logging</groupId>
          <artifactId>commons-logging</artifactId>
          <version>1.2</version>
      </dependency>   
  </dependencies>
```

#### 步骤：
1.配置web.iml：
首先要配置一个叫做DispatcherServlet的servlet

```java
<web-app>


    <servlet>
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!--  配置dispatcherServlet的一个初始化参数，作用：配置SpringMVC配置文件的位置和名称-->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath*:SpringMvc.xml</param-value>
        </init-param>
<!--        标记了servlet的加载顺序-->
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        表示访问任何路径都可以被识别到
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>

```
2.然后在配置SpringMVC的配置文件：


```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd
">

    <!--配置自动扫描的包-->
    <context:component-scan base-package="SpringMvc_handeler"></context:component-scan>


<!--   配置视图解析器:如何把handler方法返回值解析为实际的物理视图-->

      <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="internalResourceViewResolver">
         前缀名
          <property name="prefix" value="/WEB-INF/views/"></property>
          后缀名
          <property name="suffix" value=".jsp"></property>
      </bean>
   使mvc注解@RequestMapping("/HelloWorld")可以成功
    <mvc:annotation-driven />
    
    <mvc:default-servlet-handler/>

</beans>
```
然后获取：

```java
package SpringMvc_handeler;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class HelloWorld {
//使用@RequestMapping来映射请求的URL
//返回值会通过视图解析器为实际的物理视图，对于InternalResourceViewResolver视图解析器，会做如下的解析：
//用过 prefix+returnVal+后缀 这样的方式得到实际的物理视图，然后做转发操作
    @RequestMapping("/HelloWorld")
    public String hello(){
        System.out.println("hello world");
        return "success";
    }

  
}

```
### 这里我总结一下，首先你要创建dispatcherServlet，因为当服务器开启的时候，servlet就开始创建，当你在配置servlet的时候，标记为最开始创建的，访问路径设置为/,表示所有的访问路径都可以被搜索到，
@RequestMapping注解为控制器指定可以处理那些URL请求？
* 在控制器的类定义及方法都可标注
      -类定义处：提供初步的请求映射信息。相对于WEB应用的根目录
      -方法处：提供进一步的细分映射，相对于类定义处的URL。若类定义处未标注@RequestMapping，则方法处标记的URL相对于WEB应用的根目录
DispatcherServlet截获请求，就通过控制器上 
@RequestMapping提供的映射信息确定请求所对应的处理方法

##### 例子：

```java
@RequestMapping("/SpringMvc")
@Controller
public class SpringTest {
    private  static final String SUCCESS="/success";
//    @RequestMapping注解除了可以修饰方法，还可以修饰类
//    -类定义处：提供初步的请求映射信息。相对于WEB应用的根目录
//    -方法处：提供进一步的细分映射，相对于类定义处的URL。若类定义处未标注
    @RequestMapping(value = "/SpringTest1",method = RequestMethod.GET)
    public String SpringTest1(){
        System.out.println("testOk");
        return  "success";
    }
    
    
}

对应的前端的href就是SpringMvc/SpringTest1
```

#### 注意！！！！！：不要再URL的最前面加：/,好像这样：

```java
<a href="/SpringMvc/SpringTest1">SpringMvc/SpringTest1</a>

```
### 要这样！！，否则会丢失你的项目名
```java
<a href="SpringMvc/SpringTest1">SpringMvc/SpringTest1</a>

```

解决问题的连接：https://blog.csdn.net/xiaoerzyzz/article/details/72850523



## 映射请求参数，请求方法或请求头
* @RequestMapping除了可以使用请求URL映射请求外，还可以使用请求方法，请求参数及请求头映射请求
* @RequestMapping的value,method,params及heads分别表示请求URL，请求犯法，请求参数以及请求头的映射条件，可以通过关系，联合更加精确
* Params和Headers支持简单的表达式：
- param1：表示请求必须包含名为param1的请求参数。 
- !param1：表示请求必须不包含名为param1的请求参数。 
- param1！=value1：表示请求必须包含名为param1的请求参数，但他的值不能是value1。 
- {"param1=value1","param2"}：表示请求必须包含名为param1,param2的请求参数。 且param1的值必须是value1


##### 例子：

```java
//使用method 属性来指定请求方式
    @RequestMapping(value = "/method",method = RequestMethod.POST)
    public String method(){

        System.out.println("testMethod");
        return SUCCESS;
    }
    //使用params和headers属性来指定请求方式
    @RequestMapping(value = "/paramTest",params = {"username","age!=10"})
    public String paramTest(){
        return  SUCCESS;
    }
```
#### 使用@RequestMapping映射请求
Ant风格资源地址支持三种匹配符：
- ？：匹配文件名中的一个字符
- ‘*’  ：匹配文件名中的任意字符
- ** ：**匹配多层路径
- 还支持	Ant风格的URL：
- /user/*/createuser:匹配/user/aaaa/createuser
- /user/**/createuser:匹配/user/aaa/bbb/createuser
- /user/createuser??:匹配/user/createuseraa
#### 使用@PathVariable映射URL绑定的占位符
通过@PathVariable可以将URL中占位符参数绑定到控制器处理方法的入参中：URL中的{xxx}占位符可以通过@PathVariable("xxx")绑定到操作方法的入参中
例子：

```java
/*  @PathVariable演示
    来映射URL中的占位符到目标方法的参数中
    */
    @RequestMapping("/testPathVariable/{id}")
    public String PathVariableTest(@PathVariable("id") int id){
        System.out.println(id);
        return SUCCESS;
    }
```
#### REST:
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020031619371634.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTc4Nzk5OA==,size_16,color_FFFFFF,t_70)
了解REST：![在这里插入图片描述](https://img-blog.csdnimg.cn/20200316193852378.png)


步骤：
配置HiddenHttpMethodFilter,因为Form不支持put delete,这个filter可以将POST请求方式其转化，
在你的web.iml配置：

```java
   <!--配置HiddenHttpMethodFilter,因为Form不支持put delete,这个filter可以将POST请求方式其转化-->
    <filter>
        <filter-name>HiddenHttpMethodFilter</filter-name>
        <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>HiddenHttpMethodFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
```
例子：

```java
/*   HiddenHttpMethodFilter来转换请求的演示
     Rest风格的URL
       以CRUD为例
         增：/order POST
         改：/order/1 put
         获取：/order/1 GET
         删除：/order/1 delete

         如何发送请求PUT请求和DELETE请求呢？
         1.需要配置HiddenHttpMethodFilter
         2.需要先发送post请求
         3.需要发送Post请求时携带一个隐藏域 name=_method,value=post/delete像这样：
             <form action="SpringMvc/testRest/1" method="post">
                <input type="hidden" name="_method" value="PUT">
                <input type="submit" value="TestRest put">
             </form>
        4.在SpringMvc的目标方法中如何得到id呢？
          使用@PathVariable注解

    */
    @RequestMapping(value = "/testRest/{id}",method = RequestMethod.GET)
    public  String testRestGET(@PathVariable("id") int id){
        System.out.println("GET"+id);
        return SUCCESS;
    }
    @RequestMapping(value = "/testRest",method = RequestMethod.POST)
    public  String testRestPOST(){
        System.out.println("POST");
        return SUCCESS;
    }
    @RequestMapping(value = "/testRest/{id}",method = RequestMethod.DELETE)
    public  String testRestDELETE(@PathVariable("id") int id){
        System.out.println("DELETE"+id);
        return SUCCESS;
    }
    @RequestMapping(value = "/testRest/{id}",method = RequestMethod.PUT)
    public  String testRestPUT(@PathVariable("id") int id){
        System.out.println("PUT"+id);
        return SUCCESS;
    }
```

这里时对应的前端的URL：

```java
<a href="SpringMvc/testRest/1">testRest Get</a>
<br>


<form action="SpringMvc/testRest" method="post">
    <input type="submit" value="TestRest POST">
</form>
<br>

<form action="SpringMvc/testRest/1" method="post">
    <input type="hidden" name="_method" value="DELETE">
    <input type="submit" value="TestRest DELETE">
</form>
<br>

<form action="SpringMvc/testRest/1" method="post">
    <input type="hidden" name="_method" value="PUT">
    <input type="submit" value="TestRest put">
</form>
<br>
```
#### 映射请求参数&请求参数
@RequestParam：
* 可以通过@RequestParam请求参数，在处理方法入参处使用@RequestParam可以把请求参数传递给请求方法
   -value：参数名
   -required：是否必须，默认为true表示请求参数中必须包含对应的参数，若不存在，将抛出异常
  -defaultValue：可以设置默认值
例子：

```java
   @RequestMapping("/RequestParam")
    public String TestRequestParam(@RequestParam("username") String username,@RequestParam("age") int age){
        System.out.println(username);
        System.out.println(age);
        return SUCCESS;
    }
```

```java
映射请求参数
<a href="SpringMvc/RequestParam?username=murder&age=11">RequestParamTest</a>

```

@RequesHeader：
* 可以通过@RequesHeader绑定请求头的属性值
* 请求头包含了若干个属性，服务器可据此获知客户端的信息，通过@RequesHeader即可将请求头中的属性值绑定到处理方法的入参中
例子：

```java
  @RequestMapping("/RequestHeader")
    public String TestRequestHeader(@RequestHeader("Accept-Language") String al){

        System.out.println(al);

        return SUCCESS;
    }
```

@CookieValue：
例子：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317145918673.png)
### 使用POJO对象绑定请求参数值：
* SpringMVC会按请求参数名和POJO属性名进行自动匹配，自动为该对象属性值，支持级联属性。如：dept.deptid.tel等
例子：

```java
   @RequestMapping("/autoPojo")
    public String pojoTest(POJO（bean的名字就是POJO） pojo){
        System.out.println(pojo);
        return SUCCESS;
    }
```
对应的前端代码

```java
给pojo对象自动赋值

<form action="SpringMvc/autoPojo"  >
 username: <input type="text" name="username">
    <br>
 password: <input type="password" name="password">
    <br>
 email：<input type="text" name="email" >
    <br>
 age: <input  type="text" name="age">
    <br>
 city:<input type="text" name="address.city">
    <br>
 province:<input type="text" name="address.province">

    <input type="submit" value="提交">

</form>
```

#### 使用servlet原生的API作为入参：
MVC的Handler方法可以接受的ServletAPI类型的参数：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200317181648460.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTc4Nzk5OA==,size_16,color_FFFFFF,t_70)例子：

```java
   /*
     * 可是使用Servlet原生API作为目标方法的参数，可以支持：
     *HttpServletRequest
     *HttpServletResponse
     *HttpSession
     *Principal
     *InputStream
     *outStream
     * Reader
     * Writer
   */

    @RequestMapping("/testServlet")
    public void testServlet(HttpServletRequest request, HttpServletResponse response, Writer writer) throws IOException {

        System.out.println("testServlet:"+request);
        System.out.println("testServlet:"+response);

        writer.write("helloSpringMvc");
    }

```
### 处理模型数据（M&&V）：

*  ModelAndView：控制器处理方法的返回值如果为ModelAndView，则器既包含视图信息，也包含模型数据信息， SpringMVC会把ModelAndView的model中数据放入到request域对象中
* 例子：

```java
/*处理模型数据：
             目标方法的返回值可以是ModelAndView类型
             其中可以包含视图和模型
             SpringMVC会把ModelAndView的model中数据放入到request域对象中
*/
    @RequestMapping("/TestModelAndView")
    public ModelAndView TestModelAndView(HttpServletRequest req
    ){
        String viewName=SUCCESS;
        ModelAndView modelAndView=new ModelAndView(viewName);

     //   添加模型数据到ModelAndView中
       modelAndView.addObject("time", new Date());
        return modelAndView;

    }


```
* Map及Model：

```java
/*Map及Model
     目标方法可以在入参添加Map类型（实际上也可以是Model类型或ModelMap）

*/
     @RequestMapping("/testMap")
    public String testMap(Map<String,Object> map){
         map.put("names", Arrays.asList("tom","jerry","mark"));
         System.out.println(map.getClass().getName());

         return SUCCESS;
     }
```
@SessionAttributes：


```java


```

```java
/*@SessionAttributes:
         可以通过属性名指定需要放到会话的属性，还可以通过模型属性的对象类型指定那些模型属性需要放到会话中
          这个注解需要放到类的上面
    */
    @SessionAttributes(value = {"user"},types = {String.class})
	@RequestMapping("/SpringMvc")
	@Controller
	public class SpringTest {
		     @RequestMapping("/testSessionAttributes")
		    public String testSessionAttributes( Map<String,Object> map){
		         POJO pojo=new POJO("Tom", "9669", "xxx", 18);
		           map.put("user", pojo);
		           map.put("name","guigu");
                    return SUCCESS;
   							  }}
```

@ModelAttribute:

```java
/*
     ModelAttribute：
                    *1.有@ModelAttribute标记的方法，会在每个目标方法执行之前被SpringMvc调用
                    *2.@ModelAttribute注解也可以修饰目标方法的POJO的入参其value属性值有如下的作用：
                              1).SpringMVC会使用value属性值在implicitModel中查找对应的和随行，若存在，直接传入到目标方法的入参中
                              2).SpringMVC会以value为key，POJO类型的对象为value，存入到request中
                    2.运行流程：1.执行@ModelAttribute注解修饰的方法；从数据库汇中取出做对象，键：user
                               2.SpringMvc从map中取出user对象，并把表单的请求参数赋给user对象的对应属性
                               3.SpringMVC 把上述对象传入目标参数
                    3.注意：在@ModelAttribute的方法中，放入map时的键需要和目标方法入参类型的第一个字母小写的字符串一直
                    4.源码分析流程：1.调用@ModelAttribute注解修饰的方法，实际上把@ModelAttribute方法中Map中的数据放在了implicitModel中
                                   2.解析请求处理的的目标参数，实际上该目标参数来自于WebDataBinder对象的target属性
                                   WebDataBinder的创建：
                                                       1.可以确定objectName属性：若传入的attrName属性值为""，则objectName为类名第一个字母小写.
                                                             注意：若目标方法的参数使用了@ModelAttribute修饰，那么若attrName可以通过的属性value获取
                                                       2. 可以确定target属性:在implicitModel中查找attrName ,存在则继续运行，若不存在，则验证当前
                                                                           的handler是否使用@SessionAttributes() ,则尝试从Session中获取，没有则会报错抛出异常
                                                                           如果Handler中没有使用@SessionAttributes()进行修饰，或@SessionAttributes()中没有使
                                                                           用value值确定的key，则会自动创建当前使用的POJO
                                                       3.SpringMvc把表单的请求参数赋给了WebDataBinder的target对应的属性，
                                                       3.1.在通过把WebDataBinder传入dobind()，来将表单信息覆盖，在通过implicitModel的put方法将其已经被覆盖的对象传入，这样implicitModel的POJO就是赋完值的POJO了
                                                       4.SpringMvc会把WebDataBinder的attrName和target给到implicitModel进而传到request中
                                                       5.把WebDataBinder的target作为参数传递给目标方法的入参
                                                          大概是args从WebDataBinder中get到一个target然后return


                  5.SpringMvc确定目标方法POJO类型入参的过程：
                                                       1.确定一个key
                                                            若目标方法POJO类型的目标参数没有使用@ModelAttribute作为修饰，则key为Pojo类名第一个字母的小写，若使用@ModelAttribute则key为@ModelAttribute的value值
                                                       2.在implicitModel查找key对应的对象，若存在，则作为入参传入

                                                       3.如implicitModel不存在，则检查当前的Handler是否使用@SessionAttrbutes注解修饰
                                                       若使用了@SessionAttribu若在@ModelAttribute标记的方法中的map中保存过，且key和1tes注解的value属性值中包含了key，则会从HttpSession中来获取key所对应的value值，若存在则直接传入到目标方法的入参中，如不存在抛出异常

                                                       4.若handler没有标识@SessionAttributes或这个注解value不包含key，则会通过反射创建当前POJO的对象，作为目标方法的参数传入
                                                       5.SpringMVC会把key和value保存到implicitModel中，进而会保存到request中

                */
     @ModelAttribute
     public void getUser(@RequestParam(value = "id",required = false) Integer id,Map<String,Object> map){
         System.out.println("执行了");
         if(id!=null){
             //模拟从数据库汇中获取对象
             User user=new User(1,"Tom","123456","695418746@qq.com",12);
             System.out.println("@ModelAttribute执行了");
             System.out.println("成功获取一个对象"+user);
             map.put("abc", user);
         }
     }

     @RequestMapping("/ModelAttribute")
     public String ModelAttributeTest(@ModelAttribute("user")  User user){

         System.out.println("修改"+ user);
         return SUCCESS;
     }
```

### 视图和视图解析器：
1.在目标方法，无论返回一下三个哪一个，都会被修饰成ModelAndView
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200320011428365.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTc4Nzk5OA==,size_16,color_FFFFFF,t_70)
流程：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200320011708904.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTc4Nzk5OA==,size_16,color_FFFFFF,t_70)
视图:![在这里插入图片描述](https://img-blog.csdnimg.cn/20200320011802526.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTc4Nzk5OA==,size_16,color_FFFFFF,t_70)
我所理解的view就是jsp，html。model就是数据，视图解析器先解析view，再结合view将mv传出
视图解析器：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200320011910439.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTc4Nzk5OA==,size_16,color_FFFFFF,t_70)
#### InternalResouceView：将试图名解析为一个URL文件，一般使用该解析器将试图映射保存为一个保存在WEB-INF的程序文件
* 若项目中使用了JSTL，则SpringMVC会自动把试图由InternalResouce转为JstlView

* 若使用JSTL的fmt标签则需要在SpringMVC的配置文件汇中配置国际资源文件

* 例子：建立三个不同版本，且语言不同的配置文件，在获取：

```java
viewTest<br>
<fmt:message key="i18n.username"></fmt:message><br>
<fmt:message key="i18n.password"></fmt:message><br>

```
然后再去SpringMVC的配置文件中配置国际化资源文件：

```java
<!--配置国际化资源文件-->
      <bean  id="bundleMessageSource" class="org.springframework.context.support.ResourceBundleMessageSource">
          <property name="basename" value="i18n"></property>
      </bean>
```

然后当你运行的时候，就可以通过切换页面版本语言？？我也不太懂，

* 若希望直接响应通过SpringMVC渲染的页面，不通过handler，可以是使用mvc:view-controller标签实现
* 例如：

```java

<!--    在实际开发中通常要配置mvc:annotation-driven标签-->
     <mvc:annotation-driven></mvc:annotation-driven>

<!--    配置直接转发的页面
        可以直接响应转发的页面，而无需在经过Handler
-->
    <mvc:view-controller path="/success" view-name="success"></mvc:view-controller>

```

#### 自定义视图：
其实原理都差不多，这只不过是，讲一个继承view类型的bean的传入IOC容器，然后，建立目标方法，
通过@RequestMapping("/testViewAndViewResolver")知道目标方法，在通过一个叫做BeanNameViewResolver的视图解析器（这个解析器通过bean的名字来寻找目标），然后解析，跳转！
流程：
先建立一个自定义的bean（视图解析器）：

```java
@Component
public class hello implements View {
    @Override
    public String getContentType() {

        return "text/html";
    }

    @Override
    public void render(Map<String, ?> map, HttpServletRequest request, HttpServletResponse response) throws Exception {

        response.getWriter().write("hello view ,time is:"+new Date());

    }
}
```
加入@componet的原因是，在BeanNameViewResolver的源码中写有，通过IOC容器的getbean来获取
在SpringMVC配置

```java
<!--配置BeanNameViewResolver视图解析器:使用视图的名字历来解析
    通过order属性来定义视图的优先级，order值越小，级别越高

-->

       <bean  class="org.springframework.web.servlet.view.BeanNameViewResolver">
           <property name="order" value="100"></property>
       </bean>
```

在获取就可以了：

```java
     @RequestMapping("/testView")
    public String testView(){

         System.out.println("testView");
         return  "hello";
     }
```

#### 关于重定向：

```java
/*关于重定向：
           既可以转发页面，也可以转发相应的handler方法
 */
    @RequestMapping("/testRedirect")
    public String testRedirect(){
        System.out.println("testRedirect");
        return "redirect:/index.jsp";
    }
```
### 处理静态资源：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200323173556603.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTc4Nzk5OA==,size_16,color_FFFFFF,t_70)例子：在你的SpringMVC配置文件里面配置：

```javascript
		<!--让DispatcherServlet不去拦截静态资源-->
    <mvc:default-servlet-handler></mvc:default-servlet-handler>
```
### 数据绑定流程：
* 运行机制：
* 数据绑定流程
1. Spring MVC 主框架将 ServletRequest  对象及目标方• 法的入参实例传递给 WebDataBinderFactory 实例，以创 建 DataBinder 实例对象 2. DataBinder 调用装配在 Spring MVC 上下文中的 • ConversionService 组件进行数据类型转换、数据格式 化工作。将 Servlet 中的请求信息填充到入参对象中 3. 调用 Validator 组件对已经绑定了请求消息的入参对象• 进行数据合法性校验，并最终生成数据绑定结果 BindingData 对象 4. Spring MVC 抽取 BindingResult 中的入参对象和校验• 错误对象，将它们赋给处理方法的响应入参
* ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200327194255370.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTc4Nzk5OA==,size_16,color_FFFFFF,t_70)
### 自定义类型转换器：
**SpringMVC定义了3种类型的转换器接口，实现任意一个转换器接口都可以作为转换器注册到ConversionServiceFactoryBean中：
-Converter<S,T>:将S类型对象转为T类型对象
-ConverterFactory:将相同系列多个“同质”Conventer封装在一起，如果希望将一种类型的对象转换为另一种类型及其子类的对象（例如：将String转换为Number（integer，Long，double）对象）可使用该转换器工厂类
-GennericConverter：会根据原类对象及目标类对象所在的宿主类的上下文信息进行类型转换**
* ConversionService 是 Spring 类型转换体系的核心接口。
*  可以利用 ConversionServiceFactoryBean 在 * Spring 的 IOC • 容器中定义一个 ConversionService. Spring 将自动识别出 IOC 容器中的ConversionService，并在 Bean 属性配置及 Spring  MVC 处理方法入参绑定等场合使用它进行数据的转换
* 可通过 ConversionServiceFactoryBean 的 converters 属性• 注册自定义的类型转换器

**自定义转换器示例：**
1.设计你自定义的转换器继承conversion（注意是Spring提供的）

```java
package com.ConverterSpringMvcTest;

import com.SpringCRUD.Dao.Department;
import com.SpringCRUD.Dao.Employee;
import org.springframework.core.convert.converter.Converter;
import org.springframework.stereotype.Component;

@Component
public class SpringConversion implements Converter<String, Employee> {
    @Override
    public Employee convert(String s) {
//        GG-gg@qq.com-0-105

        if (s != null) {
            String[] val = s.split("-");

            if (val != null && val.length == 4) {

                String name = val[0];
                String email = val[1];
                Integer gender = Integer.parseInt(val[2]);
                Department department = new Department();
                department.setId(Integer.parseInt(val[3]));
                Employee employee = new Employee(null, name, email, gender, department);
                return employee;
            }
        }

        return  null;
    }
}

```
2.在你的SpringMVC配置文件加入ConversionServiceFactoryBean配加以配置，再你的<mvc:annotation-friven>配置：

```java
   <!--    使MVC注解生效-->
    <mvc:annotation-driven conversion-service="conversionServiceFactoryBean"></mvc:annotation-driven>

    <!--自定义类型转换器-->

    <bean id="conversionServiceFactoryBean" class="org.springframework.context.support.ConversionServiceFactoryBean">
        <property name="converters" >
            <set>
                <ref bean="springConversion"></ref>
            </set>
        </property>
    </bean>

```

**注意:InternalResourceViewResolver解析器 order默认最大（执行顺序也就最晚），通常来讲 视图解析器的顺序都是 越常用 越靠后**
然后就可以用了

#### 关于<mvc：annotation-driven>
<mvc:annotation-driven /> 会自动注册RequestMappingHandlerMapping 、RequestMappingHandlerAdapter 与 ExceptionHandlerExceptionResolver  三个bean。
* **还将提供以下支持**:
* **支持使用 ConversionService 实例对表单参数进行类型转换**
* **支持使用 @NumberFormat annotation、@DateTimeFormat  注解完成数据类型的格式化**
*  **支持使用 @Valid 注解对 JavaBean 实例进行 JSR 303 验证**
* **支持使用 @RequestBody 和 @ResponseBody 注解**
 

#### @InitBinder
*  **由 @InitBinder 标识的方法，可以对 WebDataBinder 对象进行初始化。WebDataBinder 是 DataBinder 的子类，用于完成由表单字段到 JavaBean 属性的绑定**
* **@InitBinder方法不能有返回值，它必须声明为void** 。
* **@InitBinder方法的参数通常是是 WebDataBinder**

例子：
使某个属性无法复制

```java
    @InitBinder
    public void initBinder(WebDataBinder webDataBinder){

        webDataBinder.setDisallowedFields("lastName");
    }
```


###  数据格式化：
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020040114513112.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTc4Nzk5OA==,size_16,color_FFFFFF,t_70)所以在你的SpringMvc的配置下 ，你可以把你的自定义转换器加入到转换器工厂：
```java
    <mvc:annotation-driven  conversion-service="conversionServiceFactoryBean"></mvc:annotation-driven>
    <!--让DispatcherServlet不去拦截静态资源-->
    <mvc:default-servlet-handler ></mvc:default-servlet-handler>


    <!--自定义类型转换器-->

    <bean id="conversionServiceFactoryBean"
          class="org.springframework.format.support.FormattingConversionServiceFactoryBean">
        <property name="converters" >
            <set>
                <ref bean="springConversion"></ref>
            </set>
        </property>
    </bean>
```


### JSR303数据校验：
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020040119284656.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTc4Nzk5OA==,size_16,color_FFFFFF,t_70)![在这里插入图片描述](https://img-blog.csdnimg.cn/20200401192859144.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTc4Nzk5OA==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200401192922376.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTc4Nzk5OA==,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200401193002460.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTc4Nzk5OA==,size_16![在这里插入图片描述](https://img-blog.csdnimg.cn/20200401193057836.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTc4Nzk5OA==,size_16,color_FFFFFF,t_70),color_FFFFFF,t_70)

**简单的说** 你的校验对象和你的bindingresult或者errors对象之间不允许有其他对象出现

步骤：
1.导入相应的jar包：


```java
<!--JSO303-->

    <dependency>
      <groupId>org.hibernate</groupId>
      <artifactId>hibernate-validator</artifactId>
      <version>5.1.0.Final</version>
    </dependency>


    <dependency>
      <groupId>org.jboss.logging</groupId>
      <artifactId>jboss-logging</artifactId>
      <version>3.1.1.GA</version>
    </dependency>

    <dependency>
      <groupId>javax.validation</groupId>
      <artifactId>validation-api</artifactId>
      <version>1.1.0.Final</version>
    </dependency>
```

2.在SpringMVC的配置文件加入注解<mvc:annotation-driven>
3.在你的bean中的属性赋值

```java
package com.SpringCRUD.Dao;

import org.hibernate.validator.constraints.Email;
import org.hibernate.validator.constraints.NotEmpty;
import org.springframework.format.annotation.DateTimeFormat;
import org.springframework.format.annotation.NumberFormat;

import java.util.Date;

import javax.validation.constraints.Past;

public class Employee {

	private Integer id;
	@NotEmpty
	private String lastName;
	@Email
	private String email;
	private Integer gender;
	private Department department;

	@Past
    //下面这个是conversion的格式化
	@DateTimeFormat(pattern = "yyyy-MM-dd")
	private Date birth;
   //下面这个是conversion的格式化
	@NumberFormat(pattern = "#,###,###.#")
	private  float salary;

	public float getSalary() {
		return salary;
	}

	public void setSalary(float salary) {
		this.salary = salary;
	}

	public Date getBirth() {
		return birth;
	}

	public void setBirth(Date birth) {
		this.birth = birth;
	}

	public Integer getId() {
		return id;
	}

	public void setId(Integer id) {
		this.id = id;
	}

	public String getLastName() {
		return lastName;
	}

	public void setLastName(String lastName) {
		this.lastName = lastName;
	}

	public String getEmail() {
		return email;
	}

	public void setEmail(String email) {
		this.email = email;
	}

	public Integer getGender() {
		return gender;
	}

	public void setGender(Integer gender) {
		this.gender = gender;
	}

	public Department getDepartment() {
		return department;
	}

	public void setDepartment(Department department) {
		this.department = department;
	}

	@Override
	public String toString() {
		return "Employee{" +
				"id=" + id +
				", lastName='" + lastName + '\'' +
				", email='" + email + '\'' +
				", gender=" + gender +
				", department=" + department +
				", birth=" + birth +
				", salary=" + salary +
				'}';
	}

	public Employee(Integer id, String lastName, String email, Integer gender,
					Department department) {
		super();
		this.id = id;
		this.lastName = lastName;
		this.email = email;
		this.gender = gender;
		this.department = department;
	}

	public Employee() {
		// TODO Auto-generated constructor stub
	}



}

```

4.在你的目标方法的入参修饰@valid

```java
 @RequestMapping(value = "/emp", method =RequestMethod.POST)
    public String Save(@Valid Employee employee, BindingResult bindingResult,Map map
    ){

        System.out.println("save:"+employee);

        if(bindingResult.getErrorCount()>0){
            System.out.println("出错了");

            for(FieldError fieldError:bindingResult.getFieldErrors()){
                System.out.println(fieldError.getField()+":"+fieldError.getDefaultMessage());
            }
            //若验证出错，则转向定制的页面

            map.put("departments", departmentDao.getDepartments());

            return "add";
        }
        employeeDao.save(employee);

        return "redirect:/emps";
    }

```

如何获取错误信息（具体例子在上面）：
![在这里插入图片描述](https://img-blog.csdnimg.cn/202004011937017.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTc4Nzk5OA==,size_16,color_FFFFFF,t_70)
### 错误消息的显示以及国际化
错误标签的显示：
1.使用Spring回显标签即可：

```java


  <form:form action="${pageContext.request.contextPath}/emp" method="post"
             modelAttribute="employee">


      <%--      Path属性就是html标签里的name--%>
     <c:if test="${employee.id==null}">
      LastName:<form:input path="lastName" ></form:input>
           ☆这里<form:errors path="lastName"></form:errors>
         <br>

     </c:if>

      <c:if test="${employee.id!=null}">
<%--           对于_method不能使用form:hidden标签，因为modelAttribute对应的bean中没有_method这个属性--%>
           <form:hidden path="id"></form:hidden>
           <input type="hidden",name="_method" value="PUT">
      </c:if>
      <br>
      Email:<form:input path="email"></form:input>

       ☆这里<form:errors path="email"></form:errors>
      <br>
      
      <%
          Map<String,String> genders=new HashMap<>();
          genders.put("1", "male");
          genders.put("0", "female");

          request.setAttribute("genders", genders);
      %>
      
      Gender:<form:radiobuttons path="gender" items="${genders}"></form:radiobuttons>
      <br>


      Department:<form:select path="department.id" itemLabel="departmentName" itemValue="id"                     items="${departments}"></form:select>
      <br>

      Birth:    <form:input path="birth"></form:input>
      ☆这里<form:errors path="birth"></form:errors>
      <br>
      
      Salary:    <form:input path="salary"></form:input>
      <br>
    
      <input type="submit" value="Submit">

  </form:form>
```

2.定制错误消息：
通过国际化 不知道哪里错了

### 处理JSON：使用HttpMessageConverter
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200405152640406.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTc4Nzk5OA==,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200405152740884.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTc4Nzk5OA==,size_16,color_FFFFFF,t_70)![在这里插入图片描述](https://img-blog.csdnimg.cn/20200405152756261.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTc4Nzk5OA==,size_16,color_FFFFFF,t_70)![在这里插入图片描述](https://img-blog.csdnimg.cn/20200405152846548.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTc4Nzk5OA==,size_16,color_FFFFFF,t_70)![在这里插入图片描述](https://img-blog.csdnimg.cn/20200405152910127.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTc4Nzk5OA==,size_16,color_FFFFFF,t_70)![在这里插入图片描述](https://img-blog.csdnimg.cn/20200405152924373.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTc4Nzk5OA==,size_16,color_FFFFFF,t_70)![在这里入图片描述](https://img-blog.csdnimg.cn/20200405152942301.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTc4Nzk5OA==,size_16,color_FFFFFF,t_70)
**注意：** requestBody是来接收前端传递给后端的json字符串中的数据的，使用请求体里面的，所以你的请求犯法必须是方法POST的！！！
具体的看这位大佬的吧：https://www.cnblogs.com/zly123/p/10853049.html
例子：

1.加入jar包 

```java
 <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-core</artifactId>
      <version>2.9.4</version>
    </dependency>

    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>2.9.4</version>
    </dependency>

    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-annotations</artifactId>
      <version>2.9.4</version>
    </dependency>
```

```java
 //@ResponseBody 在目标方法的返回值 可以将得到的数据通过输出流返回到页面
    @ResponseBody
    @RequestMapping("/testHttpMessageConverter")
    //@RequestBody 在目标方法的入参 可以将页面的数据通过输入流获取到SpringMvc
    public String  testHttpMessageConverter(@RequestBody String body){

        System.out.println(body);

        return "hello world"+new Date();

    }
```

前端代码：

```java

    <script>

        $(function () {
            $("#testJson").click(function () {

                var url=this.href;
                var args={}
                $.post(url,args,function (data) {
                   for( var i=0;i<data.length;i++){
                             var id =data[i].id;
                             var lastname=data[i].lastName;

                             alert(id+":"+lastname)
                   }
                })
                return false;
            })
        })
    </script>


<a href="testJson" id="testJson">Test Json</a>
</body>

<form action="testHttpMessageConverter" method="post" enctype="multipart/form-data">
    File: <input type="file" name="file">
    Desc: <input type="text" name="desc">
    <input type="submit" value="Submit">
</form>
```
### 关于国际化：
**关于国际化：
         1.在页面上能够根据浏览器语言设置的情况对文本（不是内容），时间，数值进行本地化处理
         2.可以在bean中获取国际化资源文件Locale对应的消息
         3.可以通过超链接切换Locale，而不依赖于浏览器的语言设置情况
    解决:
         1.使用JSTL的fmt标签
         2.在bean中注入ResouceBundleMessageSource的实例，使用其对应的getMessage方法即可
         3.配置LocalResolver和LocaleChangeInterceptor**

#### 1（在页面上能够根据浏览器语言设置的情况对文本（不是内容），时间，数值进行本地化处理）.步驟：
1.在你的SpringMVC配置文件配置：

```java
<!--配置国际化资源文件-->
    <bean id="bundleMessageSource" class="org.springframework.context.support.ResourceBundleMessageSource">
        <property name="basename" value="i18n_ZH"></property>

    </bean>

```

2.配置配置本地国际化文件：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200407135702284.png)
3.在你的页面使用fmt标签：

```java

  <fmt:bundle basename="i18n">
      <fmt:message key="i18n.user"></fmt:message>
  </fmt:bundle>

```


#### 2  步骤（可以在bean中获取国际化资源文件Locale对应的消息）：
1.在你的SpringMVC配置文件中配置：

```java
<!--配置国际化资源文件-->
    <bean id="bundleMessageSource" class="org.springframework.context.support.ResourceBundleMessageSource">
        <property name="basename" value="i18n"></property>

    </bean>
```
2.在你的页面加入fmt标签：

```java

  <fmt:bundle basename="i18n">
      <fmt:message key="i18n.user"></fmt:message>
  </fmt:bundle>

```
3.在你的目标方法使用（ResourceBundleMessageSource）

```java

     @Autowired
    private ResourceBundleMessageSource messageSource;

    @RequestMapping("/i18n")
    public  String testi18n(Locale locale){
        System.out.println("进来哦");
        String val= messageSource.getMessage("i18n.user", null, locale);

        System.out.println("走了");
        System.out.println(val);

        return "i18n";

    }
```
#### 3.可以通过超链接切换Locale，而不依赖于浏览器的语言设置情况
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200407180810972.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTc4Nzk5OA==,size_16,color_FFFFFF,t_70)

1.步骤：
在你的SpringMVC配置文件中加入SessionLocaleReslover和LocaleChanceInterceptor

```java
<!--    配置localeChanceInterceptor-->

    <mvc:interceptors>
        <bean class="org.springframework.web.servlet.i18n.LocaleChangeInterceptor"></bean>
    </mvc:interceptors>

<!--    配置sessionLocaleResolver-->

    <bean  id="localeResolver"
            class="org.springframework.web.servlet.i18n.SessionLocaleResolver"></bean>


```

2.在你的前端网页，创建相应的超链接：

```java
要求：超链接的href对应的URL要像下面这样的格式
<a href="i18n?locale=zh_CH">中文</a>
<br>
<a href="i18n?locale=en_US">英文</a>
<br>
```
然后就可以了


#### 文件上传：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200416213103578.png)步骤：
1.在你的SpringMVC的配置文件中配置MultipartResolver，然后配置属性：
defaultEncoding必须和用户 JSP 的 pageEncoding 属性一致，以便正确解析表单的内容

```java
<!--配置MultipartResolver-->
<bean   id="multipartResolver"
        class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
     <property name="defaultEncoding" value="UTF-8"></property>
     <property name="maxInMemorySize" value="1024000"></property>
</bean>
```


2.加入jar包为了让 CommonsMultipartResovler 正确工作，必须先将 Jakarta Commons FileUpload 及 Jakarta Commons io的类包添加到类路径下

## 拦截器：
**自定义拦截器：**
步骤1：创一个类调用HandlerInterceptor接口，并集成方法：

```javapackage com.Interceptor;

import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class FirstInterceptor implements HandlerInterceptor {

    /*
    * preHandle方法在目标方法执行之前被调用。
    *      若返回值为false，则不会再调用后续的拦截器和目标方法，也不会postHandle，afterCompletion
    *      若返回值为true，则继续再调用后续的拦截器和目标方法
    *
    * 该方法可以考虑做权限，日志，事务问题
    *
    * */

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {

        System.out.println("FirstInterceptor preHandle");

        return true;

    }

    /*
    * postHandle执行在 调用目标方法之后，但渲染图之前
    * 可以对请求域中的属性或视图进行操作
    *
    * */
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {

        System.out.println("FirstInterceptor postHandle");

    }

    /*
    *
    * 渲染视图之后调用的
    * 释放资源
    *
    * */
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {

        System.out.println("FirstInterceptor afterCompletion");

    }
}

```
2.在你的SpringMVC的配置文件中配置Interceptor：

```java
<!--    配置localeChanceInterceptor-->

    <mvc:interceptors>
        <bean class="org.springframework.web.servlet.i18n.Loc aleChangeInterceptor"></bean>
        <!--配置自定义的拦截器-->
        <bean id="firstInterceptor" class="com.Interceptor.FirstInterceptor"></bean>
    </mvc:interceptors>
```
**拦截器方法的执行顺序：**


举个例子：![在这里插入图片描述](https://img-blog.csdnimg.cn/20200418164100174.png)
## 异常处理：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200418195600587.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200418195626549.png)步骤代码：

```java
/* 1.在@ExceptionHandler方法的入参中可以加入Exception类型的参数，该参数及对应异常
      2.@ExceptionHandler方法的入参不能传入Map，若希望把异常信息传到页面，需要使用ModelAndView作为返回值
      3.@ExceptionHandler方法标记的异常优先级的问题
                根据你的实际异常与@ExceptionHandler(这里你标记的异常匹配 })，趋向于更接近 例如你的异常是数字异常，相对于 runtimeException和ArithmeticException相比选择进入第二个方法

*
*/
    @ExceptionHandler({ArithmeticException.class})
    public ModelAndView handlerArithmeticException(Exception ex){
        System.out.println("出Arithmetic异常了"+ex);
        ModelAndView mv=new ModelAndView("error");
        mv.addObject("exception",ex);
        return mv;
    }


    @ExceptionHandler({RuntimeException.class})
    public ModelAndView handlerArithmeticException2(Exception ex){
        System.out.println("出Runtime异常了"+ex);
        ModelAndView mv=new ModelAndView("error");
        mv.addObject("exception",ex);
        return mv;
    }
```

**自定义异常处理：**
当你的@ControllerAdvice如果在Handler中找不到@ExceptionHandler方法中当前的异常
      则将去@ControllerAdivice标记的类中查找@ExceptionHandler标记的方法来处理异常
      所以可以自定义一个异常
```java
package com.HandlerException;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.servlet.ModelAndView;

@ControllerAdvice
public class HandlerException {

    @ExceptionHandler({ArithmeticException.class})
    public ModelAndView handlerArithmeticException(Exception ex){
        System.out.println("出Arithmetic异常了"+ex);
        ModelAndView mv=new ModelAndView("error");
        mv.addObject("exception",ex);
        return mv;
    }

}

```
如果在你的handler下面没有找到带有@ExceptionHandler方法，那么就回去找 @controllerAdvice下面的@ExceptionHandler，如果都没有，调用默认的异常处理器.
**ResponseStatusExceptionResolver：**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200418210702235.png)
步骤：
1.创建异常类：

```java
package com.HandlerException;

import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.ResponseStatus;
value可以设置状态值，reason自定义原因
@ResponseStatus(value = HttpStatus.FORBIDDEN,reason = "用户名与密码不匹配")
public class UserEx extends RuntimeException {

  private static final long serialVersionUID=1L;

}

```
在目标方法创建好：

```java

   @RequestMapping("/testStatusException")
      public String testStatusException(@RequestParam("i")Integer i){


           System.out.println("jin");
          if(i==13){
              throw new UserEx();
          }


              System.out.println("success");

          return "success";
      }
```
### DefaultHandlerExceptionResolver：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200419180813264.png)
### SimpleMappingExceptionResolver：
如果希望对所有异常进行统一处理，可以使用 
SimpleMappingExceptionResolver，它将异常类名映射为
视图名，即发生异常时使用对应的视图报告异常
步骤：
1.在你的SpringMVC的配置文件中配置

```java
<!--配置使用SimpleMappingExceptionResolver来映射异常-->
    <bean class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
        <property name="exceptionMappings" >
            <props >
                <prop key="java.lang.ArrayIndexOutOfBoundsException">error</prop>
            </props>
        </property>
    </bean>
```
然后在目标方法直接使用就可以，他会把错误放进req中

```java
 @RequestMapping("/testSimpleMappingExceptionResolver")
      public String testSimpleMappingExceptionResolver(@RequestParam("i") Integer i){
       String val[]=new String[10];

          System.out.println(val[i]);


       return "success";
      }
```
SpringMVC运行流程：
**流程图**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200419203200799.png)
### 问题：
 * 1.需要进行Spring整合SpringMVC吗？
 * 2.还是否需要再加入Spring的IOC容器？
 * 3.是否需要在web.xml文件中配置启动SpringIOC容器的ContextLoaderListener
解决：
1.需要：通常情况下，类似于数据源，事务，整合其他框架都是方法在Spring的配置文件中而不是放在SpringMVC的配置文件中，实际放入Spring配置文件对应的IOC容器还有Service和dao
2.不需要：可以分成多个Spring的配置文件，使用import节点导入其他的配置文件

*  若SpringMvc的IOC容器金额SpringMVC的IOC容器扫描的包有重合的部分，就会导致bean被创建2次
解决：
 1.使Spring的IOC容器扫描的包和SpringMVC的ICO容器扫描的包没有重合的部分
 2.使用exclude-filter和include-filter子节点来规定只能扫描的注解：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200420154953112.png)SpringMVC的IOC容器中的bean可以来引用SpringIOC容器中的bean反之不行![在这里插入图片描述](https://img-blog.csdnimg.cn/2020042016023334.png)
 ###  补充：
 1. 在数据绑定 转换 检验的过程 可以用bindingresult来查看结果，图：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200810222216280.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTc4Nzk5OA==,size_16,color_FFFFFF,t_70)
当你的数据检验等 失败异常会传入BindingResult（本身他就是继承的errors类）
你可以通过方法里面 建立参数
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200810222340441.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTc4Nzk5OA==,size_16,color_FFFFFF,t_70)

