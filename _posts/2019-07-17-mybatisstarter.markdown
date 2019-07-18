---
layout: post
title: "MyBatis 最初使用踩到的坑"
date: "2019-07-17 19:43:14 -0700"
category: programming
---
  MyBatis可无缝衔接Spring-Boot。可使用MyBatis Generator生成相关CRUD代码。
  配置文件位于src\main\java\resources\generatorConfig.xml
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
    <context id="sprintTest">
        <commentGenerator>
            <property name="suppressAllComments" value="true"/>
        </commentGenerator>
        <jdbcConnection driverClass="com.mysql.jdbc.Driver"
                        connectionURL="jdbc:mysql://localhost:3306/springtest"
                        userId="herts"
                        password="herts">
            <property name="nullCatalogMeansCurrent" value="true"/>
        </jdbcConnection>

        <javaModelGenerator targetPackage="com.example.mybatistest3.po" targetProject=".\src\main\java">
            <property name="enableSubPackages" value="true"/>
            <property name="trimStrings" value="true"/>
            <property name="constructorBased" value="true"/>
        </javaModelGenerator>

        <sqlMapGenerator targetPackage="com.example.resources.mapper"
                         targetProject=".\src\main\java">
        </sqlMapGenerator>

        <javaClientGenerator type="XMLMAPPER" targetPackage="com.example.mybatistest3.dao" targetProject=".\src\main\java">
            <property name="enableSubPackages" value="true"/>
        </javaClientGenerator>

        <table tableName="t_student">
        </table>
    </context>
</generatorConfiguration>
```
  具体在与MySQL一起使用时，要考虑如下问题：
  - MyBatis Generator无法区别catalog和schema，
    > Just specify table names and specify the database in the JDBC URL.
    > If you are using version 8.x of Connector/J you may notice that the generator attempts to generate code for tables in the MySql information schemas (sys, information_schema, performance_schema, etc.) This is probably not what you want! To disable this behavior, add the property "nullCatalogMeansCurrent=true" to your JDBC URL.
    > Source: http://www.mybatis.org/generator/usage/mysql.html
  - MyBatis作为Maven Plugin时，要加入dependency：

```xml
<plugin>
     <groupId>org.mybatis.generator</groupId>
     <artifactId>mybatis-generator-maven-plugin</artifactId>
     <version>1.3.2</version>
     <dependencies>
         <dependency>
             <groupId>mysql</groupId>
             <artifactId>mysql-connector-java</artifactId>
             <version>8.0.16</version>
         </dependency>
     </dependencies>
 </plugin>
```
  具体在和Spring Boot使用时，要考虑如下问题：
  - 配置application.properties:
```java
#mybatis scanner
mybatis.type-aliases-package=com.example.mybatistest3.po.springtest
mybatis.mapper-locations=classpath*:mapper/*.xml
```
  - 配置Application:
``@MapperScan(basePackages = "com.example.mybatistest3.dao")``
