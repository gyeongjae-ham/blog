---
title: "application.yml과 build.gradle 설명"
date: 2023-04-20T17:59:38+09:00
draft: false
tags: ["SpringBoot"]
categories: [""]
---

```yml
debug: false # debug 전체 true 하면 너무 정보가 많아서 복잡하니까 꺼주고
# Actuator 감춰져 있는 모든 endpoint 보는 설정
# 궁금하면 찾아보도록 하자
management.endpoints.web.exposure.include: "*"

# log 설정
logging:
  level:
    # 루트 패키지에서 발생하는 모든 로그들은 debug 레벨로 보겠다
    com.fastcampus.springboard: debug
    # request, response만 볼 수 있도록 web.servlet만 debug로 켜준다
    org.springframework.web.servlet: debug
    # jpa 사용할 때 query log를 볼 때 binding parameter들을 보기 위한 설정(기본은 ?로 표현됨)
    org.hibernate.type.descriptor.sql.BasicBinder: trace

spring:
  datasource:
    url: jdbc:mysql://localhost:3306/board
    username: hiyee
    password: slfl4861
    driver-class-name: com.mysql.cj.jdbc.Driver
  jpa:
    # 테스트용 데이터베이스 데이터를 resource 안에 넣을 수 있도록 설정(data.sql)
    defer-datasource-initialization: true
    # hibernate가 entity를 보고 ddl문을 자동으로 짜줌
    hibernate.ddl-auto: create
    show-sql: true
    # 현재 설정은 jpa 구현체들은 포괄하는 spring의 추상적인 설정이라서
    # hibernate에서만 있는 특징적인 property들을 설정을 해주는 부분이다.
    properties:
      # query 문을 예쁘게 formatting해서 보여주는 설정
      hibernate.format_sql: true
      # jpa 복잡한 연관관계가 매핑되어 있는 쿼리를 사용할 때 한번에 벌크로 select 해 올 수 있도록 하는 설정
      hibernate.default_batch_fetch_size: 100
  #  인메모리 데이터베이스로 사용할 h2의 웹 콘솔 사용 여부
  h2.console.enabled: false
  # resource에 만든 data.sql 작동 시기 설정
  sql.init.mode: ALWAYS
  # rest repository 설정
  data.rest:
    base-path: /api
    detection-strategy: ANNOTATED

# yml 파일은 줄 세개로 여러 document 설정을 할 수 있다.
---
# testdb용 설정
spring:
  # 테스트할 때 사용할 profile 설정
  # 사용할 Test 클래스에 @ActiveProfiles(설정한 이름)
  config.activate.on-profile: testdb
  # h2 데이터 베이스는 호환성 모드를 지원하기 때문에 실제 DB와 유사한 환경을 원하면 설정해서 사용할 수 있다.
  datasource:
    # h2 데이터 베이스를 인메모리 데이터베이스로하고 mode는 MySQL로 한다
    url: jdbc:h2:mem:board;mode=mysql
    # 드라이버 h2로 설정해주고
    driverClassName: org.h2.Driver
  sql.init.mode: ALWAYS
  # 전역 설정이다
  # 원래 TEST할 때 기본적으로 인메모리 DB를 자동으로 띄워서 실행하기 때문에
  # 위에 내가 설정한 환경으로 실행하기 전에 먼저 기본 DB로 실행해버려서 위 설정들이 의미가 없게 된다
  # Test Class에 @AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)
  # 위 설정이 번잡하고 모든 테스트에 적용할 거라면 전역으로 설정하기 위한 부분이다.
  test.database.replace: none
```

---

```groovy
dependencies {
    // spring actuator - Spring Boot Application의 상태를 관리해준다.
    implementation 'org.springframework.boot:spring-boot-starter-actuator'

    // spring web
    implementation 'org.springframework.boot:spring-boot-starter-web'

    // spring jpa - jpa 기능을 사용하도록 하는 라이브러리
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'

    // rest repository - 엔티티 클래스와 레포지토리를 이용해서 바로 restful한 api 작성을 돕는 라이브러리
    implementation 'org.springframework.boot:spring-boot-starter-data-rest'
    // Rest Repositories HAL Explorer - rest repository 기능을 쉽게 확인하고 테스트할 수 있는 라이브러리
    implementation 'org.springframework.data:spring-data-rest-hal-explorer'

    // h2 db
    runtimeOnly 'com.h2database:h2'
    // mysql db
    runtimeOnly 'com.mysql:mysql-connector-j'

    // lombok
    compileOnly 'org.projectlombok:lombok'
    // lombok 관련
    annotationProcessor 'org.projectlombok:lombok'

    // Spring Boot DevTools - 프로젝트를 자동으로 재시작해주고, 정적 파일 수정 시 바로 반영해주는 라이브러리
    developmentOnly 'org.springframework.boot:spring-boot-devtools'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}
```

프로젝트를 진행하면서 배우는 `application` 설정들에 대한 정리 글이다. 새로운게 나올 때마다 업데이트 될 예정이다.
