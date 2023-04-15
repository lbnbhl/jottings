## Spring学习

### SpringIOC

- 定义：控制反转，把创建对象过程交给 Spring 进行管理

- 目的：降低耦合度

  [为什么要用IOC:inversion of controll反转控制（把创建对象的权利交给框架） - 周文豪 - 博客园 (cnblogs.com)](https://www.cnblogs.com/zwh0910/p/14609021.html#autoid-0-1-0)

- 底层原理：xml解析、工厂模式、反射

  ![image-20221231212912194](C:\Users\Dear~柘木塘\AppData\Roaming\Typora\typora-user-images\image-20221231212912194.png)

  ![image-20221231213838860](C:\Users\Dear~柘木塘\AppData\Roaming\Typora\typora-user-images\image-20221231213838860.png)

- 普通Bean：bean 类型就是返回类型

- 工厂Bean：bean 类型可以和返回类型不一样

  ~~~ java
  /**
   * 工厂Bean，继承了FactoryBean，原类型MyBean，返回类型为User
   * @autor wwl
   * @date 2022/9/16-20:37
   */
  public class MyBean implements FactoryBean<User> {
      @Override
      public User getObject() throws Exception {
          User user = new User("mlnr",10,new Dept("谷歌"));
          return user;
      }
  
      @Override
      public Class<?> getObjectType() {
          return null;
      }
  }
  
  ~~~

  #### IOC 操作 Bean 管理（bean 生命周期）

  ~~~ java
  （1）通过构造器创建 bean 实例（无参数构造）
  （2）为 bean 的属性设置值和对其他 bean 引用（调用 set 方法）
  （3）把 bean 实例传递 bean 后置处理器的方法 postProcessBeforeInitialization 
  （4）调用 bean 的初始化的方法（需要进行配置初始化的方法）
  （5）把 bean 实例传递 bean 后置处理器的方法 postProcessAfterInitialization
  （6）bean 可以使用了（对象获取到了）
  （7）当容器关闭时候，调用 bean 的销毁的方法（需要进行配置销毁的方法）
  
  ~~~

  ![image-20230101162620574](C:\Users\Dear~柘木塘\AppData\Roaming\Typora\typora-user-images\image-20230101162620574.png)

  ![image-20230101162704783](C:\Users\Dear~柘木塘\AppData\Roaming\Typora\typora-user-images\image-20230101162704783.png)



### SpringAOP

- 定义：面向切面，不修改源代码进行功能增强

- 实现

  1. 使用JDK动态代理，需要创建接口实现类代理对象

     ~~~java
     /**
      * JDK动态代理
      * @autor wwl
      * @date 2022/9/17-10:15
      */
     public class JDKProxyFactory {
         public static Object getProxy(Object target){
             return Proxy.newProxyInstance(target.getClass().getClassLoader(),target.getClass().getInterfaces(),new DebugInvocationHander(target));
         }
     
     }
     
     class DebugInvocationHander implements InvocationHandler{
     
         public final Object target;
     
         DebugInvocationHander(Object target) {
             this.target = target;
         }
     
         @Override
         public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
             System.out.println("before method " + method.getName());
             Object result = method.invoke(target, args);
             //调用方法之后，我们同样可以添加自己的操作
             System.out.println("after method " + method.getName());
             return result;
         }
     }
     ~~~

     

  2. 使用 CGLIB 动态代理，创建子类的代理对象，增强类的方法

     ~~~java
     /**
      * Cglib动态代理
      * @autor wwl
      * @date 2022/9/17-10:41
      */
     public class CglibProxyFactory {
         public static Object getProxy(Class<?> target){
             // 创建动态代理增强类
             Enhancer enhancer = new Enhancer();
             // 设置类加载器
             enhancer.setClassLoader(target.getClassLoader());
             // 设置被代理类
             enhancer.setSuperclass(target);
             // 设置方法拦截器
             enhancer.setCallback(new DebugMethodInterceptor());
             // 创建代理类
             return enhancer.create();
         }
     }
     
     class DebugMethodInterceptor implements MethodInterceptor{
     
     
         /**
          * @param o           代理对象（增强的对象）
          * @param method      被拦截的方法（需要增强的方法）
          * @param objects        方法入参
          * @param methodProxy 用于调用原始方法
          */
         @Override
         public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
             //调用方法之前，我们可以添加自己的操作
             System.out.println("before method " + method.getName());
             Object object =methodProxy.invokeSuper(o,objects);
             //调用方法之后，我们同样可以添加自己的操作
             System.out.println("after method " + method.getName());
             return object;
         }
     }
     ~~~

- 使用：

  1. 连接点：类里面可被增强的方法

  2. 切入点：类里面真正被增强的方法

  3. 通知（增强）：实际被增强的逻辑部分

  4. 切面：把通知实际应用到切入点的动作

  5. 切入点表达式：

     ![image-20230101154743498](C:\Users\Dear~柘木塘\AppData\Roaming\Typora\typora-user-images\image-20230101154743498.png)

  6. 控制多个切面的执行顺序：@Order（1），值越小优先级越高。

![image-20230101160854122](C:\Users\Dear~柘木塘\AppData\Roaming\Typora\typora-user-images\image-20230101160854122.png)

### 设计模式分析

#### 模板方法模式

> 模板模式的核心设计思路是通过在，抽象类中定义抽象方法的执行顺序，并将抽象方法设定为只有子类实现，但不设计`独立访问`的方法。简单说也就是把你安排的明明白白的。

- getBean

#### 单例模式

- getBean

#### 工厂模式

- BeanFactory

### 常见问题

#### 1. Spring为什么要存在**BeanDefinition**类？

因为Spring的设计理念是**IOC**，将一个Bean分成定义、注册、获取等阶段可以更好的让我们**只通过注册传入BeanDefition（类信息）**就可以通过Spring来决定怎么创建（管理）我们的Bean，比如Bean的单例还是多例。









### 重要类分析

- 

