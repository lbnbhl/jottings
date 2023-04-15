- 看看这个项目主要是做什么的，即需求是什么
- 项目中用到了那些技术，依赖什么的（开发环境）
- 项目的架构
- 走一遍项目的流程
- 项目有用到的设计模式
- 项目的安全性问题，易拓展性。
- 项目的难点

## 谷粒商城

### 项目介绍

------

谷粒商城是一套电商项目，包括前台商城系统和后套管理系统，基于SpringCloud、SpringCloud Alibaba、MyBatis Plus实现。前台商城系统包括：用户登录、注册、商品搜索、商品详情、购物车、订单、秒杀活动等模块。后台管理系统包括：系统管理、商品系统、优惠营销、库存系统、订单系统、用户系统、内容管理等七大模块。

### 技术选型

#### 后端技术

| 技术               | 说明                     | 官网                                            |
| ------------------ | ------------------------ | ----------------------------------------------- |
| SpringBoot         | 容器+MVC框架             | https://spring.io/projects/spring-boot          |
| SpringCloud        | 微服务架构               | https://spring.io/projects/spring-cloud         |
| SpringCloudAlibaba | 一系列组件               | https://spring.io/projects/spring-cloud-alibaba |
| MyBatis-Plus       | ORM框架                  | https://mp.baomidou.com                         |
| renren-generator   | 人人开源项目的代码生成器 | https://gitee.com/renrenio/renren-generator     |
| Elasticsearch      | 搜索引擎                 | https://github.com/elastic/elasticsearch        |
| RabbitMQ           | 消息队列                 | https://www.rabbitmq.com                        |
| Springsession      | 分布式缓存               | https://projects.spring.io/spring-session       |
| Redisson           | 分布式锁                 | https://github.com/redisson/redisson            |
| Docker             | 应用容器引擎             | https://www.docker.com                          |
| OSS                | 对象云存储               | https://github.com/aliyun/aliyun-oss-java-sdk   |

### 

#### 前端技术

| 技术      | 说明       | 官网                      |
| --------- | ---------- | ------------------------- |
| Vue       | 前端框架   | https://vuejs.org         |
| Element   | 前端UI框架 | https://element.eleme.io  |
| thymeleaf | 模板引擎   | https://www.thymeleaf.org |
| node.js   | 服务端的js | https://nodejs.org/en     |



### 架构方式

#### 系统架构

![image-20230126213343973](C:\Users\Dear~柘木塘\AppData\Roaming\Typora\typora-user-images\image-20230126213343973.png)

#### 业务架构

![image-20230126213516048](C:\Users\Dear~柘木塘\AppData\Roaming\Typora\typora-user-images\image-20230126213516048.png)





### 相关技术分析



#### Nacos

> Nacos 是一个更易于帮助构建云原生应用的动态服务发现、配置和服务管理平台

[nacos详细说明]: http://c.biancheng.net/springcloud/nacos.html



#### OpenFeign

> OpenFeign 全称 Spring Cloud OpenFeign，它是 Spring 官方推出的一种声明式服务调用与负载均衡组件，它的出现就是为了替代进入停更维护状态的 Feign。



[openFeign详细说明]: http://c.biancheng.net/springcloud/open-feign.html



#### Gateway

> Spring Cloud Gateway 是 Spring Cloud 的一个全新项目，该项目是基于 Spring 5.0，Spring Boot 2.0 和 Project Reactor 等响应式编程和事件流技术开发的网关，它旨在为微服务架构提供一种简单有效的统一的 API 路由管理方式。

- 为什么要使用gateway

  1. SpringCloudGateway是一个从逻辑上更贴近服务侧，一般作为API网关，与项目更是一个整体（迁移整个服务的时候更方便）。可以根据业务更可塑地进行网关逻辑的编写和调度。
  2. 同时在控制层上多加上统一的一层，有效地实现了统一鉴权，将鉴权这件事成功地从原来的代码逻辑中解耦出来。

- 在项目中使用gateway做了什么

  ~~~yaml
  #重写前端为了更好的命名发来的请求，将相关服务正确的路由
          - id: third_party_route
            uri: lb://gulimall-third-party
            predicates:
              - Path=/sms/**
              - Path=/api/thirdparty/**
            filters:
              - RewritePath=/api/thirdparty/(?<segment>.*),/$\{segment}
  #通过域名路由
          - id: gulimall_host_route
            uri: lb://gulimall-product
            predicates:
              - Host=gulimall.com,item.gulimall.com
  ~~~

  

#### 阿里云OSS

> 阿里云的对象储存

- 我们使用了阿里云oss来存储商品的图片信息

#### JSR303数字校验

> 对前端传来的参数做数据校验

- 为什么不使用Hibernate Validator做数据校验而使用JSR303呢

  ~~~text
  我选择使用JSR303是因为它是Java EE的一部分，使用起来更加标准和通用。
  JSR303是Java Bean Validation API的规范，而Hibernate Validator是其实现之一，它实现了JSR303的所有特性，同时提供了一些额外的功能。因此，实际上，使用JSR303和使用Hibernate Validator是等价的。
  ~~~

- 自定义校验注解

- 具体使用

  ~~~Java
  public class BrandEntity implements Serializable {
  	private static final long serialVersionUID = 1L;
  
  	/**
  	 * 品牌id
  	 */
  	@NotNull(message = "修改必须指定商品id",groups = {UpdateGroup.class})
  	@Null(message = "新增不能指定id",groups = {AddGroup.class})
  	@TableId
  	private Long brandId;
  	/**
  	 * 品牌名
  	 */
  
  	@NotBlank(message = "品牌名必须提交",groups = {AddGroup.class,UpdateGroup.class})
  	private String name;
  	/**
  	 * 品牌logo地址
  	 */
  	@NotBlank(groups = {AddGroup.class})
  	@URL(message = "logo必须是一个合法的url地址",groups={AddGroup.class,UpdateGroup.class})
  	private String logo;
  	/**
  	 * 介绍
  	 */
  	private String descript;
  	/**
  	 * 显示状态[0-不显示；1-显示]
  	 */
  	@NotNull(groups = {AddGroup.class, UpdateStatusGroup.class})
  	@ListValue(vals={0,1},groups = {AddGroup.class, UpdateStatusGroup.class})
  	private Integer showStatus;
  	/**
  	 * 检索首字母
  	 */
  	@NotEmpty(groups={AddGroup.class})
  	@Pattern(regexp="^[a-zA-Z]$",message = "检索首字母必须是一个字母",groups={AddGroup.class,UpdateGroup.class})
  	private String firstLetter;
  	/**
  	 * 排序
  	 */
  	@NotNull(groups={AddGroup.class})
  	@Min(value = 0,message = "排序必须大于等于0",groups={AddGroup.class,UpdateGroup.class})
  	private Integer sort;
  
  }

#### 全局异常处理

#### ElasticSearch

- 为什么使用ElasticSearch

- 具体使用
  1. 商品上架
  2. 商品搜索

#### Nginx

- 为什么在有了gateway的情况下还使用nginx

  1. nginx底层用的c++，性能更高。
  2. 静态资源的访问频率往往比动态请求更高，将静态资源放在 Nginx 上可以减轻应用服务器的负载，提高系统的性能和响应速度。

- 使用

  ~~~nginx
  server {
      listen       80;
      server_name  ie7vr6.natappfree.cc gulimall.com *.gulimall.com;
  
      #charset koi8-r;
      #access_log  /var/log/nginx/log/host.access.log  main;
      
      location /static/ {
          root  /usr/share/nginx/html;
      }
  
      location /payed/ {
          proxy_set_header Host order.gulimall.com;
          proxy_pass http://192.168.168.1:88;
      }
     
      location / {
          proxy_set_header Host $host;
          proxy_pass http://192.168.168.1:88;
      }
  
  
      error_page  404               /404.html;
  
  
      error_page   500 502 503 504  /50x.html;
      location = /index.html {
          root   /usr/share/nginx/html;
      }
  }
  
  ~~~

  

#### Redis

