---
title: MyBatis学习笔记
date: 2020-08-12 11:35:50
tags: ['数据库', 'MySQL']
category: ['数据库']
mathjax: true
typora-root-url: ..
---

环境：

* JDK 1.8
* MySQL 8.0.21
* maven 3.6.1
* IDEA

回顾：

* JDBC
* MySQL
* Java基础
* Maven
* Junit



SSM框架：配置文件，最好看官网文档。



<!--more-->



# 简介

![mybatis-logo](/images/MyBatis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/mybatis-logo.png)

## 什么是MyBatis

* MyBatis 是一款优秀的**持久层框架**
* 它支持自定义 SQL、存储过程以及高级映射。
* MyBatis 免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作。
* MyBatis 可以通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。
* MyBatis 本是[apache](https://baike.baidu.com/item/apache/6265)的一个开源项目[iBatis](https://baike.baidu.com/item/iBatis), 2010年这个项目由apache software foundation 迁移到了google code，并且改名为MyBatis 。
* 2013年11月迁移到Github。



如何获得MyBatis：

* Maven仓库

```xml
<!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.5</version>
</dependency>
```

* GitHub：https://github.com/mybatis/mybatis-3
* 中文文档：https://mybatis.org/mybatis-3/zh/index.html



## 持久化

数据持久化

* 持久化就是将程序的数据在持久状态和瞬时状态转化的过程
* 内存：**断电即失**
* 数据库(Jdbc)，IO文件持久化

**为什么需要持久化？**

* 有一些对象，不能丢失
* 内存贵



## 持久层

Dao层，Service层，Controller层...

* 完成持久化工作的代码块
* 层界限十分明显



## 为什么需要MyBatis

* 帮助程序员将数据存入到数据库中
* 方便
* 传统的JDBC代码太复杂，简化，框架，自动化
* 不用Mybatis也可以，更容易上手
* 优点：
  - 简单易学：本身就很小且简单。没有任何第三方依赖，最简单安装只要两个jar文件+配置几个sql映射文件易于学习，易于使用，通过文档和源代码，可以比较完全的掌握它的设计思路和实现。
  - 灵活：mybatis不会对应用程序或者数据库的现有设计强加任何影响。 sql写在xml里，便于统一管理和优化。通过sql语句可以满足操作数据库的所有需求。
  - 解除sql与程序代码的耦合：通过提供DAO层，将业务逻辑和数据访问逻辑分离，使系统的设计更清晰，更易维护，更易单元测试。sql和代码的分离，提高了可维护性。
  - 提供映射标签，支持对象与数据库的orm字段关系映射
  - 提供对象关系映射标签，支持对象关系组建维护
  - 提供xml标签，支持编写动态sql。

**最重要一点：使用的人多：**

Spring SpringMVC SpringBoot



# 第一个MyBatis程序

思路：搭建环境-->导入MyBatis-->编写代码-->测试

## 搭建环境

**搭建数据库**

```mysql
CREATE DATABASE `mybatis`;

USE `mybatis`;

CREATE TABLE `user`(
    `id` INT(20) NOT NULL,
    `name` VARCHAR(30) DEFAULT NULL,
    `pwd` VARCHAR(30) DEFAULT NULL,
    PRIMARY KEY (`id`)
)ENGINE=INNODB DEFAULT CHARSET=utf8 COLLATE=utf8_general_ci;

INSERT INTO `user` (`id`, `name`, `pwd`) VALUES 
(1, '狂神', '123'),
(2, '张三', '123456'),
(3, '李四', '123980')
```



**新建项目**

1. 新建一个普通Maven项目

2. 删除src目录

3. 导入maven依赖

   ```xml
   <!--导入依赖-->
       <dependencies>
           <!--mysql驱动-->
           <dependency>
               <groupId>mysql</groupId>
               <artifactId>mysql-connector-java</artifactId>
               <version>8.0.21</version>
           </dependency>
           <!--mybatis-->
           <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
           <dependency>
               <groupId>org.mybatis</groupId>
               <artifactId>mybatis</artifactId>
               <version>3.5.5</version>
           </dependency>
           <!--junit-->
           <dependency>
               <groupId>junit</groupId>
               <artifactId>junit</artifactId>
               <version>4.12</version>
           </dependency>
       </dependencies>
   ```

## 创建一个模块

* 编写MyBatis的核心配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<!--configuration核心配置文件-->
<configuration>

    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://主机IP地址:端口号/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>

</configuration>
```



* 编写MyBatis工具类

```java
package com.yan.utils;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.InputStream;

// sqlSessionFactory --> sqlSession
public class MybatisUtils {

    private static SqlSessionFactory sqlSessionFactory = null;

    // 静态代码块里的变量都是局部变量，只在块内有效。
    static {
        try {
            // 第一步：获取sqlSessionFactory对象
            String resource = "mybatis-config.xml";
            InputStream inputStream = Resources.getResourceAsStream(resource);
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    /* 既然有了 SqlSessionFactory，顾名思义，我们可以从中获得 SqlSession 的实例。
        SqlSession 提供了在数据库执行 SQL 命令所需的所有方法。
        你可以通过 SqlSession 实例来直接执行已映射的 SQL 语句。*/
    public static SqlSession getSqlSession(){
        return sqlSessionFactory.openSession();
    }
}
```



## 编写代码

* 实体类

```java
package com.yan.pojo;

// 实体类
public class User {
    private int id;
    private String name;
    private String pwd;

    public User(){}

    public User(int id, String name, String pwd) {
        this.id = id;
        this.name = name;
        this.pwd = pwd;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getPwd() {
        return pwd;
    }

    public void setPwd(String pwd) {
        this.pwd = pwd;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", pwd='" + pwd + '\'' +
                '}';
    }
}
```



* Dao(Data Access Object)接口

```java
package com.yan.dao;

import com.yan.pojo.User;

import java.util.List;

// 操作User实体类
public interface UserDao {
    List<User> getUserList();
}

```



* 接口实现类(xml配置文件)，由原来的UserDaoImpl转变为一个Mapper配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!--namespace绑定一个对应的Dao/Mapper接口-->
<mapper namespace="com.yan.dao.UserDao">

    <!--select查询语句-->
    <!--id: 方法名-->
    <select id="getUserList" resultType="com.yan.pojo.User">
        select * from mybatis.user
    </select>

</mapper>
```



## 测试

**常见错误：**

1、org.apache.ibatis.binding.BindingException: Type interface com.yan.dao.UserDao is not known to the MapperRegistry.

解决方法：需要在Mybatis核心配置文件中注册每一个Mapper.XML文件

```xml
<!--每一个Mapper.XML都需要在Mybatis核心配置文件中注册！-->
    <mappers>
        <mapper resource="com/yan/dao/UserMapper.xml"/>
    </mappers>
```



2、java.lang.ExceptionInInitializerError Caused by: org.apache.ibatis.exceptions.PersistenceException: 

解决方法：在pom.xml文件中加入：

```xml
<!--在build中配置resource，来防止资源导出失败的问题-->
    <build>
        <resources>
            <resource>
                <directory>src/main/resources</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>true</filtering>
            </resource>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>true</filtering>
            </resource>
        </resources>
    </build>
```



3、1 字节的 UTF-8 序列的字节 1 无效

解决方法：将XXXMapper.xml文件中的encoding=“UTF-8"改成encoding=“UTF8"



**MapperRegistry是什么？**

核心配置文件中注册mappers



* Junit测试

```java
package com.yan.dao;

import com.yan.pojo.User;
import com.yan.utils.MybatisUtils;
import org.apache.ibatis.session.SqlSession;
import org.junit.Test;

import java.util.List;

public class UserDaoTest {

    @Test
    public void test(){

        // 第一步：获得SqlSession的实例
        SqlSession sqlSession = MybatisUtils.getSqlSession();

        // 第二步：执行SQL
        // 方式一：getMapper
        UserDao userDao = sqlSession.getMapper(UserDao.class);
        List<User> userList = userDao.getUserList();
        for(User user : userList){
            System.out.println(user);
        }

        // 关闭sqlSession
        sqlSession.close();
    }
}
```



可能遇到的问题：

1. 配置文件没有注册
2. 绑定接口错误
3. 方法名不对
4. 返回类型不对
5. Maven导出资源问题



# CRUD

## namespace

namespace中的包名要和Dao/Mapper接口的名字一致

## select

**查询语句：**

* id：对应的namespace中的方法名
* resultType：Sql语句执行的返回值类型
* parameterType：参数类型

**步骤：**

1. 编写接口

```java
// 根据id查询用户
User getUserById(int id);
```



2. 编写对应的mapper中的sql语句

```xml
<!--根据id查询用户-->
<select id="getUserById" parameterType="int" resultType="com.yan.pojo.User">
    select * from mybatis.user where id = #{id};
</select>
```



3. 测试

```java
@Test
public void test(){

    // 第一步：获得SqlSession的实例
    SqlSession sqlSession = MybatisUtils.getSqlSession();

    // 第二步：执行SQL
    // 方式一：getMapper
    UserMapper userDao = sqlSession.getMapper(UserMapper.class);
    List<User> userList = userDao.getUserList();

    //        // 方式二：
    //        List<User> userList = sqlSession.selectList("com.yan.dao.UserDao.getUserList");

    for(User user : userList){
        System.out.println(user);
    }

    // 关闭sqlSession
    sqlSession.close();
}
```



## insert

```xml
<!--添加一个用户-->
<insert id="addUser" parameterType="com.yan.pojo.User">
    <!--对象中的属性，可以直接取出来-->
    insert into mybatis.user (id, name, pwd) values (#{id}, #{name}, #{pwd});
</insert>
```



## update

```xml
<!--修改有个用户-->
<update id="updateUser" parameterType="com.yan.pojo.User">
    update mybatis.user set name=#{name},pwd=#{pwd} where id = #{id};
</update>
```



## delete

```xml
<!--删除一个用户-->
<delete id="deleteUser" parameterType="int">
    delete from mybatis.user where id = #{id};
</delete>
```



**注意点：**

增删改需要提交事务



## 分析错误

* 标签不要匹配错误
* resource绑定mapper，需要使用路径分隔符(/)
* 程序配置文件必须符合规范
* NullPointerException，没有注册到资源
* 输出的xml文件中存在中文乱码问题
* maven资源没有导出



## 使用Map传递参数

假设，实体类，或者数据库中的表，字段或者参数过多，我们应当考虑使用Map传递参数。

**UserMapper.java：**

```java
// 万能的Map
int addUser2(Map<String, Object> map);
```

**UserMapper.xml：**

```xml
<!--添加一个用户, 参数类型为Map，传递map中的key，#{key}会直接获取key对应的value-->
<insert id="addUser2" parameterType="map">
    insert into mybatis.user (id, name, pwd) values (#{userId}, #{userName}, #{userPwd});
</insert>
```

**UserDaoTest.java：**

```java
@Test
public void addUser2(){
    SqlSession sqlSession = MybatisUtils.getSqlSession();

    UserMapper mapper = sqlSession.getMapper(UserMapper.class);

    HashMap<String, Object> map = new HashMap<String, Object>();
    map.put("userId", 5);
    map.put("userName", "Hello");
    map.put("userPwd", "2233");

    mapper.addUser2(map);

    sqlSession.commit();
    sqlSession.close();
}
```



**注意：**

1. 传递Map参数，sql中 #{} 会直接提取出key的value                 parameterType="map"

2. 传递对象参数，sql中 #{} 会直接提取出对象的属性                  parameterType="com.yan.pojo.User"

3. 只有一个基本数据类型参数的情况下，可以直接在sql中取到  parameterType="int"
4. 在多个参数的情况下，可以传对象参数、Map参数、或注解！



## 模糊查询

1. 在传递参数的时候传递通配符：_代表任意一个字符 %代表0个或多个字符

```java
List<User> list = mapper.getUserLike("李_");
```

2. 在sql拼接中使用通配符：

```xml
select * from  mybatis.user where name like "%"#{value}"%";
或者：select * from  mybatis.user where name like concat('%', #{value}, '%');
```



# 配置解析

## 核心配置文件

* mybatis-config.xml
* MyBatis的配置文件包含了会深深影响MyBatis行为的设置和属性信息

```xml
properties（属性）
settings（设置）
typeAliases（类型别名）
typeHandlers（类型处理器）
objectFactory（对象工厂）
plugins（插件）
environments（环境配置）
environment（环境变量）
transactionManager（事务管理器）
dataSource（数据源）
databaseIdProvider（数据库厂商标识）
mappers（映射器）
```



## 环境配置（environments）

* MyBatis 可以配置成适应多种环境

* **不过要记住：尽管可以配置多个环境，但每个 SqlSessionFactory 实例只能选择一种环境。**

* 学会配置多套运行环境(更换environments 中参数default的值，改为想要切换的id)
* MyBtis默认的事务管理器是JDBC，数据源是POOLED



## 属性（properties）

* 可以通过properties属性引用配置文件

* 这些属性可以在外部进行配置，并可以进行动态替换。既可以在典型的 Java 属性文件中配置这些属性，也可以在 properties 元素的子元素中设置。

* 编写一个配置文件：

**db.properties：**

```properties
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://主机IP地址:端口号/mybatis?useSSL=true&useUnicode=true&characterEncoding=UTF-8
username=root
password=123456
```

**在核心配置文件中引入**：

![properties插入位置](/images/MyBatis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/properties%E6%8F%92%E5%85%A5%E4%BD%8D%E7%BD%AE.png)

注意：properties标签只能插在第一行

```xml
<!--格外添加一些属性-->
<properties resource="db.properties">
    <property name="username" value="root"/>
    <property name="password" value="123456"/>
</properties>
```

* 可以直接引用外部文件
* 可以在其中增加一些属性配置
* 如果两个文件都有同一个字段，优先使用外部的配置文件



## 类型别名（typeAliases）

* 类型别名可为 Java 类型设置一个缩写名字。 
* 它仅用于 XML 配置，意在降低冗余的全限定类名书写。

**第一种：**

```xml
<!--给实体类起别名-->
<!--第一种方式：为类型指定别名-->
<typeAliases>
    <typeAlias type="com.yan.pojo.User" alias="User"/>
</typeAliases>
```

**第二种：**

* 指定一个包名，MyBatis 会在包名下面搜索需要的 Java Bean
* 每一个在包 `domain.blog` 中的 Java Bean，在没有注解的情况下，会使用 Bean 的首字母小写的非限定类名来作为它的别名。 比如 `domain.blog.Author` 的别名为 `author`；若有注解，则别名为其注解值。

```xml
<!--第二种方式：扫描包：别名默认为首字母为小写的类名（大写也能跑）-->
<typeAliases>
    <package name="com.yan.pojo"/>
</typeAliases>
```



**使用注意：**

* 在实体类比较少的时候，使用第一种方式。

* 如果实体类比较多，建议使用第二种方式。
* 第一种可以DIY别名，第二种则不行，如果非要改，则需要在实体类上增加注解

```xml
<!--或使用注解，给类型起一个别名-->
@Alias("user")
public class User {
    ...
}
```



## 设置（settings）

这是 MyBatis 中极为重要的调整设置，它们会改变 MyBatis 的运行时行为。 

**常用：**

![常用设置1](/images/MyBatis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E5%B8%B8%E7%94%A8%E8%AE%BE%E7%BD%AE1.png)

![常用设置2](/images/MyBatis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E5%B8%B8%E7%94%A8%E8%AE%BE%E7%BD%AE2.png)



## 其他配置

- [typeHandlers（类型处理器）](https://mybatis.org/mybatis-3/zh/configuration.html#typeHandlers)
- [objectFactory（对象工厂）](https://mybatis.org/mybatis-3/zh/configuration.html#objectFactory)
- [plugins（插件）](https://mybatis.org/mybatis-3/zh/configuration.html#plugins)
  * mybatis-generator-core
  * mybatis-plus
  * 通用mapper



## 映射器（mappers）

MapperRegistry：注册绑定我们的Mapper.xml文件

**方式一：【推荐使用】**

```xml
<!--每一个Mapper.XML都需要在Mybatis核心配置文件中注册！-->
<!-- 使用相对于类路径的资源引用 -->
<mappers>
    <mapper resource="com/yan/dao/UserMapper.xml"/>
</mappers>
```

**方式二：**使用class文件绑定注册

```xml
<!--方式二-->
<!-- 使用映射器接口实现类的完全限定类名 -->
<mappers>
    <mapper class="com.yan.dao.UserMapper"/>
</mappers>
```

注意点：

* 接口和他的Mapper.xml配置文件必须同名！
* 接口和他的Mapper.xml配置文件必须在同一个包下！

**方式三：**扫描包进行绑定注册

```xml
<!--方式三-->
<!-- 将包内的映射器接口实现全部注册为映射器 -->
<mappers>
    <package name="com.yan.dao"/>
</mappers>
```

注意点：

* 接口和他的Mapper.xml配置文件必须同名！
* 接口和他的Mapper.xml配置文件必须在同一个包下！



**练习：**

* 将数据库配置文件外部引入
* 实体类别名
* 保证UserMapper接口和UserMapper.xml文件名一致，并且放在同一个包下！



## 作用域（Scope）和生命周期

![作用域（Scope）和生命周期](/images/MyBatis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E4%BD%9C%E7%94%A8%E5%9F%9F%EF%BC%88Scope%EF%BC%89%E5%92%8C%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F.png)

不同**作用域**和**生命周期**类别是至关重要的，因为错误的使用会导致非常严重的**并发问题**。

**SqlSessionFactoryBuilder：**

* 一旦创建了 SqlSessionFactory，就不再需要它了
* 类似局部变量

**SqlSessionFactory：**

* 类似数据库连接池
* SqlSessionFactory 一旦被创建就应该在应用的运行期间一直存在，**没有任何理由丢弃它或重新创建另一个实例**
* SqlSessionFactory 的最佳作用域是应用作用域
* 最简单的就是使用单例模式或者静态单例模式

**SqlSession：**

* 连接到连接池的一个请求
* SqlSession的实例不是线程安全的，因此是不能被共享的，所以它的最佳作用域是请求或方法作用域
* 用完之后需要关闭，否则资源被占用

![SqlSessionFactory](/images/MyBatis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/SqlSessionFactory.png)

**这里的每一个Mapper，就代表一个具体的业务！**



# 解决属性名和字段名不一致的问题

## **数据库中的字段：**

![数据库中的字段](/images/MyBatis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E6%95%B0%E6%8D%AE%E5%BA%93%E4%B8%AD%E7%9A%84%E5%AD%97%E6%AE%B5.png)



## **实体类中的属性：**

```java
// 实体类
public class User {
    private int id;
    private String name;
    private String password;
}
```

**数据库中的密码字段和实体类中的密码字段不一致。**



## **测试结果：**

![字段属性不一致](/images/MyBatis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E5%AD%97%E6%AE%B5%E5%B1%9E%E6%80%A7%E4%B8%8D%E4%B8%80%E8%87%B4.png)



## **SQL语句：**

```mysql
select * from mybatis.user;
select * from mybatis.user where id = #{id};

# 类型处理器

select id,name,pwd from mybatis.user;
select id,name,pwd from mybatis.user where id = #{id};
```

在类中找不到pwd属性，无法完成赋值，所以password属性的值为null



## 原因：

MyBatis 会在幕后自动创建一个 `ResultMap`，再根据属性名来映射列到 JavaBean 的属性上。如果列名和属性名不能匹配上，可以在 SELECT 语句中设置列别名（这是一个基本的 SQL 特性）来完成匹配。



## **解决方法：**

* 在SQL语句中起别名

```mysql
<!--id: 方法名-->
    <select id="getUserList" resultType="User">
        select id,name,pwd as password from mybatis.user
    </select>
```

* 返回类型使用resultMap

  结果集映射：

```
id		name 	pwd
id 		name 	password
```

```xml
<!--结果集映射-->
<resultMap id="UserMap" type="User">
    <!--column：数据库中的字段，property：实体类中的属性-->
    <!--可以部分映射-->
    <!--        <result column="id" property="id"/>-->
    <!--        <result column="name" property="name"/>-->
    <result column="pwd" property="password"/>
</resultMap>

<!--select查询语句-->
<!--id: 方法名-->
<select id="getUserList" resultMap="UserMap">
    select * from mybatis.user
</select>
```

* `resultMap` 元素是 MyBatis 中最重要最强大的元素
* `ResultMap` 的设计思想是，对简单的语句做到零配置，对于复杂一点的语句，只需要描述语句之间的关系就行了
* `ResultMap` 的优秀之处——你完全可以不用显式地配置它们
* 如果这个世界总是这么简单就好了



# 日志

## 日志工厂

如果一个数据库操作，出现了异常，我们需要排错。日志就是最好的助手！

曾经的处理方式：sout、debug

现在：日志工厂

![常用设置2](/images/MyBatis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E5%B8%B8%E7%94%A8%E8%AE%BE%E7%BD%AE2.png)

* SLF4J
* LOG4J  【掌握】
* LOG4J2 
* JDK_LOGGING 
* COMMONS_LOGGING 
* STDOUT_LOGGING  【掌握】
* NO_LOGGING



在MyBatis中具体使用哪个日志实现，在设置中设定！

**STDOUT_LOGGING 标准日志输出**

在MyBatis核心配置文件中，配置我们的日志

```xml
<!--指定 MyBatis 所用日志的具体实现，未指定时将自动查找。-->
<settings>
    <setting name="logImpl" value="STDOUT_LOGGING"/>
</settings>
```

日志输出：

![日志输出](/images/MyBatis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E6%97%A5%E5%BF%97%E8%BE%93%E5%87%BA.png)



## Log4j

### 什么是Log4j？

* Log4j是[Apache](https://baike.baidu.com/item/Apache/8512995)的一个开源项目，通过使用Log4j，我们可以控制日志信息输送的目的地是[控制台](https://baike.baidu.com/item/控制台/2438626)、文件、[GUI](https://baike.baidu.com/item/GUI)组件。

* 可以控制每一条日志的输出格式。
* 通过定义每一条日志信息的级别，我们能够更加细致地控制日志的生成过程。
* 通过一个[配置文件](https://baike.baidu.com/item/配置文件/286550)来灵活地进行配置，而不需要修改应用的代码。



### 步骤：

1. 先导入log4j的包

```xml
<!-- https://mvnrepository.com/artifact/log4j/log4j -->
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
```



2. log4j.properties

```properties
#将等级为DEBUG的日志信息输出到console和file这两个目的地，console和file的定义在下面的代码
log4j.rootLogger=DEBUG,console,file

#控制台输出的相关设置
log4j.appender.console = org.apache.log4j.ConsoleAppender
log4j.appender.console.Target = System.out
log4j.appender.console.Threshold=DEBUG
log4j.appender.console.layout = org.apache.log4j.PatternLayout
log4j.appender.console.layout.ConversionPattern=[%c]-%m%n

#文件输出的相关设置
log4j.appender.file = org.apache.log4j.RollingFileAppender
log4j.appender.file.File=./log/yan.log
log4j.appender.file.MaxFileSize=10mb
log4j.appender.file.Threshold=DEBUG
log4j.appender.file.layout=org.apache.log4j.PatternLayout
log4j.appender.file.layout.ConversionPattern=[%p][%d{yy-MM-dd}][%c]%m%n

#日志输出级别
log4j.logger.org.mybatis=DEBUG
log4j.logger.java.sql=DEBUG
log4j.logger.java.sql.Statement=DEBUG
log4j.logger.java.sql.ResultSet=DEBUG
log4j.logger.java.sql.PreparedStatement=DEBUG
```



3. 配置Log4j为日志的实现

```xml
<!--指定 MyBatis 所用日志的具体实现，未指定时将自动查找。-->
<settings>
    <!--log4j实现-->
    <setting name="logImpl" value="LOG4J"/>
</settings>
```



4. Log4j的使用，测试

![log4j日志输出](/images/MyBatis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/log4j%E6%97%A5%E5%BF%97%E8%BE%93%E5%87%BA.png)



### 简单使用

1. 在要使用Log4j的类中，导入包  import org.apache.log4j.Logger;
2. 日志对象，参数为当前类的class

```java
static Logger logger = Logger.getLogger(UserDaoTest.class);
```

3. 日志级别

```java
logger.info("info: 进入testLog4j");
logger.debug("debug: 进入testLog4j");
logger.error("error: 进入testLog4j");
```



# 分页

**思考：为什么要分页？**

* 减少数据的处理量



## 使用Limit分页

```mysql
语法：
select * from user limit startIndex, pagesize; # 从第startIndex+1条记录开始查，查询pagesize条记录
select * from user limit 3; # 查询前3条记录
```



使用Mybatis实现分页，核心SQL

1. XXXMapper接口

```java
// 分页查询用户
List<User> getUserByLimit(Map<String, Integer> map);
```



2. XXXMapper.xml

```xml
<!--分页实现查询-->
<select id="getUserByLimit" parameterType="map" resultMap="UserMap">
    select * from mybatis.user limit #{startIndex},#{pageSize};
</select>
```



3. 测试

```java
@Test
public void getUserByLimit(){
    SqlSession sqlSession = MybatisUtils.getSqlSession();

    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    HashMap<String, Integer> map = new HashMap<String, Integer>();
    map.put("startIndex", 0);
    map.put("pageSize", 2);

    List<User> userList = mapper.getUserByLimit(map);
    for(User user : userList){
        System.out.println(user);
    }

    sqlSession.close();
}
```



## RowBounds分页

不再使用SQL实现分页

1. XXXMapper接口

```java
// 分页查询用户2
List<User> getUserByBounds();
```



2. XXXMapper.xml：

```xml
<!--分页实现查询2-->
<select id="getUserByBounds" resultMap="UserMap">
    select * from mybatis.user;
</select>
```



3. 测试：

```java
@Test
public void getUserByRowBounds(){
    SqlSession sqlSession = MybatisUtils.getSqlSession();

    // RowBounds实现
    // 从第2个用户开始查，总共查2个用户
    RowBounds rowBounds = new RowBounds(1, 2);

    // 通过Java代码层面实现分页
    List<User> userList = sqlSession.selectList("com.yan.dao.UserMapper.getUserByBounds", null, rowBounds);

    for(User user : userList){
        System.out.println(user);
    }

    sqlSession.close();
}
```



## 分页插件

![分页插件](/images/MyBatis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E5%88%86%E9%A1%B5%E6%8F%92%E4%BB%B6.png)

了解即可



# 使用注解开发

## 面向接口编程

- 在真正的开发中，很多时候是使用面向接口编程
- **根本原因：解耦，可拓展，提高复用，分层开发中，上层不用管具体的实现，大家都遵守共同的标准，使得开发变得容易，规范性更好**

- 在一个面向对象的系统中，系统的各种功能是由许许多多不同的对象协作完成的。在这种情况下，各个对象内部是如何实现的，对于系统设计人员来讲不重要
- 各个对象之间的协作关系成为系统设计的关键。小到不同类之间的通信，大到各个模块之间的交互，在系统设计之初都是要着重考虑的，这也是系统设计的主要工作内容，面向接口编程就是指按照这种思想来编程



**关于接口的理解**

- 接口从更深层次的理解，应是定义（规范，约束）与实现（名实分离的原则）的分离。
- 接口的本身反映了系统设计人员对系统的抽象理解
- 接口应有两类：
  - 第一类是对一个个 个体的抽象，它对应为一个抽象体（abstract class);
  - 第二类是对一个个 个体某一方面的抽象，即形成一个抽象面（interface);
- 一个个体可能有多个抽象面，抽象体与抽象面是有区别的



**三个面向区别**

- 面向对象是指，我们考虑问题时，以对象为单位，考虑它的属性及方法
- 面向过程是指，我们考虑问题时，以一个具体的流程（事务的过程）为单位，考虑它的实现
- 接口设计与非接口设计是针对复用技术而言的，与面向对象（过程）不是一个问题，更多的体现就是对系统整体的架构



## 使用注解开发

1. 注解在接口上实现

```java
@Select("select * from user")
List<User> getUsers();
```

2. 需要在核心配置文件中绑定接口

```xml
<!--绑定接口-->
<mappers>
    <mapper class="com.yan.dao.UserMapper"/>
</mappers>
```

3. 测试

```java
@Test
public void test(){
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    List<User> users = mapper.getUsers();

    for(User user : users){
        System.out.println(user);
    }

    sqlSession.close();
}
```



本质：反射机制实现

底层：动态代理



## MyBatis执行流程

![MyBatis执行流程](/images/MyBatis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/MyBatis%E6%89%A7%E8%A1%8C%E6%B5%81%E7%A8%8B.png)

## CRUD

我们可以在工具类创建的时候实现自动提交事务

### 自动提交事务

```java
public static SqlSession getSqlSession(){
    // autocommit设置为true
    return sqlSessionFactory.openSession(true);
}
```



### 编写接口，增加注解

```java
// 操作User实体类
public interface UserMapper {

    @Select("select * from user")
    List<User> getUsers();

    // 方法存在多个参数，所有参数前面必须加上 @Param("id")注解, 此处的"id"对应于sql中的#{id}
    @Select("select * from user where id = #{id}")
    User getUserById(@Param("id") int id);

    @Select("select * from user where name = #{name}")
    User getUserByMap(Map<String, Object> map);

    @Insert("insert into user(id, name, pwd) values (#{id}, #{name}, #{password})")
    int addUser(User user);

    @Update("update user set name=#{name},pwd=#{password} where id=#{id}")
    int updateUser(User user);

    @Delete("delete from user where id = #{uid}")
    int deleteUser(@Param("uid") int id);
}
```



### 测试类

【注意：我们必须要将接口注册绑定到我们的核心配置文件中！】

```java
@Test
public void test(){
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    List<User> users = mapper.getUsers();

    for(User user : users){
        System.out.println(user);
    }

    sqlSession.close();
}

@Test
public void getUserById(){
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);

    User user = mapper.getUserById(1);

    System.out.println(user);

    sqlSession.close();
}

@Test
public void addUser(){
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);

    mapper.addUser(new User(6, "小红", "123"));

    sqlSession.close();
}

@Test
public void getUserByMap(){
    SqlSession sqlSession = MybatisUtils.getSqlSession();

    UserMapper mapper = sqlSession.getMapper(UserMapper.class);

    HashMap<String, Object> map = new HashMap<String, Object>();
    map.put("name", "小红");

    User user = mapper.getUserByMap(map);

    sqlSession.close();
}

@Test
public void updateUser(){
    SqlSession sqlSession = MybatisUtils.getSqlSession();

    UserMapper mapper = sqlSession.getMapper(UserMapper.class);

    mapper.updateUser(new User(5, "小强", "123"));

    sqlSession.close();
}

@Test
public void deleteUser(){
    SqlSession sqlSession = MybatisUtils.getSqlSession();

    UserMapper mapper = sqlSession.getMapper(UserMapper.class);

    int i = mapper.deleteUser(6);

    sqlSession.close();
}
```



### 关于@Param()注解

* 基本数据类型的参数或者String类型，需要加上
* 引用类型不需要加
* 如果只有一个基本类型，可以忽略，但是建议还是加上
* 在SQL中引用的就是我们这里的@Param()中设定的属性名



### #{} ${} 区别

${}注入会直接注入，即注入的变量是什么就注入什么，会有`sql注入`问题

而#{}会在你注入的变量上加上“”

因此当你采用${}注入时，如果注入的内容恰好是sql语句，则会改变你开始的sql语句，#{}则不会



# Lombok

```xml
Project Lombok is a java library that automatically plugs into your editor and build tools, spicing up your java.
Never write another getter or equals method again, with one annotation your class has a fully featured builder, Automate your logging variables, and much more.
```

* Java library
* Plugs
* build tools
* with one annotation your class



**使用步骤：**

1. 在idea中安装Lombok插件

![idea中安装Lombok插件](/images/MyBatis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/idea%E4%B8%AD%E5%AE%89%E8%A3%85Lombok%E6%8F%92%E4%BB%B6.png)



2. 在Maven中添加Lombok依赖

```xml
<!-- https://mvnrepository.com/artifact/org.projectlombok/lombok -->
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.12</version>
</dependency>
```



3. 在实体类上加注解：

```java
// 实体类
@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {
    private int id;
    private String name;
    private String password;
}
```



**全部注解：**

```java
@Getter and @Setter
@FieldNameConstants
@ToString
@EqualsAndHashCode
@AllArgsConstructor, @RequiredArgsConstructor and @NoArgsConstructor
@Log, @Log4j, @Log4j2, @Slf4j, @XSlf4j, @CommonsLog, @JBossLog, @Flogger, @CustomLog
@Data
@Builder
@SuperBuilder
@Singular
@Delegate
@Value
@Accessors
@Wither
@With
@SneakyThrows
@val
@var
experimental @var
@UtilityClass
```



**常用：**

```java
@Data生成方法：无参构造、get、set、toString、hashcode、equals
@AllArgsConstructor：生成有参构造
@NoArgsConstructor：生成无参构造
@ToString：生成toString()方法
@EqualsAndHashCode：生成equals()和hashCode()方法
```



* 放在类上，生成所有属性的方法
* 放在属性上，生成该属性的方法



# 多对一处理

### 多对一：

![多对一](/images/MyBatis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E5%A4%9A%E5%AF%B9%E4%B8%80.png)

* 多个学生，对应一个老师

* 对于学生而言，关联，多个学生，关联一个老师【多对一】
* 对于老师而言，集合，一个老师有很多学生【一对多】



### 实体类：

#### 学生实体类

```java
package com.yan.pojo;

import lombok.Data;

@Data
public class Student {

    private int id;
    private String name;

    // 学生需要关联一个老师
    private Teacher teacher;
}
```



#### 老师实体类

```java
package com.yan.pojo;

import lombok.Data;

@Data
public class Teacher {
    private int id;
    private String name;
}
```



### SQL：

```mysql
CREATE TABLE `teacher`(
    `id` INT(10) NOT NULL,
    `name` VARCHAR(30) DEFAULT NULL,
    PRIMARY KEY (`id`)
)ENGINE=INNODB DEFAULT CHARSET=utf8

INSERT INTO `teacher` (`id`, `name`) VALUES ('1', '秦老师');

CREATE TABLE `student`(
    `id` INT(10) NOT NULL,
    `name` VARCHAR(30) DEFAULT NULL,
    `tid` INT(10) DEFAULT NULL,
    PRIMARY KEY (`id`),
    KEY `fktid` (`tid`),
    CONSTRAINT `fktid` FOREIGN KEY (`tid`) REFERENCES `teacher` (`id`)
)ENGINE=INNODB DEFAULT CHARSET=utf8

INSERT INTO `student` (`id`, `name`, `tid`) VALUES ('1', '小明', '1');
INSERT INTO `student` (`id`, `name`, `tid`) VALUES ('2', '小红', '1');
INSERT INTO `student` (`id`, `name`, `tid`) VALUES ('3', '小张', '1');
INSERT INTO `student` (`id`, `name`, `tid`) VALUES ('4', '小李', '1');
INSERT INTO `student` (`id`, `name`, `tid`) VALUES ('5', '小王', '1');
```



### 架构：

![数据库中的多对一](/images/MyBatis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E6%95%B0%E6%8D%AE%E5%BA%93%E4%B8%AD%E7%9A%84%E5%A4%9A%E5%AF%B9%E4%B8%80.png)

### 测试环境搭建

1. 导入Lombok
2. 新建实体类Teacher，Student
3. 建立XXXMapper接口
4. 建立XXXMapper.xml文件
5. 在核心配置文件中绑定注册XXXMapper接口或者XXXMapper.xml文件 【方式多，可随意选】
6. 测试查询是否成功



### 连表查询

#### 方式一：子查询

```xml
<!--方式一：子查询-->
<!--
连表思路：
    1. 查询所有的学生信息
    2. 根据查询出来的学生的tid，寻找对应的老师！
-->
<select id="getStudent" resultMap="StudentTeacher">
    select * from student;
</select>

<resultMap id="StudentTeacher" type="Student">
    <result property="id" column="id"/>
    <result property="name" column="name"/>
    <!--复杂的属性，我们需要单独处理
            关联：association
            集合：collection
        -->
    <association property="teacher" column="tid" javaType="Teacher" select="getTeacher"/>
</resultMap>

<select id="getTeacher" resultType="Teacher">
    select * from teacher where id = #{tid};
</select>
```



#### 方式二：按结果嵌套处理

```xml
<!--方式二：按结果嵌套处理-->
<select id="getStudent2" resultMap="StudentTeacher2">
    select `student`.id sid, `student`.name sname, `teacher`.name tname
    from `student`, `teacher`
    where `student`.tid = `teacher`.id;
</select>

<resultMap id="StudentTeacher2" type="Student">
    <result property="id" column="sid"/>
    <result property="name" column="sname"/>
    <association property="teacher" javaType="Teacher">
        <result property="name" column="tname"/>
    </association>
</resultMap>
```



**回顾 Mysql 多对一查询方式：**

* 子查询
* 按结果嵌套处理



# 一对多处理

比如：一个老师拥有多个学生！

对于老师而言，就是一对多关系！



1. 环境搭建，和刚才一样

### 实体类

#### 学生实体类：

```java
package com.yan.pojo;

import lombok.Data;

@Data
public class Student {

    private int id;
    private String name;
    private int tid;
}
```



#### 老师实体类：

```java
package com.yan.pojo;

import lombok.Data;

import java.util.List;

@Data
public class Teacher {
    private int id;
    private String name;

    // 一个老师拥有多个学生
    private List<Student> students;
}
```



## 按照结果嵌套处理

```xml
<!--方式一：按结果嵌套查询-->
<select id="getTeacher" resultMap="TeacherStudent">
    select `student`.id sid, `student`.name sname, `student`.tid,`teacher`.name tname
    from `student`, `teacher`
    where `student`.tid=`teacher`.id and `teacher`.id = #{tid};
</select>

<resultMap id="TeacherStudent" type="Teacher">
    <result property="id" column="tid"/>
    <result property="name" column="tname"/>
    <!--复杂的属性，我们需要单独处理
        关联：association
        集合：collection
        javaType：Java类型
        ofType：用于容器，值为容器中存储的数据类型
        -->
    <collection property="students" javaType="ArrayList" ofType="Student">
        <result property="id" column="sid"/>
        <result property="name" column="sname"/>
        <result property="tid" column="tid"/>
    </collection>
</resultMap>
```



## 按照查询嵌套处理（子查询）

```xml
<!--方式二：子查询-->
<select id="getTeacher2" resultMap="TeacherStudent2">
    select `id`,`name` from teacher where id = #{tid};
</select>
<resultMap id="TeacherStudent2" type="Teacher">
    <result property="id" column="id"/>
    <result property="name" column="name"/>
    <collection property="students" column="id" javaType="ArrayList" ofType="Student" select="getStudentByTeacherId"/>
</resultMap>
<select id="getStudentByTeacherId" resultType="Student">
    select * from student where tid = #{id};
</select>
```



## 小结

1. 关联 - assonciation 【多对一】
2. 集合 - collection      【一对多】
3. javaType：用来指定实体类中属性的类型
4. ofType：用于集合，指定泛型约束类型



## 注意点

1. 保证SQL可读性，尽量保证通俗易懂
2. 注意一对多和多对一中属性名和字段的问题
3. 如果问题不好排查错误，可以使用日志，建议使用Log4j



## 面试高频

* MySQL引擎
* InnoDB底层原理
* 索引
* 索引优化



# 动态SQL

**什么是动态SQL：就是指根据不同的条件生成不同的SQL条件**

利用动态 SQL，可以彻底摆脱根据不同条件拼接 SQL 语句的痛苦。

```xml
使用动态 SQL 并非一件易事，但借助可用于任何 SQL 映射语句中的强大的动态 SQL 语言，MyBatis 显著地提升了这一特性的易用性。

如果你之前用过 JSTL 或任何基于类 XML 语言的文本处理器，你对动态 SQL 元素可能会感觉似曾相识。在 MyBatis 之前的版本中，需要花时间了解大量的元素。借助功能强大的基于 OGNL 的表达式，MyBatis 3 替换了之前的大部分元素，大大精简了元素种类，现在要学习的元素种类比原来的一半还要少。

if
choose (when, otherwise)
trim (where, set)
foreach
```



## 搭建环境

```mysql
CREATE TABLE `blog`(
    `id` VARCHAR(50) NOT NULL COMMENT '博客id',
    `title` VARCHAR(100) NOT NULL COMMENT '博客标题',
    `author` VARCHAR(30) NOT NULL COMMENT '博客作者',
    `create_time` DATETIME NOT NULL COMMENT '创建时间',
    `views` INT(30) NOT NULL COMMENT '浏览量'
)ENGINE=INNODB DEFAULT CHARSET=utf8
```



## 创建基础工程

1. 导包（Maven）
2. 编写配置文件
3. 编写实体类

```java
@Data
public class Blog {
    private int id;
    private String title;
    private String author;
    private Date createTime;
    private int views;
}
```



4. 编写实体类对应的XXXMapper接口和XXXMapper.xml文件



## IF

根据条件包含 where 子句的一部分

```xml
<select id="queryBlogIF"  parameterType="map" resultType="Blog">
    select * from `blog` where 1=1
    <if test="title != null">
        and title = #{title}
    </if>
    <if test="author != null">
        and author = #{author}
    </if>
</select>
```



## choose (when, otherwise)

有时候，我们不想使用所有的条件，而只是想从多个条件中**选择一个使用**。针对这种情况，MyBatis 提供了 choose 元素，它有点像 Java 中的 switch 语句。

```xml
<select id="queryBlogChoose" parameterType="Map" resultType="Blog">
    select * from `blog`
    <where>
        <choose>
            <when test="title != null">
                title = #{title}
            </when>
            <when test="author != null">
                and author = #{author}
            </when>
            <otherwise>
                and views = #{views}
            </otherwise>
        </choose>
    </where>
</select>
```



## trim (where, set)

* where：*where* 元素只会在子元素返回任何内容的情况下才插入 “WHERE” 子句。而且，若子句的开头为 “AND” 或 “OR”，*where* 元素也会将它们去除。

```xml
<select id="queryBlogIF"  parameterType="map" resultType="Blog">
    select * from `blog`
    <where>
        <if test="title != null">
            and title = #{title}
        </if>
        <if test="author != null">
            and author = #{author}
        </if>
    </where>

</select>
```

* *set* 元素会动态地在行首插入 SET 关键字，并会删掉额外的逗号（这些逗号是在使用条件语句给列赋值时引入的）

```xml
<insert id="updateBlog" parameterType="Map" >
    update `blog`
    <set>
        <if test="title != null">
            `title` = #{title},
        </if>
        <if test="author != null">
            `author` = #{author}
        </if>
    </set>
    where `id` = #{id};
</insert>
```



## SQL字段

把SQL语句抽取出来，进行复用

1. 使用SQL标签抽取公共部分

```xml
<sql id="if-title-author">
    <if test="title != null">
        title = #{title}
    </if>
    <if test="author != null">
        and author = #{author}
    </if>
</sql>
```

2. 在需要的地方使用Include标签引用即可

```xml
<select id="queryBlogIF"  parameterType="map" resultType="Blog">
    select * from `blog`
    <where>
		<include refid="if-title-author"></include>
    </where>
</select>
```

**注意：**

* 最好基于单表来定义SQL片段
* 不要存在where标签和set标签



## Foreach

动态 SQL 的另一个常见使用场景是对集合进行遍历（尤其是在构建 IN 条件语句的时候）。



![foreach](/images/MyBatis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/foreach.png)



SQL：

```mysql
select * from blog where id in (1, 2, 3)
```

```xml
<select id="queryBlogForeach" parameterType="Map" resultType="Blog">
    select * from `blog`
    <where>
        `id` in
        <foreach collection="list" item="id" open="(" separator="," close=")">
            #{id}
        </foreach>
    </where>
</select>

或者:
<select id="queryBlogForeach" parameterType="Map" resultType="Blog">
    select * from `blog`
    <where>
        <foreach collection="list" item="id" open="(" separator="or" close=")">
            id = #{id}
        </foreach>
    </where>
</select>
```



## 总结

所谓动态SQL，本质还是SQL语句，只是我们可以在SQL层面，执行一些逻辑代码

动态就是在拼接SQL语句，我们只要保证SQL的正确性，按照SQL的格式，去排列组合就可以了

建议：

* 先在MySQL中写出完整的SQL，再去对应修改成动态SQL即可



# 缓存

## 简介

```xml
查询：连接数据库，耗资源
一次查询的结果，给他暂存在一个可以直接取到的地方-->内存：缓存
我们再次查询相同数据的时候，直接走缓存，就不用走数据库了
```



1. 什么是缓存[cache]？
   * 存在内存中的临时数据
   * 将用户经常查询的数据存放在缓存（内存）中，用户去查询数据就不用从磁盘上（关系型数据库文件）查询，从缓存中查询，从而提高查询效率，解决高并发系统的问题
2. 为什么使用缓存？
   * 减少和数据库的交互次数，减少系统开销，提高系统效率
3. 什么样的数据能使用缓存？
   * 经常查询并且不经常改变的数据



## MyBatis缓存

* MyBatis包含一个非常强大的查询缓存特性，他可以非常方便的定制和配置缓存。缓存可以极大的提升查询效率。
* MyBatis系统中默认定义了两级缓存：**一级缓存**和**二级缓存**
  * 默认情况下，只有一级缓存开启。（SqlSession级别的缓存，也称为本地缓存）
  * 二级缓存需要手动开启和配置，他是基于namespace级别的缓存
  * 为了提高扩展性，MyBatis定义了缓存接口Cache。我们可以通过实现Cache接口来自定义二级缓存



## 一级缓存

* 一级缓存也叫本地缓存：
  * 与数据库同一次会话期间查询到的数据会放在本地缓存中
  * 以后如果需要获取相同的数据，直接从缓存中拿，没必要再去查询数据库



测试步骤：

1. 开启日志
2. 测试在一个Session中查询两次相同的记录
3. 查看日志输出

![一级缓存查询结果](/images/MyBatis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E4%B8%80%E7%BA%A7%E7%BC%93%E5%AD%98%E6%9F%A5%E8%AF%A2%E7%BB%93%E6%9E%9C.png)

4. 缓存失效的情况：
   * 查询不同的东西
   * 增删改操作，可能会改变原来的数据，所以必定会刷新缓存
   * 查询不同的Mapper
   * 手动清理缓存

   ```java
   import com.yan.dao.UserMapper;
   import com.yan.pojo.User;
   import com.yan.utils.MybatisUtils;
   import org.apache.ibatis.session.SqlSession;
   import org.junit.Test;
   
   public class MyTest {
   
       @Test
       public void test(){
           SqlSession sqlSession = MybatisUtils.getSqlSession();
   
           UserMapper mapper = sqlSession.getMapper(UserMapper.class);
           User user = mapper.queryUserById(1);
           System.out.println(user);
   
           sqlSession.clearCache(); // 手动清理缓存
           System.out.println("========================");
           User user2 = mapper.queryUserById(1);
           System.out.println(user2);
   
           System.out.println(user==user2);
   
           sqlSession.close();
       }
   }
   ```

小结：一级缓存默认是开启的，只在一次SqlSession中有效，也就是拿到连接到关闭连接这个区间段！

一级缓存就是一个Map



## 二级缓存

* 二级缓存也叫全局缓存，一级缓存作用域太低了，所以诞生了二级缓存
* 基于namespace级别的缓存，一个名称空间，对应一个二级缓存
* 工作机制：
  * 一个会话查询一条数据，这个数据就会被放在当前会话的一级缓存中
  * 如果当前会话关闭了，这个会话对应的的一级缓存就没了；但是我们想要的是，会话关闭了，一级缓存中的数据被保存到二级缓存中
  * 新的会话查询信息，就可以从二级缓存中获取内容
  * 不同的mapper查出的数据会放在自己对应的缓存（map）中



步骤：

1. 开启全局缓存

```xml
<!--显示的开启全局缓存(二级缓存)-->
<setting name="cacheEnabled" value="true"/>
```

2. 在要使用二级缓存的Mapper.xml中开启

```xml
<!--在当前Mapper.xml中使用二级缓存-->
<cache/>
```

也可以自定义参数

```xml
<!--在当前Mapper.xml中使用二级缓存-->
<cache eviction="FIFO"
       flushInterval="60000"
       size="512"
       readOnly="true"/>
```

3. 测试

   1. 问题：我们需要将实体类序列化，否则就会报错

   ```java
   Caused by: java.io.NotSerializableException: com.yan.pojo.User
   ```



小结：

* 只要开启了二级缓存，在同一个Mapper.xml下就有效
* 所有的数据都会先放在一级缓存中
* 只有当会话提交，或者关闭的时候，才会提交到二级缓存中



## 缓存执行顺序

![缓存执行顺序](/images/MyBatis%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E7%BC%93%E5%AD%98%E6%89%A7%E8%A1%8C%E9%A1%BA%E5%BA%8F.png)



## 自定义缓存-ehcache

```xml
Ehcache是一种广泛使用的开源Java分布式缓存，主要面向通用缓存
```



要在程序中使用ehcache，先要导包

```xml
<!-- https://mvnrepository.com/artifact/org.mybatis.caches/mybatis-ehcache -->
<dependency>
    <groupId>org.mybatis.caches</groupId>
    <artifactId>mybatis-ehcache</artifactId>
    <version>1.2.1</version>
</dependency>
```



在Mapper中指定使用ehcache缓存

```xml
<!--使用ehcache缓存-->
<cache type="org.mybatis.caches.ehcache.EhcacheCache"/>
```



ehcache.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="http://ehcache.org/ehcache.xsd"
         updateCheck="false">

    <!--
    diskStore: 缓存路径，ehcache分为内存和磁盘两级，此属性定义磁盘的缓存位置。参数解释如下：
    user.home - 用户主目录
    user.dir - 用户当前工作目录
    java.io.tmpdir - 默认临时文件路径
    -->
    <diskStore path="./tmpdir/Tmp_EhCache"/>

    <defaultCache
            eternal="false"
            maxElementsInMemory="10000"
            overflowToDisk="false"
            diskPersistent="false"
            timeToIdleSeconds="1800"
            timeToLiveSeconds="259200"
            memoryStoreEvictionPolicy="LRU"/>

    <cache
            name="cloud_user"
            eternal="false"
            maxElementsInMemory="5000"
            overflowToDisk="false"
            diskPersistent="false"
            timeToIdleSeconds="1800"
            timeToLiveSeconds="1800"
            memoryStoreEvictionPolicy="LRU"/>
</ehcache>
```



**如今：**

大多用redis数据库做缓存，K-V