---
typora-copy-images-to: ..\..\..\Pic
---

# SpringBoor入门

## 1.简介

* SpringBoot 来建华Spring应用开发，约定大于配置，去繁从简，just run就能创建一个独立的，产品级别的应用，简化Spring应用开发的一个框架，整个Spring技术栈的一大整合，J2EE开发的一站式解决方案

### 背景

* J2EE笨重的开发，众多的配置，底下的开发效率，复杂的部署流程，第三方技术集成难度大

### 解决

* Spring全家桶时代，SpringBoot-》J2EE一站式解决方案‘
* SpringCloud-》分布式整体解决答案

## 2.微服务(架构风格)

单体运用:ALL-IN-ONE

![image-20201014173307561](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201014173315.png)

微服务:

* 一个应用应该是一组小型服务，可以通过HTTP进行交互

* 每一个功能元素最终形成的都是可独立替换和独立升级的软件单元

* 详细参考文档：https://martinfowler.com/microservices/

## 3.SpringBoot HelloWorld

一个功能：

 浏览器发送hello请求，服务器接受请求并处理，响应Hello World字符串

### 1.创建一个maven工程

 ### 2.导入依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.4.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>SpringBoot_Study</groupId>
    <artifactId>Spring-boot-01-helloworld</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>Spring-boot-01-helloworld</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```

### 3.编写主程序：启动SpringBoot

```java
package Helloworld;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ConfigurableApplicationContext;

/**
 * @author lenovo
 * 开启SpringBoot的第一个项目
 *  @SpringBootApplication 来标注一个主程序类，说明这是一个Spring Boot应用
 */
@SpringBootApplication
public class HelloWorldApplication {

    public static void main(String[] args) {
        //Spring应用启动起来
        ConfigurableApplicationContext run = SpringApplication.run(HelloWorldApplication.class, args);

    }
}

```

###  4.编写controller

**注意：**controller要和SpringBoot启动文件在一个目录，否则扫描不到controller

```java
package Controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

/**
 * @author lenovo
 */
@Controller
public class HelloController {

    @ResponseBody
    @RequestMapping()
    public String hello(){

        return "Hello World";
    }
}

```

### 5.测试结果

![image-20201014184053601](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201014184054.png)

### 6.简化部署工作

```
<!--这个插件，可以将以你的引用打包成一个可执行的jar包-->
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
```

将这个应用达成jar包，直接使用java -jar的命令进行

![image-20201014190000057](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201014190000.png)

### 7.Hello World探究

#### 1.Pom文件

##### 1.父项目

spring-boot-starter-parent：场景启动器的父类，也是来给启动器版本号的

```xml

    <groupId>SpringBoot_Study</groupId>
    <artifactId>Spring-boot-01-helloworld</artifactId>
    <version>1.0-SNAPSHOT</version>
<!--将Spring-boot-starter-parent作为父项目-->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.4.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
<!--点进去父类pom文件可以看出来，所有的依赖的版本是由这个父类来管理的
     这个父类spring-boot-starter-parent可以说是SpringBoot的版本仲裁中心
     SpringBoot的版本仲裁中心，以后我们导入依赖不需要版本号的，当然，没有在dependencies里面管理的依赖，必须声明版本号
-->
     <dependencies>
         <dependency>
             <groupId>org.springframework.boot</groupId>
             <artifactId>spring-boot-starter-web</artifactId>
         </dependency>
     </dependencies>

```

spring-boot-starter-parent可以说是SpringBoot的版本仲裁中心，以后我们导入依赖不需要版本号的，当然，没有在dependencies里面管理的依赖，必须声明版本号

**父类的pom文件**

![image-20201014191052164](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20201014191052164.png)

##### 2.启动器

1. 导入的依赖

```xml
 <dependencies>
         <dependency>
             <groupId>org.springframework.boot</groupId>
             <artifactId>spring-boot-starter-web</artifactId>
         </dependency>
     </dependencies>
