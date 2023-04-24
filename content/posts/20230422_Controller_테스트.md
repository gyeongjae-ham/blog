---
title: "Controller 테스트 작성"
date: 2023-04-22T18:41:45+09:00
draft: false
tags: ["Test"]
categories: [""]
---

## Controller 테스트 작성

### @WebMvcTest

`Application`을 완전하게 시작학지 않고, `Web layer`를 테스트하고 싶을 때 `@WebMvcTest`를 사용한다.

### MockMvc

애플리케이션을 배포하지 않고도, 서버의 MVC 동작을 테스트하게 해주는 라이브러리다.
주로 `Controller` 레이어 테스트에 많이 사용된다.

### 테스트 이름 정하기

테스트마다 이름에 테스트하는 조건을 넣기 때문에 따로 `@DisplayName`을 설정하지 않을 수도 있지만 보다 구분하기 쉽고 명확하게 하기 위해서 사용하는 것을 고려해보는게 좋다고 생각한다.

```java
@DisplayName("[view][GET] 게시글 리스트 (게시판) 페이지 - 정상 호출")
```

팀마다 이름 규칙을 정해서 맨 앞 부분만 보더라도 어디를 어떤 테스트를 하는 건지 구분짓는 것도 좋은 방법같다.

### Controller View Test하는 메서드 하나 예시

```java
// Gradle build는 Test가 통과하지 않으면 build를 실패한다.
// 개발 중인 기능으로 Test가 실패하면 안되니까 우선 테스트 메서드별로 @Disabled 처리한다
@Disabled("구현 중")
@DisplayName("[view][GET] 게시글 리스트 (게시판) 페이지 - 정상 호출")
@Test
public void givenNothing_whenRequestingArticlesView_thenReturnsArticlesView() throws Exception {
    // Give

    // When & Then
    mvc.perform(get("/articles"))
            .andExpect(status().isOk())
            .andExpect(content().contentType(MediaType.TEXT_HTML))
            // model attribute에 articles라는 이름으로 넘어온 데이터가 있는지만 확인
            .andExpect(model().attributeExists("articles"));
}
```