- 为什么选择使用redis
  1. 高性能：Redis是基于内存的，可以快速地读写数据，适合用来做缓存。而且Redis的读写操作都是原子性的，保证了并发访问的正确性。
  2. 数据结构多样性：Redis支持多种数据结构，包括字符串、哈希、列表、集合、有序集合等，这些数据结构可以满足不同的业务需求，而且操作简单方便。
  3. 持久化支持：Redis支持持久化，可以把内存中的数据定期地保存到磁盘上，避免数据丢失。
  4. 高可用性：Redis支持主从复制和哨兵机制，可以实现高可用性。
  5. 社区活跃：Redis拥有活跃的社区和强大的生态系统，有很多开源的工具和库可以方便地使用。
  
- 业务使用
  - 缓存
    1. 三级分类数据
    
    2. 以hash对象形式储存购物车数据，因为字段需要经常性的修改
    
    3. 登录用户信息session    loginUser 
    
       ~~~json
       {
         "@class": "com.lbnbhl.common.vo.MemberResponseVO",
         "id": 6,
         "levelId": null,
         "username": null,
         "password": null,
         "nickname": "lbnbhl",
         "mobile": null,
         "email": null,
         "header": null,
         "gender": null,
         "birth": null,
         "city": null,
         "job": null,
         "sign": null,
         "sourceType": null,
         "integration": null,
         "growth": null,
         "status": null,
         "createTime": null,
         "socialUid": null,
         "accessToken": "0f0ce98f85816b5b3753806e4dcf62d2",
         "expiresIn": 0
       }
       ~~~
    
       
    
    4. 发送短信的验证码，redis存的是 **set  sms:code:15270510720  code_存入时间**
    
    5. 防重令牌  **键：order:token+”userId“    值：uuid**

#### Redisson

- 业务使用

  ~~~java
      //秒杀商品上架功能的锁
      private final String upload_lock = "seckill:upload:lock";
  
      //TODO 保证幂等性问题
      // @Scheduled(cron = "*/5 * * * * ? ")
      @Scheduled(cron = "0 0 1/1 * * ? ")
      public void uploadSeckillSkuLatest3Days() {
          //1、重复上架无需处理
          log.info("上架秒杀的商品...");
  
          //分布式锁
          RLock lock = redissonClient.getLock(upload_lock);
          try {
              //加锁
              lock.lock(10, TimeUnit.SECONDS);
              seckillService.uploadSeckillSkuLatest3Days();
          } catch (Exception e) {
              e.printStackTrace();
          } finally {
              lock.unlock();
          }
      }
  
  
   //占位成功说明从来没有买过,分布式锁(获取信号量-1)
   RSemaphore semaphore = redissonClient.getSemaphore(SKU_STOCK_SEMAPHORE + randomCode);
  ~~~

  

#### SpringCache

- 分布式缓存

#### CompletableFuture

- 商品详情页

  ~~~java
  //1、sku基本信息的获取  pms_sku_info，sku图片信息的获取
          CompletableFuture<SkuInfoEntity> infoFuture = CompletableFuture.supplyAsync(() -> {
              //1、sku基本信息的获取  pms_sku_info
              SkuInfoEntity info = this.getById(skuId);
              skuItemVo.setInfo(info);
              return info;
          }, executor);
  
          CompletableFuture<Void> imageFuture = CompletableFuture.runAsync(() -> {
              List<SkuImagesEntity> imagesEntities = skuImagesService.getImagesBySkuId(skuId);
              skuItemVo.setImages(imagesEntities);
          }, executor);
  
  //2、通过sku基本信息的获取的值infoFuture，获取spu信息
          CompletableFuture<Void> saleAttrFuture = infoFuture.thenAcceptAsync((res) -> {
              //获取spu的销售属性组合
              List<SkuItemSaleAttrVo> saleAttrVos = skuSaleAttrValueService.getSaleAttrsBySpuId(res.getSpuId());
              skuItemVo.setSaleAttr(saleAttrVos);
          }, executor);
  
  
          CompletableFuture<Void> descFuture = infoFuture.thenAcceptAsync((res) -> {
              //获取spu的介绍    pms_spu_info_desc
              SpuInfoDescEntity spuInfoDescEntity = spuInfoDescService.getById(res.getSpuId());
              skuItemVo.setDesc(spuInfoDescEntity);
          }, executor);
  
  
          CompletableFuture<Void> baseAttrFuture = infoFuture.thenAcceptAsync((res) -> {
              //获取spu的规格参数信息
              List<SpuItemAttrGroupVo> attrGroupVos = attrGroupService.getAttrGroupWithAttrsBySpuId(res.getSpuId(), res.getCatalogId());
              skuItemVo.setGroupAttrs(attrGroupVos);
          }, executor);
  
  //3.等到所有任务都完成
          CompletableFuture.allOf(saleAttrFuture,descFuture,baseAttrFuture,imageFuture,seckillFuture).get();
  ~~~



#### SpringSession

分布式下session问题



#### RabbitMQ

- 业务使用

  ~~~java
  /**
   * Construct a new queue, given a name, durability flag, and auto-delete flag, and arguments.
   * Params:
   * name – the name of the queue - must not be null; set to "" to have the broker generate the name.
   * durable – true if we are declaring a durable queue (the queue will survive a server restart)
   * exclusive – true if we are declaring an exclusive queue (the queue will only be used by the 
   * declarer's connection)
   * autoDelete – true if the server should delete the queue when it is no longer in use
   * arguments – the arguments used to declare the queue
   */
  public Queue(String name, boolean durable, boolean exclusive, boolean autoDelete,
  			@Nullable Map<String, Object> arguments)
  
      /**
       * 死信队列
       * @return
       */
      @Bean
      public Queue orderDelayQueue() {
          /*
              Queue(String name,  队列名字
              boolean durable,  是否持久化
              boolean exclusive,  是否排他
              boolean autoDelete, 是否自动删除
              Map<String, Object> arguments) 属性
           */
          HashMap<String, Object> arguments = new HashMap<>();
          //设置队列的死信交换机名
          arguments.put("x-dead-letter-exchange", "order-event-exchange");
          //和死信交换机绑定的routing-key
          arguments.put("x-dead-letter-routing-key", "order.release.order");
          arguments.put("x-message-ttl", 60000); // 消息过期时间 1分钟
          //order.delay.queue(订单延时队列)是一条持久化，非独占式，非自动删除（临时）队列
          Queue queue = new Queue("order.delay.queue", true, false, false, arguments);
          return queue;
      }
  
  
      /**
       * 普通队列
       * @return
       */
      @Bean
      public Queue orderReleaseQueue() {
          //order.release.order.queue(订单释放队列)
          Queue queue = new Queue("order.release.order.queue", true, false, false);
          return queue;
      }
  
      /**
       * 普通队列
       * @return
       */
      @Bean
      public Queue orderSecKillOrrderQueue() {
          //商品秒杀队列
          Queue queue = new Queue("order.seckill.order.queue", true, false, false);
          return queue;
      }
  
  
  
      /**
       * TopicExchange
       * 广播型交换机
       * @return
       */
      @Bean
      public Exchange orderEventExchange() {
          /*
           *   String name,
           *   boolean durable,
           *   boolean autoDelete,
           *   Map<String, Object> arguments
           *   order-event-exchange(订单事件交换机)是一个可持久化的，非自动删除（临时）交换机
           * */
          return new TopicExchange("order-event-exchange", true, false);
  
      }
  
      @Bean
      public Binding orderCreateBinding() {
          /*
           * String destination, 目的地（队列名或者交换机名字）
           * DestinationType destinationType, 目的地类型（Queue、Exhcange）
           * String exchange,
           * String routingKey,
           * Map<String, Object> arguments
           * order.delay.queue(订单延时队列)通过值为order.create.order的routing-key和order-event-exchange交换机绑定
           * */
          return new Binding("order.delay.queue",
                  Binding.DestinationType.QUEUE,
                  "order-event-exchange",
                  "order.create.order",
                  null);
      }
  
      @Bean
      public Binding orderReleaseBinding() {
          //order.release.order.queue(订单释放队列)通过值为order.release.order的routing-key和order-event-exchange
          return new Binding("order.release.order.queue",
                  Binding.DestinationType.QUEUE,
                  "order-event-exchange",
                  "order.release.order",
                  null);
      }
  
      /**
       * 订单释放直接和库存释放进行绑定
       * @return
       */
      @Bean
      public Binding orderReleaseOtherBinding() {
  
          return new Binding("stock.release.stock.queue",
                  Binding.DestinationType.QUEUE,
                  "order-event-exchange",
                  "order.release.other.#",
                  null);
      }
  
      @Bean
      public Binding orderSecKillOrrderQueueBinding() {
          //String destination, DestinationType destinationType, String exchange, String routingKey,
          // 			Map<String, Object> arguments
          Binding binding = new Binding(
                  "order.seckill.order.queue",
                  Binding.DestinationType.QUEUE,
                  "order-event-exchange",
                  "order.seckill.order",
                  null);
  
          return binding;
      }
  ~~~

