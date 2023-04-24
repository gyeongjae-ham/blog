---
title: "Test Double"
date: 2023-04-24T22:23:00+09:00
draft: false
tags: ["Test"]
categories: [""]
---

## Mock 이란

가짜 객체라고 불리며, 객체의 행위를 검증하기 위해 사용되는 가짜 객체이다

### 가짜 객체를 만드는 이유

실제 객체를 만드는데 드는 시간을 절약하고, 의존성의 연결고리가 많이 연결된 경우에 구현의 복잡함을 피하고 간단하게 테스트 검증을 하기 위해서 쓴다

## Test Double

테스트 더블은 영화를 촬영할 때 배우를 대신하여 위험한 역할을 하는 스턴트 더블이라는 용어에서 유래된 단어이다

자동화된 테스트를 작성할 때, 여러 객체들이 의존성을 갖는 경우 테스트하기 까다로운 경우가 있다. 예를 들어서 프로덕션 코드에서 `Service layer`는 `DAO`에 직접적으로 의존하고, 따라서 `Database`까지 의존하는 형태를 갖는다

## 테스트 형태

### Sociable Test

의존 관계가 간단한 경우 테스트 대상과 의존하고 있는 대상을 함께 테스트할 수 있다. `Sociable Test`에서 우리가 테스트하는 `Service` 객체는 실제로 동작하는 `DAO(Repository)` 객체를 통해 데이터베이스에 엑세스한다

### Solitary Test

테스트 대상이 아닌 의존성이 맺어진 대상의 결함으로 테스트가 실패하는 경우가 발생할 수 있다. 의존 대상 때문에 테스트가 실패하는 것을 막기 위해서 의존 대상 대신에 **실제 동작하는 것처럼 보이는 별개의 객체를 만드는 것을 생각해볼 수 있다.** 이 방식을 `Solitary Test`라고 한다

이 때, 만드는 별개의 객체를 **Test Double(테스트 더블)** 이라고 한다. 즉, 테스트 코드에서 `Service`가 데이터베이스를 실제로 조작하는 `DAO` 대신에 가짜 `DAO`를 사용하게 만드는 것이다

테스트 더블은 테스트하고자 하는 대상만 독립적으로 테스트할 수 있도록 별개로 구현한 실제 객체보다 단순한 객체를 의미한다. 테스트 대상을 `SUT(System Under Test)`라고 하고, `SUT`가 의존하고 있는 구성요소를 `DOC(Depended-on Component)`라고 하는데, 테스트 더블은 이 `DOC`와 동일한 `API`를 제공한다.

## 테스트 더블의 종류

테스트 더블도 테스트에서 수행하는 역할에 따라서 많은 종류로 나뉜다. 대표적으로는 `Dummy`, `Fake`, `Stub`, `Spy`, `Mock` 5가지로 크게 분류된다.

### Dummy

`Dummy`는 아무런 동작도 하지 않는다. 주로 파라미터로 전달되기 위해서 사용된다.

예를 들어 로깅을 하는 객체는 테스트에서는 사용되지 않을 수 있다. 그렇다면 아래와 같이 아무런 행위를 가지지 않은 `Dummy`를 만들어볼 수 있을 것이다.

```java
public interface Logger {
  void log();
}
```

```java
public class LoggerDummy implements Logger {
  @Override
  public void log() {

  }
}
```

### Fake

[![](https://miro.medium.com/v2/resize:fit:1400/format:webp/0*snrzYwepyaPu3uC9.png)](https://miro.medium.com/v2/resize:fit:1400/format:webp/0*snrzYwepyaPu3uC9.png)

`Fake`는 실제 동작하는 구현을 가지고 있지만, 프로덕션에서는 사용되기 적합하지 않은 객체이다.

예를 들어 위 그림처럼 `LoginService`가 실제 프로덕션에서는 `AccountDao`에 의존하여 데이터베이스를 사용하고 있다. 하지만 테스트코드에서는 데이터베이스 대신에 `HashMap`을 사용하는 `FakeAccountDao`를 대신 `LoginService`에 주입하여, 데이터베이스와 연결을 끊고 테스트할 수 있다.

```java
public class BoardDaoFake implements BoardDao {
    // 실제 dao 대신에 fake dao
    private final Map<Position, Piece> fakeBoard = new HashMap<>();

    // ...

    @Override
    public void createPiece(RoomId roomId, Position position, Piece piece) {
        fakeBoard.put(position, piece);
    }

    // ...
```

### Stub

[![](https://miro.medium.com/v2/resize:fit:1400/format:webp/0*KdpZaEVy6GNnrUpB.png)](https://miro.medium.com/v2/resize:fit:1400/format:webp/0*KdpZaEVy6GNnrUpB.png)

`Stub`은 `Dummy`가 마치 실제로 동작하는 것처럼 보이게 만든 객체이다. 미리 반환할 데이터가 정의되어 있으며, 메소드를 호출하였을 경우 그것을 그대로 반환하는 역할만 수행한다.

### Spy

실체 객체를 부분적으로 `Stubbing`하면서 동시에 약간의 정보를 기록하는 객체이다. 기록하는 정보에는 메소드 호출 여부, 메소드 호출 횟수 등이 포함된다.

### Mock

[![](https://miro.medium.com/v2/resize:fit:1400/format:webp/0*k7mwTF60slyMxRlm.png)](https://miro.medium.com/v2/resize:fit:1400/format:webp/0*k7mwTF60slyMxRlm.png)

**호출에 대한 기대를 명세할 수 있고, 그 명세 내용에 따라 동작하도록 프로그래밍된 객체**이다. `Mock`외의 것은 개발자가 임의로 코드를 사용하여 생성할 수 있지만, `Mock`은 라이브러리에 의해 동적으로 생성된다. 또한 설정에 따라서 충분히 `Dummy`, `Stub`, `Spy`처럼 동작할 수 있어서 **가장 강력한 테스트 더블이라고 할 수 있다**
