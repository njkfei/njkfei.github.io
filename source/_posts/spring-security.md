title: springsecurity学习
date: 2016.01.07
categories: java #分类
tags: [java, spring security]
toc: true
---
  spring security是安全框架,它可以和springmvc进行集成．
PS: 据说shiro比spring security 更简单．
## 学习参考
　http://websystique.com/spring-security/spring-security-4-hello-world-annotation-xml-example/  
## 依赖管理
``` bash
        <properties>
          <springframework.version>4.1.6.RELEASE</springframework.version>
          <springsecurity.version>4.0.1.RELEASE</springsecurity.version>
        </properties>
        <!-- Spring Security -->
        <dependency>
            <groupId>org.springframework.security</groupId>
            <artifactId>spring-security-web</artifactId>
            <version>${springsecurity.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.security</groupId>
            <artifactId>spring-security-config</artifactId>
            <version>${springsecurity.version}</version>
        </dependency
```

## 代码
### HelloWorldConfiguration
``` bash
package xyz.hollysys.springsecurity.configuration;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.ViewResolver;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;
import org.springframework.web.servlet.view.InternalResourceViewResolver;
import org.springframework.web.servlet.view.JstlView;

@Configuration
@EnableWebMvc
@ComponentScan(basePackages = "xyz.hollysys.springsecurity")
public class HelloWorldConfiguration {
  
  @Bean(name="HelloWorld")
  public ViewResolver viewResolver() {
    InternalResourceViewResolver viewResolver = new InternalResourceViewResolver();
    viewResolver.setViewClass(JstlView.class);
    viewResolver.setPrefix("/WEB-INF/views/");
    viewResolver.setSuffix(".jsp");

    return viewResolver;
  }

}
```

### SecurityConfiguration.java
``` bash
package xyz.hollysys.springsecurity.configuration;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;

@Configuration
@EnableWebSecurity
public class SecurityConfiguration extends WebSecurityConfigurerAdapter {

  @Autowired
  public void configureGlobalSecurity(AuthenticationManagerBuilder auth) throws Exception {
    auth.inMemoryAuthentication().withUser("bill").password("abc123").roles("USER");
    auth.inMemoryAuthentication().withUser("admin").password("root123").roles("ADMIN");
    auth.inMemoryAuthentication().withUser("dba").password("root123").roles("ADMIN","DBA");
  }
  
  @Override
  protected void configure(HttpSecurity http) throws Exception {
 
    http.authorizeRequests()
      .antMatchers("/", "/home").permitAll() 
    .antMatchers("/admin/**").access("hasRole('ADMIN')")
    .antMatchers("/db/**").access("hasRole('ADMIN') and hasRole('DBA')")
    .and().formLogin()
    .and().exceptionHandling().accessDeniedPage("/Access_Denied");
 
  }
}

```

### SecurityWebApplicationInitializer
``` bash
package xyz.hollysys.springsecurity.configuration;

import org.springframework.security.web.context.AbstractSecurityWebApplicationInitializer;

public class SecurityWebApplicationInitializer extends AbstractSecurityWebApplicationInitializer {

}
```
### SpringMvcInitializer.java
``` bash
package xyz.hollysys.springsecurity.configuration;

import org.springframework.web.servlet.support.AbstractAnnotationConfigDispatcherServletInitializer;

public class SpringMvcInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {

  @Override
  protected Class<?>[] getRootConfigClasses() {
    return new Class[] { HelloWorldConfiguration.class };
  }
 
  @Override
  protected Class<?>[] getServletConfigClasses() {
    return null;
  }
 
  @Override
  protected String[] getServletMappings() {
    return new String[] { "/" };
  }

}
```
### 
``` bash
package xyz.hollysys.springsecurity.controller;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.springframework.security.core.Authentication;
import org.springframework.security.core.context.SecurityContextHolder;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.web.authentication.logout.SecurityContextLogoutHandler;
import org.springframework.stereotype.Controller;
import org.springframework.ui.ModelMap;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

@Controller
public class HelloWorldController {

  
  @RequestMapping(value = { "/", "/home" }, method = RequestMethod.GET)
  public String homePage(ModelMap model) {
    model.addAttribute("greeting", "Hi, Welcome to mysite. ");
    return "welcome";
  }

  @RequestMapping(value = "/admin", method = RequestMethod.GET)
  public String adminPage(ModelMap model) {
    model.addAttribute("user", getPrincipal());
    return "admin";
  }

  @RequestMapping(value = "/db", method = RequestMethod.GET)
  public String dbaPage(ModelMap model) {
    model.addAttribute("user", getPrincipal());
    return "dba";
  }

  @RequestMapping(value="/logout", method = RequestMethod.GET)
     public String logoutPage (HttpServletRequest request, HttpServletResponse response) {
        Authentication auth = SecurityContextHolder.getContext().getAuthentication();
        if (auth != null){    
           new SecurityContextLogoutHandler().logout(request, response, auth);
        }
        return "welcome";
     }

  @RequestMapping(value = "/Access_Denied", method = RequestMethod.GET)
  public String accessDeniedPage(ModelMap model) {
    model.addAttribute("user", getPrincipal());
    return "accessDenied";
  }
  
  private String getPrincipal(){
    String userName = null;
    Object principal = SecurityContextHolder.getContext().getAuthentication().getPrincipal();

    if (principal instanceof UserDetails) {
      userName = ((UserDetails)principal).getUsername();
    } else {
      userName = principal.toString();
    }
    return userName;
  }
}
```

### welcome.jsp
``` bash
<%@ page language="java" contentType="text/html; charset=ISO-8859-1" pageEncoding="ISO-8859-1"%>
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>HelloWorld page</title>
</head>
<body>
    Greeting : ${greeting}
    This is a welcome page.
</body>
</html>
```
### admin.jsp
``` bash
<%@ page language="java" contentType="text/html; charset=ISO-8859-1" pageEncoding="ISO-8859-1"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>HelloWorld Admin page</title>
</head>
<body>
    Dear <strong>${user}</strong>, Welcome to Admin Page.
    <a href="<c:url value="/logout" />">Logout</a>
</body>
</html>
```

### dba.jsp
``` bash
<%@ page language="java" contentType="text/html; charset=ISO-8859-1" pageEncoding="ISO-8859-1"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>DBA page</title>
</head>
<body>
    Dear <strong>${user}</strong>, Welcome to DBA Page.
    <a href="<c:url value="/logout" />">Logout</a>
</body>
</html>
```

### accessDenied.jsp
``` bash
<%@ page language="java" contentType="text/html; charset=ISO-8859-1" pageEncoding="ISO-8859-1"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>AccessDenied page</title>
</head>
<body>
    Dear <strong>${user}</strong>, You are not authorized to access this page
    <a href="<c:url value="/logout" />">Logout</a>
</body>
</html>
```

### 
##　附
完整pom
``` bash

```