- 总结

  - 交换机

    1. stockEventExchange（库存事件交换机，广播型）
    2. orderEventExchange  (订单事件交换价机，广播型)

  - 队列

    1. stockReleaseStockQueue（库存释放队列）

    2. stockDelayQueue（库存延时队列，两分钟后通过order.release的routing-key传入stockEventExchange（库存事件交换机）

       ~~~java
       Map<String, Object> arguments = new HashMap<>();
       arguments.put("x-dead-letter-exchange", "stock-event-exchange");
       arguments.put("x-dead-letter-routing-key", "stock.release");
       arguments.put("x-message-ttl", 120000);
       return new Queue("stock.delay.queue", true, false, false, arguments);
       ~~~

    3. 订单延时队列**orderDelayQueue**，消息一分钟过期后将通过**order.release.order**发送到**orderEventExchange**

    4. orderReleaseQueue（订单释放队列）

    5. orderSecKillOrrderQueue（秒杀队列）

  - 路由关系

    1. stockReleaseStockBinding（库存释放绑定），将stockReleaseStockQueue（库存释放队列）通过 stock.release.# 和库存事件交换机绑定

       ~~~java
           @Bean
           public Binding stockReleaseStockBinding() {
               return new Binding("stock.release.stock.queue",
                       Binding.DestinationType.QUEUE,
                       "stock-event-exchange",
                       "stock.release.#",
                       new HashMap<>());
           }
       ~~~

    2. orderLockedBinding（订单锁定绑定），将stockDelayQueue（库存延时队列）通过 stock.locked 和 库存事件交换机绑定

       ~~~java
           @Bean
           public Binding orderLockedBinding() {
               return new Binding("stock.delay.queue",
                       Binding.DestinationType.QUEUE,
                       "stock-event-exchange",
                       "stock.locked",
                       new HashMap<>());
           }
       ~~~

    3. orderCreateBinding（订单创建绑定）

       ~~~java
           @Bean
           public Binding orderCreateBinding() {
               /*
                * String destination, 目的地（队列名或者交换机名字）
                * DestinationType destinationType, 目的地类型（Queue、Exhcange）
                * String exchange,
                * String routingKey,
                * Map<String, Object> arguments
                * */
               return new Binding("order.delay.queue",
                       Binding.DestinationType.QUEUE,
                       "order-event-exchange",
                       "order.create.order",
                       null);
           }
       ~~~

    4. orderReleaseBinding（订单释放绑定）

       ~~~java
           @Bean
           public Binding orderReleaseBinding() {
               return new Binding("order.release.order.queue",
                       Binding.DestinationType.QUEUE,
                       "order-event-exchange",
                       "order.release.order",
                       null);
           }
       ~~~

    5. orderReleaseOtherBinding（订单释放直接和库存释放进行绑定）

       ~~~java
           /**
            * @return
            */
           @Bean
           public Binding orderReleaseOtherBinding() {
       
               return new Binding("stock.release.stock.queue",
                       Binding.DestinationType.QUEUE,
                       "order-event-exchange",
                       "order.release.other.#",
                       null);
           }
       ~~~

    6. orderSecKillOrrderQueueBinding（订单秒杀绑定）

       ~~~java
           @Bean
           public Binding orderSecKillOrrderQueueBinding() {
               //String destination, DestinationType destinationType, String exchange, String routingKey,
               // 			Map<String, Object> arguments
               Binding binding = new Binding(
                       "order.seckill.order.queue",
                       Binding.DestinationType.QUEUE,
                       "order-event-exchange",
                       "order.seckill.order",
                       null);
       
               return binding;
           }
       ~~~

       

  - 监听器

    1. stock.release.stock.queue

       1. 库存自动解锁

          下订单成功，库存锁定成功，接下来的业务调用失败，导致订单回滚。之前锁定的库存就要自动解锁

       2. 订单失败

          库存锁定失败，释放库存

    2. order.release.order.queue

       ```text
       收到过期的订单信息，准备关闭订单
       ```

    3. 订单支付成功监听器

    4. order.seckill.order.queue

       ```
       准备创建秒杀单的详细信息
       ```

  - 事件

    1. 库存锁定

       > ```java
       > rabbitTemplate.convertAndSend("stock-event-exchange","stock.locked",lockedTo);
       > ```

       - 由于可能订单回滚的情况，所以为了能够得到库存锁定的信息，在锁定时需要记录库存工作单，其中包括订单信息和锁定库存时的信息(仓库id，商品id，锁了几件...)
       - 在锁定成功后，向延迟队列**stock-event-exchange**发消息，带上库存锁定的相关信息。
         1. 向stock-event-exchange发送msg
         2. 通过stock.locked路由到stock.delay.queue(库存延时队列)
            1. 过期：两分钟后通过stock.release传入stockEventExchange（库存事件交换机）然后再路由到stock.release.stock.queue 被监听器监听到，然后解锁库存
            2. 没过期，被

    2. 关闭订单

       > ~~~java
       > rabbitTemplate.convertAndSend("order-event-exchange", "order.release.other", orderTo);
       > ~~~

       

#### Seata

#### 定时任务

#### Sentinel

#### ThreadLocal

- 业务使用

  1. 购物车模块中使用，封装用户信息，用来做本地购物车

     ~~~Java
     //ThreadLocal同一个线程共享数据
         public static ThreadLocal<UserInfoTo> threadLocal = new ThreadLocal<>();
         
     public class UserInfoTo {
     
         private Long userId;
     
         private String userKey; //一定封装
     
         private boolean tempUser = false;  //判断是否有临时用户
     
     }
     
     
     ~~~

  2. 订单模块使用，封装用户详细信息，用来存用户的session详细信息

     ~~~java
     public static ThreadLocal<MemberResponseVO> loginUser = new ThreadLocal<>();
     
     public class MemberResponseVO implements Serializable {
     
         private static final long serialVersionUID = 9211654870040265916L;
     
         private Long id;
         /**
          * 会员等级id
          */
         private Long levelId;
         /**
          * 用户名
          */
         private String username;
         /**
          * 密码
          */
         private String password;
         /**
          * 昵称
          */
         private String nickname;
         /**
          * 手机号码
          */
         private String mobile;
         /**
          * 邮箱
          */
         private String email;
         /**
          * 头像
          */
         private String header;
         /**
          * 性别
          */
         private Integer gender;
         /**
          * 生日
          */
         private Date birth;
         /**
          * 所在城市
          */
         private String city;
         /**
          * 职业
          */
         private String job;
         /**
          * 个性签名
          */
         private String sign;
         /**
          * 用户来源
          */
         private Integer sourceType;
         /**
          * 积分
          */
         private Integer integration;
         /**
          * 成长值
          */
         private Integer growth;
         /**
          * 启用状态
          */
         private Integer status;
         /**
          * 注册时间
          */
         private Date createTime;
     
         private String socialUid;
     
         private String accessToken;
     
         private long expiresIn;
     }
     ~~~

     

### 模块分析



#### gulimall-common

> 各个其他模块的公共部分，单独抽取出来可以方便降低代码耦合度

##### 具体作用

