title: spring4 mybatis mvc security集成
date: 2016.01.13
categories: java #分类
tags: [java, spring, mvc, security, web, mybatis]
toc: true
---
　spring4基本使用注解来驱动依赖注入的配置解析，不再使用xml进行配置了，这样一来，与程序员的交互更加清爽了．
　spring security是spring提供的安全框架，它基于用户角色的方式进行安全检查．
　mybatis提供了用户账户，用户角色落地存储．

搭建这个工程，花了我大量的时间．有几个坑要记录一下．

## properties文件无法加载
*描述*: jdbc.properties文件中的配置，无法加载．原来是spring4的一个bug.需要加入配置：
``` bash
	// 必须要有这一行，否则上面的＠VALUE无法注入
    @Bean
    public static PropertySourcesPlaceholderConfigurer propertySourcesPlaceholderConfigurer() {
       return new PropertySourcesPlaceholderConfigurer();
    }
```

## 无法扫dao包
*描述*：dao包包括两部分，一部分是接口配置，一部分是model配置，扫描时，需要加入下面的@MapperScan配置．
``` bash
@Configuration
@EnableTransactionManagement
@MapperScan({ "xyz.hollysys.springsecurity.dao" }) // 这行扫描DAO,还需要扫描dao的相关MODEL
@PropertySource(value = { "classpath:jdbc.properties", "classpath:log4j.properties" })
public class MybatisConfiguration {
}
```

还需要扫描dao的相关MODEL
``` bash
	public static final String MAPPERS_PACKAGE_NAME_1 = "xyz.hollysys.springsecurity.model";

	@Bean
	public SqlSessionFactory sqlSessionFactory() throws Exception {
		SqlSessionFactoryBean sessionFactory = new SqlSessionFactoryBean();
		sessionFactory.setDataSource(dataSource());
		sessionFactory.setTypeAliasesPackage(MAPPERS_PACKAGE_NAME_1);  //　需要加入这行，没有这行，DAO无法注入
		return sessionFactory.getObject();
	}
```

## REST控制器
*描述*: spring4 mvc 提供支持json返回的@RestController,如果@ResponseBody没有json绑定，那么会返回错误．如下所示：
``` bash
org.springframework.web.util.NestedServletException: Request processing failed; nested exception is java.lang.IllegalArgumentException: No converter found for return value of type: class jav
a.util.ArrayList
        at org.springframework.web.servlet.FrameworkServlet.processRequest(FrameworkServlet.java:982)
        at org.springframework.web.servlet.FrameworkServlet.doGet(FrameworkServlet.java:861)
        at javax.servlet.http.HttpServlet.service(HttpServlet.java:687)
        at org.springframework.web.servlet.FrameworkServlet.service(FrameworkServlet.java:846)
``` 
解决方法：加入json解析包
``` bash
		<dependency>
			<groupId>org.codehaus.jackson</groupId>
			<artifactId>jackson-mapper-asl</artifactId>
			<version>1.4.2</version>
		</dependency>
		<dependency>
			<groupId>org.codehaus.jackson</groupId>
			<artifactId>jackson-core-asl</artifactId>
			<version>1.4.2</version>
		</dependency>
		<dependency>
			<groupId>com.fasterxml.jackson.core</groupId>
			<artifactId>jackson-databind</artifactId>
			<version>2.5.3</version>
		</dependency>
```

### 附REST控制器
``` bash
@RestController
public class JsonController {
	static Logger logger = Logger.getLogger(JsonController.class);

	@Autowired
	@Qualifier("userService")
	private UserService userService;

	@RequestMapping(value = "/json/{user_id}", method = RequestMethod.GET)
	public @ResponseBody List<UserProfile> jsonPage(@PathVariable int user_id) {
		logger.info("json");
		return userService.getUserProfiles(user_id);
	}
}

 后记：搞完这个，走了很多弯路．现在这些记下来．
## GITHUB
  事后加入
```
  OK.