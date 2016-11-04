title: dubbo-spring3实践
date: 2016.03.14
categories: java #分类
tags: [java,dubbo,服务化]
---

## 背景
　dubbo 基于spring2.5.6进行构建．目前spring版本已更新到4.0以上了．使用javaconfig的方式，使用起来比xml配置更符合程序员的习惯．
因此，有必要研究基于spring3与dubbo进行集成．

## 集成方法
### 引入dubbo库
引用dubbo库时，需要解决dubbo对spring2.5.6的依赖关系．如下所示．
```
		<dependency>
			<groupId>com.alibaba</groupId>
			<artifactId>dubbo</artifactId>
			<version>2.5.3</version>：
			<exclusions>
				<exclusion>
					<artifactId>spring</artifactId>
					<groupId>org.springframework</groupId>
				</exclusion>
			</exclusions>
		</dependency>
```

如果不排除依赖关系，在运行时，将产生jar包冲突，程序无法运行．

### 服务提供者加入dubbo配置
需要配置dubbo的注册信息和协议信息
```
/**
 * ref: http://dubbo.io/User+Guide-zh.htm
 * @author sanhao
 *
 */

@Configuration
public class DubboBaseConfig {

	@Bean
	public ApplicationConfig application() {
		ApplicationConfig application = new ApplicationConfig();
		application.setName("demoService");

		return application;
	}

	// 连接注册中心
	@Bean
	public RegistryConfig registry() {
		RegistryConfig registry = new RegistryConfig();
		registry.setProtocol("zookeeper");                       // 也可以是其它注册中心
		registry.setAddress("zookeeper://127.0.0.1:2181");
/*		registry.setUsername("aaa");
		registry.setPassword("bbb");*/

		// 不向注册中心注册，在正式上线后，这句话要去掉，建议在properties里面注入异性
/*		问题：
		为方便开发测试，经常会在线下共用一个所有服务可用的注册中心，这时，如果一个正在开发中的服务提供者注册，可能会影响消费者不能正常运行。
		解决方案：
		可以让服务提供者开发方，只订阅服务(开发的服务可能依赖其它服务)，而不注册正在开发的服务，通过直连测试正在开发的服务。*/
		registry.setRegister(false);
		
		return registry;
	}
	
	@Bean
	public List<RegistryConfig> registries(){
		List<RegistryConfig> registries = new ArrayList<RegistryConfig>();
		
		registries.add(registry());
		
		return registries;
	}
	
	// 服务提供者协议配置，可以配置多协议
	@Bean
	public ProtocolConfig protocol(){
	ProtocolConfig protocol = new ProtocolConfig();
	protocol.setName("dubbo");
	protocol.setPort(20880);
	protocol.setThreads(200);
	
	return protocol;
	}
}
```

### 暴露服务
暴露服务时，需要使用dubbo的基本配置信息，例如，本文中暴露了订单服务．
```
@Configuration
@Import(DubboBaseConfig.class)
public class OrderServiceConfig {
	@Autowired 
	DubboBaseConfig dubboBaseConfig;

	@Bean
	public OrderService orderService(){
		OrderService orderService = new OrderServiceImpl();
		return orderService;
	}
	 
	// 服务提供者暴露服务配置
	@Bean
	public ServiceConfig<OrderService> service(){
	ServiceConfig<OrderService> service = new ServiceConfig<OrderService>(); // 此实例很重，封装了与注册中心的连接，请自行缓存，否则可能造成内存和连接泄漏
	service.setApplication(dubboBaseConfig.application());
	service.setRegistry(dubboBaseConfig.registry()); // 多个注册中心可以用setRegistries(),service.setRegistries(registries());
	service.setProtocol(dubboBaseConfig.protocol()); // 多个协议可以用setProtocols()
	
	service.setInterface(OrderService.class);
	service.setVersion("1.0.0");
	service.setRef(orderService());
	service.setCluster("failsafe");  // 集群配置模式，可以是failover,failfast,failsave,failback等，根据需求场景选择合适的配置
	service.export();
	
	// service.setGroup("XXX"); //设置分组
	
	return service;
	}
}
```

### 通过web服务进行暴露
引入暴露的服务和数据访问服务
```
public class AppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {

	@Override
	protected Class<?>[] getRootConfigClasses() {
		// MDC.put("environment", System.getenv("APP_ENV") != null ? System.getenv("APP_ENV") : "dev");
		return new Class<?>[]{OrderServiceConfig.class,MybatisConfiguration.class};
	}

	@Override
	protected Class<?>[] getServletConfigClasses() {
		// TODO Auto-generated method stub
		return null;
	}

	@Override
	protected String[] getServletMappings() {
		// TODO Auto-generated method stub
		return null;
	}

}
```

### 编译
```
mvn clean install
```

### 运行
#### 先启动zookeeper
```
sh zkServer.sh
```

#### 通过web容器运行
通过tomcat7插件，进行测试
```
mvn tomcat7:run
```

插件pom配置为
```
			<plugin>
				<groupId>org.apache.tomcat.maven</groupId>
				<artifactId>tomcat7-maven-plugin</artifactId>
				<version>2.2</version>
				<configuration>
					<charset>UTF-8</charset>
					<port>8888</port>
					<path>/${project.build.finalName}</path>
				</configuration>
			</plugin>
```

### 测试
#### 服务注册
通过zk客户端，看注册是否成功
```
ls /dubbo
```
看返回信息即可．

#### 客户端测试
客户端测试前，需要编写测户端代码．通过与dubbo服务提供者共享的接口文件，获取dubbo的服务．


## 结论
### 优点
* dubbo简单
* java config 面向程序员，比xml配置清爽
* dubbo可以实现服务化

### 缺点
* 生成的war包，还是需要部署在tomcat之上，如果能生成内嵌tomcat或jetty容器的话，会更简单

## github
示例已上传至github.
路径为：
* [dubbo服务提供者](https://github.com/njkfei/duboo-provider.git)
* [dubbo服务消费者](https://github.com/njkfei/dubbo-consumer.git)