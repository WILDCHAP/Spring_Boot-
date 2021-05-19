# Spring Boot学习-基础

## 1、Spring Boot - 从官网入门

进入Spring官网：https://spring.io/

在Projects下拉栏中选择Spring Boot

<img src="C:\Users\99370\AppData\Roaming\Typora\typora-user-images\image-20210516180956528.png" alt="image-20210516180956528" style="zoom:67%;" />

在LEARN中，可以选择最新版本的Spring Boot查看其文件

- GA：General Availability,正式发布的版本，官方推荐使用该版本
- PRE：预览版
- SNAPSHOT：快照版，可以稳定使用，且该版本会一直进行小量的优化和改进

<img src="C:\Users\99370\AppData\Roaming\Typora\typora-user-images\image-20210516181509121.png" alt="image-20210516181509121" style="zoom:50%;" />

在一大堆文件导航中，有几个是比较重要的：

- Documentation Overview：开发文档概述，告诉你怎么看这个文件，里面有下载[PDF](https://docs.spring.io/spring-boot/docs/2.4.5/reference/pdf/spring-boot-reference.pdf)地址，还可以查看[历史版本信息](https://github.com/spring-projects/spring-boot/wiki#release-notes)

- **Getting Started**：快速的开发构建一个Spring Boot项目

  1）检查环境

  ​      首先，你需要用`java -version`查看java版本确保使用java8

  ​      其次，要确保你的Maven版本在3.3+

  2）构建环境，导入jar包

  ​      可以通过在IDEA中创建一个Maven项目，在pox.xml中添加以下spring boot父依赖，同时引入spring-boot-starter-web来快速构        建一个spring boot项目

  ​      这里之所以不需要指定版本号，就是因为spring-boot-starter-parent这个父依赖有个父工程-spring-boot-starter-dependence，里面已经指定好了依赖版本，同时spring-boot-starter-web中导入了web开发所需要的包。如果自己需要修改版本号，可以自己再在pom.xml中写<properties>修改。

  ```xml
  <!--引入spring boot父依赖-->
  <parent>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-parent</artifactId>
      <version>2.4.5</version>
  </parent>
  <!--引入spring-boot-starter-web-->
  <dependencies>
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-web</artifactId>
      </dependency>
  </dependencies>
  ```

  3）编写启动类和Controller

  ​      启动类：添加@SpringBootApplication注解，使用SpringBoot.run方法启动项目

  ```java
  /**
   * 主程序类
   * @SpringBootApplication：这是一个SpringBoot应用
   */
  @SpringBootApplication
  public class MainApplication {
      public static void main(String[] args) {
          //1.IOC容器
          ConfigurableApplicationContext run = SpringApplication.run(MainApplication.class, args);
          //2.查看IOC容器中的组件
          String[] beanDefinitionNames = run.getBeanDefinitionNames();
          for (String beanDefinitionName : beanDefinitionNames) {
              System.out.println(beanDefinitionName);
  
          }
      }
  }
  ```

  ​      Controller：

  ​						`@ResponseBody`：返回字符串，而不是作页面跳转

  ​						`@RestController` = `@ResponseBody`+`@Controller`

  ​						`@RequestMapping`：指定方法处理前端何种请求

  ```java
  @RestController
  public class HelloController {
      @RequestMapping("/hello")
      public String handle01(){
          return "Hello, spring Boot 2!";
      }
  }
  ```

  4）测试效果

  ​      运行启动类，url输入*http://localhost:8080/hello*

  <img src="C:\Users\99370\AppData\Roaming\Typora\typora-user-images\image-20210516184258169.png" alt="image-20210516184258169" style="zoom:50%;" />

  5）打jar包运行

  ​      在pom.xml中加入以下打jar包依赖，之后我们用IDEA打包发布，再用`java -jar 'jar包名'`命令即可发布运行

  ```xml
  <build>
      <plugins>
          <plugin>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-maven-plugin</artifactId>
          </plugin>
      </plugins>
  </build>
  ```

  6）简单尝试一下配置

  ​      可以在resources文件夹下新建一个application.properties

  ```properties
  server.port=8888
  ```

  ​      这样重启服务之后就会发现，端口号变成了8888

  <u>注：每次修改配置文件后必须重启服务</u>

- Using Spring Boot：里面详细描述了如何使用Spring Boot，总结Spring Boot就是：构建一切的工具

  - [spring-boot-starter-xxx](https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-starter)：各种pom导入依赖

- Spring Boot Actuator：监控

- Deploying Spring Boot Applications：部署到云端
- Build Tool Plugins：一些插件描述
- [Application Properties](https://docs.spring.io/spring-boot/docs/current/reference/html/appendix-application-properties.html#common-application-properties)：配置文件中可配置值含义及其默认值

![image-20210516182123225](C:\Users\99370\AppData\Roaming\Typora\typora-user-images\image-20210516182123225.png)

**小技巧：**

1. 每次修改配置文件后必须重启服务
2. IDEA下，crtl+H打开基础树，或者鼠标右键生成

## 2、Spring Boot - 配置

总所周知，在我们用SSM开发项目的时候，总需要编写一大堆的配置文件：Spring编写一个、mybaties编写一个、web.xml、bean...

Spring Boot最大的三个特点：

​	1、集成Tomcat

​	2、父项目做依赖管理，上一节已经提到了

​	3、自动配置

> **Spring Boot利用自动配置功能，结合其约定大于配置特性，大大简化了开发人员的工作量，各个配置类能根据条件去配置文件中读取相应的配置，自动的配置好组件的设置**

---

在正式介绍自动配置之前，先来对Spring Boot的一些基本注解进行复习

### 2.1、Spring Boot注解

#### 2.1.1、基本注解

`@RequestMapping`：映射处理相应前端URL请求

```java
@RequestMapping("/a")
public class TestController{
    @RequestMapping("/aa")
    public String Test(){
        return "hello";		//访问"/a/aa"跳转至hello页面
    }
}
```

`@ResponseBody`：返回的是json样式而不是跳转

`@RestController`：Spring4新增，代替@Controller+@ResponseBody

```java
@RestController
@RequestMapping("/a")
public class TestController{
    @RequestMapping("/aa")
    public String Test(){
        return "hello";		//访问"/a/aa"返回json格式hello
    }
}
```

`@Autowired`：用来自动装配bean，写在字段或方法上，默认情况下必须要求依赖对象存在，如果允许空值可设置required为false

`@Qualifier`：当有多个实现类时，指定实现类

```java
@RestController
@RequestMapping("/a")
public class TestController{
    @Autowired(required=false)	//允许为空值
    @Qualifier("service1")		//有多个指定名为service1的
    private TestService service;
	...
}
```

`@Resource`：和上者不同在于，它是byName方式注入，

`@RequestParam`：用于将请求参数映射到方法参数上，写在方法参数体上

`@NonNull`、`@Nullable`：表示对应值能否为空

`@Required`：必须在Spring容器中存在

`@Scope`：设置容器作用域，singleton（单例）、prototype（原型）、Web（request/session/application）

**声明组件的注解：**

`@Bean`：在配置类中修饰产生一个bean的方法，类似于原来XML中的<bean>，方法名就是组件ID，返回类型就是组件类型

```java
@Configuration
public class TestConfig{
    @Bean					//等同用xml方式创建了一个bean的实例
    public User getUser(){
        return new User("小明", 26);
    }
}
```

被`@Component`、`@Controller`、`@Service`、`@Repository`注解修饰的类，都会纳入Spring容器中管理

`@ComponentScan({"com.wildchap"})`：扫描指定包下的组件到Spring容器中

**AOP相关注解：（需要打开注解支持）**

`@Aspect`：标记是一个切面类

`@Pointcut`：定义切点，切点表达式(execution(权限访问符 返回值类型 方法所属的类名包路径.方法名(形参类型) 异常类型))

`@Before`、`@After`、`@AfterReturning`、@AfterThrowing、`@Around`

**小技巧：**

- 在Spring Boot项目中，启动类同级及以下级别的包会被自动扫描
  - 要是想扫描启动类之上的包，可以加上`@ComponentScan("包名")`
- 在非Spring Boot项目中，通过`@ComponentScan("包名")`或xml配置文件开启
- `@Component`、`@Controller`等注解就像举手，`@ComponentScan`去扫描某一个区域举手的人，扫描到之后就可以用于自动装配了
  - `@Bean`和`@Component`的共同点在于，他们都是举手的人，被扫描到都可以自动装配
  - 不同点在于，`@Bean`需要在配置类中使用，即类上需要加上@Configuration注解
  - 之所以要再弄出一个@Bean来，是因为当你想用第三方库的组件时，你没办法在它的类上添加@Component

#### 2.1.2、@Configuration

作用：声明这个类是一个<u>配置类</u>，类似原来的.xml文件，配置类也会作为一个组件被加载

- 它通常在里面用`@Bean`标注配合给容器注册组件

- 它里面有一个proxyBeanMethods属性，默认为True

  - Full（proxyBeanMethods=true）：保证@Bean方法被调用多少次返回的组件都是实例的
  - Lite（proxyBeanMethods=false）：每次调用方法都是新创建的
  - 简而言之，他们之间的区别在于如果设置为true的话，走的是代理，会先扫描容器中有没有

  下面以一个例子来说明两者区别：

  ```java
  /*配置类*/
  @Configuration	//默认为true
  public class TestConfig{
      @Bean
      public User getUser(){
          User user = new User("小明", 21);
          user.setPet(getPet());	//用户设置宠物属性
          return user;
      }
      @Bean
      public Pet getPet(){
          return new Pet("cat");
      }
  }
  /*===============================*/
  /*测试类*/
  @SpringBootApplication
  public MainApplication{
      public static void main(String[] args){
          //Spring IOC容器
          ConfigurableApplicationContext run = SpringApplication.run(MainApplication.class, args);
          User user = run.getBean("getUser", User.class);
          Pet pet = run.getBean("getPet", Pet.class);
          System.out.println(user.getPet()==pet);
      }
  }
  /*===============================*/
  输出：true
  ```

  proxyBeanMethod=false设置user.setPet(getPet());会报错

#### 2.1.3、@Import

前面我们说了，`@Bean`、`@Component`等注解就像是举手的人一样

在非Spring Boot开发中，我们可以通过`@ComponentScan`扫描指定区域举手的人（即将组件放入Spring容器）

在Spring Boot中，会自动扫描启动类同级及以下级别举手的人

但是当我们某些类并没有举手（或者说没有被`@Component`等注解修饰），怎么样才能创建这种类的组件并放入容器呢？

`@Import`的功能就是：给容器中自动创建出一个或多个类型的组件，默认组件名是全类名

下面举个例子：

```java
/*domain*/
public class User{
    ...
}

public class Pet{
    ...
}

/*Config*/
@Import({User.class, Pet.class})	//创建组件并加入容器
@Configuration
public class TestConfig{
    ...
}

/*测试类*/
@SpringBootApplication
public class MainApplication{
    public static void main(String[] args){
        ConfigurableApplicationContext run = SpringApplication.run(MainApplication.class, args);
        User user = run.getBean(User.class);
        Pet pet = run.getBean(Pet.class);
        System.out.println(user);
        System.out.println(pet);
    }
}

/*输出*/
com.wildchap.domain.User@5f212d84
com.wildchap.domain.Pet@127d7908
```

#### 2.1.4、@Conditional

功能：只有满足指定的条件，才会进行该组件的注入

我们可以在IDEA中搜索Conditional，然后crtl+H打开继承树

<img src="C:\Users\99370\AppData\Roaming\Typora\typora-user-images\image-20210517110207266.png" alt="image-20210517110207266" style="zoom:67%;" />

被`@Conditional`注解所修饰的组件需符合Condition条件，Condition是一个接口，需要实现接口里的matches方法，返回为true则可以注入bean

```java
@FunctionalInterface
public interface Condition {
    boolean matches(ConditionContext var1, AnnotatedTypeMetadata var2);
}
```

举个例子说明：

```java
/*domain*/
public class User{
    ...
}
public class Pet{
    ...
}

/*config*/
@Configuration
public class TestConfig{
    @Autowired(required=false)
    private Pet pet;
    
    @ConditionalOnBean({Pet.class})	//只有Pet组件被加载了，User组件才会被加载
    @Bean("user")
    public User getUser(){
        return new User("小明", pet);
    }
}

/*测试类*/
@SpringBootApplication
public class MainApplication{
    public static void main(String[] args){
        ConfigurableApplicationContext run = SpringApplication.run(MainApplication.class, args);
        System.out.println(run.containsBean("user"));
    }
}

/*输出*/
如果没加@ConditionalOnBean，User仍然会被注册入容器，但Pet值为空
如果加了@ConditionalOnBean，User由于没有Pet的组件，不会被注册入容器
tag：要想注入Pet，可以通过@Import(Pet.class)或直接在domain上加@Repository
```

#### 2.1.5、@ImportResource

前面我们说了，`@Configuration`注解的作用类似于xml配置文件，用于声明这是一个配置类，那么当公司有些人仍然是用xml配置文件的方式声明组件bean，此时我们想导入xml配置文件的时候怎么办呢

`@ImportResource`注解可以帮组我们导入配置文件

```java
@ImportResource("classpath:beans.xml")	//导入beans.xml文件中的配置
public class TestConfig{
    ...
}
```

---

#### 2.1.6、配置绑定

配置绑定就是如何读取properties文件中的内容，并把它封装到相应JavaBean中，以供随时使用

例如，MySQL的配置类需要读取配置文件中MySQL的相关配置

##### @ConfigurationProperties

`@ConfigurationProperties`用于为加入容器的组件，进行配置绑定

需要注意的是，只有加入容器的组件，才能正确读取到配置

例如，我们配置用户的姓名的年龄

```properties
user.names="小明"
user.age=21
```

然后在domain中声明User读入prefix为user的配置

```java
@Component	//只有容器中的组件才能自动配置
@ConfigrationProperties(prefix="user")
public class User{
    private String names;
    private int age;
    ...
}

/*controller*/
@RestControll
public class TestController{
    @Autowired
    private User user;		//容器中有，可以自动注入
    
    @RequestMapping("/a")
    public String getUser(){
        return user.toString();
    }
}

/*测试类*/
@SpringBootApplication
public class MainApplication{
    public static void main(String[] args){
        SpringApplication.run(MainApplication.class, args);
    }
}

/*输出*/
在浏览器中输入localhost:8080/a
输出"User(names=小明, age=21)"
```

注：要是properties中出现中文乱码问题，这是因为IDEA中properties是ascii编码，在setting里找到File Encoding修改即可

<img src="C:\Users\99370\AppData\Roaming\Typora\typora-user-images\image-20210517180955447.png" alt="image-20210517180955447" style="zoom:50%;" />

有个有趣的地方是，如果在配置文件中设置user.name是无效的，看到的是你windows的用户名，大家可以去看看源码找原因

##### @EnableConfigurationProperties

上面那种自动配置的方式是通过组件（举手的人）通过`@ConfigurationProperties`实现配置绑定的

即`@Component` + `@ConfigrationProperties`

要是我们domain这种类不想做为一个组件（或者说不想举手），还有一种方法实现

这种方法就是在<u>配置类</u>里`@EnableConfigrationProperties` + `@ConfigurationProperties`

> 也就是说，一个配置绑定要生效，需要满足两个条件之一
>
> 1. 类作为一个组件在容器中
>
> 2. 用@EnableConfigrationProperties开启该类（不要求为组件）的配置绑定功能【在配置类中写】
>
>    而@EnableConfigrationProperties会自动把这个类作为组件导入容器中

之所以这样，其实和为什么要引入`@Bean`是一样的道理。当我们需要引入第三方包的内容时，我们总不能在它代码上面加`@Component`吧，这时候我们要想配置第三方包的属性，那么就通过@EnableConfigurationProperties来配置！

同样，我们可以举个例子：（properties配置同上）

```java
/*domain*/
@ConfigurationProperties(prefix="user")
public class User{
    private String names;
    private int age;
    ...
}

/*config*/
@Configuration
@EnableConfigurationProperties({User.class})	//把User注入绑定配置后放入容器
public class TestConfig{
    ...
}

/*controller*/
@RestController
public class TestController{
    @Autowire
    private User user;
    
    @RequestMapping("/a")
    public String f(){
        return user.toString();
    }
}

/*测试类*/
@SpringBootApplication
public class MainApplication{
    public static void main(String[] args){
        SpringApplication.run(MainApplication.class, args);
    }
}

/*结果*/
User(names=小明, age=21)
```

### 2.2、Spring Boot自动配置原理

前面我们说了Spring Boot的一些注解，这些注解能够帮助我们将相应的配置注入到所对应的组件中，并把这些组件加入到容器里

* 在我们自己编写的代码中，我们可以通过`@Component`等实现组件加入容器中，然后结合`@ConfigurationProperties`进行属性注入

- 当我们需要使用第三方组件时，又不好去给他们源代码添加注解，我们可以在配置类中`@Bean`使其加入容器，或者使用`@Import`导入它，或者使用`@EnableConfigurationProperties`在导入容器前进行属性注入
- 只有在容器中的组件才能用`@Autowired`自动装配

本节，我们将在配置绑定的基础上进一步探讨Spring Boot的自动配置原理（为什么我们需要配置的东西那么少，甚至可以说'零配置'？这是因为Spring Boot的自动配置功能帮我们导入了大量配置）

---

首先我们从启动类入手，即`@SpringBootApplication`

<img src="C:\Users\99370\AppData\Roaming\Typora\typora-user-images\image-20210518101824827.png" alt="image-20210518101824827" style="zoom:67%;" />

`@SpringBootConfiguration`：同样是个配置类，只不过它是主配置类

`@ComponentScan`：扫描指定路径下的组件加入容器

`@EnableAutoConfiguration`：

```xml
@AutoConfigurationPackage
	- @Import({Registrar.class})
	  Registrar将启动类下的包名打包
@Import({AutoConfigurationImportSelector.class})
	- AutoConfigurationImportSelector，可以叫它自动配置类扫描器，将符合条件的自动配置类扫描进来
```

![image-20210518103646264](C:\Users\99370\AppData\Roaming\Typora\typora-user-images\image-20210518103646264.png)

也就是说，在Spring.factories里写死了在SpringBoot启动的时候，需要把哪些配置自动加载进来，需要我们配置的就很少了

整个顺序就是：自动配置类（XXAutoConfiguration）作为一个组件在加入Spring容器前，会先去XXProperties里面拿值，再去application.properties里面拿值，最后再放入Spring容器中，所以我们可以根据自己的需要去修改某些配置值

### 2.3、Spring Boot开发技巧

#### 2.3.1、配置

我们要开发一个Spring Boot，在配置阶段，遵循以下步骤，会让我们开发变得更加简便

1. 导入所需要的依赖，例如我们要开发一个web项目，就导入web相关start

   [spring-boot-starter-xxx](https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-starter)：各种pom导入依赖

   [Application Properties](https://docs.spring.io/spring-boot/docs/current/reference/html/appendix-application-properties.html#common-application-properties)：配置文件中可配置值含义及其默认值

2. 查看自动配置，看看自动配置是否需要修改

   可以通过配置debug=true开启自动配置报告来查看哪些配置生效了，Native-不生效；Positive-生效

   可以参照上面的配置链接自己修改配置值

#### 2.3.2、lombok

使用lombok需要导入依赖，同时记得在IDEA安装lombok插件

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@ToString
@EqualsAndHashCode		//重写Equals和HashCode方法
@Slf4j	//注入日志属性，之后就可以使用log.info("...")即可
```

#### 2.3.3、dev-tools

有时候我们在调试的时候，修改了一点点，还需要重新启动项目，这是非常麻烦的

在官网[Using Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-devtools)中，给我们提供了开发者工具，我们只需要导入以下依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <optional>true</optional>
</dependency>
```

之后我们再在项目中修改什么东西，只需要**ctrl+F9**，修改的就会生效，不需要再去重启了

或者我们也可以直接在IDEA中重新编译

<img src="C:\Users\99370\AppData\Roaming\Typora\typora-user-images\image-20210518121229720.png" alt="image-20210518121229720" style="zoom:67%;" />

# Spring Boot学习-开发

## 1、配置文件

首先值得一说的是，我们在官方的spring-boot-starter-parent这个父项目中，可以看到项目会自动读取的配置文件名字

```xml
<includes>
    <include>**/application*.yml</include>
    <include>**/application*.yaml</include>
    <include>**/application*.properties</include>
</includes>
```

在上面，我们都是拿properties文件做例子，其实Spring Boot还支持一种配置文件格式，即yaml

### 1.1、yaml

yaml配置文件格式其实就是以【key: val】键值对方式设定配置，然后用缩进来表示层级关系（注意key:后的空格）

- 对于String、Date、boolean、number等单个值

  ```yaml
  key: val
  ```

- 对象、Map、Set

  ```yaml
  key: {k1: v1, k2: v2, k3: v3}
  #或
  key:
  	k1: v1
  	k2: v2
  	k3: v3
  ```

- 数组、arraylist

  ```yaml
  key: [v1, v2, v3]
  #或
  key:
  	- v1
  	- v2
  	- v3
  ```

下面举个例子说明：

```java
/*domain*/
@Data
@ConfigurationProperties(prefix="user")
@Component
public class User{
    private String userName;
    private int age;
    private String[] interests;
    private List<String> hobby;
    private Map<String, String> score;
    private Pet pet;
}
@Data
public class Pet{
    private String name;
}

/*controller*/
@RestController
public class TestController{
    @Autowired
    private User user;
    
    @RequestMapping("/a")
    public class f(){
        return user.toString();
    }
}

/*测试类*/
//略
```

```yaml
user:
	userName: 小明
	age: 21
	#interests: [唱歌, 跳舞, rap]
	interests:
		- 唱歌
		- 跳舞
		- rap
	#hobby: [唱歌, 跳舞, rap]
	hobby:
		- 唱歌
		- 跳舞
		- rap
	score: {Chinese: 90, Math: 90}
	pet:
		name: cat
```

这里有个小坑就是，如果Map的key值要写中文，那么要用**"[中文]"**的形式来写，否则不会被读到

另外，如果val值用单引号引起来，会对里面的符号作为字符串输出，双引号会输出为特殊符号意义

也就是说，单引号会转义，双引号不会转义

## 2、使用Spring Boot进行web开发

在[Spring官网](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-developing-web-applications)中有描述Spring对web官方自动配置设置

在Spring Boot帮我们配置好一大堆东西后，大多数MVC场景我们都不需要配置太多，他帮我们做了以下自动配置设置

- Inclusion of `ContentNegotiatingViewResolver` and `BeanNameViewResolver` beans.

  内容协商视图解析器和BeanName视图解析器

- 静态资源配置，有了它我们把静态资源放到指定位置，Spring会去读
- 支持`HttpMessageConverters`
- 静态index.html支持，即欢迎页
- 自动使用`ConfigurableWebBindingInitializer`绑定器（即将请求中的参数封装成JavaBean）

### 2.1、静态资源访问

Spring官方给我们提供的静态资源目录有四个

```properties
/static
/public
/resources
/META-INF/resources
```

![image-20210518181508337](C:\Users\99370\AppData\Roaming\Typora\typora-user-images\image-20210518181508337.png)

只要我们把静态资源放到这些文件夹下，我们就能访问的到

我们可以放一些文件到这些文件夹下，然后输入url测试一下

> 请求进来，先去找相应的Controller处理，不能处理就会交给静态资源处理器处理，静态资源处理器处理不了就报404
>
> （可以再写一个和静态资源名同名的Controller试一下，访问的是Controller而非静态资源）

---

**静态资源访问前缀**

静态资源的访问默认是无前缀的，我们可以修改其前缀

```yaml
spring:
  mvc:
    static-path-pattern: "/resources/**"	#原来为/**
```

这样做的目的是在我们开发中，可能涉及到要写一些登录拦截器之类的东西，为了避免拦截动态请求的同时拦截静态资源请求，所以为静态资源请求加上前缀

---

**静态资源访问路径***

官方给我们定的静态资源路径我们不需要，需要自己定义的话，这个路径我们也可以修改，用`static-locations`

---

**webjars***

简而言之就是把jQuery、BootStrap等打成jar包形式，我们可以用maven导入，它会自动映射我们访问只需要<u>/webjars/资源名</u>即可

例如：[http://localhost:8080/webjars/**jquery/3.5.1/jquery.js**](http://localhost:8080/webjars/jquery/3.5.1/jquery.js)后面地址可以自己展开包路径看

官网地址：[https://www.webjars.org/]

---

#### 2.1.1、欢迎页

Spring Boot支持两种欢迎页的访问方式（static & template）

<img src="C:\Users\99370\AppData\Roaming\Typora\typora-user-images\image-20210519102451694.png" alt="image-20210519102451694" style="zoom:67%;" />

- 把欢迎页放静态资源路径下 index.html（静态资源路径设置见上上节）
- 自己编写一个controller去处理/index请求跳转至欢迎页

---

#### 2.1.2、小图标

把小图标命名为favicon.ico放到静态资源目录下即可

---

#### 2.1.3、静态资源访问原理

我们可以在包中找到自动配置包，org->web->servlet。

<img src="C:\Users\99370\AppData\Roaming\Typora\typora-user-images\image-20210519104502308.png" alt="image-20210519104502308" style="zoom:67%;" />

里面有个关于配置webMVC的自动配置类WebMvcAutoConfiguration

### 2.2、请求处理原理

我们通常写controller的时候，第一步都是先用`@RequestMapping`进行请求映射

在`@RequestMapping`中，有一个请求提交方式RequestMethod可以设置

<img src="C:\Users\99370\AppData\Roaming\Typora\typora-user-images\image-20210519111005220.png" alt="image-20210519111005220" style="zoom:67%;" />

```java
public enum RequestMethod {
    GET,
    HEAD,
    POST,
    PUT,
    PATCH,
    DELETE,
    OPTIONS,
    TRACE;
    private RequestMethod() {
    }
}
```

在这里，有种我们之后开发常用的请求处理风格--Rest风格

---

#### 2.2.1、Rest风格

Rest风格概括来说就是根据HTTP请求方式来确定对资源的操作

- 以前：/getUser 获取用户   /deleteUser 删除用户   /editUser 修改用户   /saveUser 保存用户
- 现在：/user  GET-获取用户；DELETE-保存用户；PUT-修改用户；POST-保存用户

由于我们HTTP里面from表单只能发出POST和GET请求，无法发出其他请求怎么办呢？

---

我们打开webMVC自动配置类WebMvcAutoConfiguration，找到里面有个有序隐藏方法过滤器--**OrderedHiddenHttpMethodFilter**

```java
@Bean
@ConditionalOnMissingBean({HiddenHttpMethodFilter.class})
@ConditionalOnProperty(
    prefix = "spring.mvc.hiddenmethod.filter",
    name = {"enabled"},
    matchIfMissing = false
)
public OrderedHiddenHttpMethodFilter hiddenHttpMethodFilter() {
    return new OrderedHiddenHttpMethodFilter();
}
```

顾名思义，这个过滤器就是用来过滤隐藏方法的，我们点进去看

```java
public class OrderedHiddenHttpMethodFilter extends HiddenHttpMethodFilter implements OrderedFilter{}
```

这个过滤器继承了一个HiddenHttpMethodFilter，即HTTP隐藏方法过滤器，我们来分析一下它的代码

![image-20210519112213335](C:\Users\99370\AppData\Roaming\Typora\typora-user-images\image-20210519112213335.png)

这样就一目了然了

> WebMvcAutoConfiguration也就是WebMvc自动配置类通过获取OrderedHiddenHttpMethodFilter，得到隐藏的提交方式
>
> 这个隐藏的提交方式是通过设置_method值完成设置的，request会被一个Wrapper重新包装method
>
> 另外还有一点需要特别注意的是，我们看到Bean生效需要两个条件：一个是HiddenHttpMethod还未加载(所以我们之后加载)，还有一个是配置spring.mvc.hiddenmethod.filter需要打开(默认是false)

我们来举个例子测试一下：

1. 首先在Http中编写几个提交表单

   ```html
   <form action="/user" method="get">
       <input value="get方式提交" type="submit"/>
   </form>
   <form action="/user" method="post">
       <input value="post方式提交" type="submit"/>
   </form>
   <form action="/user" method="post">
       <input name="_method" value="put" type="hidden"/>
       <input value="put方式提交" type="submit"/>
   </form>
   <form action="/user" method="post">
       <input name="_method" value="delete" type="hidden"/>
       <input value="delete方式提交" type="submit"/>
   </form>
   ```

2. 然后我们编写相应controller

   ```java
   @RestController
   public class TestController{
       @RequestMapping(value="/user", method=RequestMethod.GET)
       public String f1(){
           return "GET方式提交"
       }
       @RequestMapping(value="/user", method=RequestMethod.POST)
       public String f2(){
           return "POST方式提交"
       }
       @RequestMapping(value="/user", RequestMethod.PUT)
       public String f3(){
           return "PUT方式提交"
       }
       @RequestMapping(value="/user", RequestMethod.DELETE)
       public String f4(){
           return "DELETE方式提交"
       }
   }
   ```

3. 需要注意的是，我们要在配置文件中开启Http隐藏请求方法过滤器

   ```yaml
   spring:
     mvc:
       hiddenmethod:
         filter:
           enabled: true
   ```

   
