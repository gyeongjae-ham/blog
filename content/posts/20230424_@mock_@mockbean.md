---
title: "@Mock, @MockBean"
date: 2023-04-24T23:06:29+09:00
draft: false
tags: ["Test"]
categories: [""]
---

## @Mock과 @MockBean

`Mock` 객체를 선언할 때 쓰이는 어노테이션

`Spring`의 `ApplicationContext`에 `Mock` 객체들을 넣어준다

- `@Mock`
  - import org.mockito.Mock
- `@MockBean`
  - import org.springframework.boo.test.mock.mockito.MockBean
  - 스프링 테스트에서 지원

**`Spring Boot Container`가 테스트 시에 필요하고, `Bean`이 `Container`에 존재한다면 `@MockBean`을 사용하고 아닌 경우에는 `@Mock`을 사용한다**

### @Mock

필드명에 `@Mock`을 선언해주어 에러검증을 쉽게 하고, 해당 필드가 `Mock` 객체임을 더 명확하게 표시한다

**`Service`** 레이어 테스트할 때, `Repository`를 가짜 객체로 만드는 용도로 사용될 수 있다.

### @MockBean

**`@WebMvcTest`**를 이용한 테스트에서 사용할 수 있다.

`@WebMvcTest`는 `Controller`를 테스트할 때 주로 이용되며, 단일 클래스의 테스트를 진행하므로 `@MockBean`을 통해 가짜 객체를 만들어 준다. => `Controller` 객체까지만 생성되고 `Service` 객체는 생성하지 않는다.
