# 项目实战笔记

## 一、内容管理系统

项目技术栈：SpringBoot、SpringMVC、JWT、Shiro、Mybatis、Swagger、Redis、Thymeleaf、ElasticSearch。 

* 运行项目：

  1. 开启 redis 后，在 IDEA 中运行 renren-fast 后台；

  2. 在前端项目目录下运行：

     ```shell
     npm run dev
     ```

### 1、用户管理和权限管理

#### 1.1 数据库设计

RBAC权限模型：基于角色的权限管理。

数据库的权限设计：对象表、操作表、权限表之间的关系：资源+操作=权限。角色表和权限表关联。用户表和角色表关联。

![image-20200527210949651](C:\Users\49769\AppData\Roaming\Typora\typora-user-images\image-20200527210949651.png)

共涉及五张表：

* 权限表：sys_menu

* 角色表：sys_role

* 用户表：sys_user

* 角色权限关联表：sys_role_menu

* 用户角色关联表：sys_user_role

#### 1.2 JWT

> 什么是 JWT？原理是什么？

JSON Web Token，简称JWT，本质是一个token，用于在网络通信的双方之间传递。一般放在HTTP的 headers 参数里面的 Authorization 里面，也可以在 url 和 request body 中传递。

**JWT原理**：用户向服务器发送请求，服务器认证后生成一个 JSON 对象(JWT)，发回给用户。以后用户与服务端通信的时候，都要带上 JWT。客户端收到服务器返回的 JWT 后，把它放在 HTTP 请求的头信息 Authorization 字段里面。服务器解密的时候没有使用数据库，仅仅使用的是 secret 进行解密，即靠这个对象认定用户身份。服务器就不保存任何 session 数据了，也就是说，服务器变成`无状态`了，从而比较容易实现扩展。当我们要把服务器做成无状态时（即服务器端不会保存session），这里我们就可以用到JWT。

#### 1.3 Shiro与OAuth2

> 什么是 Shiro？

使用 Shiro 框架进行过滤。Shiro 是一套权限管理框架，包括认证、授权等，用于**权限验证**。通过过滤器 Filter 对权限进行拦截。 

`ShiroConfig` 类中的 `shiroFilter` 方法很重要。采用了 OAuth2 验证，比如第三方网站可以使用微信、支付宝账户登录等等。

![image-20200527214450726](C:\Users\49769\AppData\Roaming\Typora\typora-user-images\image-20200527214450726.png)

权限拦截：可以使用配置文件或者注解的方式。其中权限注解要写在 **Controller 类**的 Web 方法上，当用户在数据库中权限内容和该注解内权限一致时即可访问，不一致则不能访问。

比如：

```java
@RequestMapping("/list")
@RequiresPermissions("generator:student:list")
public R list(@RequestParam Map<String, Object> params){
   PageUtils page = studentService.queryPage(params);
   return R.ok().put("page", page);
}
```

> 什么是 OAuth2？

OAuth 是一个开放授权标准，允许用户授权第三方移动应用访问他们存储在另外的服务提供者上的信息，而不需要将用户名和密码提供给第三方移动应用或分享他们数据的所有内容。利用 OAuth2 可实现单点登录（SSO）功能。

**OAuth2 原理**：

流程图如下：

![image-20200527214935649](C:\Users\49769\AppData\Roaming\Typora\typora-user-images\image-20200527214935649.png)

`AccessToken` 是用户通过第三方应用访问 OAuth 接口后获得的令牌。

![image-20200527215119424](C:\Users\49769\AppData\Roaming\Typora\typora-user-images\image-20200527215119424.png)

任意一次访问授权方的API都要拼接一次URL，都会用到AccessToken，AccessToken具有较长的生命周期。快过期时可以使用RefreshToken动态拼接URL进行请求来获取新的AccessToken，一般在有定时任务时可使用。

### 2、网站国际化

> 什么是国际化？

