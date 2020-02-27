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

### Javaspring框架

- @RestController`控制层`
- @GetMapping(url)
- @PostMapping(url)
- @Service`业务层`

### SpringIOC

- @Autowired`自动注入`
- @Bean