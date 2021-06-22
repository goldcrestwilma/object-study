## 개방-폐쇄 원칙

개방-폐쇄 원칙(Open-Closed Principle, OCP) - *로버트 마틴*

- 소프트웨어 개체(클래스, 모듈, 함수 등등)는 확장에 대해 열려 있어야 하고, 수정에 대해서는 닫혀 있어야 한다.
- 확장에 대해 열려 있다
    - 애플리케이션의 요구사항이 변경될 때 이 변경에 맞게 새로운 '동작'을 추가해서 기능을 확장할 수 있다.
- 수정에 대해 닫혀 있다
    - 기존의 '코드'를 수정하지 않고도 애플리케이션의 동작을 추가하거나 변경할 수 있다.

### 컴파일타임 의존성을 고정시키고 런타임 의존성을 변경하라

컴파일타임 의존성

- 코드에서 드러나는 클래스들 사이의 관계

런타임 의존성

- 실행시에 협력에 참여하는 객체들 사이의 관계

![1](https://user-images.githubusercontent.com/61505386/122842550-949eda80-d338-11eb-8811-c5fa67ae465d.png)


개방-폐쇄 원칙을 수용하는 코드는 컴파일타임 의존성을 수정하지 않고도 런타임 의존성을 쉽게 변경할 수 있다.

### 추상화가 핵심이다

개방-폐쇄 원칙의 핵심은 **추상화에 의존하는 것**이다.

```java
public abstract class DiscountPolicy {
    private List<DiscountCondition> conditions = new ArrayList<>();

    public DiscountPolicy(DiscountCondition ... conditions) {
        this.conditions = Arrays.asList(conditions);
    }

    public Money calculateDiscountAmount(Screening screening) {
        for(DiscountCondition each : conditions) {
            if (each.isSatisfiedBy(screening)) {
                return getDiscountAmount(screening);
            }
        }

        return screening.getMovieFee();
    }

    abstract protected Money getDiscountAmount(Screening Screening);
}
```

상속을 통해 생략된 부분을 구체화함으로써 할인 정책을 확장할 수 있다.

변하지 않는 부분을 고정하고

변하는 부분을 생략하는

추상화 메커니즘이 개방-폐쇄 원칙의 기반이 된다는 사실에 주목하자.

- 언제라도 추상화의 생략된 부분을 채워넣음으로써 새로운 문맥에 맞게 기능을 확장할 수 있다.

---

## 생성 사용 분리

생성과 사용을 분리(separating use from creation)

소프트웨어 시스템은 응용 프로그램 객체를 제작하고 의존성을 서로 "연결"하는 시작 단계와

시작 단계 이후에 이어지는 실행 단계를 분리해야 한다.

사용으로부터 생성을 분리하는 데 사용되는 가장 보편적인 방법은 객체를 생성할 책임을 클라이언트로 옮기는 것이다.

- Movie 는 특정 클라이언트에 결합되지 않고 독립적일 수 있다.

![2](https://user-images.githubusercontent.com/61505386/122842559-98caf800-d338-11eb-95b1-c2af4414e78a.png)


```java
public class Client {
    public Money getAvatarFee() {
        Movie avatar = new Movie("아바타",
                Duration.ofMinutes(120),
                Money.wons(10000),
                new AmountDiscountPolicy(
                    Money.wons(800),
                    new SequenceCondition(1),
                    new SequenceCondition(10)));
        return avatar.getFee();
    }
}
```

### FACTORY 추가하기

Client 의 코드를 살펴보면 Movie 의 인스턴스를 생성하는 동시에 getFee 메시지도 함께 전송한다.

- Client 역시 생성과 사용의 책임을 함께 지니고 있다.

객체 생성과 관련된 책임만 전담하는 별도의 객체를 추가하고 Client 는 이 객체를 사용하도록 만들 수 있다.

생성과 사용을 분리하기 위해 객체 생성에 특화된 객체를 FACTORY라고 부른다.

```java
public class Factory {
    public Movie createAvatarMovie() {
        return new Movie("아바타",
                Duration.ofMinutes(120),
                Money.wons(10000),
                new AmountDiscountPolicy(
                    Money.wons(800),
                    new SequenceCondition(1),
                    new SequenceCondition(10)));
    }
}
```

이제 Client 는 Factory를 사용해서 생성된 Movie 의 인스턴스를 반환받아 사용하기만 하면 된다.

```java
public class Client {
    private Factory factory;

    public Client(Factory factory) {
        this.factory = factory;
    }

    public Money getAvatarFee() {
        Movie avatar = factory.createAvatarMovie();
        return avatar.getFee();
    }
}
```

![3](https://user-images.githubusercontent.com/61505386/122842561-99638e80-d338-11eb-9656-fc3cb08ffed8.png)


FACTORY를 사용하면 Movie와 AmountDiscountPolicy 를 생성하는 책임 모두를 FACTORY로 이동할 수 있다.

### 순수한 가공물에게 책임 할당하기

책임을 수행하는 데 필요한 정보를 가장 많이 알고 있는 INFORMATION EXPERT에게 책임을 할당한다.

시스템을 객체로 분해하는 두 가지 방식

- 표현적 분해(representational decomposition)
    - 도메인에 존재하는 사물 또는 개념을 표현하는 객체들을 이용해 시스템을 분해하는 것
- 행위적 분해(behavioral decomposition)

모든 책임을 도메인 객체에게 할당하면 낮은 응집도, 높은 결합도, 재사용성 저하와 같은 심각한 문제점에 봉착하게 될 가능성이 높아진다.

이 경우 도메인 개념을 표현한 객체가 아닌 설계자가 편의를 위해 임의로 만들어낸 가공의 객체에게 책임을 할당해서 문제를 해결해야 한다.

책임을 할당하기 위해 창조되는 도메인과 무관한 인공적인 객체 - PURE FABRICATION(순수한 가공물) - 크레이그 라만

객체지향 애플리케이션

- 도메인 개념 + 인공적인 추상화
- 먼저 도메인의 본질적인 개념을 표현하는 추상화를 이용해 구축 시작
- 도메인 개념이 만족스럽지 못하다면 인공적인 객체를 창조하라

---

## 의존성 주입

의존성 주입(Dependency Injection)

외부의 독립적인 객체가 인스턴스를 생성한 후 이를 전달해서 의존성을 해결하는 방법

- 생성자 주입(constructor injection)

```java
Movie avatar = new Movie("아바타", Duration.ofMinutes(120), Money.wons(10000), new AmountDiscountPolicy(...));
```

- setter 주입(setter injection)

```java
avatar.setDiscountPolicy(new AmountDiscountPolicy(...));
```

- 메서드 주입(method injection)

```java
avatar.calculateDiscountAmount(screening, new AmountDiscountPolicy(...));
```

### 숨겨진 의존성은 나쁘다

의존성 주입 외에도 의존성을 해결할 수 있는 다양한 방법이 존재한다.

SERVICE LOCATOR 패턴

- 의존성을 해결할 객체들을 보관하는 일종의 저장소

```java
public class ServiceLocator {
    private static ServiceLocator soleInstance = new ServiceLocator();
    private DiscountPolicy discountPolicy;

    public static DiscountPolicy discountPolicy() {
        return soleInstance.discountPolicy;
    }

    public static void provide(DiscountPolicy discountPolicy) {
        soleInstance.discountPolicy = discountPolicy;
    }

    private ServiceLocator() {
    }
}
```

ServiceLocator 는 DiscountPolicy 의 인스턴스를 등록하고 반환할 수 있는 메서드를 구현한 저장소다.

```java
ServiceLocator.provide(new AmountDiscountPolicy(...));
Movie avatar = new Movie("아바타", Duration.ofMinutes(120), Money.wons(10000));
```

SERVICE LOCATOR 가장 큰 단점은 **의존성을 감춘다**는 것이다.

- Movie 는 DiscountPolicy 에 의존하고 있지만 Movie 의 퍼블릭 인터페이스 어디에도 이 의존성에 대한 정보가 표시돼 있지 않다.
- 의존성은 암시적이며 코드 깊숙한 곳에 숨겨져 있다.

의존성을 숨기는 코드는 단위 테스트 작성도 어렵다.

- ServiceLocator 는 내부적으로 정적 변수를 사용해 객체들을 관리하기 때문에
- 모든 단위 테스트 케이스에 걸쳐 ServiceLocator 의 상태를 공유하게 된다.
- 각 단위 테스트는 서로 고립돼야 한다는 단위 테스트의 기본 원칙을 위반한다.

> 가능하다면 의존성을 명시적으로 표현할 수 있는 기법을 사용하라

---

## 의존성 역전 원칙

### 추상화와 의존성 역전

의존성 역전 원칙(Dependency Inversion Principle, DIP)

- 상위 수준의 모듈은 하위 수준의 모듈에 의존해서는 안 된다. 둘 모두 추상화에 의존해야 한다.
- 추상화는 구체적인 사항에 의존해서는 안 된다. 구체적인 사항은 추상화에 의존해야 한다.

### 의존성 역전 원칙과 패키지

![4](https://user-images.githubusercontent.com/61505386/122842562-9a94bb80-d338-11eb-8a8e-dcd8d3f3bd98.png)


SEPARATED INTERFACE 패턴

- 추상화를 클라이언트가 속한 패키지에 포함시켜야 한다.
- 함께 재사용될 필요가 없는 클래스들은 별도의 독립적인 패키지에 모아야 한다.

전통적인 패러다임

- 상위 수준 모듈이 하위 수준 모듈에 의존
- 인터페이스가 하위 수준 모듈에 속한다.

객체지향 패러다임

- 상위 수준 모듈과 하위 수준 모듈 모두 추상화에 의존한다.
- 인터페이스가 상위 수준 모듈에 속한다.

---

## 유연성에 대한 조언

### 유연한 설계는 유연성이 필요할 때만 옳다

유연하고 재사용 가능한 설계

- 런타임 의존성과 컴파일타임 의존성의 차이를 인식하고
- 동일한 컴파일타임 의존성으로부터 다양한 런타임 의존성을 만들 수 있는 코드 구조를 가지는 설계

하지만, 유연하고 재사용 가능한 설계가 항상 좋은 것은 아니다.

- 설계의 미덕은 단순함과 명확함으로부터 나온다.

    읽기 쉽고 이해하기 편하다.

변경하기 쉽고 확장하기 쉬운 구조를 만들기 위해서는 단순함과 명확함의 미덕을 버리게 될 가능성이 높다.

- 설계가 유연할수록 클래스 구조와 객체 구조 사이의 거리는 점점 멀어진다.
- 불필요한 유연성은 불필요한 복잡성을 낳는다.
- 단순하고 명확한 해법이 만족스럽다면 유연성을 제거하라.

### 협력과 책임이 중요하다

설계를 유연하게 만들기 위해서는 

- 협력에 참여하는 객체가 다른 객체에게 어떤 메시지를 전송하는지가 중요하다.

![5](https://user-images.githubusercontent.com/61505386/122842565-9b2d5200-d338-11eb-8d1d-45b07e036e93.png)

Movie 가 다양한 할인 정책과 협력할 수 있는 이유는 무엇인가?

- 모든 할인 정책이 Movie가 전송하는 calculateDiscountAmount **메시지를 이해할 수 있기 때문이다.**

역할, 책임, 협력에 초점을 맞춰야 한다.

초보자가 자주 저지르는 실수 중 하나는

- 객체의 역할과 책임이 자리를 잡기 전에 객체 생성에 집중하는 것이다.
- 이것은 객체 생성과 관련된 불필요한 세부사항에 객체를 결합시킨다.
- 객체를 생성할 책임을 담당할 객체나 객체 생성 메커니즘을 결정하는 시점은 책임 할당의 마지막 단계로 미뤄야만 한다.
- 중요한 비즈니스 로직을 처리하기 위해 책임을 할당하고 협력의 균형을 맞추는 것이 객체 생성에 관한 책임을 할당하는 것보다 우선이다.