网站国际化是根据用户浏览器的语言区域，显示不同的内容。主要是静态资源国际化。

#### 2.1 单体架构网站

国际化在**后端**完成，要在 SpringMVC 上完成。步骤如下：

1. 通过配置文件，可使用Thymeleaf引擎。

   在application配置文件中添加：

   ```yaml
     thymeleaf:
       prefix: classpath:/templates/  #模板文件存储目录
       check-template-location: true
       cache: false
       suffix: .html
       encoding: UTF-8
       mode: HTML5
       servlet:
         content-type: text/html
     messages:
       basename: i18n.page   #在i18n文件夹中加入配置文件
   ```

2. i18n 中创建3个配置文件。

3. page1 的前端页面 thymeleaf 引用国际化内容：

   ```html
   <body>
       <h1>国际化测试</h1>
       <form action="" method="post">
           <div>
               <label for="username" th:text="#{page1.login.username}"></label>
               <input type="text" id="username" name="username" th:placeholder="#{page1.login.username.placeholder}"/>
           </div>
           <div>
               <label for="password" th:tezt="#{page1.login.password}">密码</label>
               <input type="password" id="password" name="password" th:placeholder="#{page1.login.password.placeholder}"/>
           </div>
           <div>
               <input type="submit" th:value="#{page1.login.submit}">
           </div>
       </form>
   </body>
   ```

   其中HTML页面不具备thymeleaf语法提示，加上xmlns属性即可：

   ```html
   <html lang="zh" xmlns:th="http://www.thymeleaf.org">
   ```

4. 测试时，`配置跳转路径 test 绕过 Shiro 的验证`。在 ShiroConfig 中添加：

   ```java
   filterMap.put("/test","anon");
   ```

#### 2.2 前后端分离架构网站[非重点]

国际化在**前端**完成，要在前端项目上完成。

1. VUE提供了国际化插件。

   安装vue-i18n：

   ```shell
   cnpm install vue-i18n --save
   ```

2. 关闭 eslint 验证，因为和 i18n 有冲突。

   修改 build/webpack.base.config 文件中为：

   ```xml
   ...(config.dev.useEslint ? [] : [])
   ```

3. 声明国际化模块。

   在 src/i18n/index.js 中创建 i18n 对象，添加相应的 json 文件。在需要国际化的页面修改：

   ```js
   {{$t("main-sidebar.home")}}
   ```

4. 在src/i18n 文件夹下编写 index.js 文件：

   ```js
   import Vue from 'vue'
   import VueI18n from 'vue-i18n'
   
   Vue.use(VueI18n)
   
   var language = navigator.language
   console.log(language)
   var locale = null
   if (language.toLowerCase() == 'zh-cn') {
     locale = 'zh_cn'
   } else if (language.toLowerCase() == 'en-us') {
     locale = 'en_us'
   } else {
     locale = 'zh_cn'
   }
   
   const i18n = new VueI18n({
     locale: locale,
     messages: {
       'zh_cn': require('@/assets/languages/zh_cn.json'),
       'en_us': require('@/assets/languages/en_us.json')
     }
   })
   export default i18n
   ```

5. 在 languages 文件夹下创建中英文的国际化文件：

   ```properties
   {
     "main-sidebar":{
       "home":"首页"
     }
   }
   ```

   ```properties
   {
     "main-sidebar":{
       "home":"index"
     }
   }
   ```

6. vue 前端页面中修改要被国际化的内容为：

   ```js
   {{$t("main-sidebar.home")}}
   ```

7. 在 src 文件夹下的 main.js 中的 VUE 对象中引用 i18n 对象：

   ```js
   import i18n from './i18n'
   
   new Vue({
     el: '#app',
     i18n,
     router,
     store,
     template: '<App/>',
     components: { App }
   })
   ```

### 3、防重复提交

> 怎么避免重复请求？

1. 前端方案：在用户获得响应前，禁用前端页面的提交按钮（加disable属性）。缺点：用户刷新页面或用 URL 提交即可绕过；

