title: springboot集成ssl tomcat
date: 2016.05.19
categories: java
tags: [java,https,ssl,tomcat,springboot]
---

## 证书制作

```
 C:\Users\njp\sanhao\workspace-netty>keytool -genkey -alias server -keyalg RSA -k
eystore server.jks -validity 3650
输入密钥库口令:
密钥库口令太短 - 至少必须为 6 个字符
输入密钥库口令:
再次输入新口令:
您的名字与姓氏是什么?
  [Unknown]:  nie
您的组织单位名称是什么?
  [Unknown]:  sanhao
您的组织名称是什么?
  [Unknown]:  sanhao
您所在的城市或区域名称是什么?
  [Unknown]:  beijing
您所在的省/市/自治区名称是什么?
  [Unknown]:  beijing
该单位的双字母国家/地区代码是什么?
  [Unknown]:  cn
CN=nie, OU=sanhao, O=sanhao, L=beijing, ST=beijing, C=cn是否正确?
  [否]:  yes
您的名字与姓氏是什么?
  [nie]:  niejinping
您的组织单位名称是什么?
  [sanhao]:
您的组织名称是什么?
  [sanhao]:
您所在的城市或区域名称是什么?
  [beijing]:
您所在的省/市/自治区名称是什么?
  [beijing]:
该单位的双字母国家/地区代码是什么?
  [cn]:
CN=niejinping, OU=sanhao, O=sanhao, L=beijing, ST=beijing, C=cn是否正确?
  [否]:
您的名字与姓氏是什么?
  [niejinping]:
您的组织单位名称是什么?
  [sanhao]:
您的组织名称是什么?
  [sanhao]:
您所在的城市或区域名称是什么?
  [beijing]:
您所在的省/市/自治区名称是什么?
  [beijing]:
该单位的双字母国家/地区代码是什么?
  [cn]:
CN=niejinping, OU=sanhao, O=sanhao, L=beijing, ST=beijing, C=cn是否正确?
  [否]:  y

输入 <server> 的密钥口令
        (如果和密钥库口令相同, 按回车):
```
## 买证书
上[startssl.com](startssl.com),免费的。但格式是P12格式的，需要转换为jks格式。方法如下：
```
C:\>keytool -importkeysto
re -srckeystore "niejinping2014%40gmail.com.p12" -srcstoretype pkcs12 -destkeyst
ore startssl.jks
输入目标密钥库口令:
再次输入新口令:
输入源密钥库口令:
已成功导入别名 1 的条目。
已完成导入命令: 1 个条目成功导入, 0 个条目失败或取消
```

## APP代码
```
@SpringBootApplication
@EnableAutoConfiguration(exclude = {DataSourceAutoConfiguration.class, HibernateJpaAutoConfiguration.class})
public class App {

    public static void main(String[] args) throws Exception {
        SpringApplication.run(App.class, args);
    }
```

上面的exclude必须写上，要不然加报无法连接数据源的错误。

## WEB代码
```
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
public class SslController {

    @RequestMapping("/")
    @ResponseBody
    public String helloWorld() {
        return "Hello, world";
    }

}
```

很简单，是吧。

## 测试
在浏览器中输入：
```
https://localhost:8443
```

这时浏览器会报警告，类似于12306的场景，继续前往即可。

测试OK.

## github
  [https://github.com/njkfei/springboot-ssl-tomcat](https://github.com/njkfei/springboot-ssl-tomcat)