# 8. 의존성 관리하기

객체지향 설계의 핵심은 

- 협력을 위해 필요한 의존성을 유지
- 변경을 방해하는 의존성은 제거

---

## 의존성 이해하기

### 변경과 의존성

어떤 객체가 협력하기 위해 다른 객체를 필요로 할 때 두 객체 사이에 의존성이 존재한다.

의존성의 두 가지 시점

- 실행 시점
    - 실행 시에 의존 대상 객체가 반드시 존재해야 한다.
- 구현 시점
    - 의존 대상 객체가 변경될 경우 의존하는 객체도 함께 변경된다.

```java
public class PeriodCondition implements DiscountCondition {
    private DayOfWeek dayOfWeek;
    private LocalTime startTime;
    private LocalTime endTime;

    public PeriodCondition(DayOfWeek dayOfWeek, LocalTime startTime, LocalTime endTime) {
        this.dayOfWeek = dayOfWeek;
        this.startTime = startTime;
        this.endTime = endTime;
    }

    public boolean isSatisfiedBy(Screening screening) {
        return screening.getStartTime().getDayOfWeek().equals(dayOfWeek) &&
                startTime.compareTo(screening.getStartTime().toLocalTime()) <= 0&&
                endTime.compareTo(screening.getStartTime().toLocalTime()) >= 0;
    }
}
```
![Untitled](https://user-images.githubusercontent.com/61505386/121376805-c9945000-c97c-11eb-9471-7bd3ca1c1c4f.png)

![https://github.com/goldcrestwilma/object-study/blob/main/class%20diagram%20arrows.png?raw=true](https://github.com/goldcrestwilma/object-study/blob/main/class%20diagram%20arrows.png?raw=true)

### 의존성 전이(trasnsitive dependency)

```java
public class Screening {
    private Movie movie;
    private int sequence;
    private LocalDateTime whenScreened;

    public Screening(Movie movie, int sequence, LocalDateTime whenScreened) {
        this.movie = movie;
        this.sequence = sequence;
        this.whenScreened = whenScreened;
    }

    public LocalDateTime getStartTime() {
        return whenScreened;
    }

    public boolean isSequence(int sequence) {
        return this.sequence == sequence;
    }

    public Money getMovieFee() {
        return movie.getFee();
    }

    public Reservation reserve(Customer customer, int audienceCount) {
        return new Reservation(customer, this, calculateFee(audienceCount),
                audienceCount);
    }

    private Money calculateFee(int audienceCount) {
        return movie.calculateMovieFee(this).times(audienceCount);
    }
}
```

`Screening` 이 `Movie`, `LocalDateTime`, `Customer` 에 의존한다.

PeriodCondition 이 Screening 에 의존할 경우 PeriodCondition 은 Screening 이 의존하는 대상에 대해서도 자동적으로 의존하게 된다. - 의존성 전이



![Untitled](https://user-images.githubusercontent.com/61505386/121377025-fe080c00-c97c-11eb-80ce-7f758bfd0ad2.png)



> 의존성이 전이될 지 여부는 변경의 방향과 캡슐화의 정도에 따라 달라진다.
의존성 전이는 변경에 의해 영향이 널리 전파될 수 있다는 경고일 뿐이다.

- 직접 의존성(direct dependency)

    한 요소가 다른 요소에 직접 의존하는 경우(PeriodCondition → Screening)

- 간접 의존성(indirect dependency)

    직접적인 관계는 존재하지 않지만 의존성 전이에 의해 영향이 전파되는 경우(PeriodCondition → Movie)

의존성의 대상은 객체일 수도 있고 모듈이나 더 큰 규모의 실행 시스템일 수도 있다.
의존성이란 의존하고 있는 대상의 변경에 영향을 받을 수 있는 가능성이다.

### 런타임 의존성과 컴파일타임 의존성

런타임 의존성(run-time dependency)

- 애플리케이션이 실행되는 시점
- 객체 사이의 의존성

컴파일타임 의존성(compile-time dependency)

- 일반적으로 작성된 코드를 컴파일하는 시점을 가리키지만
- 문맥에 따라서는 코드 그 자체를 가리키기도 한다.
- 클래스 사이의 의존성

유연하고 재사용 가능한 코드를 설계하기 위해서는 두 종류의 의존성을 서로 다르게 만들어야 한다


![Untitled](https://user-images.githubusercontent.com/61505386/121377259-28f26000-c97d-11eb-89be-0088be2aafa2.png)


- Movie 클래스에서 AmountDiscountPolicy, PercentDiscountPolicy 클래스로 향하는 의존성이 존재하지 않는다.
- Movie 클래스는 오직 추상 클래스 DiscountPolicy 클래스에만 의존한다.

![111](https://user-images.githubusercontent.com/61505386/121377248-25f76f80-c97d-11eb-9859-cbc96fd51f80.png)


어떤 클래스의 인스턴스가 다양한 클래스의 인스턴스와 협력하기 위해서는 협력할 인스턴스의 구체적인 클래스를 알아서는 안 된다.

 

### 컨텍스트 독립성

클래스가 사용될 특정한 문맥에 대해 최소한의 가정만으로 이뤄져 있다면 다른 문맥에서 재사용하기가 더 수월해진다.

### 의존성 해결하기

컴파일타임 의존성을 실행 컨텍스트에 맞는 적절한 런타임 의존성으로 교체하는 것

- 객체를 생성하는 시점에 `생성자`를 통해 의존성 해결

    ```java
    public class Movie {
        public Movie(String title, Duration runningTime, Money fee, DiscountPolicy discountPolicy) {
            ...
            this.discountPolicy = discountPolicy;
        }
    }

    Movie avatar = new Movie("아바타", Duration.OfMinute(120), Money.wons(10000), new AmountDiscountPolicy(...));
    ```

- 객체 생성 후 `setter` 메서드를 통해 의존성 해결

    ```java
    public class Moive {
    	public void setDiscountPolicy(DiscountPolicy discountPolicy) {
    		this.discountPolicy = discountPolicy;
    	}
    }

    Movie avatar = new Movie(...);
    avatar.setDiscountPolicy(new AmountDiscountPolicy(...));
    ```

- `메서드 실행 시 인자`를 이용해 의존성 해결

    ```java
    public class Moive {
    	public Money calculateMovieFee(Screening screening, DiscountPolicy discountPolicy) {
    		return fee.minus(discountPolicy.calculateDiscountAmount(screening));
    	}
    }
    ```

좋은 방법은
생성자 방식과 setter 방식을 혼합하는 것

항상 객체를 생성할 때 의존성을 해결해서 완전한 상태의 객체를 생성한 후, 
필요에 따라 setter 메서드를 이용해 의존 대상을 변경할 수 있게 할 수 있다.

```java
Movie avatar = new Movie(..., new AmountDiscountPolicy(...));
...
avatar.setDiscountPolicy(new PercentDiscountPolicy(...));
```

---

## 유연한 설계

의존성을 관리하는 데 유용한 원칙과 기법

### 의존성과 결합도

```java
public class Moive {
		...
		private PercentDiscountPolicy percentDiscountPolicy;

		public Movie(String title, Duration runningTime, Money fee, PercentDiscountPolicy percentDiscountPolicy) {
			this.percentDiscountPolicy = percentDiscountPolicy
		}

		public Money calculateMovieFee(Screening screening) {
				return fee.minus(percentDiscountPolicy.calculateDiscountAmount(screening));
		}
}
```

- Movie 가 PercentDiscountPolicy 에 의존하고 있다는 사실을 명시적으로 드러낸다. - 컨텍스트에 강하게 결합된 의존성
- 하지만 다른 종류의 할인 정책이 필요한 문맥에서 Movie 를 재사용할 수 없다.
- DiscountPolicy 에만 의존하도록 만듦으로써 DiscountPolicy 클래스에 대한 컴파일타임 의존성을

    AmountDiscountPolicy 인스턴스와 PercentDiscountPolicy 인스턴스에 대한 런타임 의존성으로 대체할 수 있다.

### 지식이 결합을 낳는다

PercentDiscountPolicy: Movie는 협력할 객체가 **비율 할인 정책에 따라 할인 요금을 계산할 것**이라는 사실을 알고 있다.

DiscountPolicy: **할인 요금을 계산한다**는 사실만 알고 있다.

- 알아야 하는 지식의 양이 적을수록 결합도는 느슨해진다.
- 결합도는 느슨하게 만들기 위해서는 협력하는 대상에 대해 필요한 정보 외에는 최대한 감추는 것이 중요하다.

    → 추상화

### 추상화에 의존하라

추상화란

- 어떤 양상, 세부사항, 구조를 좀 더 명확하게 이해하기 위해 특정 절차나 물체를 의도적으로 생략하거나 감춤으로써 *복잡도를 극복하는 방법*
- 대상에 대해 알아야 하는 지식의 양을 줄일 수 있다 → 느슨한 결합도
    - 인터페이스 의존성 > 추상 클래스 의존성 > 구체 클래스 의존성

### 명시적인 의존성

```java
public class Moive {
	...
	private DiscountPolicy discountPolicy;	

	public Movie(String title, Duration runningTime, Money fee) {
    ...
	  this.discountPolicy = new AmountDiscountPolicy(...);
  }
}

// 개선
public class Moive {
	...
	private DiscountPolicy discountPolicy;	

	public Movie(String title, Duration runningTime, Money fee, DiscountPolicy discountPolicy) {
    ...
	  this.discountPolicy = discountPolicy;
  }
}

```

- 생성자에서 구체 클래스인 AmountDiscountPolicy 의 인스턴스를 직접 생성해서 대입
- DiscountPolicy 추상클래스뿐만 아니라 구체 클래스에도 의존하게 된다.

- 생성자의 인자에서 보여주듯이 Movie 가 DiscountPolicy에 의존한다는 사실을 Movie의 퍼블릭 인터페이스에 드러냄

    → 명시적인 의존성(explicit dependency)

> 의존성이 명시적이지 않으면 클래스를 다른 컨텍스트에서 재사용하기 위해 내부 구현을 직접 변경해야 한다. → 버그 발생 가능성 내포

### new는 해롭다

- new 연산자를 사용하기 위해서는 구체 클래스의 이름을 직접 기술해야 한다.
- new 를 사용하는 클라이언트는 추상화가 아닌 *구체 클래스에 의존*할 수밖에 없기 때문에 **결합도가 높아진다**.
- new 연산자는 생성하려는 구체 클래스뿐만 아니라 어떤 인자를 이용해 클래스의 생성자를 호출해야 하는지 알아야 한다.
- *클라이언트가 알아야 하는 지식의 양*이 늘어나기 때문에 **결합도가 높아진다**.

인스턴스를 생성하는 로직과 생성된 인스턴스를 사용하는 로직을 분리하라

```java
public class Movie {
    private DiscountPolicy discountPolicy;

    public Movie(String title, Duration runningTime, Money fee) {
        ...
        this.discountPolicy = new AmountDiscountPolicy(Money.wons(800),
															new SequenceCondition(1),
															new SequenceCondition(10),
															new PeriodCondition(DayOfWeek.MONDAY, LocalTime.of(10, 0), LocalTime.of(11, 59)),
															new PeriodCondition(DayOfWeek.THURSDAY, LocalTime.of(10, 0), LocalTime.of(20, 59))));															
    }
}

// 개선
public class Movie {
    private DiscountPolicy discountPolicy;

    public Movie(String title, Duration runningTime, Money fee, DiscountPolicy discountPolicy) {
        ...
        this.discountPolicy = discountPolicy;												
    }
}

Movie avatar = new Movie("아바타",
		Duration.ofMinutes(120),
		Money.wons(10000),
		new AmountDiscountPolicy(Money.wons(800),
		new SequenceCondition(1),
		new SequenceCondition(10),
		new PeriodCondition(DayOfWeek.MONDAY, LocalTime.of(10, 0), LocalTime.of(11, 59)),
		new PeriodCondition(DayOfWeek.THURSDAY, LocalTime.of(10, 0), LocalTime.of(20, 59))));															
```

Movie의 클라이언트가 AmountDiscountPolicy 의 인스턴스를 생성을 처리한다.

> 사용과 생성의 책임을 분리해서 Movie 의 결합도를 낮추면 설계를 유연하게 만들 수 있다.

### 가끔은 생성해도 무방하다

```java
public class Movie {
    private DiscountPolicy discountPolicy;

		public Movie(String title, Duration runningTime, Money fee) {
				this(title, runningTime, fee, new AmountDiscountPolicy(...));
		}

    public Movie(String title, Duration runningTime, Money fee, DiscountPolicy discountPolicy) {
        ...
        this.discountPolicy = discountPolicy;
    }
}
```

Movie 가 대부분의 경우에는 AmountDiscountPolicy의 인스턴스와 협력하고

가끔씩만 PercentDiscountPolicy의 인스턴스와 협력할 수 있는 구조가 되었다.

> 트레이드오프: 결합도와 사용성

구체 클래스에 의존하게 되더라도 클래스의 사용성이 더 중요하다면 결합도를 높이는 방향으로 코드를 작성할 수 있다.

### 표준 클래스에 대한 의존은 해롭지 않다

JDK의 표준 컬렉션 라이브러리에 속하는 `ArrayList`

```java
public abstract class DiscountPolicy {
		private List<DiscountCondition> conditions = new ArrayList<>();
}
```

> **수정될 확률은 0에 가깝기 때문에** 인스턴스를 직접생성하더라도 문제가 되지 않는다.

### 컨텍스트 확장하기

다른 컨텍스트에서 Movie를 확장해서 재사용

- 할인 혜택을 제공하지 않는 영화

```java
public class NoneDiscountPolicy extends DiscountPolicy {
    @Override
    protected Money getDiscountAmount(Screening screening) {
        return Money.ZERO;
    }
}
```

- 다수의 할인 정책을 중복해서 적용하는 영화
