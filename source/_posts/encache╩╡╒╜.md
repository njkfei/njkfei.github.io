title: encache　spring实战
date: 2016.03.18
categories: linux #分类
tags: [linux]
toc: true
---
## 简介
　encache是开源，标准化缓存中间件
### 优点
* 提升性能
* 减轻数据库负载
* 简单可伸缩
* 健状，功能齐全
* 本地缓存

## 安装
### maven集成　
```
    <dependency>
      <groupId>org.ehcache</groupId>
      <artifactId>ehcache</artifactId>
      <version>3.0.0.rc1</version>
    </dependency>    
```
### API简介
```

      CacheManager cacheManager = CacheManagerBuilder.newCacheManagerBuilder()
          .withCache("preConfigured",
               CacheConfigurationBuilder.newCacheConfigurationBuilder(Long.class, String.class)
               .build())
          .build(true);

      Cache<Long, String> preConfigured
          = cacheManager.getCache("preConfigured", Long.class, String.class);

      Cache<Long, String> myCache = cacheManager.createCache("myCache",
          CacheConfigurationBuilder.newCacheConfigurationBuilder(Long.class, String.class).build());

      myCache.put(1L, "da one!");
      String value = myCache.get(1L);

      cacheManager.close();
```

## demo
没搞出来．encache官网比较坑，直接抄源代码编译都过不去，差评．


## 小结 