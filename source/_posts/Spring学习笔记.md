---
title: Spring学习笔记
date: 2020-08-16 10:24:43
tags: ['Spring']
category: ['Spring']
mathjax: true
typora-root-url: ..
---

# 整合MyBatis

步骤：

1. 导入相关jar包
   * junit
   * mybatis
   * mysql数据库
   * spring相关的
   * aop织入
   * spring-jdbc
   * mybatis-spring

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>spring-study</artifactId>
        <groupId>org.example</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>spring-10-mybatis</artifactId>

    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.21</version>
        </dependency>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.5</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.2.7.RELEASE</version>
        </dependency>
        <!--Spring操作数据库的话，还需要一个spring-jdbc-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>5.2.7.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.9.4</version>
        </dependency>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>2.0.5</version>
        </dependency>
        <!--偷懒包-->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.12</version>
        </dependency>
    </dependencies>
</project>
```

2. 编写配置文件
3. 测试



## 回忆MyBatis

1. 编写实体类和工具类
2. 编写核心配置文件
3. 编写接口
4. 编写Mapper.xml
5. 测试



## MyBatis-Spring

1. 编写数据源配置
2. sqlSessionFactory
3. sqlSessionTemplate
4. 需要给接口加实现类
5. 将自己写的实现类，注入Spring中
6. 测试使用即可！





# 声明式事务

## 回顾事务

* 把一组业务当成一个业务来做，要么都成功，要么都失败
* 事务在项目开发中，十分重要，涉及到数据的一致性问题，不能马虎
* 确保完整性和一致性



## 事务的ACID原则

* 原子性(atomicity)
* 一致性(Consistency)
* 隔离性(Isolation)
  * 多个业务可能操作同一个资源，防止数据损坏
* 持久性(Durability)
  * 事务一旦提交，无论系统发生什么问题，结果都不会再被影响，被持久化的写到存储器中



## Spring中的事务管理

* 声明式事务：AOP
* 编程式事务：需要在代码中，进行事务的管理



思考：

为什么需要事务？

* 如果不配置事务，可能存在数据提交不一致的情况
* 如果不在Spring中去配置声明式事务，我们就需要在代码中手动配置事务
* 事务在项目的开发中十分重要，涉及到数据的一致性和完整性问题，不容马虎