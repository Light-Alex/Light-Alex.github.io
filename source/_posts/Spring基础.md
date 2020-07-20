---
title: Spring基础
date: 2020-07-16 16:02:46
categories: ['Spring']
---

## 常用依赖
```xml
<dependencies>
        <!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.2.7.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
</dependencies>
```

<!--more-->

## 注解说明
- @Autowired: 自动装配，先通过类型查找，查找失败则通过名字查找
- @Qualifier(value="xxx"): 通常与@Autowired连用，@Autowired装配失败，则在IOC容器中查找id为@Qualifier中的value值的bean，将其装配到对象中
- @Nullable: 字段标记了这个注解，说明这个字段可以为null
- @Resource: 自动装配，先通过名字再通过类型
- @Component: 组件，放在类上，说明这个类被Spring管理了，就是bean!
