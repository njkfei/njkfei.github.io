title: spring @order注解的作用
date: 2016.03.30
categories: java #分类
tags: [java,spring]
toc: true
---

 看一例子就能明白。
 在下面的示例中，Cars需要注入一个列表，通过@Order可以指定注入时的顺序。值越小，优先级越高。

```
@Component
@Order(2)
class NJK extends Student {
    public String getName() {
        return "niejinkun";
    }
}

@Component
@Order(1)
class Njp extends Student {
    public String getName() {
        return "niejinping";
    }
}

@Component
public class MyClass {
    @Autowire
    List<Student> students;

    public void printNames(String [] args) {

        for(Student item : students) {
            System.out.println(item.getName())
        }
    }
}
```
