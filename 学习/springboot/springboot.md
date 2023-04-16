# SpringBoot

Spring Boot 是 Spring 开源组织下的子项目，是 Spring 组件一站式解决方案，主要是简化了使用Spring 的难度，简省了繁重的配置，提供了各种启动器，开发者能快速上手。



Springboot的优点：

1. 容易上手，提升开发效率，为 Spring 开发提供一个更快、更广泛的入门体验。
2. 开箱即用，远离繁琐的配置。
3. 提供了一系列大型项目通用的非业务性功能，例如：内嵌服务器、安全管理、运行数据监控、运行状况检查和外部化配置等。
4. 没有代码生成，也不需要XML配置。
5. 避免大量的 Maven 导入和各种版本冲突。



parent

1. 开发SpringBoot程序要继承spring-boot-starter-parent
2. spring-boot-starter-parent中定义了若干个依赖管理
3. 继承parent模块可以避免多个依赖使用相同技术时出现依赖版本冲突
4. 继承parent的形式也可以采用引入依赖的形式实现效果



starter

SpringBoot中常见项目名称，定义了当前项目使用的所有依赖坐标，以达到减少依赖配置的目的



整合Junit

1. 导入测试对应的starter

2. 测试类使用@SpringBootTest修饰

   ~~~java
   //如果测试类在SpringBoot启动类的包或子包中，可以省略启动类的设置，也就是省略classes的设定
   @SpringBootTest(classes = Springboot05JUnitApplication.class)
   class Springboot07JUnitApplicationTests {}
   ~~~

   

3. 使用自动装配的形式添加要测试的对象







自动配置

1. springboot启动时先加载spring.factories文件中的org.springframework.boot.autoconfigure.EnableAutoConfiguration配置项，将其中配置的所有的类都加载成bean
2. 在加载bean的时候，bean对应的类定义上都设置有加载条件，因此有可能加载成功，也可能条件检测失败不加载bean
3. 对于可以正常加载成bean的类，通常会通过@EnableConfigurationProperties注解初始化对应的配置属性类并加载对应的配置
4. 配置属性类上通常会通过@ConfigurationProperties加载指定前缀的配置，当然这些配置通常都有默认值。如果没有默认值，就强制你必须配置后使用了





拦截器、过滤器和监听器

~~~text
当一个请求发送到Web应用程序时，请求会被容器的过滤器拦截，过滤器负责在请求到达目标资源之前或响应返回客户端之后，执行某些操作。过滤器通常用于执行一些基于请求和响应的预处理操作，比如日志记录、字符编码、权限验证等，可以对应用程序的每个请求执行相同的操作。在Spring Boot中，过滤器是基于Java Servlet规范的，可以通过实现javax.servlet.Filter接口或者继承javax.servlet.Filter类来自定义过滤器，然后将其注册到Spring Boot中。

与过滤器不同，拦截器是在控制器执行前或执行后进行拦截和处理的。在Spring Boot中，拦截器通常用于处理控制器请求，比如权限控制、参数验证、日志记录等。与过滤器相比，拦截器能够访问控制器方法的参数和返回值，可以根据需要对其进行修改。拦截器可以通过实现HandlerInterceptor接口或者继承HandlerInterceptorAdapter类来自定义，然后通过配置文件或者注解将其注册到Spring Boot中。

除了过滤器和拦截器，Spring Boot还支持监听器，监听器用于监听应用程序事件，比如应用程序启动和关闭、会话创建和销毁等。监听器通常用于初始化应用程序资源、记录日志、监控应用程序等。Spring Boot提供了许多内置的监听器，比如ContextRefreshedEvent监听器，在应用程序上下文初始化或刷新时触发。监听器可以通过实现对应的监听器接口或者继承对应的监听器类来自定义，然后通过配置文件或者注解将其注册到Spring Boot中。

总之，过滤器、拦截器和监听器都是处理请求和响应的机制，它们的作用和使用场景不同，需要根据具体情况选择合适的处理机制来实现预期的功能。
~~~







请求的执行流程

![l5q1ofq712](img\l5q1ofq712.png)

![8s5vicqkwv](img\8s5vicqkwv.png)