```

spring-boot-starter-web可以分为两个模块：

* 1.spring-boot-starte:场景启动器，帮我们导入web模块正常运行所依赖的组件
* 2.web表示对应模块
* 加在一起就是表示，让启动器加入web模块的依赖

![image-20201014192236846](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20201014192236846.png)

当你想要不同的功能，你可以去官网查找并导入不同的启动器

![image-20201014192530496](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201014192537.png)

 2.结论

* Spring将所有的功能场景都抽取出来，做成一个个的starters(启动器)，只需要在项目里面引入这些这些starter相关场景的所有依赖都会导入进来，要什么 功能就导入什么场景的启动器

#### 2.主程序类，主入口类

```java
package Helloworld;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ConfigurableApplicationContext;

/**
 * @author lenovo
 * 开启SpringBoot的第一个项目
 *  @SpringBootApplication 来标注一个主程序类，说明这是一个Spring Boot应用
 */
@SpringBootApplication
public class HelloWorldApplication {

    public static void main(String[] args) {
        //Spring应用启动起来
        ConfigurableApplicationContext run = SpringApplication.run(HelloWorldApplication.class, args);

    }
}

```

**@SpringBootApplication:**SpringBoot应用标注在某个类上，说明这个类是SpringBoot的主配置类，SpringBoot就应该运行这个类的main方法来启动SpringBoot

```java
package org.springframework.boot.autoconfigure;

import java.lang.annotation.Documented;
import java.lang.annotation.ElementType;
import java.lang.annotation.Inherited;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

import org.springframework.beans.factory.support.BeanNameGenerator;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.SpringBootConfiguration;
import org.springframework.boot.context.TypeExcludeFilter;
import org.springframework.context.annotation.AnnotationBeanNameGenerator;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.ComponentScan.Filter;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.FilterType;
import org.springframework.core.annotation.AliasFor;
import org.springframework.data.repository.Repository;

@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {

	@AliasFor(annotation = EnableAutoConfiguration.class)
	Class<?>[] exclude() default {};

	@AliasFor(annotation = EnableAutoConfiguration.class)
	String[] excludeName() default {};

	
	@AliasFor(annotation = ComponentScan.class, attribute = "basePackages")
	String[] scanBasePackages() default {};

	
	@AliasFor(annotation = ComponentScan.class, attribute = "basePackageClasses")
	Class<?>[] scanBasePackageClasses() default {};

	@AliasFor(annotation = ComponentScan.class, attribute = "nameGenerator")
	Class<? extends BeanNameGenerator> nameGenerator() default BeanNameGenerator.class;

	@AliasFor(annotation = Configuration.class)
	boolean proxyBeanMethods() default true;

}

