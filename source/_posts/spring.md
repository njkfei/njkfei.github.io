title: spring入门指南
date: 2016.01.07
categories: java #分类
tags: [java, spring]
toc: true
---
  spring真是一个伟大的东西，它提供了一致性的编程模型，极大的简化了编程工作．使得我专注于业务逻辑的思考，从而不再关注于语言特性身上．
  我不再编写解析配置文件的代码，也不再编写对象之间的组合代码了，因为，这些工作，经常重复，又占用时间．有了spring,可以和配置的对象之间的关系组合代码再见了．
  其实，我最喜欢spring的地方，是使用注解来进行代码的组装.

## xml配置
　xml配置，我一般是配置_消息系统_,_数据库连接_,_redis_连接,__自动事务处理__,__dubbo__服务管理之类的东西．以及驱动自动注解的配置．
### 自动扫包
　一般上下面这行就行，如果要特殊处理，比较_exclude_也是可以的，不过我不常用．
``` bash
<context:component-scan base-package="package com.sanhao.media.mq" />
```
### 加载属性配置文件
``` bash
<context:property-placeholder location="classpath:conf/activemq.properties,classpath:conf/jdbc.properties,classpath:conf/oss.properties,classpath:conf/ffmpeg.properties,classpath:conf/app.properties" ignore-unresolvable="true" />
```

### 配置list,set,map复杂对象
　　这个，使用注解还是不太方便，但是，复杂系统，总会越来一复杂的配置，这时候，xml配置还是挺好看的．看下面．
##### set配置
``` bash
   <util:set id="recommendTypeSet" value-type="java.lang.Integer" set-class="java.util.HashSet">
      <value>18</value>
      <value>19</value>
   </util:set>
```
##### map 配置
``` bash
  <util:map id="Error_Code_Map" key-type="java.lang.Integer">
   		<!-- 非法请求 -->
   		<entry key="99" value="非法RPC请求" />
   		<entry key="100" value="缺少参数"/>
  </util:map>
```

##### list配置
　和**set配置**类似，略．

## 注解配置
　这个，太清爽啦．
  常用注解有：
* @Controller
* @Service
* @Component
* @Autowired
* @Qualifier
* @Value
* @Resource

### @Controller
  **@Controller**用于注解MVC中的注解．**括号**里面是bean的名字．示例如下：
``` bash
@Controller("apiController")
@Path("/")
public class ApiController{
```
关于mvc中的注解，后续会详细介绍．

### @Service
 **@Service**用于标识服务，一般是指执行特定的业务逻辑实体．**括号**里面是bean的名字示例如下：
``` bash
@Service("ossService") 在
public class OssServiceImpl implements OssService {}
```

### @Component
 其实，**@Service**和**@Controller**都是具有逻辑意义的**@Component**.它们之间除了意义上不一样，实质是一样的．
 示例如下：
``` bash
@Component
public class ApiResultUtilImpl implements ApiResultUtil{}
```

### @Autowired
  其实，前面几个比起这个来，弱爆了．**@Autowired**是对象之间组合的利器．通过这个注解，可实现对象之间的组合．
　在使用**@Autowired**时，我会结合**@Qualifier**使用．通过@Qualifier,可以指定具体的bean．而不是交给**spring框架**去做．
示例如下：
##### 自动注入
``` bash
	@Autowired
	private CouponDAO couponDAO;
```

##### 指定注入对象
``` bash
	@Autowired
	@Qualifier("couponDAO")
	private CouponDAO couponDAO;
```

##### 允许注入对象为空
``` bash
	@Autowired(required=false)
	private CouponDAO couponDAO;
```

### @Qualifier
  前面已介绍，略．

### @Resource
　**@Resource**与**@Autowired**类似．它们之间的关系如下．
``` bash
@Resource默认是按照名称来装配注入的，只有当找不到与名称匹配的bean才会按照类型来装配注入.

@Autowired默认是按照类型装配注入的，如果想按照名称来转配注入，则需要结合@Qualifier一起使用.

@Resource注解是又J2EE提供，而@Autowired是由Spring提供，故减少系统对spring的依赖建议使用@Resource的方式；

@Resource和@Autowired都可以书写标注在字段或者该字段的setter方法之上
```

### @Value
　**@Resource**和**@Autowired**解决了对象之间的问题，那么，谁来解决属性配置文件和对象之间的关系了，答案是**@Value**．
示例如下：
``` bash
public class AudioMessageListener implements MessageListener {
	private static final Logger logger = Logger.getLogger("audio");

	@Autowired
	@Qualifier("convertCodeService")
	private ConvertCodeService convertCodeService;
	
	@Value("${app.mq.timelimit:0}")
	private int timelimit;
}
```
其中，_app.mq.timelimit_在文件app.properties当中．
``` bash
app.mq.timelimit=0
app.delete.tmpfile=false
app.src.audio.postfix=mp3
app.src.video.postfix=mp4
```
 如果配置文件不存在_app.mq.timelimit_配置项，则使用默认值_0_．