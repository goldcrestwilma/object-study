
## 협력과 메시지

### 클라이언트-서버 모델

협력안에서

- 메시지를 전송하는 객체를 클라이언트
- 메시지를 수신하는 객체를 서버

협력은 클라이언트가 서버의 서비스를 요청하는 단방향 상호작용이다.

3

클라이언트와 서버의 역할을 동시에 수행하는 Movie

![https://github.com/goldcrestwilma/object-study/blob/main/6.%20%EB%A9%94%EC%8B%9C%EC%A7%80%EC%99%80%20%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4/example.png?raw=true](https://github.com/goldcrestwilma/object-study/blob/main/6.%20%EB%A9%94%EC%8B%9C%EC%A7%80%EC%99%80%20%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4/example.png?raw=true)

### 메시지와 메시지 전송

- 객체들이 협력하기 위해 사용할 수 있는 유일한 의사소통 수단
- 한 객체가 다른 객체에게 도움을 요청하는 것 - 메시지 전송(message seding), 메시지 패싱(message passing)
- 메시지를 전송하는 객체 - 메시지 전송자(message sender), 클라이언트
- 메시지를 수신하는 객체 - 메시지 수신자(message receiver), 서버

메시지 = 오퍼레이션명(operation name) + 인자(argument)

메시지 전송 = 메시지 + 메시지 수신자

```java
condition.isSatisfiedBy(screening);
   수신자      오퍼레이션명     인자
```

### 메시지와 메서드

메시지 수신자의 실제로 실행되는 코드는 인터페이스를 실체화한 클래스의 종류에 따라 달라질 수 있다.

메서드(method): 메시지를 수신했을 때 실제로 실행되는 함수 또는 프로시저

메시지와 메서드라는 두 가지 서로 다른 개념을 실행 시점에 연결해야 하기 때문에 컴파일 시점과 실행 시점의 의미가 달라질 수 있다.

> 메시지와 메서드의 구분은 메시지 전송자와 메시지 수신자가 느슨하게 결합될 수 있게 한다 - 유연하고 확장 가능한 코드 작성

### 퍼블릭 인터페이스와 오퍼레이션

퍼블릭 인터페이스

- 객체가 의사소통을 위해 외부에 공개하는 메시지의 집합

오퍼레이션

- 퍼블릭 인터페이스에 포함된 메시지
- 수행 가능한 어떤 행동에 대한 *추상화*

SequenceCondition 과 PeriodCondition 에 정의된 각각의 isSatisfiedBy는 실제 구현을 포함하기 때문에 메서드라고 부른다.

SequenceCondition 과 PeriodCondition 의 두 메서드는 DiscountCondition 인터페이스에 정의된 isSatisfiedBy 오퍼레이션의 여러 가능한 구현 중 하나다.

![https://github.com/goldcrestwilma/object-study/blob/main/message_operation_method.png?raw=true](https://github.com/goldcrestwilma/object-study/blob/main/message_operation_method.png?raw=true)

### 시그니처

시그니처 = 오퍼레이션의 이름과 파라미터 목록

---

## 인터페이스와 설계 품질

### 디미터 법칙(Law of Demeter)

- 객체의 내부 구조에 강하게 결합되지 않도록 협력 경로를 제한하라
- "낯선 자에게 말하지 말라(don't talk to strangers)"
- "오직 인접한 이웃하고만 말하라(only talk to your immediate neighbors)"
- "오직 하나의 도트만 사용하라(use only one dot)"

```java
screening.getMovie().getDiscountCondistions();
```

메시지 전송자가 수신자의 내부 구조에 대해 물어보고 반환받은 요소에 대해 연쇄적으로 메시지를 전송한다 - 기차 충돌(train wreck)

자신이 원하는 것이 무엇인지를 명시하고 단순히 수행하도록 요청

```java
screeening.calculateFee(audienceCount);
```

### 디미터 법칙은 하나의 도트(.)를 강제하는 규칙이 아니다

```java
 IntStream.of(1, 15, 20, 3, 9).filter(x → x > 10).distinct().count();
```

`of`, `filter`, `distinct` 메서드는 모두 IntStream 이라는 동일한 클래스의 인스턴스를 반환한다.
디미터의 법칙은 결합도와 관련된 것이며 캡슐화를 그대로 유지한다.

기차 충돌처럼 보이는 코드라도 객체의 내부 구현에 대한 어떤 정보도 외부로 노출하지 않는다면 그것은 디미터 법칙을 준수한 것이다.

### 묻지 말고 시켜라

객체는 자신이 내부적으로 보유하고 있는 정보나 메시지 전송의 결과로 얻게 되는 정보만 사용해서 의사결정을 내리게 된다. 그렇게 하면 객체는 다른 객체의 내부를 탐색하지 않아도 된다.

인터페이스는 객체가 어떻게 하는지가 아니라 무엇을 하는지 서술해야 한다.

### 의도를 드러내는 인터페이스

```java
public class PeriodCondition {
	public boolean isSatisfiedByPeriod(Screening screening) { ... }
}

public class SequenceCondition {
	public boolean isSatisfiedBySequence(Screening screening) { ... }
}
```

- 클라이언트 관점에서 isSatisfiedByPeriod 와 isSatisfiedBySequence 모두 할인 조건을 판단하는 동일한 작업을 수행한다.
- 하지만 메서드의 이름이 다르기 때문에 두 메서드의 내부 구현을 정확하게 이해하지 못한다면 두 메서드가 동일한 작업을 수행한다는 사실을 알기 어렵다.
- 메서드 수준에서 캡슐화 위반 - 메서드 변경 시 클라이언트의 코드도 함께 변경해야 한다.

> **의도를 드러내는 선택자(Intention Revealing Selector) - *Kent Beck***
메서드의 이름은 '어떻게'가 아니라 '무엇'을 하는지에 따라

```java
public class PeriodCondition {
	public boolean isSatisfiedBy(Screening screening) { ... }
}

public class SequenceCondition {
	public boolean isSatisfiedBy(Screening screening) { ... }
}

public interface DiscountCondition{
	boolean isSatisfiedBy(Screening screening);
}
```

- isSatisfiedBy 메서드가 동일한 목적을 가진다는 것을 메서드의 이름을 통해 명확하게 표현한다.

의도를 드러내는 인터페이스(Intetion Revealing Interface) - *Eric Evans*

- 구현과 관련된 모든 정보를 캡슐화하고 객체의 퍼블릭 인터페이스에 협력과 관련된 의도만 표현해야 한다.

---

## 명령-쿼리 분리 원칙

루틴

- 어떤 절차를 묶어 호출 가능하도록 이름을 부여하는 기능 모듈
- 프로시저(procedure)
    - 정해진 절차에 따라 `내부의 상태를 변경`하는 루틴
    - 부수효과 O
    - 값 반환 X
- 함수(function)
    - 어떤 절차에 따라 필요한 값을 계산해서 `반환`하는 루틴
    - 값 반환 O
    - 부수효과 X

객체의 인터페이스 측면

- 명령(Command) - 프로시저
    - 객체의 상태를 수정하는 오퍼레이션
- 쿼리(Query) - 함수
    - 객체와 관련된 정보를 반환하는 오퍼레이션

```java
public class Event {
	public boolean isSatisfied(RecurringSchedule schedule) {
		if (from.getDayOfWeek() != schedule.getDayOfWeek() ||
			  !from.toLocalTime().equals(schedule.getFrom() ||
			  !duration.equals(schedule.getDuration())) {
			reschedule(schedule);
			return false;
		}
	}
}
```

`reschedule 메서드를 호출`하고 `false 를 반환`하는 로직을 동시에 수행하고 있다.

명령과 쿼리 두 가지 활동을 동시에 수행할 경우

- 디버깅이 어렵다.
- 실행 결과를 예측하기가 어려워질 수 있다.

## 책임에 초점을 맞춰라

메시지를 먼저 선택하라

- 묻지 말고 시켜라 - 디미터 법칙
- 의도를 드러내는 인터페이스 설계 - 메시지를 전송하는 클라이언트의 관점에서 메시지의 이름을 정한다
- 명령과 쿼리를 분리
