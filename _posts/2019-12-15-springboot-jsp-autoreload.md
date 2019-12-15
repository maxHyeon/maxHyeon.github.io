---
title: "SpringBoot JSP 변경사항 반영. gradle"
date: 2019-12-15 12:00:00 -0400
categories: Spring, SpringBoot, JSP, Gradle
---

1. Spring Boot Migration 중 JSP 변경사항이 서버 reload 전까지 반영 안되는 사항이 있어서 찾아보았다.

2. 설정방법
    1. Gradle Dependencies 추가  
```gradle 
dependencies {
    implementation 'org.springframework.boot:spring-boot-devtools'
```
    2. application.properties 설정 추가
```
spring.devtools.livereload.enabled=true
```