- 对各个模块常量进行定义

  <img src="C:\Users\Dear~柘木塘\AppData\Roaming\Typora\typora-user-images\image-20230402172922504.png" alt="image-20230402172922504" style="zoom:100%;" />

  

- 自定义的异常

  <img src="C:\Users\Dear~柘木塘\AppData\Roaming\Typora\typora-user-images\image-20230402174256592.png" alt="image-20230402174256592" style="zoom:100%;" />

- 传输对象类型

  <img src="C:\Users\Dear~柘木塘\AppData\Roaming\Typora\typora-user-images\image-20230402174355646.png" alt="image-20230402174355646" style="zoom:100%;" />

- 各种工具类

  <img src="C:\Users\Dear~柘木塘\AppData\Roaming\Typora\typora-user-images\image-20230402174838363.png" alt="image-20230402174838363" style="zoom:100%;" />

- 参数的校验

  <img src="C:\Users\Dear~柘木塘\AppData\Roaming\Typora\typora-user-images\image-20230402174941885.png" alt="image-20230402174941885" style="zoom:100%;" />

- 提供安全方式

  ![image-20230402175110620](C:\Users\Dear~柘木塘\AppData\Roaming\Typora\typora-user-images\image-20230402175110620.png)





#### gulimall-gateway

> 对路由进行管理，是调用者可以正确的访问到服务提供者

##### 具体作用

- 对所有请求配置跨域

  ~~~java
  @Configuration
  public class GulimallCorsConfiguration {
  
      @Bean
      public CorsWebFilter corsWebFilter(){
  
  
          UrlBasedCorsConfigurationSource source=new UrlBasedCorsConfigurationSource();
          CorsConfiguration corsConfiguration=new CorsConfiguration();
  //        1.配置跨域
          corsConfiguration.setAllowCredentials(true);
          corsConfiguration.addAllowedHeader("*");
          corsConfiguration.addAllowedMethod("*");
          corsConfiguration.addAllowedOrigin("*");
          source.registerCorsConfiguration("/**",corsConfiguration);
          return new CorsWebFilter(source);
      }
  }
  ~~~

- 对请求进行断言，再经过过滤器处理，然后路由到具体服务

  ~~~java
  spring:
    cloud:
      gateway:
        routes:
          - id: third_party_route
            uri: lb://gulimall-third-party
            predicates:
              - Path=/sms/**
              - Path=/api/thirdparty/**
            filters:
              - RewritePath=/api/thirdparty/(?<segment>.*),/$\{segment}
  
          - id: member_route
            uri: lb://gulimall-member
            predicates:
              - Path=/api/member/**
            filters:
              - RewritePath=/api/(?<segment>.*),/$\{segment}
  
          - id: product_route
            uri: lb://gulimall-product
            predicates:
              - Path=/api/product/**
            filters:
              - RewritePath=/api/(?<segment>.*),/$\{segment}
  
          - id: ware_route
            uri: lb://gulimall-ware
            predicates:
              - Path=/api/ware/**
            filters:
              - RewritePath=/api/(?<segment>.*),/$\{segment}
  
          - id: admin_route
            uri: lb://renren-fast
            predicates:
              - Path=/api/**
            filters:
              - RewritePath=/api/(?<segment>.*),/renren-fast/$\{segment}
  
          - id: gulimall_host_route
            uri: lb://gulimall-product
            predicates:
              - Host=gulimall.com,item.gulimall.com
  
          - id: gulimall_search_route
            uri: lb://gulimall-search
            predicates:
              - Host=search.gulimall.com
  
  
          - id: gulimall_auth_route
            uri: lb://gulimall-auth-server
            predicates:
              - Host=auth.gulimall.com
  
  
          - id: gulimall_cart_route
            uri: lb://gulimall-cart
            predicates:
              - Host=cart.gulimall.com
  
          - id: gulimall_order_route
            uri: lb://gulimall-order
            predicates:
              - Host=order.gulimall.com
  
          - id: gulimall_member_route
            uri: lb://gulimall-member
            predicates:
              - Host=member.gulimall.com
  
          - id: coupon_route
            uri: lb://gulimall-coupon
            predicates:
              - Path=/api/coupon/**
            filters:
              - RewritePath=/api/(?<segment>.*),/$\{segment}
  
          - id: gulimall_seckill_route
            uri: lb://gulimall-seckill
            predicates:
              - Host=seckill.gulimall.com
  ~~~

- Sentinel全局处理



#### gulimall-product

> 商品模块

##### 具体作用

- 三级分类管理
- 品牌管理，品牌指一个手机分类的品牌可以有小米，华为，苹果等品牌
- 属性分组管理，比如一个手机可以有主体，屏幕，主芯片这些属性分组
- 商品属性管理，比如主芯片这个属性分组可以有cpu的品牌和cpu的型号这两个属性
- 商品管理
  - spu管理：SPU（Standard Product Unit）是标准产品单元，是一种商品的抽象概念，是指同一产品的不同规格、型号、颜色等的统称。比如，同一款手机，不同颜色的手机都属于同一个SPU。
  - sku管理：SKU（Stock Keeping Unit）是库存量单位，指同一SPU下不同颜色、尺寸、款式等具体的商品单元，是一种可以唯一标识一个商品的编码。比如，同一款手机，不同颜色、不同存储容量的手机，都可以用不同的SKU进行标识。

##### 相关流程

- 商品上架

  > 商品上架需要在es中保存spu信息并更新spu的状态信息

  ~~~java
  @Override
      public void up(Long spuId) {
  
          //组装需要的数据
          //1、查出当前spuid对应的所有sku信息
          List<SkuInfoEntity> skus = skuInfoService.getSkusBySpuId(spuId);
          List<Long> skuIds = skus.stream().map(SkuInfoEntity::getSkuId).collect(Collectors.toList());
  
          //调用远程仓储服务，提前查出来，避免循环查库
          Map<Long, Boolean> stockMap = null;
          // todo 1、发送远程调用，库存系统查询是否有库存
          try {
              //会有8次库存查询，所以希望远程服务有一个接口可以统一帮我们查到所有sku有没有库存
  //            R<List<SkuHasStockVo>> skuHasStock = wareFeignService.getSkusHasStock(skuIds);
              R r=wareFeignService.getSkusHasStock(skuIds);
              TypeReference<List<SkuHasStockVo>> typeReference=new TypeReference<List<SkuHasStockVo>>(){};
  
              stockMap = r.getData(typeReference).stream().collect(Collectors.toMap(SkuHasStockVo::getSkuId, SkuHasStockVo::getHasStock));
          } catch (Exception e) {
              log.error("调用库存服务查询异常，原因为{}" + e);
          }
  
          //封装attrs
          //todo 4、查询当前sku所有可以被检索的规格属性
  //        productAttrValueService.baseAttrListForSpu();
          List<ProductAttrValueEntity> attrsBySpuId = productAttrValueService.getAttrsBySpuId(spuId);
          List<Long> attrIds = attrsBySpuId.stream().map(ProductAttrValueEntity::getAttrId).collect(Collectors.toList());
  
          //这是可被检索属性的id集合
          List<Long> searchAttrIds = attrService.selectSearchAttrIds(attrIds);
          //为了筛选出可被检索的商品attrs
          Set<Long> setAttrIds = new HashSet<>(searchAttrIds);
          //拿到所有的可以被检索的商品属性关系表中数据，并提取出商品需要的attrs
          List<SkuEsModel.Attrs> attrsList = attrsBySpuId.stream()
                  .filter(item -> setAttrIds.contains(item.getAttrId()))
                  .map(item -> {
                      SkuEsModel.Attrs attrs = new SkuEsModel.Attrs();
                      BeanUtils.copyProperties(item, attrs);
                      return attrs;
                  }).collect(Collectors.toList());
  
          //封装每个sku信息
          Map<Long, Boolean> finalStockMap = stockMap;
          List<SkuEsModel> upProducts = skus.stream().map(sku -> {
              SkuEsModel skuEsModel = new SkuEsModel();
              BeanUtils.copyProperties(sku, skuEsModel);
  
              // skuPrice skuImg
              skuEsModel.setSkuPrice(sku.getPrice());
              skuEsModel.setSkuImg(sku.getSkuDefaultImg());
  
              // hasStock hotScore
              if (finalStockMap == null) {
                  skuEsModel.setHasStock(true);
              } else {
                  skuEsModel.setHasStock(finalStockMap.get(sku.getSkuId()));
              }
              // todo 2、热度评分。0
              skuEsModel.setHotScore(0L);
  
              // brandName brandImg
              //todo 3、查询品牌和分类的名字信息
              BrandEntity brand = brandService.getById(sku.getBrandId());
              skuEsModel.setBrandName(brand.getName());
              skuEsModel.setBrandImg(brand.getLogo());
              //  catalogName
              CategoryEntity category = categoryService.getById(sku.getCatalogId());
              skuEsModel.setCatalogName(category.getName());
  
              //   private Long attrId;
              //   private String attrName;
              //   private String attrValue;
              //设置检索属性
              skuEsModel.setAttrs(attrsList);
              return skuEsModel;
          }).collect(Collectors.toList());
          // 远程调用上架商品
          //todo 5、将数据发送给es保存
          R r = searchFeignService.productStatusUp(upProducts);
          if (r.getCode()==0){
              //远程调用成功
              //todo 更改spuinfo中商品的发布状态为已上架
              //状态应该作为枚举类存在的
              baseMapper.updateSpuStatus(spuId, ProductConstant.StatusEnum.SPU_UP.getCode());
          }else {
              //远程调用失败
              //todo 7、重复调用？接口幂等性；重试机制？xxx
          }
      }
  ~~~

  





#### gulimall-ware

> 仓库管理

##### 具体作用

- 维护仓库信息，比如仓库名、仓库地点、区域编码

- 维护sku商品库存信息 ，商品id、库存id、库存量

- 维护采购需求信息

  ![image-20230402203358429](C:\Users\Dear~柘木塘\AppData\Roaming\Typora\typora-user-images\image-20230402203358429.png)

- 维护采购单信息

  ![image-20230402203448602](C:\Users\Dear~柘木塘\AppData\Roaming\Typora\typora-user-images\image-20230402203448602.png)



##### 相关流程

- 采购简要流程

  ![image-20230402203645570](C:\Users\Dear~柘木塘\AppData\Roaming\Typora\typora-user-images\image-20230402203645570.png)



#### gulimall-search

> 提供搜索服务

##### 检索条件分析

- 全文检索：skuTitle-》keyword
- 排序：saleCount（销量）、hotScore（热度分）、skuPrice（价格）
- 过滤：hasStock、skuPrice区间、brandId、catalog3Id、attrs
- 聚合：attrs

##### 请求参数和返回结果

~~~java
 /**
 * 封装页面所有可能传递过来的查询条件
 * catalog3Id=225&keyword=小米&sort=saleCount_asc
 */