```

**@SpringBootConfiguration:**SpringBoot配置类

​      标记在某个类上，表示这是一个Spring Boot的配置类

​         **@Configuration:**Spring的注解，告诉当前的类为配置类,配置类上标注这个注解

​                配置类-------配置及文件   

​                     **@Component:**表示配置类（被@Configuration修饰的类）也是一个

**@EnableAutoConfiguration：**开启自动配置功能

​           以前我们需要配置的东西，SpringBoot帮我们自动配置，@EnableAutoConfiguration：告诉SpringBoot自动配置功能，这样自动配置才能生效

```java
@Inherited
@AutoConfigurationPackage
@Import(AutoConfigurationImportSelector.class)
public @interface EnableAutoConfiguration {
```

**@AutoConfigurationPackage:**自动配置包

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@Import(AutoConfigurationPackages.Registrar.class)
public @interface AutoConfigurationPackage {
```



​           **@Import(AutoConfigurationPackages.Registrar.class)：**Spring底层注解@import，作用是给容器导入一个组件，导入的组件由`AutoConfigurationPackages.Registrar.class`将当前主配置类(SpringBoot)所在的包下的所以的组件扫描进Spring容器中

源码调试结果：

![image-20201014211824143](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201014211824.png)

举个例子：

![image-20201014211429898](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20201014211429898.png)

这样的就不行了，因为扫描的包是Helloworld.Test

![image-20201014211604001](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20201014211604001.png)

这样就可以了，因为现在扫描包是Helloworld

**@EnableAutoConfiguration**注解下还有一个：**@Import(AutoConfigurationImportSelector.class)**，表示导入组件，告诉**EnableAutoConfiguration**导入组件**AutoConfigurationImportSelector**(导入那些组件的选择器),

**AutoConfigurationImportSelector**将所有需要导入的组件以全类名的方式返回，这些组件就会被添加到容器中，他会给容器导入非常多的自动配置类（xxxxautoConfiguration）

![image-20201015182453301](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20201015182453301.png)

那么如何来判断来扫描这些类呢

![image-20201015191524358](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20201015191524358.png)

![image-20201015191630997](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20201015191630997.png)

![image-20201015192315953](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201015192316.png)

从类路径下的META-INF/Spring.factorys中获取 EanbelAutoConfiguratin指定的值，将这些作为自动配置类导入到容器中，自动配置类就生效，帮我们自动进行配置工作

J2EE的整体整合解决方案和自动配置都在SpringBoot-autoconfigure-1.5.9RELEASE.jar



#### 7. 使用Spring Initializer快速创建SpringBoot项目

IDE都支持Sprnng的项目创建向导快速创建一个Spring Boot项目

![image-20201015195121502](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201015195121.png)

![image-20201015195131051](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201015195131.png)

导入模块

![image-20201015195250736](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201015195250.png)

创建成功：

![image-20201015203053364](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201015203053.png)

写入Handler

这里提一下：@@RestController：就是@ResponseBody和@controller

```java
package springbootinitializerquick.springbootinitializer.Controller;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * @author lenovo
 *
 *@RestController 就是@controller和@Response的集合
 * RestController类的代码头：
 * @Target(ElementType.TYPE)
 * @Retention(RetentionPolicy.RUNTIME)
 * @Documented
 * @Controller
 * @ResponseBody
 * public @interface RestController {
 *
 */
@RestController
public class RestHandler {

    @RequestMapping("/hello")
    public String  test(){

        return "hello world";
    }
}

```

运行结果

![image-20201015202606078](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20201015202606078.png)

总结：

默认生成的Spring Boot项目：

* 选择我们需要的模块，向导会联网创建Spring Boot

* resources文件夹中目录结构

  * static：保存所有的静态资源：js css images

  * templates：保存所有的模板页面；SpringBoot默认jar包使用嵌入式的Tomcat，默认不支持jsp的！！！！！！！！，但我们可以使用模板引擎（freemarker，thymeleaf）  

  * application.properties：Spring Boot应用的配置文件，可以在里面配置服务器端口号等属性

    如：

![image-20201015203619125](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20201015203619125.png)







# SpirngBoot配置





## 1.配置Yaml文件

SpringBoot支持两种配置文件

* application.properties
* application.yml