2. 后端方案：依赖 Session，后台使用 UUID 生成 token 字符串令牌来避免。流程如下：

   ![image-20200527221753022](C:\Users\49769\AppData\Roaming\Typora\typora-user-images\image-20200527221753022.png)

   用户访问到页面但是提交请求前先请求一次到后台，后台先使用 UUID 生成随机令牌字符串，保存至 HttpSession 中。再次发起提交请求时，带上令牌传给后台，后台比较是否和之前的一致，如果一致则通过，并删除已保存的令牌。

   步骤如下：

   * 在 ShiroConfig 中将 token 免验证。创建TokenController：

     ```java
     @RestController
     @RequestMapping("/token")
     public class TokenController {
     
         // 生成随机令牌字符串，并保存到HttpSession中
         @RequestMapping("/save")
         public R save(HttpServletRequest request) {
             HttpSession session = request.getSession();
             String token = UUID.randomUUID().toString();
             session.setAttribute("token", token);
             return R.ok().put("token", token);
         }
     
         // 将HttpSession中的令牌删除
         @RequestMapping("/remove")
         public R remove(HttpServletRequest request) {
             HttpSession session = request.getSession();
             session.removeAttribute("token");
             return R.ok();
         }
     }
     ```

   * 创建TestController：

     ```java
     @RestController
     @RequestMapping("test")
     public class TestController {
     
         @RequestMapping("/demo_1")
         public String demo_1() {
             return "page1";
         }
     
         // 防重复提交逻辑
         @RequestMapping("/demo_2")
         public R demo_2(HttpServletRequest request, @RequestParam Map<String, Object> params) {
             HttpSession session = request.getSession();
             String token = "";
             // 提取令牌
             if (session.getAttribute("token") != null) {
                 token = session.getAttribute("token").toString();
             }
             // 比较令牌
             // 如果服务器端保存的令牌和客户端发来的令牌不同，则为重复提交
             if (!token.equals(params.get("token").toString())) {
                 return R.error("token error");
             }
             // 如果token相等，则删除session中的token
             session.removeAttribute("token");
             // 执行业务code
             return R.ok();
         }
     }
     ```

     

### 4、缓存提速

> 为什么要用缓存？ ->  减少数据库与硬盘交互的次数，提升数据读写速度。

提升IO速度的常用方案：

1. 使用缓存；
2. 增加硬盘来做IO；
3. 搭建数据库集群（最优）。

#### 4.1 数据库缓存

不推荐使用MySQL高速查询缓存。

因为如果开启了查询缓存，数据库会把select语句查询结果保存到缓存中，之后如果 insert、update、delete 数据，数据库会清空一级缓存，会导致大量缓存失效，增加IO负担。

开启查询缓存在MySQL中配置如下：

```properties
query_cache_size = 2048M #缓存大小
query_cache_type = 1N #开启缓存
```

#### 4.2 持久层缓存

作用：用于缓存/更新缓存 Service 层的方法操作的数据。

> 有哪些持久层缓存？

Hibernate 和 Mybatis 框架都支持，还分为一级、二级缓存。

* 一级缓存是`会话级别`，session关闭则缓存失效。

  由于Spring接管了Mybatis，所以SqlSession每次执行一条SQL语句则关闭了。一级缓存主要使用在**子查询**中。Mybatis会先执行子查询，然后把子查询的结果缓存起来，就可避免相关子查询的情况下反复执行同一个子查询

* 二级缓存要引入第三方存储机制（存在框架之外）。Mybatis支持的二级缓存（mapper级别）：EHCache、`Redis`、Simple等。

> 怎么使用？

* 使用时主要注意两个注解：

1. @Cacheable，写到实现类的方法上，向二级缓存保存数据：

   ```java
   // 以 id 为 key，查询出的数据缓存到 student 中
   @Cacheable(value = "student", key = "#id")
   public StudentEntity getById(Serializable id) {
      return super.getById(id);
   }
   ```