@Data
public class SearchParam {
    private String keyword;//页面传递过来的全文匹配关键字
 
    private Long catalog3Id;//三级分类id
 
    /**
     * sort=saleCount_asc/desc
     * sort=skuPrice_asc/desc
     * sort=hotScore_asc/desc
     */
    private String sort;//排序条件
 
    /**
     * 好多的过滤条件
     * hasStock(是否有货)、skuPrice区间、brandId、catalog3Id、attrs
     * hasStock=0/1
     * skuPrice=1_500
     */
    private Integer hasStock;//是否只显示有货
 
    private String skuPrice;//价格区间查询
 
    private List<Long> brandId;//按照品牌进行查询，可以多选
 
    private List<String> attrs;//按照属性进行筛选
 
    private Integer pageNum = 1;//页码
}

package com.atguigu.gulimall.search.vo;
 
import com.atguigu.common.to.es.SkuEsModel;
import lombok.Data;
 
import java.util.List;
 
/**
 * @Description: SearchResponse
 * 返回结果
 */
@Data
public class SearchResult {
 
    /**
     * 查询到的商品信息
     */
    private List<SkuEsModel> products;
 
    private Integer pageNum;//当前页码
 
    private Long total;//总记录数
 
    private Integer totalPages;//总页码
 
    private List<BrandVo> brands;//当前查询到的结果，所有涉及到的品牌
 
    private List<CatalogVo> catalogs;//当前查询到的结果，所有涉及到的分类
 
    private List<AttrVo> attrs;//当前查询到的结果，所有涉及到的属性
 
    //=====================以上是返给页面的信息==========================
 
    @Data
    public static class BrandVo{
        private Long brandId;
 
        private String brandName;
 
        private String brandImg;
    }
 
    @Data
    public static class CatalogVo{
        private Long catalogId;
 
        private String catalogName;
 
        private String brandImg;
    }
 
    @Data
    public static class AttrVo{
        private Long attrId;
 
        private String attrName;
 
        private List<String> attrValue;
    }
}
~~~

##### 搜索服务

