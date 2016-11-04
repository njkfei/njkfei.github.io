title: dubbo-springboot集成
date: 2016.03.17
categories: java #分类
tags: [dubbo,spirng,服务化]
---

## 背景
　前面有文章介绍dubbo和spring3 javaconfig集成．这一篇，将介绍dubbo与springboot集成．通过springboot内置的tomcat或
jetty容器．可以实现单包部署．相对dubbo和spring3 javaconfig集成，springboot集成更爽．

## 集成方法
# springboot-dubbo-provider
spring boot 与dubbo进行集成．
搞通之后，觉得很简单，在搞通之前，有不少坑．主要还是依赖问题和配置问题．

## maven配置
### 加入spring boot依赖
原来的配置，配置为允许自动配置
```
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>1.3.3.RELEASE</version>
	</parent>
		
	<dependencies>
	<!--  spring boot -->
			<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-tomcat</artifactId>
			<scope>provided</scope>
		</dependency>
				<!--添加适用于生产环境的功能，如性能指标和监测等功能。 -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-actuator</artifactId>
		</dependency>
</dependencies>
```

### 加入打包插件
```
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
				<version>1.3.3.RELEASE</version>
				<executions>
					<execution>
						<goals>
							<goal>repackage</goal>
						</goals>
					</execution>
				</executions>
			</plugin>
```

## 加入配置
下面的注解一句都不能少，少了就会报错．
```
@SpringBootApplication
@EnableAutoConfiguration(exclude={DataSourceAutoConfiguration.class})
@Import({OrderServiceConfig.class,MybatisConfiguration.class})
public class App 
{
    public static void main( String[] args )
    {
    	 SpringApplication.run(App.class, args);
    }
}
```
如果少了
```
@Import({OrderServiceConfig.class,MybatisConfiguration.class})
```
则无法暴露dubbo服务．
```
Caused by: org.springframework.boot.autoconfigure.jdbc.DataSourceProperties$DataSourceBeanCreationException: Cannot determine embedded database driver class for database type NONE. If you want an em
bedded database please put a supported one on the classpath. If you have database settings to be loaded from a particular profile you may need to active it (no profiles are currently active).
        at org.springframework.boot.autoconfigure.jdbc.DataSourceProperties.getDriverClassName(DataSourceProperties.java:180)
        at org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration$NonEmbeddedConfiguration.dataSource(DataSourceAutoConfiguration.java:121)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(Unknown Source)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(Unknown Source)
        at java.lang.reflect.Method.invoke(Unknown Source)
        at org.springframework.beans.factory.support.SimpleInstantiationStrategy.instantiate(SimpleInstantiationStrategy.java:162)
        ... 38 more
```

```
// 后面经实测，这句可以去掉
@EnableAutoConfiguration(exclude={DataSourceAutoConfiguration.class})
```

## 编译
```
mvn clean install
```

## 运行
```
java -jar target\dataservice-provider-0.0.1-SNAPSHOT.jar
```
## 注意事项
### 加入监控中心后，无法显示统计数据
解雇方案：
需要在服务端开启监控配置。
修改两个地方：
配置监控信息
```
@Bean(name="registry")
public RegistryConfig registry(){}

@Bean
public MonitorConfig monitor(){
    MonitorConfig monitor = new MonitorConfig();
    monitor.setProtocol("registry");
    return monitor;
}
```
将服务加入监控
```
@Bean
public ServiceConfg serviceConfg(){
    ....
    service.setMonitor(dubboConfig.monitor());)
    ....
}
```

## 结论
### 优点
* 部署简单


## github
示例已上传至github.
路径为：
* [dubbo服务提供者](https://github.com/njkfei/springboot-dubbo-provider.git)
* [dubbo服务消费者](https://github.com/njkfei/dubbo-consumer.git)
