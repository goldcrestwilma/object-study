# object-study

## 1. 객체, 설계

로버트 마틴 <클린 소프트웨어: 애자일 원칙과 패턴, 그리고 실천 방법>에서 소프트웨어 모듈이 가져야 하는 세 가지 기능에 관해 설명한다.
> 모든 소프트웨어 모듈에는 세 가지 목적이 있다.
> 1. 실행 중에 제대로 동작하는 것
> 2. 목적은 변경을 위해 존재하는 것
> 3. 코드를 읽는 사람과 의사소통하는 것

### 변경에 취약한 코드
의존성이라는 말 속에는 `어떤 객체가 변경될 때 그 객체에게 의존하는 다른 객체도 함께 변경될 수 있다`는 사실이 내포돼 있다.

그렇다고 해서 객체 사이의 의존성을 완전히 없애는 것이 정답은 아니다.
객체지향 설계: 서로 의존하면서 협력하는 객체들의 공동체를 구축하는 것
- 애플리케이션의 기능을 구현하는 데 필요한 최소한의 의존성만 유지, 불필요한 의존성 제거

의존성이 과하다 = 결합도(Coupling)가 높다.

### 캡슐화
- 개념적이나 물리적으로 객체 내부의 세부적인 사항을 감추는 것
- 변경하기 쉬운 객체를 만드는 것
- 캡슐화를 통해 객체 내부로의 접근을 제한하면 객체와 객체 사이의 결합도를 낮출 수 있다

### 응집도
- 밀접하게 연관된 작업만을 수행하고 연관성 없는 작업은 다른 객체에게 위임하는 객체를 가리켜 `응집도`가 높다고 말함
- 객체의 응집도를 높이기 위해서는 객체 스스로 자신의 데이터를 책임져야 함.

객체지향 설계의 핵심은 적절한 객체에 적절한 책임을 할당하는 것

> *_객체 내부의 상태를 `캡슐화`하고 객체 간에 오직 `메시지`를 통해서만 상호작용하도록 만드는 것_*
> 
> 외부의 간섭을 최대한 베재하고 메시지를 통해서만 협력하는 자율적인 객체들의 공동체를 만드는 것이 훌륭한 객체지향 설계를 얻을 수 있는 지름길이다.
> 

### 책임의 이동
- 객체지향 설계에서는 독재자가 존재하지 않고 각 객체에 책임이 적절하게 분배된다.
- 각 객체는 자신을 스스로 책임진다.

### 의인화
- 비록 현실에서는 수동적인 존재라고 하더라도 일단 객체지향의 세계에 들어오면 모든것이 능동적이고 자율적인 존재로 바뀐다.
- 능동적이고 자율적인 존재로 소프트웨어 객체를 설계하는 원칙
- 대상이 비록 실세계에서는 생명이 없는 수동적인 존재라고 하더라도 객체지향의 세계로 넘어오는 순간 그들은 생명과 지능을 가진 싱싱한 존재로 다시 태어난다.

---
## 객체지향 설계
> *설계란? 코드를 배치하는 것*
> 

#### 좋은 설계란?
- 기능을 구현하는 코드를 짜야 하는 동시에 내일 쉽게 변경할 수 있는 코드를 짜야한다.
- 오늘 요구하는 기능을 온전히 수행하면서 내일의 변경을 매끄럽게 수용할 수 있는 설계다.


> __변경 가능한 코드 = 이해하기 쉬운 코드__


---
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

---
## 3장 역할, 책임, 협력
객체지향 패러다임의 관점 핵심

- 역할(role)
- 책임(responsibility)
- 협력(collaboration)

> 객체지향의 본질은 협력하는 객체들의 공동체를 창조하는 것

> 객체지향 설계의 핵심은 `협력`을 구성하기 위해 적절한 객체를 찾고 적절한 `책임`을 할당하는 과정에서 드러난다.

---

### 협력

영화 예매 시스템에서 `영화 예매`라는 기능을 구현하기 위해 `메시지`를 주고받으면서 상호작용한다.

- 이처럼 객체들이 애플리케이션의 기능을 구현하기 위해 수행하는 상호작용을 `협력`이라고 한다.
- 객체가 협력에 참여하기 위해 수행하는 로직은 `책임`이라고 부른다.
- 객체들이 협력 안에서 수행하는 책임들이 모여 객체가 수행하는 `역할`을 구성한다.

메시지 전송은 객체 사이의 협력을 위해 사용할 수 있는 유일한 커뮤니케이션 수단이다. → 다른 객체의 내부 구현에 접근 불가