~~~java
    /**
     * @param param 检索的所有参数
     * @return  返回检索的结果，里面包含页面需要的所有信息
     */
    SearchResult search(SearchParam param);


    //去es进行检索
    @Override
    public SearchResult search(SearchParam param) {
        //动态构建出查询需要的DSL语句
        SearchResult result = null;
        //1、准备检索请求
        SearchRequest searchRequest = buildSearchRequest(param);
        try {
            //2、执行检索请求
            SearchResponse response = restHighLevelClient.search(searchRequest, GulimallElasticSearchConfig.COMMON_OPTIONS);
            //分析响应数据封装我们需要的格式
            result = buildSearchResult(response,param);
        } catch (IOException e) {
            e.printStackTrace();
        }
        return result;
    }

    /**
     * 准备检索请求
     * 模糊匹配、过滤（按照属性、分类、品牌、价格区间、库存），排序，分页，高亮，聚合分析
     * @return
     */
    private SearchRequest buildSearchRequest(SearchParam param) {
        SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();//构建DSL语句的
        /**
         * 过滤（按照属性、分类、品牌、价格区间、库存）
         */
        //1、构建bool-query
        BoolQueryBuilder boolQuery = QueryBuilders.boolQuery();
        sourceBuilder.query(boolQuery);
        //1.1 must-模糊匹配、
        if (!StringUtils.isEmpty(param.getKeyword())){
            boolQuery.must(QueryBuilders.matchQuery("skuTitle",param.getKeyword()));
        }
        //1.2.1 filter-按照三级分类id查询
        if (null != param.getCatalog3Id()){
            boolQuery.filter(QueryBuilders.termQuery("catalogId",param.getCatalog3Id()));
        }
        //1.2.2 filter-按照品牌id查询
        if (null != param.getBrandId() && param.getBrandId().size()>0) {
            boolQuery.filter(QueryBuilders.termsQuery("brandId",param.getBrandId()));
        }
        //1.2.3 filter-按照是否有库存进行查询
        if (null != param.getHasStock() ) {
            boolQuery.filter(QueryBuilders.termQuery("hasStock", param.getHasStock() == 1));
        }
        //1.2.4 filter-按照区间进行查询  1_500/_500/500_
        RangeQueryBuilder rangeQueryBuilder = QueryBuilders.rangeQuery("skuPrice");
        if (!StringUtils.isEmpty(param.getSkuPrice())) {
            String[] prices = param.getSkuPrice().split("_");
            if (prices.length == 1) {
                if (param.getSkuPrice().startsWith("_")) {
                    rangeQueryBuilder.lte(Integer.parseInt(prices[0]));
                }else {
                    rangeQueryBuilder.gte(Integer.parseInt(prices[0]));
                }
            } else if (prices.length == 2) {
                //_6000会截取成["","6000"]
                if (!prices[0].isEmpty()) {
                    rangeQueryBuilder.gte(Integer.parseInt(prices[0]));
                }
                rangeQueryBuilder.lte(Integer.parseInt(prices[1]));
            }
            boolQuery.filter(rangeQueryBuilder);
        }
        //1.2.5 filter-按照属性进行查询
        List<String> attrs = param.getAttrs();
        if (null != attrs && attrs.size() > 0) {
            //attrs=1_5寸:8寸&2_16G:8G
            attrs.forEach(attr->{
                BoolQueryBuilder queryBuilder = new BoolQueryBuilder();
                String[] attrSplit = attr.split("_");
                queryBuilder.must(QueryBuilders.termQuery("attrs.attrId", attrSplit[0]));//检索的属性的id
                String[] attrValues = attrSplit[1].split(":");
                queryBuilder.must(QueryBuilders.termsQuery("attrs.attrValue", attrValues));//检索的属性的值
                //每一个必须都得生成一个nested查询
                NestedQueryBuilder nestedQueryBuilder = QueryBuilders.nestedQuery("attrs", queryBuilder, ScoreMode.None);
                boolQuery.filter(nestedQueryBuilder);
            });
        }
        //把以前所有的条件都拿来进行封装
        sourceBuilder.query(boolQuery);
        /**
         * 排序，分页，高亮，
         */
        //2.1 排序  eg:sort=saleCount_desc/asc
        if (!StringUtils.isEmpty(param.getSort())) {
            String[] sortSplit = param.getSort().split("_");
            sourceBuilder.sort(sortSplit[0], sortSplit[1].equalsIgnoreCase("asc") ? SortOrder.ASC : SortOrder.DESC);
        }

        //2.2、分页
        sourceBuilder.from((param.getPageNum() - 1) * EsConstant.PRODUCT_PAGESIZE);
        sourceBuilder.size(EsConstant.PRODUCT_PAGESIZE);

        //2.3 高亮highlight
        if (!StringUtils.isEmpty(param.getKeyword())) {
            HighlightBuilder highlightBuilder = new HighlightBuilder();
            highlightBuilder.field("skuTitle");
            highlightBuilder.preTags("<b style='color:red'>");
            highlightBuilder.postTags("</b>");
            sourceBuilder.highlighter(highlightBuilder);
        }

        /**
         * 聚合分析
         */
        //5. 聚合
        //5.1 按照品牌聚合
        TermsAggregationBuilder brand_agg = AggregationBuilders.terms("brand_agg").field("brandId").size(50);
        //品牌聚合的子聚合
        TermsAggregationBuilder brand_name_agg = AggregationBuilders.terms("brand_name_agg").field("brandName").size(1);
        TermsAggregationBuilder brand_img_agg = AggregationBuilders.terms("brand_img_agg").field("brandImg");
        brand_agg.subAggregation(brand_name_agg);
        brand_agg.subAggregation(brand_img_agg);
        sourceBuilder.aggregation(brand_agg);

        //5.2 按照catalog聚合
        TermsAggregationBuilder catalog_agg = AggregationBuilders.terms("catalog_agg").field("catalogId").size(20);
        TermsAggregationBuilder catalog_name_agg = AggregationBuilders.terms("catalog_name_agg").field("catalogName").size(1);
        catalog_agg.subAggregation(catalog_name_agg);
        sourceBuilder.aggregation(catalog_agg);

        //5.3 按照attrs聚合
        NestedAggregationBuilder nestedAggregationBuilder = new NestedAggregationBuilder("attr_agg", "attrs");
        //按照attrId聚合
        TermsAggregationBuilder attr_id_agg = AggregationBuilders.terms("attr_id_agg").field("attrs.attrId");
        //按照attrId聚合之后再按照attrName和attrValue聚合
        TermsAggregationBuilder attr_name_agg = AggregationBuilders.terms("attr_name_agg").field("attrs.attrName").size(1);
        TermsAggregationBuilder attr_value_agg = AggregationBuilders.terms("attr_value_agg").field("attrs.attrValue").size(50);
        attr_id_agg.subAggregation(attr_name_agg);
        attr_id_agg.subAggregation(attr_value_agg);

        nestedAggregationBuilder.subAggregation(attr_id_agg);
        sourceBuilder.aggregation(nestedAggregationBuilder);

        String s = sourceBuilder.toString();
        System.out.println("构建的DSL"+s);

        SearchRequest request = new SearchRequest(new String[]{EsConstant.PRODUCT_INDEX}, sourceBuilder);
        return request;

    }

    /**
     * 构建结果数据
     * @param response
     * @return
     */
    private SearchResult buildSearchResult(SearchResponse response,SearchParam param) {
        SearchResult result = new SearchResult();
        //1、返回的所有查询到的商品
        SearchHits hits = response.getHits();
        List<SkuEsModel> esModels = new ArrayList<>();
        if (null != hits.getHits() && hits.getHits().length>0){
            for (SearchHit hit : hits.getHits()) {
                String sourceAsString = hit.getSourceAsString();
                SkuEsModel esModel = JSON.parseObject(sourceAsString, SkuEsModel.class);
                if (!StringUtils.isEmpty(param.getKeyword())) {
                    HighlightField skuTitle = hit.getHighlightFields().get("skuTitle");
                    esModel.setSkuTitle(skuTitle.fragments()[0].string());
                }
                esModels.add(esModel);
            }
        }
        result.setProducts(esModels);
        //2、当前所有商品涉及到的所有属性
        List<SearchResult.AttrVo> attrVos = new ArrayList<>();
        ParsedNested attr_agg = response.getAggregations().get("attr_agg");
        ParsedLongTerms attr_id_agg = attr_agg.getAggregations().get("attr_id_agg");
        for (Terms.Bucket bucket : attr_id_agg.getBuckets()) {
            SearchResult.AttrVo attrVo = new SearchResult.AttrVo();
            //1、得到属性的id;
            long attrId = bucket.getKeyAsNumber().longValue();
            //2、得到属性的名字
            String attrName = ((ParsedStringTerms) bucket.getAggregations().get("attr_name_agg")).getBuckets().get(0).getKeyAsString();
            //3、得到属性的所有值
            List<String> attrValues = ((ParsedStringTerms) bucket.getAggregations().get("attr_value_agg")).getBuckets().stream().map(item -> {
                String keyAsString = item.getKeyAsString();
                return keyAsString;
            }).collect(Collectors.toList());
            attrVo.setAttrId(attrId);
            attrVo.setAttrName(attrName);
            attrVo.setAttrValue(attrValues);
            attrVos.add(attrVo);
        }

        result.setAttrs(attrVos);
        //3、当前所有品牌涉及到的所有属性
        List<SearchResult.BrandVo> brandVos = new ArrayList<>();
        ParsedLongTerms brand_agg = response.getAggregations().get("brand_agg");
        for (Terms.Bucket bucket : brand_agg.getBuckets()) {
            SearchResult.BrandVo brandVo = new SearchResult.BrandVo();
            //1、得到品牌的id
            long brandId = bucket.getKeyAsNumber().longValue();
            //2、得到品牌的名
            String brandName = ((ParsedStringTerms) bucket.getAggregations().get("brand_name_agg")).getBuckets().get(0).getKeyAsString();
            //3、得到品牌的图片
            String brandImg = ((ParsedStringTerms) bucket.getAggregations().get("brand_img_agg")).getBuckets().get(0).getKeyAsString();
            brandVo.setBrandId(brandId);
            brandVo.setBrandName(brandName);
            brandVo.setBrandImg(brandImg);
            brandVos.add(brandVo);
        }
        result.setBrands(brandVos);
        //4、当前商品所涉及的分类信息
        ParsedLongTerms catalog_agg = response.getAggregations().get("catalog_agg");

        List<SearchResult.CatalogVo> catalogVos = new ArrayList<>();
        List<? extends Terms.Bucket> buckets = catalog_agg.getBuckets();
        for (Terms.Bucket bucket : buckets) {
            SearchResult.CatalogVo catalogVo = new SearchResult.CatalogVo();
            //得到分类id
            String keyAsString = bucket.getKeyAsString();
            catalogVo.setCatalogId(Long.parseLong(keyAsString));

            //得到分类名
            ParsedStringTerms catalog_name_agg = bucket.getAggregations().get("catalog_name_agg");
            String catalog_name = catalog_name_agg.getBuckets().get(0).getKeyAsString();
            catalogVo.setCatalogName(catalog_name);
            catalogVos.add(catalogVo);
        }
        result.setCatalogs(catalogVos);
        //===========以上从聚合信息获取到=============
        //5、分页信息-页码
        result.setPageNum(param.getPageNum());
        //6、分页信息-总记录数
        long total = hits.getTotalHits().value;
        result.setTotal(total);
        //7、分页信息-总页码-计算
        int totalPages = total%EsConstant.PRODUCT_PAGESIZE == 0 ?(int) total/EsConstant.PRODUCT_PAGESIZE:((int)total/EsConstant.PRODUCT_PAGESIZE+1);
        result.setTotalPages(totalPages);


        List<Integer> pageNavs = new ArrayList<>();
        for (int i = 1; i <= totalPages; i++) {
            pageNavs.add(i);
        }
        result.setPageNavs(pageNavs);


        //6、构建面包屑导航功能
        List<String> attrs = param.getAttrs();
        if (attrs != null && attrs.size() > 0) {
            List<SearchResult.NavVo> navVos = attrs.stream().map(attr -> {
                String[] split = attr.split("_");
                SearchResult.NavVo navVo = new SearchResult.NavVo();
                //6.1 设置属性值
                navVo.setNavValue(split[1]);
                //6.2 查询并设置属性名
                try {
                    R r = productFeignService.attrInfo(Long.parseLong(split[0]));
                    result.getAttrIds().add(Long.valueOf(split[0]));
                    if (r.getCode() == 0) {
                        AttrResponseVo attrResponseVo = JSON.parseObject(JSON.toJSONString(r.get("attr")), new TypeReference<AttrResponseVo>() {
                        });
                        navVo.setNavName(attrResponseVo.getAttrName());
                    }else {
                        navVo.setNavName(split[0]);
                    }
                } catch (Exception e) {

                }
                //6.3 设置面包屑跳转链接(当点击该链接时剔除点击属性)
                //取消了这个面包屑以后，我们就要跳转到那个地方，将请求地址的url里面的当前置空
                //拿到所有的查询条件，去掉当前
                String replace = replaceQueryString(param, attr,"attrs");
                navVo.setLink("http://search.gulimall.com/list.html" + (replace.isEmpty()?"":"?"+replace));
                return navVo;
            }).collect(Collectors.toList());
            result.setNavs(navVos);
        }
        //品牌、分类
        if (null != param.getBrandId() && param.getBrandId().size() > 0){
            List<SearchResult.NavVo> navs = result.getNavs();
            SearchResult.NavVo navVo = new SearchResult.NavVo();
            navVo.setNavName("品牌");
            //TODO 远程查询所有品牌
            R r = productFeignService.brandInfo(param.getBrandId());
            if (0 == r.getCode()){
                List<BrandVo> brands = r.getData("brands", new TypeReference<List<BrandVo>>() {
                });
                StringBuffer buffer = new StringBuffer();
                String replace = "";
                for (BrandVo brand : brands) {
                    buffer.append(brand.getBrandName()+";");
                    replace = replaceQueryString(param, brand.getBrandId()+"","attrs");
                }
                navVo.setNavValue(buffer.toString());
                navVo.setLink("http://search.gulimall.com/list.html" + (replace.isEmpty()?"":"?"+replace));
            }
            navs.add(navVo);
        }
        //TODO 分类，不需要导航取消


        return result;
    }
