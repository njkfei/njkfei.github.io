title: spring async异步框架
date: 2016.01.18
categories: java #分类
tags: [java, springmvc, async]
toc: true
---

## 项目简介
 spring异步框架，加上注解@Async即可。本文以获取个人的github用户信息为例，进行说明。

## 项目依赖
``` bash
  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
      <springframework.version>4.2.0.RELEASE</springframework.version>
  </properties>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-web</artifactId>
      <version>${springframework.version}</version>
    </dependency>
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
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>2.5.4</version>
    </dependency>
```

## 异步调用服务类
``` baah
@Service
public class GitHubLookupService {

    RestTemplate restTemplate = new RestTemplate();

    @Async
    public Future<User> findUser(String user) throws InterruptedException {
        System.out.println("Looking up " + user);
        User results = restTemplate.getForObject("https://api.github.com/users/" + user, User.class);
        // Artificial delay of 1s for demonstration purposes
        Thread.sleep(1000L);
        return new AsyncResult<User>(results);
    }
}
```

## 实体类
``` bash
@JsonIgnoreProperties(ignoreUnknown=true)
public class User {

    private String name;
    private String blog;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getBlog() {
        return blog;
    }

    public void setBlog(String blog) {
        this.blog = blog;
    }

    @Override
    public String toString() {
        return "User [name=" + name + ", blog=" + blog + "]";
    }
}
```
## 配置类
``` bash
@Configuration
@EnableAsync
@EnableScheduling
@ComponentScan(basePackages = "xyz.hollysys.spring.async.service")
public class AppConfig {
  
}
```

## 测试类
``` bash
public class AppMain {
  public static void main(String args[]) {
    AbstractApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
    GitHubLookupService service = (GitHubLookupService) context.getBean("gitHubLookupService");
    
    try {
      Future<User> user  = service.findUser("njkfei");
      System.out.println(user.get());
    } catch (InterruptedException e) {
      // TODO Auto-generated catch block
      e.printStackTrace();
    }catch (ExecutionException e) {
      // TODO Auto-generated catch block
      e.printStackTrace();
    }
    
  }
}
```

本项目没有xml配置文件，全部以注解的方式，进行配置注入。

#### github: [https://github.com/njkfei/spring-async.git](https://github.com/njkfei/spring-async.git)
#### 项目参考：[www.websystique.com](http://www.websystique.com)
#### 个人blog: [wiki.niejinkun.com](http://wiki.niejinkun.com)