2. @EnableCaching，写到启动类上，打开二级缓存功能：

   ```java
   @SpringBootApplication
   @EnableCaching
   public class RenrenApplication {
     public static void main(String[] args) {
        SpringApplication.run(RenrenApplication.class, args);
     }
   }
   ```

   注意：在Spring 的application.yml文件也要提前进行配置：

   ```yaml
   cache:
      type: redis
      cache-names: student  #配置命名空间为student
   ```
   
* 删除数据时，需要把二级缓存中的数据也删除：

  1. 在方法上使用注解@CacheEvict：

     ```java
     @CacheEvict(value = "student", key = "#id")
     public boolean removeById(Serializable id) {
        return super.removeById(id);
     }
     ```

  2. 如果要清空所有缓存，这样配置：

     ```java
     @CacheEvict(value = "student", allEntries = true)
     ```

> 使用原则？

**原则：单条数据适合缓存，多条数据不适合缓存（相当于查询结果的缓存）**

建议在Service方法中使用二级缓存（一个service方法可能调用多个dao方法），不建议在Controller、Dao中使用。负载均衡/分布式环境可使用、多个系统可共享使用二级缓存。

#### 4.3 业务层缓存

用于缓存业务数据，例如购物车数据、头条新闻、秒杀商品库存、大V微博的账号等。这样减轻了网站的负载。

#### 4.4 缓存持久化

`刺穿缓存`：当服务器出现故障需要重启后，缓存中数据会被清空，所有读写请求都由数据库处理，可能导致数据库再次奔溃。

> 怎么避免刺穿缓存？ ->  将缓存做持久化，则在缓存启动后会重新建立数据，可以避免刺穿缓存现象。

Redis 持久化方案，在 Redis 的 `redis.windows.config` 文件中配置：

1. 默认采用 **RDB** 持久化方案，每隔一段时间以快照的方式持久化到`dump.rdb`，不能实时保存数据，用于一般数据；

   ```properties
   save 900 1				#900秒内有1条记录改变
   save 300 10				#300秒内有10条记录改变
   save 60 10000			#60秒内有1W条记录改变
   ```

2. **AOF** 模式会把数据实时保存到硬盘上，持久化到`appendonly.aof`，用于重要数据，比如秒杀数据等。

   ```properties
   appendonly no			#开启AOF
   appendfsync always		#缓存变化立即持久化
   ```

### 5、防XSS攻击

> 什么是 XSS？

XSS跨站脚本攻击：是让浏览器渲染 DOM 的时候意外执行了恶意 JS 代码。

原理：

1. 在网页中嵌入一个恶意脚本。比如浏览器渲染以下代码时会获得用户浏览器本地存储的 cookie 内容：

   ```html
   <img src="null" onerror='alert(document.cookie)'/>
   ```

   浏览器是个沙箱环境（不可读写本地文件，不可连接数据库，不可创建线程），所以XSS不对系统造成破坏，但会盗取用户账号信息。

2. 通过 HTML 格式邮件的方式发送恶意脚本。

> 怎么避免 XSS？

在过滤器 FilterConfig 中配置了 XSS 过滤器：`xssFilterRegistration`，源码如下。用户提交的数据会经过`转译`，不会再被恶意执行。

```java
    @Bean
    public FilterRegistrationBean xssFilterRegistration() {
        FilterRegistrationBean registration = new FilterRegistrationBean();
        registration.setDispatcherTypes(DispatcherType.REQUEST);
        registration.setFilter(new XssFilter());
        registration.addUrlPatterns("/*");
        registration.setName("xssFilter");
        registration.setOrder(Integer.MAX_VALUE);
        return registration;
    }
```

### 6、评论模块

#### 6.1 两层式回帖

数据库里只需要把回复作为一个字段存进去就可以了。

最上层的评论的pid是0，子评论的pid就是父评论的id，这样的表结构就可以把所有的评论放到一个表中。