~~~

- 商品上架

  ~~~Java
  /**
   * @autor wwl
   * @date 2022/10/30-17:24
   */
  @Slf4j
  @Service
  public class ProductSaveServiceImpl implements ProductSaveService {
  
      @Autowired
      RestHighLevelClient restHighLevelClient;
      @Override
      public boolean productStatusUp(List<SkuEsModel> skuEsModels) throws IOException {
          //保存到es
          //1、给es中建立索引。product,建立好映射关系
          //2、给es中保存这些数据
          //BulkRequest bulkRequest, RequestOptions options
          BulkRequest bulkRequest = new BulkRequest();
          for (SkuEsModel model : skuEsModels){
              //1、构造保存请求
              IndexRequest indexRequest = new IndexRequest(EsConstant.PRODUCT_INDEX);
              indexRequest.id(model.getSkuId().toString());
              String jsonString = JSON.toJSONString(model);
              indexRequest.source(jsonString, XContentType.JSON);
              bulkRequest.add(indexRequest);
          }
          BulkResponse bulk = restHighLevelClient.bulk(bulkRequest, GulimallElasticSearchConfig.COMMON_OPTIONS);
          //TODO 如果批量错误
          boolean b = bulk.hasFailures();
          List<String> collect = Arrays.stream(bulk.getItems()).map(BulkItemResponse::getId).collect(Collectors.toList());
          log.info("商品上架完成：{},返回数据：{}",collect,bulk.toString());
          return b;
      }
  }
  ~~~

  

#### gulimall-auth-server

##### 主要功能

- 注册

  > 整合了腾讯云的短信服务

  redis上   set   sms:code:15270510720    code_now();

  所以可以通过时间有没有超过60s来做接口防刷

- 登录

  - 用户名密码登录

    直接去数据库查询

    ~~~java
    MemberEntity memberEntity = memberDao.selectOne(new QueryWrapper<MemberEntity>().eq("username", loginacct).or().eq("mobile", loginacct));
    ~~~

    其中密码进行了MD5研制加密，即使攻击者获得了加密后的密码和盐，也很难通过反向推导得到原始密码，提高了密码的安全性。

    ~~~java
                //1、获取到数据库的password
                String passwordDB = memberEntity.getPassword();
                BCryptPasswordEncoder passwordEncoder = new BCryptPasswordEncoder();
                //2、密码匹配
                boolean matches = passwordEncoder.matches(password, passwordDB);
    ~~~

  - 社交登录

    ![image-20230403211853609](C:\Users\Dear~柘木塘\AppData\Roaming\Typora\typora-user-images\image-20230403211853609.png)

    这里我们使用的是gitee的接口

    ~~~text
    如果之前未使用该社交账号登录，则使用token调用开放api获取社交账号相关信息，注册并将结果返回
    如果之前已经使用该社交账号登录，则更新token并将结果返回
    登录，设置redis  loginUser 用户信息  
    ~~~
    
    



#### gulimall-cart

> 用户购物车服务

- 数据储存

  购物车是一个读多写多的场景，因此放入数据库并不合适，但购物车又是需要持久化，因此这里我们选用redis存储购物车数据。

  一个购物车是由各个购物项组成的，但是我们用`List`进行存储并不合适，因为使用`List`查找某个购物项时需要挨个遍历每个购物项，会造成大量时间损耗，为保证查找速度，我们使用`hash`进行存储

- ThreadLocal用户身份鉴别

  ~~~java
  //ThreadLocal同一个线程共享数据
  public static ThreadLocal<UserInfoTo> threadLocal = new ThreadLocal<>();
  
  @ToString
  @Data
  public class UserInfoTo {
  
      private Long userId;
  
      private String userKey; //一定封装
  
      private boolean tempUser = false;  //判断是否有临时用户
  
  }
  
  /**在执行目标方法之前，判断用户的登录状态。并封装传递给目标请求
   * @autor wwl
   * @date 2022/11/22-22:15
   */
  public class CartInterceptor implements HandlerInterceptor {
      //ThreadLocal同一个线程共享数据
      public static ThreadLocal<UserInfoTo> threadLocal = new ThreadLocal<>();
  
      /**
       * 在目标方法执行之前拦截
       * @param request
       * @param response
       * @return
       * @throws Exception
       */
      @Override
      public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
          UserInfoTo userInfoTo = new UserInfoTo();
          HttpSession session = request.getSession();
          MemberResponseVO member = (MemberResponseVO) session.getAttribute(AuthServerConstant.LOGIN_USER);
          if (member != null){
              //用户登录
              userInfoTo.setUserId(member.getId());
          }
          Cookie[] cookies = request.getCookies();
          if (cookies!=null && cookies.length >0){
              for (Cookie cookie : cookies) {
                  String name = cookie.getName();
                  //有user-key，把这个值注入
                  if (name.equals(CartConstant.TEMP_USER_COOKIE_NAME)){
                      userInfoTo.setUserKey(cookie.getValue());
                      userInfoTo.setTempUser(true);
                  }
              }
          }
  
          //如果没有临时用户，一定保存一个临时用户
          if (StringUtils.isEmpty(userInfoTo.getUserKey())){
              String uuid = UUID.randomUUID().toString();
              userInfoTo.setUserKey(uuid);
          }
          //目标方法执行之前，一定有个用户
          threadLocal.set(userInfoTo);
          return true;
      }
  
      /**
       * 业务执行之后 分配临时用户，让浏览器保存
       * @param request
       * @param response
       * @param handler
       * @param modelAndView
       * @throws Exception
       */
      @Override
      public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
          UserInfoTo userInfoTo = threadLocal.get();
          //如果没有临时用户，第一次访问购物车就添加临时用户
          if (!userInfoTo.isTempUser()){
              //持续的延长用户的过期时间
              Cookie cookie = new Cookie(CartConstant.TEMP_USER_COOKIE_NAME, userInfoTo.getUserKey());
              cookie.setDomain("gulimall.com");
              cookie.setMaxAge(CartConstant.TEMP_USER_COOKIE_TIMEOUT);
              response.addCookie(cookie);
          }
  
      }
  }
  ~~~

