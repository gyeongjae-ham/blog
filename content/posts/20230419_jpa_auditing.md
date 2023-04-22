---
title: "JPA Auditing"
date: 2023-04-19T20:18:08+09:00
draft: false
tags: ["JPA"]
categories: [""]
---

`Java`에서 `ORM` 기술인 `JPA` 를 사용해서 도메인(엔티티)을 관계형 데이터베이스 테이블에 매핑할 때 공통적으로 도메인들이 가지고 있는 필드나 컬럼들이 존재한다. 예를 들면 생성일자, 생성자, 수정일자, 수정자 등의 필드 및 컬럼이 있다.

도메인마다 공통적으로 필요하다면 결국 코드가 중복으로 작성될 수밖에 없게 되고 중복을 무척이나 싫어하는 개발자들은 이 문제를 해결하기 위해서 `JPA Auditing` 이라는 기능을 개발하게 됩니다.

`Audit` 은 감시하다, 감사하다 라는 뜻으로 `Spring Date JPA`에서 시간에 대해서 자동으로 값을 넣어주는 기능이다. 도메인(엔티티)을 영속성 컨텍스트에 저장하거나 조회를 수행한 후에 `update` 하는 경우 매번 시간 데이터를 입력해 줘야 하는데, 이 기능을 사용하면 자동으로 시간을 매핑해서 테이블에 넣어준다.

```java
package com.fastcampus.springboard.config;

import org.springframework.context.annotat
import org.springframework.context.annotat
import org.springframework.data.domain.Aud
import org.springframework.data.jpa.reposi

import java.util.Optional;

@EnableJpaAuditing // JPA Auditing 기능 활성화
@Configuration
public class JpaConfig {

    @Bean
    public AuditorAware<String> auditorAwa
        return () -> Optional.of("hiyee");
    }
}

```

위와 같이 `config` 패키지에 `JpaConfig` 클래스에 설정해주고 안에 설정해주고 쓰면 된다. 지금 `@Bean`은 아직 `Spring Boot Security` 가 붙지 않은 상태에서 임의로 모든 수정자의 이름을 지정해준 모습이다. `Spring Boot Security`가 구현된다면 수정될 부분이다.

```java
@CreatedDate
private LocalDateTime createdAt; // 생성일시

@CreatedBy
private String createdBy; // 생성자

@LastModifiedDate
private LocalDateTime modifiedAt; // 수정일시

@LastModifiedBy
private String modifiedBy; // 수정자
```

이처럼 `Entity` 에서 해당 부분에 해당하는 애노테이션을 달아주기만 하면 사용할 수 있다.
`JpaConfig` 부분은 `Spring Boot Security` 구현 후에 이 글에 추가적으로 업데이트 할 예정이다.

`Entity` 클래스에서도 `Auditing`을 사용한다는 애노테이션을 달아줘야 한다. `Entity` 클래스 위에

```java
@EntityListeners(AuditingEntityListener.class)
```

를 표시해줘야 해당 클래스에서 `Auditing` 기능이 동작한다
