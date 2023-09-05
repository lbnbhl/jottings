## 已知问题

- bean注入后对象值为null

  在成员变量赋值时，调用该对象的方法，此时bean还未注入。我将变量放进方法中，问题解决

- 王数据库插入数据值为null时，字段为关键词或者有参构造器没创建

- ssh服务器拒绝了密码，更改了下密码，可能是密码格式不对

- xshell连接时密码是灰色的，配置文件里的no改成yes

- 虚拟机可以ping到baidu，物理机ping不到虚拟机

  检查vnnet8网卡的配置，并诊断看是否有问题

- Could not connect to Redis at 127.0.0.1:6379: Connection refused

  未执行redis-server redis.conf

- nested exception is io.lettuce.core.RedisConnectionException即访问不到redis

  在linux中执行： **/sbin/iptables -I INPUT -p tcp --dport 6379 -j ACCEPT**
  redis默认端口号6379是不允许进行远程连接的，所以在防火墙中设置6379开启远程服务；

- nginx启动出现：Job for nginx.service failed because the control process exited with error code. See "systemctl status nginx.service" and "journalctl -xe" for details

  一般是配置文件有问题

- 不断弹[NACOS HTTP-POST]

  项目中`pom.xml`引入了nacos依赖包却没有读取到对应的配置文件引起的，将配置文件名改成`bootstrap.扩展名`即可解决。

- nacos配置文件中不能写127.0.0.1，要写真是ip

