---
title: "SpringBoot Actuator"
date: 2023-04-19T13:12:04+09:00
draft: false
tags: ["SpringBoot"]
categories: [""]
---

웹 개발을 하면서 어플리케이션을 만들 때 서비스 로직뿐만 아니라 사용자의 정보라던지 어떤 경로로 요청이 들어오는지 등 많은 것을 고려하고 개발해야 한다. `spring-boot-actuator`라는 모듈은 애플리케이션 상태를 종합적으로 정리해서 보여준다.

## Spring Boot Actuator

간단히 말하자면 Spring Boot Application의 상태를 관리해준다.

- Spring Boot Application의 상태정보(health, properties, beans, 구동된 AutoConfiguration 목록 등)을 다룰 수 있도록 자동으로 설정
- 각종 추상화 클래스(HealthIndicator 등)을 제공하여, 상태 정보를 변경할 수 있도록 Service 제공

### 노출할 항목 설정

```yml
# Actuator 감춰져 있는 모든 endpoint 정보 표출하도록 설정
management.endpoints.web.exposure.include: "*"
```

```yml
# health와 metrics 정보만 노출
management.endpoints.web.exposure.include: "health, metrics"
```

### Endpoint 경로 설정

- `management.endpoint.web.base-path(기본값 /actuator)`를 수정하여 base-path를 수정할 수 있다.
- `management.endpoint.web.path-mapping.<id>` 값을 수정하여, 특정 id의 endpoint의 경로를 수정할 수 있다.

### CORS

`spring-boot-actuator`는 기본적으로 클라우드 환경에서 관리자가 애플리케이션 상태를 확인하기 쉽도록 되어 있다. 때문에 필요한 경우에 외부 도메인명을 가진 Appication에서 각각 서비스의 상태를 확인하기 위해서 정보를 요청할 수 있다. 그럴 경우 CORS를 설정해서 사용할 수 있다.

```yml
management.endpoints.web.cors.allowed-origins=http://other-domain.com
management.endpoints.web.cors.allowed-methods=GET,POST
```

우선 이 기록에서는 Actuator가 무슨 기능을 가지고 있는지 확인해봤다. 추후에 기능을 추가하거나 하는 기능은 적용할 때 미디움에서 기록할 듯 싶다.
