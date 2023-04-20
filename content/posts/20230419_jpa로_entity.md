---
title: "SpringBoot JPA로 Entity 클래스 구성하기"
date: 2023-04-19T20:42:57+09:00
draft: false
tags: ["SpringBoot"]
categories: [""]
---

자주 사용하다보면 익숙하게 익혀지겠지만, 그래도 기록으로 남겨서 빠르게 와서 복습할 수 있도록 자세하게 남겨보도록 하려한다.

```java
package com.fastcampus.springboard.domain;

import lombok.Getter;
import lombok.Setter;
import lombok.ToString;
import org.springframework.data.annotation.CreatedBy;
import org.springframework.data.annotation.CreatedDate;
import org.springframework.data.annotation.LastModifiedBy;
import org.springframework.data.annotation.LastModifiedDate;

import javax.persistence.*;
import java.time.LocalDateTime;
import java.util.LinkedHashSet;
import java.util.Objects;
import java.util.Set;

@Getter // 전체 레벨에서는 Setter를 설정하지 말자(데이터 보호를 위해서 필요한 값만)
@ToString // 쉽게 볼 수 있도록
@Table(indexes = { // 빠르게 검색할 수 있도록 인덱스 설정
        @Index(columnList = "title"),
        @Index(columnList = "hashtag"),
        @Index(columnList = "createdAt"),
        @Index(columnList = "createdBy"),
})
// Entity에도 Auditing을 사용한다는 설정을 해줘야 한다.
@EntityListeners(AuditingEntityListener.class)
@Entity
public class Article {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Setter
    @Column(nullable = false)
    private String title; // 제목
    @Setter
    @Column(nullable = false, length = 10000) // nullable = true가 기본설정
    private String content; // 본문

    // @Column은 기본 설정을 변경하는 경우가 아니라면 생략이 가능하다
    @Setter
    private String hashtag; // 해시태그

    // Jpa 양방향 설정
    // mappedBy를 하지 않으면 양방향 관계의 두 테이블명을 합쳐서 테이블을 생성한다
    // mappedBy로 article 테이블로부터 온 것이라고 표시해 주기
    @ToString.Exclude // 순환 참조의 가능성이 있으므로 양방향 중에서 보통 one의 쪽에서 끊어준다
    @OrderBy("id")
    @OneToMany(mappedBy = "article", cascade = CascadeType.ALL)
    private final Set<ArticleComment> articleComments = new LinkedHashSet<>();

    @CreatedDate
    @Column(nullable = false)
    private LocalDateTime createdAt; // 생성일시
    @CreatedBy
    @Column(nullable = false, length = 100)
    private String createdBy; // 생성자
    @LastModifiedDate
    @Column(nullable = false)
    private LocalDateTime modifiedAt; // 수정일시
    @LastModifiedBy
    @Column(nullable = false, length = 100)
    private String modifiedBy; // 수정자

    protected Article() {
    }

    // private 제어자로 막아놓고 팩토리 메서드로 편리하게 바로 생성하자
    private Article(String title, String content, String hashtag) {
        this.title = title;
        this.content = content;
        this.hashtag = hashtag;
    }

    // 팩토리 메서드
    public static Article of(String title, String content, String hashtag) {
        return new Article(title, content, hashtag);
    }

    // 그냥 lombok의 EqualsAndHashCode를 사용하면 전체 필드를 비교하기 때문에
    // Entity 클래스에서는 고유값인 id만 가지고 비교하기 위해서 따로 생성해 줌
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Article article)) return false;
        return id != null && id.equals(article.id);
    }

    @Override
    public int hashCode() {
        return Objects.hash(id);
    }
}
```

다른 설정들은 위 클래스를 찬찬히 보면서 기능을 익히면 되고 `equals and hash` 를 구현한 부분에서

```java
@Override
public boolean equals(Object o) {
    if (this == o) return true;
    if (!(o instanceof Article article)) return false;
    return id != null && id.equals(article.id);
}
```

이 부분에서 `if (!(o instanceof Article article))` 이 부분은 `java 14` 에서 추가된 `pattern matching` 이다. 기존 `instancof` 는 비교후에 `casting`을 해주는 코드가 한 줄 더 들어갔는데 `if (!(o instanceof Article article))` 이렇게 바로 사용할 수 있도록 추가됐다.