> 자율적인 객체는 자신에게 할당된 책임을 수행하던 중에 필요한 정보를 알지 못하거나 외부의 도움이 필요한 경우 적절한 객체에게 메시지를 전송해서 협력을 요청한다.

---

### 협력이 설계를 위한 문맥을 결정한다

객체란 `상태`와 `행동`을 함께 캡슐화하는 실행 단위다.

객체의 `행동`을 결정하는 것은 객체가 참여하고 있는 `협력`이다. → 협력이 바뀌면 객체가 제공해야 하는 행동도 바뀌어야 한다.

- Movie 객체는 영화를 예매하기 위한 협력에 참여하고 있고 그 안에서 요금을 계산하는 책임을 지고 있다.

객체의 `상태`를 결정하는 것은 `행동`이다. → 객체의 상태는 그 객체가 행동을 수행하는 데 필요한 정보가 무엇인지로 결정된다.

- Movie 클래스는 기본요금 `fee` 와 할인 정책 `discountPolicy` 라는 인스턴스 변수를 상태의 일부로 포함하고 `요금 계산`이라는 행동을 수행한다.

> 객체가 참여하는 협력이 객체를 구성하는 행동과 상태 모두를 결정하고 협력은 객체를 설계하는 데 필요한 일종의 `**문맥(context)**`을 제공한다.

---

### 책임이란 무엇인가

책임: 협력에 참여하기 위해 객체가 수행하는 행동

객체의 책임은 크게 `하는 것(doing)` 과 `아는 것(knowing)`의 두 가지 범주로 나누어 세분화하고 있다 - *크레이그 라만(Craig Larman)*

하는 것

- 객체를 생성하거나 계산을 수행하는 등의 스스로 하는 것(영화 예매, 예매 가격 계산)
- 다른 객체의 행동을 시작시키는 것
- 다른 객체의 활동을 제어하고 조절하는 것

아는 것

- 사적인 정보에 관해 아는 것 (가격, 할인 정책)
- 관련된 객체에 관해 아는 것
- 자신이 유도하거나 계산할 수 있는 것에 관해 아는 것

CRC 카드

후보(`C`andidate)

- Screening

책임(`R`esponsibility)

- 예매 정보를 생성한다
- 상영 정보를 알고 있다

협력(`C`ollaborator)

- Movie

Screening이 reserve 메시지를 수신하고 `movie`를 인스턴스 변수로 포함하는 이유는 협력안에서 영화를 예매할 책임을 수행해야 하기 때문이다.

협력 안에서 객체에게 할당한 책임이 외부의 인터페이스와 내부의 속성을 결정한다.

책임의 관점에서 '아는 것'과 '하는 것'이 밀접하게 연관되어 있다.

- 객체는 자신이 맡은 책임을 수행하는 데 필요한 정보를 알고 있을 책임이 있다.
- 객체는 자신이 할 수 없는 작업을 도와줄 객체를 알고 있을 책임이 있다.
- 어떤 책임을 수행하기 위해서는 그 책임을 수행하는 데 필요한 정보도 함께 알아야 할 책임이 있다.

---

### 책임 할당

자율적인 객체를 만드는 가장 기본적인 방법은 책임을 수행하는 데 필요한 정보를 가장 잘 알고 있는 전문가에게 그 책임을 할당 하는 것

책임 할당을 위한 `INFORMATION EXPERT(정보 전문가)패턴` 이라고 부른다.

일상 생활에서도 어떤 도움이 필요한 경우 전문가에게 도움을 청하는 것과 마찬가지로 객체들 역시 협력에 필요한 지식과 방법을 가장 잘 알고 있는 객체에 도움을 요청한다.

영화 예매 시스템에서 제공해야 할 기능은 영화를 예매하는 것

*예매하라*라는 이름의 메시지로 협력을 시작 → 상영 시간과 기본요금을 알고 있는 Screening 전달 → 예매 가격을 계산하는 데 필요한 정보가 부족하기 떄문에 *가격을 계산하라*라는 메시지 전달 → Movie 가격 계산 책임 할당

객체지향 설계는 협력에 필요한 메시지를 찾고 메시지에 적절한 객체를 선택하는 반복적인 과정을 통해 이루어진다.

---

### 책임 주도 설계(Responsibility-Driven Design, RDD)

책임을 찾고 책임을 수행할 적절한 객체를 찾아 책임을 할당하는 방식으로 협력을 설계하는 방법

