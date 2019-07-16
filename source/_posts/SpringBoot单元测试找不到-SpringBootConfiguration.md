---
title: SpringBoot单元测试找不到SpringBootConfiguration注解
date: 2018-01-09 11:07:45
tags:
  - SpringBoot
categories:
  - 问题
---

进行Jpa单元测试时出现错误：java.lang.IllegalStateException: Unable to find a @SpringBootConfiguration, you need to use @ContextConfiguration or @SpringBootTest(classes=...) with your test

<!-- more -->

**当Application跟测试类不在一个包层次结构分支中时会报错**，例如Application路径为com.example.demo.Application，测试类路径为com.example.repository.RepositoryTest时会报错

#### 解决办法

* ##### 第一种方法
  修改后Application路径为com.example.Application，测试类路径为com.example.repository.RepositoryTest，这样测试类就能找到@SpringBootConfiguration

* ##### 第二种方法
  或者 在测试类中改为@SpringBootTest(classes=com.example.demo.Application.class)