- **业务逻辑**

  - 有本地用户并且也已经登陆，合并购物车，没登陆获取所有购物项放进购物车返回
  - 若当前商品已经存在购物车，只需增添数量
  - 否则需要查询商品购物项所需信息，并添加新商品至购物车





#### gulimall-order

> 订单模块

- 订单流程

  订单生成 -> 支付订单 -> 卖家发货 -> 确认收货 -> 交易成功

- 订单提交流程

  ![image-20230403232123000](C:\Users\Dear~柘木塘\AppData\Roaming\Typora\typora-user-images\image-20230403232123000.png)

- 分布式事务

  ![image-20230403233001923](C:\Users\Dear~柘木塘\AppData\Roaming\Typora\typora-user-images\image-20230403233001923.png)

  ![20201201091000559](D:\work\image\20201201091000559.png)

- 详细流程

  ![image-20230410214703237](C:\Users\Dear~柘木塘\AppData\Roaming\Typora\typora-user-images\image-20230410214703237.png)

  > ~~~markdown
  > 1. 点击cartList的去结算，调用 http://order.gulimall.com/toTrade接口
  > ~~~ java
  >     @GetMapping("/toTrade")
  >     public String toTrade(Model model) throws ExecutionException, InterruptedException {
  >         OrderConfirmVo confirmVo = orderService.confirmOrder();
  >         //展示订单确认的数据
  >         model.addAttribute("orderConfirmData",confirmVo);
  >         return "confirm";
  >     }
  >    1.1 先确认订单，调用confirmOrder()返回订单需要确认的数据------- public OrderConfirmVo confirmOrder();
  >        1.1.1 从threadlocal中获取用户信息
  >        1.1.2 获取之前的请求并将其设置进当前线程（feign远程调用请求头丢失后做了处理，给新请求老请求的cookie）
  >        1.1.3 异步编排获取数据如收货地址、购物项、库存等
  >        1.1.4 添加防虫令牌 order:token:userid   uuid
  >        1.1.5 返回订单需要确认的数据 cofirmVo
  >    1.2 像model写入订单确认的数据 model.addAttribute("orderConfirmData",confirmVo);
  >    1.3 返回订单确认页面
  >    ![image-20230411003555512](C:\Users\Dear~柘木塘\AppData\Roaming\Typora\typora-user-images\image-20230411003555512.png)
  >    
  > 2. 有问题返回，没问题的话就可以提交订单了，点击提交订单
  > @PostMapping(value = "/submitOrder")
  >     public String submitOrder(OrderSubmitVo vo, Model model, RedirectAttributes attributes)
  >     ~~~ java
  >      /**
  >      * 下单功能
  >      * @param vo
  >      * @return
  >      */
  >     @PostMapping(value = "/submitOrder")
  >     public String submitOrder(OrderSubmitVo vo, Model model, RedirectAttributes attributes) {
  > 
  >         try {
  >             SubmitOrderResponseVo responseVo = orderService.submitOrder(vo);
  >             //下单成功来到支付选择页
  >             //下单失败回到订单确认页重新确定订单信息
  >             if (responseVo.getCode() == 0) {
  >                 //成功
  >                 model.addAttribute("submitOrderResp",responseVo);
  >                 return "pay";
  >             } else {
  >                 String msg = "下单失败";
  >                 switch (responseVo.getCode()) {
  >                     case 1: msg += "令牌订单信息过期，请刷新再次提交"; break;
  >                     case 2: msg += "订单商品价格发生变化，请确认后再次提交"; break;
  >                     case 3: msg += "库存锁定失败，商品库存不足"; break;
  >                 }
  >                 attributes.addFlashAttribute("msg",msg);
  >                 return "redirect:http://order.gulimall.com/toTrade";
  >             }
  >         } catch (Exception e) {
  >             if (e instanceof NoStockException) {
  >                 String message = ((NoStockException)e).getMessage();
  >                 attributes.addFlashAttribute("msg",message);
  >             }
  >             return "redirect:http://order.gulimall.com/toTrade";
  >         }
  >     }
  > 
  >    2.1 public SubmitOrderResponseVo submitOrder(OrderSubmitVo submitVo);
  >      2.1.1 对比提交订单数据的防虫令牌是否和之前redis上确认订单的防虫令牌相等
  >      如果不相等：验证失败，返回
  >      如果相等：验证成功，然后执行如下，再返回结果
  >        2.1.1.1 创建订单（生成订单号，设置所有订单项，计算价格） 
  >        2.1.1.2 验证价格
  >        2.1.1.3 保存价格
  >        2.1.1.4 库存锁定,只要有异常回滚订单数据（seata的@GlobalTransactional和@Transactional）。
  >                订单号，订单项 信息（skuId,skuName,num）
  >          2.1.1.4.1 保存库存工作单详情信息
  >          2.1.1.4.2 按照下单的收货地址，找到一个就近仓库，锁定库存
  >          2.1.1.4.3 锁定库存，没有任何仓库有这个商品的库存，抛异常回滚
  >                            如果每一个商品都锁定成功,将当前商品锁定了几件的工作单记录发给MQ
  >                    rabbitTemplate.convertAndSend("stock-event-exchange","stock.locked",lockedTo       
  >    2.2 判断返回值，提交成功携带返回数据转发至支付页面，提交失败，则携带错误信息重定向至确认页
  > 3. 点击l支付
  > ~~~
  >
  
  

#### gulimall-seckill

> 秒杀服务

![image-20230403234146515](C:\Users\Dear~柘木塘\AppData\Roaming\Typora\typora-user-images\image-20230403234146515.png)



秒杀架构思路

    项目独立部署，独立秒杀模块gulimall-seckill
    使用定时任务每天三点上架最新秒杀商品，削减高峰期压力
    秒杀链接加密，为秒杀商品添加唯一商品随机码，在开始秒杀时才暴露接口
    库存预热，先从数据库中扣除一部分库存以redisson信号量的形式存储在redis中
    队列削峰，秒杀成功后立即返回，然后以发送消息的形式创建订单


![image-20230403234450971](C:\Users\Dear~柘木塘\AppData\Roaming\Typora\typora-user-images\image-20230403234450971.png)



![image-20230403234654430](C:\Users\Dear~柘木塘\AppData\Roaming\Typora\typora-user-images\image-20230403234654430.png)

![image-20230403234706308](C:\Users\Dear~柘木塘\AppData\Roaming\Typora\typora-user-images\image-20230403234706308.png)



![image-20230403234827328](C:\Users\Dear~柘木塘\AppData\Roaming\Typora\typora-user-images\image-20230403234827328.png) 







#### 相关问题

##### feign远程调用

##### feign异步调用

##### 防虫令牌

> Token令牌方案：在每次请求时，服务端生成一个Token令牌，将令牌返回给客户端，客户端在下次请求时将令牌作为参数发送到服务端，服务端在执行操作前检查令牌是否已经使用过，如果已经使用过则返回已有的结果，否则执行操作并将令牌标记为已使用。
