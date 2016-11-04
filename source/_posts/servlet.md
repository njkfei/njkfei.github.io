title: servlet实践
date: 2016.01.08
categories: java #分类
tags: [java, servlet, mvc]
toc: true
---
 	从事java web开发，必须了解servlet．servlet是基础性的东西，springmvc,tomcat都是servlet容器．
## [基本概念](https://zh.wikipedia.org/wiki/Java_Servlet)
Servlet（Server Applet），全称Java Servlet，未有中文译文。是用Java编写的服务器端程序。其主要功能在于交互式地浏览和修改数据，生成动态Web内容。狭义的Servlet是指Java语言实现的一个接口，广义的Servlet是指任何实现了这个Servlet接口的类，一般情况下，人们将Servlet理解为后者。
Servlet运行于支持Java的应用服务器中。从实现上讲，Servlet可以响应任何类型的请求，但绝大多数情况下Servlet只用来扩展基于HTTP协议的Web服务器。
最早支持Servlet标准的是JavaSoft的Java Web Server。此后，一些其它的基于Java的Web服务器开始支持标准的Servlet。
　上面这段话抄自[WIKI](https://zh.wikipedia.org/wiki/Java_Servlet).
　简单来说：servlet是一个前端控制器，它接收来自外部的(http)请求．经过业务处理后，生成响应结果提供给外部系统．

## 题外话：web三大组件
* servlet : 本文的重点
* listerner : 监听器，可以监听配置文件，可以监听session的变化．
* filter : 过滤器，可以做一些比较琐碎的工作，比如统一编码，比如检查入口参数，比如在输出时，统一添加数据等等．



## 关键对象
* HttpServlet : 处理_Http_请求的类．
* HttpServletRequest : 外部的_Http_请求数据
* HttpServletResponse : 响应_Http_结果

## 关键方法
* doGet : 处理_Get_请求
* doPost : 处理_Post_请求
* doPut : 处理_Put_请求
* doDelete : 处理_Delete_请求
* doOptions : 处理_Options_请求
* doTrace　: 处理_Trace_请求
* doService : 处理_Http_请求，并根据请求类型，调用上面6个中的方法．

下面一一介绍
## HttpServlet
	_HttpServlet_用于通过_doService_方法,处理来自外部的_Get_或_Post_请求．并生成响应结果．一般需要根据业务需要，_extends_该类．
再_override_掉_doGet_或_doPost_方法．在_doGet_或_doPost_里面，完成业务逻辑的处理．
示例如下：
``` bash
/**
 * 最简单的Servlet
 * @author niejinping
 */
public class HelloServlet extends HttpServlet {


/*	最好不要这样做哈
	@Override
	protected void service(HttpServletRequest req, HttpServletResponse res)
			throws ServletException, IOException {
		res.getWriter().println("Hello World!");
	} */

	// 处理Get请求
	 public void doGet(HttpServletRequest request,
             HttpServletResponse response)
     throws ServletException, IOException{
	      PrintWriter out = response.getWriter();
	      out.println("hello world");
	  }
	 
	 // 处理Post请求
	 public void doPost(HttpServletRequest request,
             HttpServletResponse response)
     throws ServletException, IOException{
		  PrintWriter out = response.getWriter();
	      out.println("hello world");
		}
}
```

## HttpServletRequest
	_HttpServletRequest_里面，含有request的上下文，包括：
* header信息
* 路径信息
* query信息
* session信息
* cookie信息
* body请求
### header信息

### 路径信息
### query信息
### session信息
### cookie信息

## HttpServletResponse
	_HttpServletResponse_里面，含有response的上下文，包括：
* session设置信息
* cookie设置信息
* body响应信息

### session设置信息
### cookie设置信息
### body响应信息

## web.xml配置
``` bash
<?xml version="1.0" encoding="ISO-8859-1"?>
<web-app xmlns="http://java.sun.com/xml/ns/javaee"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
                      http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
  version="3.0"
  metadata-complete="true">
  <display-name>Welcome to Tomcat</display-name>
  <description>
     Welcome to Tomcat
  </description>
	<filter>
		<filter-name>helloFilter</filter-name>
		<filter-class>cn.njp.learn.io.servlet_demo.HelloFilter</filter-class>
	</filter>
	<filter-mapping>
		<filter-name>helloFilter</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>
	<servlet>
		<servlet-name>hello_world</servlet-name>
		<servlet-class>cn.njp.learn.io.servlet_demo.HelloServlet</servlet-class>
		<load-on-startup>1</load-on-startup>
	</servlet>
	<servlet-mapping>
		<servlet-name>hello_world</servlet-name>
		<url-pattern>/hello</url-pattern>
	</servlet-mapping>

</web-app>
```
## 全部代码
下面这段代码，基本上包含了所有常用的方法调用了．
HelloServlet.java
``` bash
package cn.njp.learn.io.servlet_demo;

import java.io.IOException;
import java.io.PrintWriter;
import java.util.Enumeration;
import java.util.Map;
import java.util.Set;

import javax.servlet.ServletException;
import javax.servlet.http.Cookie;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

import com.google.gson.*;
/**
 * 最简单的Servlet
 * @author niejinping
 */
public class HelloServlet extends HttpServlet {

	/**
	 * 
	 */
	private static final long serialVersionUID = 1L;

/*	@Override
	protected void service(HttpServletRequest req, HttpServletResponse res)
			throws ServletException, IOException {
		res.getWriter().println("Hello World!");
	}*/

	 public void doGet(HttpServletRequest request,
             HttpServletResponse response)
     throws ServletException, IOException{
/*		 	User user = new User(1,"niejinping");
		 	String json = new Gson().toJson(user);
			// 设置响应内容类型
			response.setContentType("text/html");
			response.setCharacterEncoding("utf-8");
			response.setHeader("cookee","mycooki");
			//response.setStatus(404);
			Cookie cookie = new Cookie("cookie1", "cookie1");
			response.addCookie(cookie);
			response.addIntHeader("h1", 111);
			response.addHeader("h2", "header2");
			// response.sendRedirect("localhost:8080/hello/index.html");
			
			// 实际的逻辑是在这里
			PrintWriter out = response.getWriter();
			out.println(json);*/
			// System.out.println("处理URI="+request.getRequestURI());
			// 设置响应内容类型
	   	  	//response.setContentType("text/html");
	      	//response.setContentType("utf-8");
	      	//response.setCharacterEncoding("utf-8");
		 	
			Cookie cookie = new Cookie("cookie1", "cookie1");
			cookie.setMaxAge(3600*12);
			response.addCookie(cookie);
			
			Cookie cookie2 = new Cookie("cookie2", "cookie2");
			cookie2.setMaxAge(3600*24);
			response.addCookie(cookie2);
	     	response.setHeader("Content-Type", "text/html;charset=UTF-8");
	      
	      	PrintWriter out = response.getWriter();
		  	String title = "使用 GET 方法读取表单数据";
	      	String docType =
	      	"<!doctype html public \"-//w3c//dtd html 4.0 " +
	      	"transitional//en\">\n";
	      	out.println(docType +
	                "<html>\n" +
	                "<head><title>" + title + "</title></head>\n" +
	                "<body bgcolor=\"#f0f0f0\">\n" +
	                "<h1 align=\"center\">" + title + "</h1>\n" +
	                "<ul>\n" +
	                "  <li><b>名字</b>："
	                + request.getParameter("first_name") + "\n" +
	                "  <li><b>姓氏</b>："
	                + request.getParameter("last_name") + "\n" +
	                "</ul>\n" +
	                "</body></html>");
	  }
	 
/*	 1. 获得客户机信息
	    getRequestURL方法返回客户端发出请求时的完整URL。
	    getRequestURI方法返回请求行中的资源名部分。
	    getQueryString 方法返回请求行中的参数部分。
	    getRemoteAddr方法返回发出请求的客户机的IP地址 
	    getRemoteHost方法返回发出请求的客户机的完整主机名
	    getRemotePort方法返回客户机所使用的网络端口号
	    getLocalAddr方法返回WEB服务器的IP地址。
	    getLocalName方法返回WEB服务器的主机名 
	    getMethod得到客户机请求方式
	 2.获得客户机请求头 
	    getHeader(string name)方法 
	    getHeaders(String name)方法 
	    getHeaderNames方法 

	 3. 获得客户机请求参数(客户端提交的数据)
	    getParameter(name)方法
	    getParameterValues（String name）方法
	    getParameterNames方法 
	    getParameterMap方法*/
	 public void doPost(HttpServletRequest request,
             HttpServletResponse response)
     throws ServletException, IOException{
		StringBuilder sb = new StringBuilder();
		
		sb.append("characterencoding : ");
		sb.append(request.getCharacterEncoding()); 
		
		sb.append("\nauthtype:");
		sb.append(request.getAuthType());
		
		sb.append("\ngetContentLength : ");
		sb.append(request.getContentLength());
		
		sb.append("\ngetContextPath : ");
		sb.append(request.getContextPath());
		
		sb.append("\ngetLocalAddr:");
		sb.append(request.getLocalAddr());
		
		sb.append("\ngetLocalName");
		sb.append(request.getLocalName());
		
		sb.append("\n requesturl : ");
		sb.append(request.getRequestURL().toString());
		
		sb.append("\n requestURI :");
		sb.append(request.getRequestURI());
		
		sb.append("\n requestQueryString : ");
		sb.append(request.getQueryString());
		
		sb.append("\n q1 =");
		sb.append(request.getParameter("q1"));
		
		sb.append("\n q4 =");
		sb.append(request.getParameter("q4"));
		
		sb.append("\n remotehost");
		sb.append(request.getRemoteHost());
		
		sb.append("\n method :");
		sb.append(request.getMethod());
		
		sb.append("\n header : h1 = ");
		sb.append(request.getHeader("h1"));
		
		sb.append("\n queryMap : ");
		//得到请求的参数Map，注意map的value是String数组类型  
		Map map = request.getParameterMap();
		Set<String> keySet = map.keySet();
		for (String key : keySet) {
			String[] values = (String[]) map.get(key);
			for (String value : values) {
				sb.append(key + "=" + value + " \t");
			}
		}
		
		for (String key : keySet) {
			String[] values = (String[]) map.get(key);
			sb.append("key : \t");
			sb.append(key);
			
			for (String value : values) {
				sb.append( value + " \t");
			}
		}
		
		sb.append("\n getSession : session = ");
		HttpSession session = request.getSession();
		sb.append(session.getId());
		Enumeration en = session.getAttributeNames();
		
		while(en.hasMoreElements()){
			sb.append("\n");
			sb.append(en.nextElement());
		}
		
		session.setAttribute("name", "Njp");
		
		sb.append("\n cookie : ");
		
		for(Cookie item : request.getCookies()){
			sb.append(item.getName() + " _ " +  item.getValue());
			sb.append("\n");
		}
		
		// 设置响应内容类型
		response.setContentType("text/html");
		response.setCharacterEncoding("utf-8");
		
		// 实际的逻辑是在这里
		PrintWriter out = response.getWriter();
		out.println(sb.toString());
		}
}
```

HelloFilter.java
``` bash
package cn.njp.learn.io.servlet_demo;

import java.io.IOException;

import javax.servlet.Filter;
import javax.servlet.FilterChain;
import javax.servlet.FilterConfig;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.http.HttpServletRequest;

public class HelloFilter implements Filter {

	public void init(FilterConfig arg0) throws ServletException {
		System.out.println("Filter 初始化");
	}

	// 过滤器，可以记录日志，可以设置编码，但不要读content哦，否则后面拿不到数据了
	public void doFilter(ServletRequest req, ServletResponse res,
			FilterChain chain) throws IOException, ServletException {
		HttpServletRequest request = (HttpServletRequest)req;
		System.out.println("拦截 URI="+request.getRequestURI());
		long before = System.currentTimeMillis(); 
		System.out.println("开始过滤... "); 
		chain.doFilter(req, res);
		long after = System.currentTimeMillis(); 
	    // 记录日志 
		System.out.println("过滤结束"); 
	     // 再次记录日志 
		System.out.println(" 请求被定位到" + ((HttpServletRequest) request).getRequestURI() 
	                + "所花的时间为: " + (after - before)); 
	}

	public void destroy() {
		System.out.println("Filter 结束");
	}
}
```