더 간단한 코드 예시로 표현하면

```java
if (animal instanceof Cat){
	Cat cat = (Cat) animal;
  cat.meow();
  // other cat operations
}else if(animal instanceof Dog){
	Dog dog = (Dog) animal;
  dog.woof();
  // other dog operations
}
```

=> `pattern matching`

```java
if(animal instanceof Cat cat){
	cat.meow();
}else if(animal instanceof Dog dog){
	dog.woof();
}
```

## Auditing Field 분리하기

사실 이 부분은 팀의 규약에 따라서 다를 수도 있는 부분이다. 어떤 팀은 `Entity` 클래스 하나가 데이터 베이스 테이블 하나로 완전하게 일치하기를 원해서 생성일시, 생성자 등의 필드가 반복됨에도 그냥 두기를 원할 수도 있고, 어떤 팀은 반복되는 코드가 싫어서 분리할 수도 있다. 이 부분은 어떤 점이 명확히 좋다기 보다는 트레이드오프가 있는 부분이기 때문에 취향이라고 할 수 있다. 하지만 나는 공부하는 중이므로 최대한 많은 기능을 경험해 보고자 분리하는 방법도 기록해두려고 한다.

예시로 있는 `Article` 클래스에서 반복적으로 사용되는

```java
@CreatedDate
@Column(nullable = false)
private LocalDateTime createdAt; // 생성일시

@CreatedBy
@Column(nullable = false, length = 100)
private String createdBy; // 생성자

@LastModifiedDate
@Column(nullable = false)
private LocalDateTime modifiedAt; // 수정일시

@LastModifiedBy
@Column(nullable = false, length = 100)
private String modifiedBy; // 수정자
```

이 부분을 분리해보려고 한다. 분리할 수 있는 방법에는 두 가지가 있는데, 첫번째는 `@Embedded`를 사용해서 필드화 하는 것이다. 그냥 임의의 클래스 하나를 생성하고 그 안에 중복되는 필드들을 모은 다음에 저 애노테이션을 붙이고 필드로 넣어두는 방법이다.

```java
@Embedded Example example;
```

다른 한 방법은 상속을 사용하는 방법이다.

```java
@Getter
@ToString
@EntityListeners(AuditingEntityListener.class) // Entity 클래스에 붙어있던 Auditing 설정을 가져와서 적용할 수도 있다
@MappedSuperclass
public class AuditingFields {

    @DateTimeFormat(iso = DateTimeFormat.ISO.DATE_TIME) // DATE_TIME에 대한 format 설정
    @CreatedDate
    @Column(nullable = false, updatable = false) // 최초 한번만 생성되어야 하므로 updatable false 설정
    private LocalDateTime createdAt; // 생성일시

    @CreatedBy
    @Column(nullable = false, updatable = false, length = 100)
    private String createdBy; // 생성자

    @DateTimeFormat(iso = DateTimeFormat.ISO.DATE_TIME)
    @LastModifiedDate
    @Column(nullable = false)
    private LocalDateTime modifiedAt; // 수정일시

    @LastModifiedBy
    @Column(nullable = false, length = 100)
    private String modifiedBy; // 수정자

}
```

하나의 클래스를 만든 후에 `MappedSuperClass` 애노테이션을 붙이고 중복 필드들을 가져온다. 여기서 `Entity` 클래스에 붙어있던 `@EntityListeners`도 가져와서 적용할 수 있다. 편리한 출력을 위해 `@ToString`과 `@Getter`를 붙였다. 중복 필드들의 값은 내가 세팅하는 값이 아니므로 당연히 `@Setter`는 붙이지 않았다. 이렇게 한 다음 적용은 해당 `@Entity` 클래스에 상속으로 시키면 된다.

```java
public class Article extends AuditingFields {
  // 내용
}
```

첫 번째 방법은 필드로 사용될 클래스 명이 중요하다는 특징이 있다. 해당 클래스는 사실 데이터 베이스에는 없는 클래스인데 가져올 때 해당 클래스 타입으로 가져온 후 가공해야 하는 괴리감이 살짝 있다. 따라서 상속으로 사용하는 방법을 사용하는 게 더 좋을 것 같다.