但是这样的表结构取出来的数据默认的排序规则是按照id排序的，在前台数据展示的时候，怎么把一个子评论放到其应有的位置呢？

进行排序：就是把数据库中的每一条记录都当成是一个对象实体，把整个表的数据放到一个对象数组里面，然后针对这个对象数组进行排序；首先肯定要遍历这个数组，每次循环只需要找到该条评论的子评论就行了。

```shell
Comment [id=1001, pid=0, content=a, userName=a's comment, level=1]
Comment [id=1002, pid=0, content=b, userName=b's comment, level=1]
Comment [id=1005, pid=1002, content=e, userName=e's comment, level=2]
Comment [id=1007, pid=1005, content=g, userName=g's comment, level=3]
Comment [id=10012, pid=1007, content=l, userName=l's comment, level=1]
Comment [id=1006, pid=1002, content=f, userName=f's comment, level=2]
Comment [id=10013, pid=1006, content=m, userName=m's comment, level=1]
Comment [id=1003, pid=0, content=c, userName=c's comment, level=1]
Comment [id=10010, pid=1003, content=j, userName=j's comment, level=1]
Comment [id=1004, pid=0, content=d, userName=d's comment, level=1]
Comment [id=1008, pid=0, content=h, userName=h's comment, level=1]
Comment [id=1009, pid=0, content=i, userName=i's comment, level=1]
Comment [id=10014, pid=0, content=n, userName=n's comment, level=1]
```

从控制台输出的结果来看，每条数据的pid都等于上一条数据的id，按照这样的排序规则输出到前台页面就满足需求，但是还有一个问题：如何去控制缩进（从上面的示例图片可以看出，如果一条评论是子评论，该条评论相对于父评论是有一些缩进的），刚刚在讲数据库设计的时候，可以看到表结构中有一个属性：level，这个值就是来控制每一条子评论的缩进的，把level值作为div的类名，通过样式来控制每个div的缩进。

### 7、内容检索模块

内容检索很难，MySQL做不到，这时候就需要用到ES。ES是基于Lucene的搜索服务器，提供了分布式多用户能力的全文搜索引擎，基于RESTful web接口。

> 怎么实现内容检索？  ->  对内容分词后，创建索引。

ES的操作使用了`elasticsearch-head-5.0.0`和`elasticHD`插件。

#### 7.1 ES中文分词器

ES自带英文分词器，按空格进行分词，按照单词编制索引。

中文分词器分为两种：

1. ik

   `ik_max_word`分词器提供`细颗粒`度的分词，用于标题。
   `ik_smart`分词器提供`粗颗粒`度的分词，用于正文。

2. hanlp

Java程序中想使用中文分词只要在POJO类中配置分词器即可：

```java
@Document(indexName = "article_index", type = "article")
@Data
@Mapping(mappingPath = "article_mapping.json")
public class ArticleEntity {
    private String id;
    private String title;
    private String content;
}
```

其中 article_mapping.json 放在 resources 路径下，他规定哪些字段用哪个分词器，比如：

```json
{
  "article":{
    "properties":{
      "id":{
        "type":"text"
      },
      "title":{			//标题，用细颗粒度的
        "type":"text",
        "analyzer":"ik_max_word",         //保存时的分词器
        "search_analyzer":"ik_max_word"   //检索时的分词器
      },
      "content":{    	//内容，粗颗粒度的
        "type":"text",
        "analyzer":"ik_smart",         //保存时的分词器
        "search_analyzer":"ik_smart"   //检索时的分词器
      }
    }
  }
}
```

#### 7.2 分词检索

1. 为系统添加ES存储，将录入的数据再保存到ES中。系统要把内容数据分别保存至数据库和ES中。

   比如在 SpiderMissionServiceImpl.java 的回调函数中补充：

   ```java
   // 保存数据至ES
   ArticleEntity articleEntity = new ArticleEntity();
   articleEntity.setContent(id);
   articleEntity.setTitle(title);
   // 去除正文中的html标签
   content = content.replaceAll("\\&[a-zA-Z]{1,10};", "").replaceAll("<[^>]*>", "").replaceAll("[(/>)<]", "").replaceAll(" ", "");
   articleEntity.setContent(content);
   articleService.save(articleEntity);
   ```