- 初始化filter时tomcat启动不了

  原因是，过滤器中没有[重写](https://so.csdn.net/so/search?q=重写&spm=1001.2101.3001.7020)public void init（）和 public void destroy() 方法，jdk 1.9 之后，可以不写这两个方法，如果用jdk1.8必须要写。

- 项目启动不起来：Failed to configure a DataSource: 'url' attribute is not specified and no embedded datasource could be configured.Reason: Failed to determine a suitable driver class

  禁用掉springboot的数据源自动配置，在启动类注解上添加排除数据源自动配置的参数即可：@SpringBootApplication(exclude= {DataSourceAutoConfiguration.class})//排除数据源的自动配置

- druid的依赖

- 使用ossclient的时候，出现自动注入为null，首先其自动注入类型应该为OSS接口，其次要项目加载起来了才会注入，然后运行，其次测试环境的话要使用以下爱环境才行

  ~~~java
  import org.junit.Test;
  @SpringBootTest
  @RunWith(SpringRunner.class)
  @Test
  ~~~

- 如果要更改依赖中的源码

  ~~~
  只要三步：
  
  1.在项目的Maven Dependencies依赖里面，找到你需要修改的包，找到包里面需要修改的class文件。
  
  2.在你的src/main/java目录下创建一个和class文件一样的路径，然后创建一样的java文件名。
  
  3.把class的源码复制到java文件的源码，就可以直接修改，修改后直接保存，自动生效，也不需要替换回原来的文件，原来的位置。
  ~~~

- 报错java.lang.NoClassDefFoundError: com/alibaba/nacos/client/logging/NacosLogging

  ~~~java
  解决办法：升级nacos-config的版本
          <dependency>
              <groupId>com.alibaba.cloud</groupId>
              <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
              <version>2.2.5.RELEASE</version>
          </dependency>
  ~~~

  

- Failed to instantiate [com.zaxxer.hikari.HikariDataSource]: Factory method ‘dataSource‘ threw except

  ~~~java
  直接再启动来加上
  @SpringBootApplication(exclude= {DataSourceAutoConfiguration.class})
  
  HikariDataSource是spingboot的默认数据源。但是你使用的时候并没有对它进行配置，所以就报错喽
  ~~~

  

## 微服务雪崩问题

> **微服务项目中，由于一个 服务的不可用，导致服务调用者得不到返回结果，随着时间的过去，越来越多的资源得不到释放，最后导致所有服务不可用。**

#### 超时处理

#### 舱壁模式（线程隔离）

#### 熔断器模式

#### 限流





------

## 缓存雪崩

> **缓存雪崩是指在我们设置缓存时key采用了相同的过期时间，导致缓存在某一时刻同时失效，请求全部转发到DB，DB瞬时压力过重雪崩。**

#### 过期时间随机

#### 分布式部署，让热点数据均匀在数据库

#### 热点数据永不过期

#### 降级 熔断





------

## 缓存击穿

> **缓存击穿 指 并发查同一条数据。缓存击穿是指缓存中没有但数据库中有的数据（一般是缓存时间到期），这时由于并发用户特别多，同时读缓存没读到数据，又同时去数据库去取数据，引起数据库压力瞬间增大，造成过大压力**

#### 热点数据不过期

#### 加互斥锁





------

## 缓存穿透

> **关键在于在Redis查不到key值，这和缓存击穿有根本的区别，区别在于缓存穿透的情况是传进来的key在Redis中是不存在的。**

#### 将无效的key存入redis中 

#### 使用布隆过滤器 





------

## 分布式下session共享问题

> **传统session信息只在一个服务中有，其他服务没有。**



#### session复制

- 优点：tomcat原生支持，只需要修改配置文件即可
- 缺点：
  1. session同步需要数据传输，会占用大量带宽，降低服务器集群的业务处理能力
  2. 任意一台web-server保存的都是所有web-server的session总和，浪费了大量的空间，且受内存限制无法水平扩展更多的web-server



#### 客户端存储

- 优点：节省服务器资源
- 缺点：
  1. 每次http请求，携带用户在cookie中的完整信息，浪费网络带宽
  2. 用户信息存放在cookie中，cookie有长度4k限制，不能存放大量信息
  3. 用户信息存储在cookie中，存在泄漏、篡改、窃取等安全隐患



#### hash一致性

> nginx负载均衡的时候采用ip-hash策略，这样同一个客户端每次的请求都会被同一个服务器处理。

- 优点：
  1. 只需要修改nginx配置，不需要修改应用程序代码
  2. 可以支持web-server水平扩展
- 缺点：
  1. session还是存在web-server中的，所以web-server重启可能导致部分session丢失，影响业务，如部分用户需要重新登录
  2. 如果web-server水平扩展，rehash后session重新分布，也会有一部分用户路由不到正确session。



#### 统一存储

> **将用户的信息存储在第三方中间件上，做到统一存储，如redis中，所有的服务都到redis中获取用户信息，从而实现session共享。**

优点

- 没有安全隐患
- 可以水平扩展
- 服务器重启或扩容都不会造成session的丢失

缺点：

- 增加了一次网络调用，速度有所下降
- 需要修改应用程序代码，如将所有的getSession方法替换为Redis查数据的方式。但这个问题可以通过spring session完美解决





------

## Feign远程调用丢失请求头问题

> **feign远程调用时会丢失老请求的请求头**

#### 定制`RequestInterceptor`为请求加上`cookie`

> **在`feign`的调用过程中，会使用容器中的`RequestInterceptor`对`RequestTemplate`进行处理，因此我们可以通过向容器中导入定制的`RequestInterceptor`为请求加上`cookie`**

~~~java
//RequestContextHolder为SpingMVC中共享request数据的上下文，底层由ThreadLocal实现。经过RequestInterceptor处理后的请求如下，已经加上了请求头的Cookie信息
@Configuration
public class GuliFeignConfig {
 
    @Bean("requestInterceptor")
    public RequestInterceptor requestInterceptor(){
        return new RequestInterceptor(){
            @Override
            public void apply(RequestTemplate requestTemplate) {
                //1、RequestContextHolder拿到刚进来的请求
                ServletRequestAttributes attributes = (ServletRequestAttributes) RequestContextHolder.getRequestAttributes();
                HttpServletRequest request = attributes.getRequest();//老请求
                //同步请求头数据。Cookie
                String cookie = request.getHeader("Cookie");
                //给新请求同步了老请求的cookie
                requestTemplate.header("Cookie",cookie);
                System.out.println("feign远程之前先执行RequestInterceptor.apply()");
            }
        };
    }
}
~~~





------

## 接口幂等性

> **由于网络延迟或者无响应状态影响了正常业务流程，导致客户端会发起多次重复请求，要求一次请求和多次请求的处理数据的结果要一致**

#### token机制

#### 各种锁机制

#### 各种唯一性约束

#### 防重表

#### 全球请求唯一id





### 缓存问题

[缓存](https://xiaolincoding.com/redis/architecture/mysql_redis_consistency.html#%E5%85%88%E6%9B%B4%E6%96%B0%E6%95%B0%E6%8D%AE%E5%BA%93-%E8%BF%98%E6%98%AF%E5%85%88%E5%88%A0%E9%99%A4%E7%BC%93%E5%AD%98)

- 先更新数据库，再更新缓存

  存在脏读问题

- 先更新缓存，再更新数据库

  存在数据不一致的问题

- 旁路缓存策略：

  - 写策略：

    先写数据库，再删除缓存

  - 读策略：

    命中缓存，直接返回

    缓存没有命中，从数据库中读数据，再将数据写入缓存

  先删除缓存，再更新数据库

  问题：读写并发时，数据不一致

  先更新数据库，在删除缓存

  问题：因为缓存的写入往往快于数据库的写入，所以问题不大，而且设置了过期时间，最终都会一致



## Git常见问题

### github解决大文件的上传

> git不允许上传文件超过100mb
> 版权声明：本文为CSDN博主「Java川」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
> 原文链接：https://blog.csdn.net/weixin_43919632/article/details/103552220

**报错**

~~~git
Push failed
send-pack: unexpected disconnect while reading sideband packet
Connection to github.com closed by remote host.
sha1 file '<stdout>' write error: Broken pipe
the remote end hung up unexpectedly
~~~

**解决**

~~~git
#设置缓存 这是500mb
git config http.postBuffer 524288000
~~~

~~~git
# 1、安装git-lfs
git lfs install

# 2、跟踪一些文件，那么上传大小就会变少  比如可以跟踪py结尾的文件 git lfs track "*.py"
git lfs track "你要跟踪的大文件的拓展名"

# 3、push常规操作
git add  你要上传的大文件
git commit -m "add large file"
git push -u origin master
~~~

可能还是会错

![20191215231241615](\image\20191215231241615.png)
那么输入

~~~git
git config lfs.https://github.com/468336329Zc/face-recognition.git/info/lfs.                                                                                                                          locksverify false
#然后再次push
git push -u origin master
~~~





## 登录问题

#### OAuth 2.0的工作流程为什么不直接给客户端一个访问令牌，而是要先给客户端授权码再去请求获取访问令牌呢

![image-20230905194344749](C:\Users\Dear~柘木塘\AppData\Roaming\Typora\typora-user-images\image-20230905194344749.png)

~~~mar
直接返回访问令牌的时
比如通过浏览器，重定向到了授权页面，我授权后，授权服务器返回访问令牌到浏览器，浏览器再把数据给xx软件。此时我还在授权页面，需要再次重定向到xx软件。这个过程中访问令牌暴露在浏览器
如果使用授权码的话
这期间就授权码暴露在浏览器，我们利用授权码直接访问授权服务器，然后服务器返回我们访问令牌的话这样就避免访问
~~~

