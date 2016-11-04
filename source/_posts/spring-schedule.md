title: spring schedule 任务调度框架
date: 2016.01.18
categories: java #分类
tags: [java, springmvc, schedule, quartz]
toc: true
---

## 项目简介
 对于小批量的定时或批处理定时任务。spring @Scheduled非常合适。
 spring定时任务或周期性任务调度框架学习，基于spring4构建本项目。

## 关键注解
### @Scheduled
@Scheduled注解非常强大，可以设置成定周期运行任务，也可以设置成类似于crontab的方式进行调度。

## 项目依赖
``` bash
  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
      <springframework.version>4.2.0.RELEASE</springframework.version>
  </properties>
  <dependencies>
         <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>${springframework.version}</version>
        </dependency>
         <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>${springframework.version}</version>
        </dependency>
  </dependencies>
```

## 固定周期代码
固定周期单位是毫秒
``` bash
public class FixRateJob {
	@Scheduled(initialDelay=1000, fixedRate=5000)
	public void doSomething() {
	    // something that should execute periodically
		System.out.println("hello fixedRate job");
	}
}
```

## crontab风格job
直接在注解中设置crontab参数
``` bash
public class CronJob {
	@Scheduled(cron="*/5 * * * * MON-FRI")
	public void doSomething() {
	    // something that should execute periodically
		System.out.println("hello cron job");
	}
}

```

### crontab风格job2
通过配置文件设置参数
```
public class CronJobByProps {
	@Scheduled(cron="${app.cron:*/3 * * * * MON-FRI}")
	public void doSomething() {
	    // something that should execute periodically
		System.out.println(new Date() + "　==> hello cron job by properties");
	}
}
```

## 项目配置
通过注解来配置项目，比xml清爽多了。
``` bash
@Configuration
@EnableAsync
@EnableScheduling
@PropertySource(value = { "classpath:app.properties" }, ignoreResourceNotFound = true)
public class AppConfig {
	@Bean
	public FixRateJob job(){
		return new FixRateJob();
	}
	
	@Bean
	public CronJob cornJob(){
		return new CronJob();
	}

	@Bean
	public CronJobByProps propsJob(){
		return new CronJobByProps();
	}

    @Bean
    public static PropertySourcesPlaceholderConfigurer propertySourcesPlaceholderConfigurer() {
       return new PropertySourcesPlaceholderConfigurer();
    }
```

## 测试代码
``` bash
public class AppMain {
	public static void main(String args[]) {
		AbstractApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);

	}
}
```

本项目没有xml配置文件，全部以注解的方式，进行配置注入。

#### github: [https://github.com/njkfei/spring-schedule.git](https://github.com/njkfei/spring-schedule.git)
#### 项目参考：[www.websystique.com](http://www.websystique.com)
#### 个人blog: [wiki.niejinkun.com](http://wiki.niejinkun.com)
