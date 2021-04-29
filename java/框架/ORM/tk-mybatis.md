# MyBatis 通用方法tkMybatis

## 概述

> 通用 Mapper4 是一个可以实现任意 MyBatis 通用方法的框架，项目提供了常规的增删改查操作以及`Example` 相关的单表操作。通用 Mapper 是为了解决 MyBatis 使用中 90% 的基本操作，使用它可以很方便的进行开发，可以节省开发人员大量的时间。

* 资料
   * 官方github ：https://github.com/abel533/Mapper/wiki
   * gitee：https://gitee.com/free/Mapper/wikis/pages
   * spring-boot与tk集成：https://www.jianshu.com/p/54de4b6db043
   * 尚硅谷-通用mapper：https://www.bilibili.com/video/av21297807
   
* 引入

   * 与java编码集成

   * 与spring集成

   * 与spring boot 集成

     * 依赖
     
         ```xml
         <dependency>
           <groupId>tk.mybatis</groupId>
           <artifactId>mapper-spring-boot-starter</artifactId>
           <version>版本号</version>
         </dependency>
         ```
     
     * 配置-xml
     
       ```yaml
       mapper:
         mappers:
           - tk.mybatis.mapper.common.Mapper
           - tk.mybatis.mapper.common.Mapper2
         notEmpty: true
       ```
     
       ```properties
       mapper.mappers=tk.mybatis.mapper.common.Mapper,tk.mybatis.mapper.common.Mapper2
       mapper.notEmpty=true
       ```
     
       
     
     * 配置-注解
     
       ```java
       @tk.mybatis.spring.annotation.MapperScan(basePackages = "扫描包")
           //此注解直接加在@Configuration 或启动类上
       ```
     
       


# 快速入门

# 其他参数