   配置文件的作用：修改SpringBoot自动配置的默认值，SpringBoot在底层都给我们配好了

Yaml(YAML Ain't Markup language)

​        YAML  A Markup Language:yaml是一个标记语言

​        YAML  isn't Markup Language:yaml是一个标记语言

标记语言：

​            以前的配置文件；大多都是用的是.xml

​            Yaml：以数据为重心，比JSON，XML等更适合做配置文件

​             Yaml配置例子

```yaml
server:
  port: 6666
```

​            Xml配置例子

```xml
<server>
      <port>6666</port>
</server>
```





## 2.yaml语法

### 1.基本语法

k:(空格)v：表示一对键值对(空格必须有)    

以**空格**的缩进控制层级关系；只要是左对齐一列数据，都是同一层级的

```yaml
server:
  port: 8080
  error:
    path: /
```

属性和值也是大小写敏感的

### 2.值的写法

* **字面量：普通的值(数字，字符串，布尔)**

  * 字符串默认不用加上单双引号；

    * "":双引号,不会转义字符串里面的特殊字符；特殊字符回作为本身想要表示的意思

      name:"zhangsan \n lisi"  输出：zhangsan  （换行）lsi

    * ’‘:单引号：回转仪特殊字符，特殊字符最终只是一个普通的字符串数据

       name：’zhangsan  /n lisi‘输出：zhabgsan  \n  isi

* **对象(属性)、Map(键值对)**

  ```yaml
  Friedn:
         lastName: zahngsan
         age: 20
  ```

  行列写法

  ```yaml
  Friend: {lastName: zahngsan,age:18}
  ```

  

* **数组(list,set)**

  使用-表示数组中的一个元素

```yaml
Pet:
   -cat
   -dag
```

行列写法

```yaml
pet: {cat, dog,pig}
```





## 3. 配置文件值注入

1. 创建对象

2. 导入配置文件处理器，以后编写配置就有提示了

   ```xml
    <!--spring-boot-configuration-processor：导入配置文件处理器，配置文件及进行绑定会有提示-->
   		<dependency>
   			<groupId>org.springframework.boot</groupId>
   			<artifactId>spring-boot-configuration-processor</artifactId>
   			<optional>true</optional>
   		</dependency>
   ```

3. 配置yaml

   ![image-20201016183351338](D:\LearnNotes\Pic\image-20201016183351338.png)

4. 在你指定的类上面加入@ConfigurationProperties(告诉SpringBoot让配置文件和这和为类一一注解，@Component告诉将这个类作为组件加入到容器)

   提几个注意事项：

* @ConfigurationProperties的prefix一定要小写
* 一定要加入@Component

```java
package springbootinitializerquick.springbootinitializer.Bean;

import jdk.nashorn.internal.objects.annotations.Setter;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

import java.util.List;
import java.util.Map;

/**
 * @author lenovo
 * 将配置文件中配置的每一个属性的值，映射到这个组件中
 * @ConfigurationProperties: 告诉SpringBoot将本类中的所有属性和配置文件中相关的配置文件进行绑定
 *      prefix=“person”：配置文件中指定person下面的属性一一对应
 *      只有这个组件是容器中的组件，才能提供@ConfigurationProperties功能
 */
@Component
//prefix一定要小写
@ConfigurationProperties(prefix = "person")
public class Person {

    private String lastName;
    private Integer age;
    private boolean isPropose;
    private Son son;
    private List<String> pets;
    private Map<String,String> PersonMessage;


    public String getLastName() {
        return lastName;
    }

    public void setLastName(String lastName) {
        this.lastName = lastName;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public boolean isPropose() {
        return isPropose;
    }

    public void setPropose(boolean propose) {
        isPropose = propose;
    }

    public Son getSon() {
        return son;
    }

    public void setSon(Son son) {
        this.son = son;
    }

    public List getPets() {
        return pets;
    }

    public void setPets(List pets) {
        this.pets = pets;
    }

    public Map<String, String> getPersonMessage() {
        return PersonMessage;
    }

    public void setPersonMessage(Map<String, String> personMessage) {
        PersonMessage = personMessage;
    }

    @Override
    public String toString() {
        return "Person{" +
                "lastName='" + lastName + '\'' +
                ", age=" + age +
                ", isPropose=" + isPropose +
                ", son=" + son +
                ", pets=" + pets +
                ", PersonMessage=" + PersonMessage +
                '}';
    }
}

```

运行结果

关于Properties的配置方法：

![image-20201016183244922](https://raw.githubusercontent.com/Codemilk/LearnNotes/main/Pic/20201016183501.png)

### 1.@Value和@ConfigurationProperties的区别

|                  | @ConfigurationProperties | @Value     |
| ---------------- | ------------------------ | ---------- |
| **功能**         | 批量注入配置文件中的属性 | 一个个指定 |
| **松散绑定**     | 支持                     | 不支持     |
| **Spel**         | 不支持                   | 支持       |
| **JSR303**       | 支持                     | 不支持     |
| **复杂类型封装** | 支持                     | 不支持     |

**松散绑定**

![image-20201016185104922](D:\LearnNotes\Pic\image-20201016185104922.png)

选择原则：**直接赋值**（@ConfigurationProperties）还是**复杂赋值**（@Value）

* 如果我们专门编写一个javaBean来和配置文件进行映射，我们直接使用@ConfigurationProperties
* 如果我们专门在某一个逻辑中，需要获取一下配置文件中的某项之，我们直接使用@Value



### 2.@PropertySource和@ImportResource

#### 1.@PropertySource

* @PropertySource：加载指定的配置文件

```java
package springbootinitializerquick.springbootinitializer.Bean;

import jdk.nashorn.internal.objects.annotations.Setter;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.context.annotation.PropertySource;
import org.springframework.stereotype.Component;
import org.springframework.validation.annotation.Validated;

import java.util.List;
import java.util.Map;

/**
 * @author lenovo
 * 将配置文件中配置的每一个属性的值，映射到这个组件中
 * @ConfigurationProperties: 告诉SpringBoot将本类中的所有属性和配置文件中相关的配置文件进行绑定
 *       1. prefix=“person”：配置文件中指定person下面的属性一一对应,且一定要小写
 *       2. 只有这个组件是容器中的组件，才能提供@ConfigurationProperties功能，所以需要加入@Component
 *       3. 默认是从全局配置文件中获取值
 */

@Component
//prefix一定要小写
//@ConfigurationProperties(prefix = "person")
@PropertySource(value = "classpath:person.properties")
/**JSR303校验*/
@Validated
public class Person {
//    @Value属性赋值

//    @Value("${person.last-name}")

    private String lastName;
    private Integer age;
    private boolean isPropose;
    private Son son;
    private List<String> pets;
    private Map<String,String> PersonMessage;


    public String getLastName() {
        return lastName;
    }

    public void setLastName(String lastName) {
        this.lastName = lastName;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public boolean isPropose() {
        return isPropose;
    }

    public void setPropose(boolean propose) {
        isPropose = propose;
    }

    public Son getSon() {
        return son;
    }

    public void setSon(Son son) {
        this.son = son;
    }

    public List getPets() {
        return pets;
    }

    public void setPets(List pets) {
        this.pets = pets;
    }

    public Map<String, String> getPersonMessage() {
        return PersonMessage;
    }

    public void setPersonMessage(Map<String, String> personMessage) {
        PersonMessage = personMessage;
    }

    @Override
    public String toString() {
        return "Person{" +
                "lastName='" + lastName + '\'' +
                ", age=" + age +
                ", isPropose=" + isPropose +
                ", son=" + son +
                ", pets=" + pets +
                ", PersonMessage=" + PersonMessage +
                '}';
    }
}

```

#### 2.@ImportResource

* @ImportResource:导入Spring配置文件，使其可以生效

![image-20201019164451532](D:\LearnNotes\Pic\image-20201019164451532.png)

![image-20201019164806968](D:\LearnNotes\Pic\image-20201019164806968.png)

**SpringBoot不会自动扫描我们的Spring配置文件，我们需要通过在你的主配置类加入`@ImportResource`**

```java
package springbootinitializerquick.springbootinitializer;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.ImportResource;

/**
 * @author lenovo
 * SpringBootInitializerApplication：配置类
 */
@ImportResource("classpath:Spring.xml")
@SpringBootApplication
public class SpringBootInitializerApplication {

	public static void main(String[] args) {
		SpringApplication.run(SpringBootInitializerApplication.class, args);
	}

}

```

但是多一个文件，我们还要去手写，SpringBoot推荐使用全注解的方式

配置类代替配置文件

```java
package springbootinitializerquick.springbootinitializer.Config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import springbootinitializerquick.springbootinitializer.Bean.TestImportResource;

/**
 * @author lenovo
 * @Configuration为Spring的配置类,将当前类作为配置类传入IOC
 */
@Configuration
public class configuration {

    /**将方法的返回值添加到容器,并且这个组件的默认Id就是方法名字*/
    @Bean
    public TestImportResource testImportResource(){
        System.out.println("testImportResource");
        return new TestImportResource();

    }
}

```





##  4.配置文件占位符

* 随机数
* 默认值
* 引用

![image-20201019171906684](D:\LearnNotes\Pic\image-20201019171906684.png)

![image-20201019173151746](D:\LearnNotes\Pic\image-20201019173151746.png)



## 5.Profile

* Profile是Spirng对不同环境提供不同配置功能的支持，可以通过激活，指定参数等方式快速切换环境

![image-20201019175913342](D:\LearnNotes\Pic\image-20201019175913342.png)

### 1.多Profile文件

### 1.创建不同的properties

我们在主配置文件编写的时候，文件名可以是，application-{profile}.properties/yml

默认使用application.properties的配置

### 2.yaml多profile文档块模式

```yaml
server:
  port: 8080
spring:
  profiles:
    active: dev
#---是文档分割器
---
server:
  port: 8081
spring:
  profiles: dev
---
server:
  port: 8082
spring:
  profiles: test
---
person:
#  字面量
  lastName: yazu
  age: ${random.int}
  isPropose: false
#  从上往下，依次 list map 对象
  pets: { cat,dog,mouse}
  PersonMessage: { name: yazu,son: tom}
#    Son
#    private String s_name;
#    private Integer age;
  son:
    s_name: ${person.lastName}_dog
    age: ${person.GroundSon:46}
spring:
  profiles:
    active:
```



### 3.指定激活Profile

* 在主配置文件写入spring.profiles.active=dev 

* 命令行激活

  * 配置文件：--spring-profiles.active={profiles}

    ![image-20201019182842800](D:\LearnNotes\Pic\image-20201019182842800.png)

  * 命令行 :

    java -jar spring-boot-initializer-0.0.1-SNAPSHOT.jar --spring.profiles.actives=dev

    可以直接在测试的时候，配置传入命令行参数

  * jvm参数： –Dspring.profiles.active=dev

## 6.配置文件的加载

![image-20201019191048051](D:\LearnNotes\Pic\image-20201019191048051.png)

项目路径大于类路径，config路径大于根路径

SpringBoot会从这四个位置全部加载主配置文件，低级的主配置文件配置了端口和虚拟路径，但是高级的配置文件配置了端口，不会覆盖掉虚拟路径，这就是**互补配置**

我们还可以通过Spring-config.location来改变默认的配置文件位置，当项目打包好了，我们可以使用命令行参数的形式，启动项目的时候来指定配置文件的新位置，它们一起作用	





## 7.外部配置加载顺序

**SpringBoot也可以从以下位置加载配置； 优先级从高到低；高优先级的配置覆盖低优先级的配置，所有的配置会形成互补配置**

**1.命令行参数**

所有的配置都可以在命令行上进行指定

java -jar spring-boot-02-config-02-0.0.1-SNAPSHOT.jar --server.port=8087  --server.context-path=/abc

多个配置用空格分开； --配置项=值

2.来自java:comp/env的JNDI属性

3.Java系统属性（System.getProperties()）

4.操作系统环境变量

5.RandomValuePropertySource配置的random.*属性值

**由jar包外向jar包内进行寻找；**

**优先加载带profile**

**6.jar包外部的application-{profile}.properties或application.yml(带spring.profile)配置文件**

**7.jar包内部的application-{profile}.properties或application.yml(带spring.profile)配置文件**

![image-20201019222736930](D:\LearnNotes\Pic\image-20201019222736930.png)

**再来加载不带profile**

**8.jar包外部的application.properties或application.yml(不带spring.profile)配置文件**

**9.jar包内部的application.properties或application.yml(不带spring.profile)配置文件**

10.@Configuration注解类上的@PropertySource

11.通过SpringApplication.setDefaultProperties指定的默认属性



## 8.自动配置原理

**参照：https://docs.spring.io/spring-boot/docs/1.5.9.RELEASE/reference/htmlsingle/#common-application-properties**

### 8.2自动配置报告

我们可以通过在主配置文件中添加属性debug=true，来获得自动配置报告

![image-20201020202025031](D:\LearnNotes\Pic\image-20201020202025031.png)



# 日志

## 日志框架

