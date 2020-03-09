---

title: 常见注解的学习笔记
date:  	2020-02-22 21:56:36 +0800
category: Original
tags: [Java,Annotation,Learning]
excerpt: 用于长期可更新的常见注解的学习笔记
---

### 内置注解（JDK5开始）

- @SuppressWarning("unchecked")`压制警告`
- @Override`重载`
- @Deprecated`舍弃`

### 测试注解

- @RunWith(SpringRunner.class)

  @SpringBootTest

  - ```
    <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-test</artifactId>
              <scope>test</scope>
              <exclusions>
                  <exclusion>
                      <groupId>org.junit.vintage</groupId>
                      <artifactId>junit-vintage-engine</artifactId>
                  </exclusion>
              </exclusions>
          </dependency>
    ```

- @BeforeClass`类之前`

- @Before`之前`

- @Test`测试`

- @After`之后`

- @AfterClass`类之后`

### @interface注解

- Retention(RetentionPolicy.生命周期)
- Target({ElementType.作用位置,……})

### Spring框架

- @RestController`控制层`
  - @Controller
  - @ResponseBody
- @GetMapping(url)
- @PostMapping(url)
- @Service`业务层`

### SpringIOC

- @Autowired`自动注入`
- @Bean

### 启动类

- @SpringBootApplication
- @MapperScan

### Mybatis注解

- @Select
  SQL语句
- @Results
  查询结果字段和Mapper字段不一致时，使用Results映射
  - @Result
    包含在Results的每个字段映射实现，id为主键，property为实体类中的字段，column为数据库中查询得到的字段
  - @Many
    用于联表操作实现一对多的关系，因此`javaType = List.class`需要包装成集合
  - @One
    用于联表操作实现一对一的关系，比如用教室id对于一个教室，那么`javaType = ClassRoom.class`包装

#### 实例

```
    @Select("select * from sys_user where username = #{username}")
    @Results({
            @Result(id = true, property = "id", column = "id"),
            @Result(property = "roles", column = "id", javaType = List.class,
                many = @Many(select = "com.itheima.mapper.RoleMapper.findByUid"))
    })
```

#### 依赖包

```
<dependency>
    <groupId>tk.mybatis</groupId>
    <artifactId>mapper-spring-boot-starter</artifactId>
    <version>2.1.5</version>
</dependency>
```