2. 为内容搜索添加ES查询。

   补充DAO和SERVICE。主要添加通过多个id查找文章的功能。

   比如在ArticleMapper.xml中添加：

   ```xml
   <select id="selectArticleByIds" parameterType="list" resultMap="ArticleResult">
       <include refid="selectArticleVo"/>
       <where>
           id IN
           <foreach collection="list" item="one" separator="," open="(" close=")">
               #{one}
           </foreach>
       </where>
   </select>
   ```

   在ArticleMapper.java中添加：

   ```java
   public List<Article> selectArticleByIds(List<String> ids);
   ```

   在IArticleService.java中添加：

   ```java
   public List<Article> selectArticleByIds(List<String> ids);
   ```

   在ArticleServiceImpl.java中添加：

   ```java
   @Override
   public List<Article> selectArticleByIds(List<String> ids) {
       List<Article> list = articleMapper.selectArticleByIds(ids);
       return list;
   }
   ```

   进入BlogController.java中找到文本框搜索方法search并添加：

   ```java
   @Qualifier("blogArticleService")
   private ArticleService esArticleService;
   
   List<ArticleEntity> list = esArticleService.findByKeyword(content.trim());
   List<String> ids = new ArrayList<>();
   list.forEach(one -> {
       String id = one.getId();
       ids.add(id);
   });
   List<Article> articles = articleService.selectArticleByIds(ids);
   ```

### 8、nginx负载均衡

部署方面采用Nginx对项目进行负载均衡配置，Nginx本身也是采用双节点，一个工作，一个待机，保证了负载均衡容灾。 

## 二、秒杀系统

项目技术栈：前端主要使用HTML，CSS，Jquery，Bootstrap。后台使用Java语言，使用Spring、SpringMVC、Mybatis框架进行 秒杀系统的开发。

> 秒杀难点是什么？

难点：如何高效处理竞争问题，同一时间抢一个商品。

秒杀过程：

1. start transaction
2. update库存数量（获取行级锁）
3. insert购买明细
4. commit

### 1、高并发优化

> 高并发发生在哪里？

如图所示：

<img src="C:\Users\49769\AppData\Roaming\Typora\typora-user-images\image-20200527235012651.png" alt="image-20200527235012651" style="zoom:80%;" />

#### 1.1 详情页

大量的用户在刷新时会造成资源请求频繁，我们将详情页面静态化，css、js等静态资源部署到CDN节点上（不在秒杀系统后端服务器上），实现优化的效果。

#### 1.2 获取秒杀地址（待完善）

无法使用CDN缓存，但适合放在 redis缓存上（10w/s的QPS）。

#### 1.3 秒杀操作

问题：存在数据竞争，进行事务竞争优化。

现流程：update减库存后获取行级锁rowLock -> insert购买明细 -> commit/rollback 释放行级锁freeLock。

> 怎么进行优化？

1. 事务执行优化：交换顺序，缩短行级锁持有时间（缩短网络延迟/GC时间）：先insert购买明细，再update减库存，再commit/rollback；

2. 使用存储过程优化（事务SQL在MYSQL端执行）：上述写成存储过程。把客户端逻辑放到MYSQL服务端，避免网络延迟和GC影响。

   原因：同一个id执行update减库存：压测结果能抗住约4W的QPS。但是每个请求都是事务化的，`请求间存在行锁串行的问题`，其中穿插了网络延迟、GC等原因。所以瓶颈在于：`Java和数据库之间的网络通信慢`。而行级锁在Commit后会释放，所以优化方向为：减少行级锁的持有时间。

### 2、前端优化

按钮防重复。

参考内容管理系统的第3章。