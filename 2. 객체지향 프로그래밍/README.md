## 2.객체지향 프로그래밍
객체지향 프로그램을 작성할 때 가정 먼저 고려하는 것은 무엇인가?

클래스? → 속성과 메소드

객체지향은 말 그대로 객체를 지향하는 것이다.

클래스가 아닌 `객체`에 초점을 맞춰야 한다.

> 객체: 상태(state)와 행동(behavior)을 함께 가지는 복합적인 존재

1. 어떤 클래스가 필요한지를 고민하기 전에 어떤 객체들이 필요한지 고민하라.
    - 클래스의 윤곽을 잡기 위해서는 어떤 객체들이 어떤 상태와 행동을 갖는지를 먼저 결정해야 한다.
2. 객체를 독립적인 존재가 아니라 기능을 구현하기 위해 협력하는 공동체의 일원으로 봐야한다.
    - 객체 지향적으로 생각하고 싶다면 객체를 고립된 존재로 바라보지 말고 협력에 참여하는 협력자로 바라봐야 한다.

---
### 도메인의 구조를 따르는 프로그램 구조

소프트웨어는 사용자가 원하는 어떤 문제를 해결하기 위해 만들어진다.

문제를 해결하기 위해 사용자가 프로그램을 사용하는 분야를 `도메인` 이라고 부른다.

---
### 클래스 구현

클래스는 내부와 외부로 구분되며 훌륭한 클래스를 설계하기 위한 핵심은 어떤 부분을 외부에 `공개`하고 어떤 부분을 `감출지`를 결정하는 것이다. → 왜? 경계의 명확성이 객체의 자율성을 보장하기 때문이다.

---
### 자율적인 객체

`캡슐화`라는 특징을 갖고 있으며, 외부에서의 접근을 통제할 수 있는 `접근 제어(access control)` 메커니즘도 함께 제공한다. 접근 제어를 위해 public, protected, private과 같은 `접근 수정자(access modifier)`를 제공한다. → 이와 같이 접근을 통제하는 이유는 객체를 자율적인 존재로 만들기 위함 → 외부의 간섭을 최소화

퍼블릭 인터페이스(public interface): 외부에서 접근 가능한 부분

구현(implementation): 외부에서 접근 불가능하고 오직 내부에서만 접근 가능한 부분

---
### 프로그래머의 자유

프로그래머의 역할

클래스 작성자(class creator)

- 새로운 데이터 타입을 프로그램에 추가
- 클라이언트 프로그래머에게 필요한 부분만 공개하고 나머지는 꽁꽁 숨김

클라이언트 프로그래머(client programmer)

- 클래스 작성자가 추가한 데이터 타입을 사용
- 필요한 클래스들을 엮어서 애플리케이션을 빠르고 안정적으로 구축하는 것이 목표

> `접근 제어` 매커니즘은 클래스의 내부와 외부를 명확하게 경계 지을 수 있게 하는 동시에 클래스 작성자가 내부 구현을 은닉할 수 있게 해준다. 클라이언트 프로그래머가 실수로 숨겨진 부분에 접근하는 것을 막아준다.

클래스를 개발할 때 인터페이스와 구현을 깔끔하게 분리하기 위해 노력해야 한다.

설계가 필요한 이유는 변경을 관리하기 위함이다.

변경될 가능성이 있는 세부적인 구현 내용을 `private` 영역 안에 감춤으로써 변경으로 인한 혼란을 최소활 수 있다.

---
### 협력하는 객체들의 공동체

```java
public class Reservation {
	private Customer customer;
	private Screening screening;
	private Money fee;
	private int audienceCount;

	public Reservation(Customer customer, Screening screening, Money fee, int audienceCount) {
		this.customer = customer;
		this.screening = screening;
		this.fee = fee;
		this.audienceCount = audienceCount;
	}
}
```

Reservation 클래스는 고객(customer), 상영 정보(screening), 예매 요금(fee), 인원 수(audienceCount)를 속성으로 포함한다.

영화를 예매하기 위해 Screening, Movie, Reservation 인스턴스들은 서로의 메서드를 호출하며 상호작용한다.

→ 시스템의 어떤 기능을 구현하기 위해 객체들 사이에 이루어지는 상호작용을 협력(collaboration)이라고 부른다.

객체가 다른 객체와 상호작용할 수 있는 방법은 자신만의 메서드를 이용해 `메시지`를 전송하고 수신하는 방법이다.

---
### 할인요금 구하기

할인 정책: 할인 요금을 계산하는 목적

- 금액 할인(AmountDiscountPolicy)
- 비율 할인(PercentDiscountPolicy)

DiscountPolicy 라는 추상 클래스(abstract class)를 구현하여 부모클래스에 기본적인 알고리즘의 흐름(할인 조건 만족 여부)을 구현 하고 중간에 필요한 처리(할인 요금 계산)를 자식 클래스에게 위임하는 디자인 패턴을 Template Method 패턴이라고 부른다.

오버라이딩(overriding): 부모 클래스에 정의된 같은 이름, 같은 파라미터 목록을 가진 메서드를 자식 클래스에서 재정의하는 경우

오버로딩(overroading): 메서드의 이름은 같지만 제공되는 파라미터의 목록이 다른 경우

---
### 컴파일 시간 의존성과 실행 시간 의존성

어떤 클래스가 다른 클래스에 접근할 수 있는 경로를 가지거나 해당 클래스의 객체의 메서드를 호출할 경우 두 클래스 사이에 의존성이 존재한다고 말한다.

![https://github.com/goldcrestwilma/object-study/blob/main/object-chapter2_2.7.png?raw=true](https://github.com/goldcrestwilma/object-study/blob/main/object-chapter2_2.7.png?raw=true)

`Movie` 클래스가 `DiscountPolicy` 클래스와 연결

요금 계산을 위해서는 `AmountDiscountPolicy` 와 `PercentDiscountPolicy` 인스턴스가 필요하다.

`Movie` 클래스는 코드 수준에서 두 클래스 중 어떤것도 의존하지 않지만 `Movie` 의 인스턴스는 실행 시에 `AmountDiscountPolicy` 와 `PercentDiscountPolicy` 인스턴스에 의존해야 한다.

`Movie` 의 인스턴스 생성 시 인자로 `AmountDiscountPolicy` 와 `PercentDiscountPolicy` 인스턴스를 전달하여 실행 시점에 협력할 수 있게 한다.

> 클래스 사이의 의존성과 객체 사이의 의존성이 동일하지 않을 수 있다.

- 코드의 의존성과 실행 시점의 의존성이 다르면 다를수록 코드를 이해하기 어려워진다.
- 코드를 이해하기 위해서는 코드 뿐만 아니라 객체를 생성하고 연결하는 부분을 찾아야한다.
- 코드의 의존성과 실행 시점의 의존성이 다르면 다를수록 코드는 더 유연해지고 확장 가능해진다.
- 의존성의 양면성은 설계가 트레이드오프의 산물이라는 사실을 잘 보여준다.

---
### 차이에 의한 프로그래밍

클래스를 하나 추가하고 싶은데 그 클래스가 기존의 어떤 클래스와 매우 흡사하다고 가정한다.

상속: 두 클래스 사이의 관계를 정의하는 방법

> 부모 클래스와 다른 부분만을 추가해서 새로운 클래스를 쉽고 빠르게 만드는 방법