- 시스템이 사용자에게 제공해야 하는 기능인 시스템 책임을 파악한다.
- 시스템 책임을 더 작은 책임으로 분할한다.
- 분할된 책임을 수행할 수 있는 적절한 객체 또는 역할을 찾아 책임을 할당한다.
- 객체가 책임을 수행하는 도중 다른 객체의 도움이 필요한 경우 이를 책임질 적절한 객체 또는 역할을 찾는다.
- 해당 객체 또는 역할에게 책임을 할당함으로써 두 객체가 협력하게 한다.

---

### 메시지가 객체를 결정한다

메시지가 객체를 선택하게 해야 하는 두 가지 중요한 이유

1. 객체가 **최소한의 인터페이스(minimal interface)**를 가질 수 있게 된다.
    - 필요한 메시지가 식별될 때까지 객체의 퍼블릭 인터페이스에 어떤 것도 추가하지 않기 때문에 크지도, 작지도 않은 꼭 필요한 크기의 퍼블릭 인터페이스를 가질 수 있다.
2. 객체는 **추상적인 인터페이스(abstract interface)**를 가질 수 있게 된다.
    - 인터페이스는 무엇(what)을 하는지는 표현해야 하지만 어떻게(how) 수행하는지를 노출해서는 안된다.
    - 무엇을 수행할지에 초점을 맞추는 인터페이스를 얻을 수 있다.

---

### 행동이 상태를 결정한다

객체가 존재하는 이유는 협력에 참여하기 위해서다.

따라서 객체는 협력에 필요한 행동을 제공해야 한다.

객체를 객체답게 만드는 것은 객체가 다른 객체에게 제공하는 행동이다.

객체지향 패러다임에 갓 입문한 사람들이 가장 쉽게 빠지는 실수는 객체의 행동이 아니라 상태에 초점을 맞추는 것

→ 먼저 객체에 필요한 상태가 무엇인지 결정 → 상태에 필요한 행동을 결정 → 문제점: 객체의 내부 구현이 객체의 퍼블릭 인터페이스에 노출되도록 만들기 떄문에 캡슐화를 저해한다.

캡슐화를 위반하지 않도록 구현하기 위해서는 항상 협력이라는 문맥 안에서 객체를 생각해야 한다.

협력 관계 속에서 다른 객체에게 무엇을 제공해야 하고 다른 객체로부터 무엇을 얻어야 하는지를 고민해야만 훌륭한 책임을 수확할 수 있다.

---

### 역할과 협력

역할: 객체가 어떤 특정한 협력 안에서 수행하는 책임의 집합

적절한 역할이 무엇인가를 찾고 역할을 수행할 객체를 선택하는 것

---

### 유연하고 재사용 가능한 협력

역할은 다른 것으로 교체할 수 있는 책임의 집합이다.

동일한 책임을 수행하는 역할을 기반으로 두 개의 협력을 하나로 통합할 수 있다.

책임과 역할을 중심으로 협력을 바라보는 것이 변경과 확장이 용이한 유연한 설계를 위한 길이다.

역할을 구현하는 가장 일반적인 방법은 **추상 클래스**와 **인터페이스**를 사용하는 것이다. 

추상 클래스 - 책임의 일부를 구현해 놓은 것

인터페이스 - 일체의 구현없이 책임의 집합만을 나열

둘의 차이점이 있지만 협력의 관점에서는 둘 모두 역할을 정의할 수 있는 구현 방법이라는 공통점을 공유한다.

---

### 객체 대 역할

협력에 적합한 책임을 수행하는 대상이 한 종류라면 `객체`로 간주하고 만약 여러 종류의 객체들이 참여할 수 있다면 `역할`이라고 부른다.

협력은 역할들의 상호작용으로 구성되고, 협력을 구성하기 위해 역할에 적합한 객체가 선택되며, 객체는 클래스를 이용해 구현되고 생성된다 - *트리그비 린스카우(Trygve Reenskaug)*

협력(Collaboration)  `- reference →` 역할(Role) `- select from →` 객체(Object) `- instance of →` 클래스(Class)

 

> 중요한 것은 책임이다: 중요한 것은 협력을 위해 어떤 책임이 필요한지를 이해하는 것

---

### 역할과 추상화

추상화를 이용한 설계가 가지는 두 가지 장점

- 추상화 계층만을 이용하면 중요한 정책을 상위 수준에서 단순화할 수 있다.
- 설계가 좀 더 유연해진다.
