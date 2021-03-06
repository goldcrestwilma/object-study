## 4. 설계 품질과 트레이드오프
객체지향 설계란

- 올바른 객체에게 올바른 책임을 할당하면서
- 낮은 결합도와 높은 응집도를 가진 구조를 창조하는 활동

이 정의에는 객체지향 설계에 관한 두 가지 관점이 섞여 있다.

- 객체지향 설계의 핵심이 책임이라는 것
- 책임을 할당하는 작업이 응집도와 결합도 같은 설계 품질과 깊이 연관돼 있다는 것

설계는 변경을 위해 존재하고 변경에는 어떤 식으로든 비용이 발생한다.

`훌륭한 설계`란 합리적인 비용안에서 변경을 수용할 수 있는 구조를 만드는 것이다.

결합도와 응집도를 합리적인 수준으로 유지할 수 있는 중요한 원칙

- 객체의 상태가 아니라 객체의 행동에 초점을 맞추는 것

---

## 설계 트레이드오프

객체지향 커뮤니티에서는 오랜 기간 동안 좋은 설계의 특징을 판단할 수 있는 기준에 관한 다양한 논의가 있어 왔다. 
여기서는 세 가지 품질 척도의 의미를 살펴보자

- 캡슐화
- 응집도
- 결합도

### 캡슐화

- 변경 가능성이 높은 부분을 객체 내부로 숨기는 추상화 기법
- 변경될 수 있는 어떤 것이라도 캡슐화해야 한다.

> 유지보수성이 목표다. 유지보수성이란 두려움 없이, 주저함 없이, 저항감 없이 코드를 변경할 수 있는 능력을 말한다. 
가장 중요한 동료는 캡슐화다.

---

## 응집도와 결합도

응집도(cohesion)

- 모듈에 포함된 내부 요소들이 연관돼 있는 정도
- 모듈 내의 요소들이 `하나의 목적`을 위해 긴밀하게 협력한다면 `높은 응집도`를 가진다.
- 모듈 내의 요소들이 `서로 다른 목적`을 추구한다면 `낮은 응집도`를 가진다.

```java
public class Screening {
    public Money calculateFee(int audienceCount) {
        switch (movie.getMovieType()) {
            case AMOUNT_DISCOUNT:
                if (movie.isDiscountable(whenScreened, sequence)) {
                    return movie.calculateAmountDiscountedFee().times(audienceCount);
                }
                break;
            case PERCENT_DISCOUNT:
                if (movie.isDiscountable(whenScreened, sequence)) {
                    return movie.calculatePercentDiscountedFee().times(audienceCount);
                }
            case NONE_DISCOUNT:
                movie.calculateNoneDiscountedFee().times(audienceCount);
        }

        return movie.calculateNoneDiscountedFee().times(audienceCount);
    }
}
```

위 코드의 문제점

- 할인 조건의 종류를 변경하기 위해서는 DiscountCondition, Movie, Movie를 사용하는 Screening을 함께 수정해야 한다.
- DiscountCondition과 Movie 내부 구현이 인터페이스에 그대로 노출되고 있고 Screening은 노출된 구현에 직접적으로 의존하고 있다.

결합도(coupling)

- 의존성의 정도를 나타내며 다른 모듈에 대해 얼마나 많은 지식을 갖고 있는지를 나타내는 정도
- 어떤 모듈이 다른 모듈에 대해 `너무 자세한 부분까지 알고 있다`면 두 모듈은 `높은 결합도`를 가진다.
- 어떤 모듈이 다른 모듈에 대해 `꼭 필요한 지식만 알고 있다`면 두 모듈은 `낮은 결합도`를 가진다.

```java
public class Movie {
	public boolean isDiscountable(LocalDateTime whenScreened, int sequence) {
        for(DiscountCondition condition : discountConditions) {
            if (condition.getType() == DiscountConditionType.PERIOD) {
                if (condition.isDiscountable(whenScreened.getDayOfWeek(), whenScreened.toLocalTime())) {
                    return true;
                }
            } else {
                if (condition.isDiscountable(sequence)) {
                    return true;
                }
            }
        }

        return false;
    }
}
```

위 코드의 문제점

- DiscountCondition의 기간 할인 조건의 명칭이 PERIOD에서 다른 값으로 변경된다면 Movie를 수정해야 한다.
- DiscountCondition의 종류가 추가되거나 삭제된다면 Moive 안의 if ~ else 구문을 수정해야 한다.
- 각 DiscountCondition의 만족 여부를 판단하는 데 필요한 정보가 변경된다면 Movie의 isDiscountable 메서드로 전달된 파라미터를 변경해야 한다.

대부분의 사람들이 이런 애매한 설명만으로는 응집도와 결합도의 의미를 명확하게 이해하기 어렵다.

> 좋은 설계 = 높은 응집도 + 낮은 결합도
설계를 변경하기 쉽게 만든다.

변경의 관점에서

- 응집도란 변경이 발생할 때 `모듈 내부에서 발생하는 변경의 정도`로 측정할 수 있다.
- 결합도는 한 모듈이 변경되기 위해서 `다른 모듈의 변경을 요구하는 정도`로 측정할 수 있다.

클래스의 구현이 아닌 인터페이스에 의존하도록 코드를 작성해야 낮은 결합도를 얻을 수 있다.

---

## 캡슐화 위반

접근자와 수정자 메서드는 객체 내부의 상태에 대한 어떤 정보도 캡슐화하지 못한다.

get 메서드는 클래스 내부에서 사용하는 타입의 인스턴스 변수가 존재한다는 사실을 퍼블릭 인터페이스에 노골적으로 드러낸다.

접근자와 수정자에 과도하게 의존하는 설계 방식을 추측에 의한 설계 전략(design-by-guessing strategy)이라고 부른다. - *앨런 홀럽(Allen Holub)*

---

## 단일 책임 원칙(Single Responsibility Principle, SRP)

로버트 마틴(Robert C. Martin)은 모듈의 응집도가 변경과 연관이 있다는 사실을 강조하기 위해 `단일 책임 원칙`이라는 설계 원칙을 제시했다.

- 클래스는 단 한 가지의 변경 이유만 가져야 한다.
- 단일 책임 원칙이라는 맥락에서 '책임'이라는 말이 '변경의 이유'라는 의미로 사용된다.
