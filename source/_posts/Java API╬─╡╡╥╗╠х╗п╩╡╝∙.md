title: java API文档一体化集成实践
date: 2016.08.26
categories: 设计 #分类
tags: [架构,java]
---
 
# springboot api文档在线生成demo
 api文档和代码同步是一个麻烦的问题。程序员中有一个笑话是：
 * 我讨厌写文档
 * 我讨厌没有文档
 
 即使有文档，也不保证文档和代码是一致的，特别是随着时间的推移，API文档和代码不同步的情况更严重。
 
 因此，解决办法就是，将API文档植入到代码当中。这样，同步性的问题基本解决。
 
 但是，需要考虑一个问题，就是API文档的安全性。文档和API一起发布在线上，那么需要避免API文档被外界窃取。
 当然，方法是有的。
 
 ## 文档代码一体化
 采用swagger开源插件，即可完美解决API文档和代码的一致性问题。
 
 ## 安全问题
 通过拦截机制，在生产环境下，拦截API文档的URL.测试环境下，打开API文档的URL.
 
 
 ## 相关依赖
```
    compile("io.springfox:springfox-swagger2:2.5.0")
    compile("io.springfox:springfox-swagger-ui:2.5.0")
```

## 配置
```
public class SwaggerDocumentationConfig {

    ApiInfo apiInfo() {
        return new ApiInfoBuilder()
            .title("MY API")
            .description("Move your app forward with the MY API")
            .license("sanhao")
            .licenseUrl("sanhao")
            .termsOfServiceUrl("sanhao")
            .version("1.0.0")
            .contact(new Contact("niejinping","www.sanhao.com", "297206021@qq.com"))
            .build();
    }

    @Bean
    public Docket customImplementation(){
        return new Docket(DocumentationType.SWAGGER_2)
                .select()
                    .apis(RequestHandlerSelectors.basePackage("com.niejinkun.springbootgradle.api"))
                    .build()
                .directModelSubstitute(org.joda.time.LocalDate.class, java.sql.Date.class)
                .directModelSubstitute(org.joda.time.DateTime.class, java.util.Date.class)
                .apiInfo(apiInfo());
    }
}
```

## 安全设置
```
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests().antMatchers("/swagger-ui.html").authenticated().and().httpBasic();

        if(api_enable == false) {
            http.authorizeRequests().antMatchers("/swagger-ui.html").denyAll();
        }

        http.csrf().disable();

    }

    @Autowired
    public void configureGlobal(AuthenticationManagerBuilder auth) throws Exception {
        auth
                .inMemoryAuthentication()
                .withUser(user).password(password).roles("USER");
    }
```

## API发布
使用注解即可完成API的发布
* ApiOperation : api基于说明
* ApiResponses : API响应结果说明
* ApiParam : API参数说明
```
@RestController
@RequestMapping("/api")
public class ApiController {
    @RequestMapping(value = "user/{name}/{password}",method= RequestMethod.GET,produces = "application/json")
    @ApiOperation(value="测试-user", notes="获取用户名",response = User.class)
    @ApiResponses(value = {
        @ApiResponse(code = 200, message = "返回结果正常", response = User.class),
        @ApiResponse(code = 201, message = "返回结果异常", response = User.class) })
    public User user(  @ApiParam(required=true, name="name", value="姓名") @PathVariable("name")String name,
                       @ApiParam(required=true, name="password", value="密码") @PathVariable("password")String password
    ){
        // DO YOUR LOGIC HERE
        return new User(1,name,password);
    }

    @ApiIgnore//使用该注解忽略这个API
    @RequestMapping(value = "/ignore")
    public String ignore() {

        return "ignore";
    }
}
```

## 效果图
输入：
```
http://localhost:8090/v1/api-docs
```
返回：josn格式API说明，可以有editor.swagger.io里面直接查看。
输入
```
http://localhost:8090/v1/swagger-ui.html
```
返回API UI界面，非常的漂亮。


## 关于坑
### 坑1
API返回的结果是json格式，因此，需要json格式转换包
```
    compile('org.codehaus.jackson:jackson-jaxrs:1.5.4')
    compile('org.codehaus.jackson:jackson-mapper-asl:1.9.13')
    compile('org.codehaus.jackson:jackson-core-asl:1.9.13')
```

### 坑2
API反序列化对象是，必须设置getter/setter
如果model不设置getter/setter，会返回序列化异常


## github
 [https://github.com/njkfei/springboot-api-docs](https://github.com/njkfei/springboot-api-docs)