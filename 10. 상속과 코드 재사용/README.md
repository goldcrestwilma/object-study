상속

- 클래스 안에 정의된 인스턴스 변수와 메서드를 자동으로 새로운 클래스에 추가하는 구현 기법

합성

- 새로운 클래스의 인스턴스 안에 기존 클래스의 인스턴스를 포함시키는 방법

# 상속과 중복 코드

중복 코드는

- 사람들의 마음속에 의심과 불신의 씨앗을 뿌린다.
- 유사한 코드가 이미 존재하는데도 새로운 코드를 만든 이유는 무엇일까?
- 의도적으로 그렇게 한 것이가
- 두 코드가 중복이기는 한 걸까?
- 중복을 없애도 문제가 없을까?
- 양쪽을 수정하기보다는 한쪽 코드만 수정하는 게 더 안전한 방법이 아닐까?

## DRY 원칙

**중복 코드**는 *변경*을 방해한다.

새로운 코드를 추가하고 나면 언젠가는 변경될 것이라고 생각하는 것이 현명하다.

**중복** 여부를 판단하는 기준은 *변경*이다.

요구사항이 변경됐을 때 두 코드를 함께 수정해야 한다면 이 코드는 중복이다.

Don't Repeat Yourself - *앤드류 헌트, 데이비드 토마스*

동일한 지식을 중복하지 말라

- 한 번, 단 한번(Once and Only Once) 원칙
- 단일 지점 제어(Single Point Control) 원칙

> DRY 원칙
모든 지식은 시스템 내에서 단일하고, 애매하지 않고, 정말로 믿을 만한 표현 양식을 가져야 한다.

## 중복과 변경

### 중복 코드 살펴보기

가입자별로 전화 요금을 계산하는 애플리케이션

개별 통화 기간 저장

```java
public class Call {
    private LocalDateTime from;
    private LocalDateTime to;

    public Call(LocalDateTime from, LocalDateTime to) {
        this.from = from;
        this.to = to;
    }

    public Duration getDuration() {
        return Duration.between(from, to);
    }

    public LocalDateTime getFrom() {
        return from;
    }
}
```

전체 통화 목록 - 통화 요금 계산

```java
public class Phone {
    private Money amount;
    private Duration seconds;
    private List<Call> calls = new ArrayList<>();

    public Phone(Money amount, Duration seconds) {
        this.amount = amount;
        this.seconds = seconds;
    }

    public void call(Call call) {
        calls.add(call);
    }

    public List<Call> getCalls() {
        return calls;
    }

    public Money getAmount() {
        return amount;
    }

    public Duration getSeconds() {
        return seconds;
    }

    public Money calculateFee() {
        Money result = Money.ZERO;

        for(Call call : calls) {
            result = result.plus(amount.times(call.getDuration().getSeconds() / seconds.getSeconds()));
        }

        return result;
    }
}
```

새로운 요금 방식 추가 - 심야 할인 요금제 

```java
public class NightlyDiscountPhone {
    private static final int LATE_NIGHT_HOUR = 22;

    private Money nightlyAmount;
    private Money regularAmount;
    private Duration seconds;
    private List<Call> calls = new ArrayList<>();

    public NightlyDiscountPhone(Money nightlyAmount, Money regularAmount, Duration seconds) {
        this.nightlyAmount = nightlyAmount;
        this.regularAmount = regularAmount;
        this.seconds = seconds;
    }

    public Money calculateFee() {
        Money result = Money.ZERO;

        for(Call call : calls) {
            if (call.getFrom().getHour() >= LATE_NIGHT_HOUR) {
                result = result.plus(nightlyAmount.times(call.getDuration().getSeconds() / seconds.getSeconds()));
            } else {
                result = result.plus(regularAmount.times(call.getDuration().getSeconds() / seconds.getSeconds()));
            }
        }

        return result;
    }
}
```

### 중복 코드 수정하기

통화 요금에 부과할 세금을 계산하기

통화 요금을 계산하는 로직이 Phone 과 NightlyDiscountPhone 양쪽 모두에 구현돼 있기 때문에 두 클래스 함께 수정해야 한다.

```java
public Money calculateFee() {
		...
    return result.plus(result.times(taxRate));
}
```

- 중복 코드가 늘어날수록 애플리케이션은 변경에 취약해지고 버그가 발생할 가능성이 높아진다
- 코드를 변경하는 속도 또한 점점 더 느려진다.

중복 코드를 추가하는 대신 제거해야 한다. 기회가 생길 때마다 코드를 DRY하게 만들기 위해 노력하라.

### 타입 코드 사용하기

```java
public class Phone {
    private static final int LATE_NIGHT_HOUR = 22;
    enum PhoneType { REGULAR, NIGHTLY }

    private PhoneType type;

    ...

    public Money calculateFee() {
        Money result = Money.ZERO;

        for(Call call : calls) {
            **if (type == PhoneType.REGULAR) {**
                result = result.plus(amount.times(call.getDuration().getSeconds() / seconds.getSeconds()));
            } else {
                **if (call.getFrom().getHour() >= LATE_NIGHT_HOUR) {**
                    result = result.plus(nightlyAmount.times(call.getDuration().getSeconds() / seconds.getSeconds()));
                } else {
                    result = result.plus(regularAmount.times(call.getDuration().getSeconds() / seconds.getSeconds()));
                }
            }
        }

        return result;
    }
}
```

타입 코드를 사용하는 클래스는 낮은 응집도와 높은 결합도라는 문제에 시달리게 된다. 쓰지 말자

## 상속을 이용해서 중복 코드 제거하기

이미 존재하는 클래스와 유사한 클래스가 필요하다면

- 코드를 복사하지 말고 상속을 이용해 코드를 재사용하라

```java
public class NightlyDiscountPhone extends Phone {
    ...

    @Override
    public Money calculateFee() {
        // 부모클래스의 calculateFee 호출
        Money result = super.calculateFee();

        ...
        return result.minus(nightlyFee);
    }
}
```

super 참조를 통해 부모 클래스인 Phone의 calculateFee 메서드를 호출해서 로직을 구현하고 있다.

이렇게 구현된 이유를 이해하기 위해서는 개발자가 Phone 의 코드를 재사용하기 위해 세운 가정을 이해하는 것이 중요하다.

자식 클래스의 작성자가 부모 클래스의 구현 방법에 대한 정확한 지식을 가져야 한다는 것을 의미한다.

- 상속은 결합도를 높인다.

## 강하게 결합된 Phone과 NightlyDiscountPhone

새로운 요구사항(세금 부과) 추가된다면

- Phone 의 calculateFee 로직을 추가하고 NightlyDiscountPhone에도 동일한 로직을 추가해야 한다.
- 부모 클래스의 변경에 의해 자식 클래스가 영향을 받는다

취약한 기반 클래스 문제

- 상속 관계로 연결된 자식 클래스가 부모 클래스의 변경에 취약해지는 현상
- 코드 재사용을 목적으로 상속을 사용할 때 발생하는 가장 대표적인 문제

# 취약한 기반 클래스 문제

상속은 자식 클래스를 점진적으로 추가해서

- 기능을 확장하는 데는 용이하지만
- 높은 결합도로 인해 부모 클래스를 점진적으로 개선하는 것은 어렵게 만든다
- 자식 클래스가 부모 클래스의 구현 세부사항에 의존하도록 만들기 때문에 캡슐화를 약화시킨다

## 불필요한 인터페이스 상속 문제

![1](https://user-images.githubusercontent.com/61505386/123278987-6987d700-d542-11eb-87b2-23e55c436180.png)


자바의 초기 컬렉션 프레임워크 개발자들은 요소의 추가, 삭제 오퍼레이션을 제공하는 Vector를 재사용하기 위해 Stack을 Vector의 자식 클래스로 구현했다.

```java
Stack<String> stack = new Stack<>();
stack.push("1st");
stack.push("2nd");

stack.add(0, "3rd");

assertEquals("3rd", stack.pop()); // Error!
```

Vector의 add 메서드를 이용해 Stack의 맨 앞에 "3rd"를 추가했다.

stack.pop() 의 값은 "2nd"

위 문제의 원인은 

- Stack이 규칙을 무너뜨릴 여지가 있는

    Vector의 퍼블릭 인터페이스까지도 함께 상속받았기 때문이다.

![2](https://user-images.githubusercontent.com/61505386/123279047-760c2f80-d542-11eb-8085-9c9dc1f6c4a1.png)


Properties 클래스는 Map과 유사하지만 키와 값의 타입으로 String만 가질 수 있다.

```java
Properties properties = new Properties();
properties.setProperty("Bjarne Stroustrup", "C++");
properties.setProperty("James Gosling", "Java");

properties.put("Dennis Ritchie", 67);

assertEquals("C", properties.getProperty("Dennis Ritchie")); // Error!
```

"Dennis Ritchie" 를 키로 검색할 경우 null 이 반환된다

getProperty 메서드가 반환할 값의 타입이 String이 아닐 경우 null을 반환하도록 구현돼 있기 때문이다.

> 상속받은 부모 클래스의 메서드가 자식 클래스의 내부 구조에 대한 규칙을 깨트릴 수 있다.

## 메서드 오버라이딩 오작용 문제

```java
public class InstrumentedHashSet<E> extends HashSet<E> {
    private int addCount = 0;

    @Override
    public boolean add(E e) {
        addCount++;
        return super.add(e);
    }

    @Override
    public boolean addAll(Collection<? extends E> c) {
        addCount += c.size();
        return super.addAll(c);
    }

}
```

InstrumentedHashSet 은 요소를 추가한 횟수를 기록하기 위해 addCount 라는 인스턴스 변수를 포함한다.

add, addAll 메서드에 addCount를 증가 시킨 후 super 참조를 이용해 부모 클래스의 메서드를 호출해서 요소를 추가한다.

```java
InstrumentedHashSet<String> languages = new InstrumentedHashSet<>();
languages.addAll(Arrays.asList("Java", "Ruby", "Scala"));
```

위와 같이 메서드 호출시 addCount 의 값이 3을 기대할 수 있다.

addAll 호출해서 addCount 의 값이 3이 되고, HashSet은 각각의 요소를 추가하기 위해 내부적으로 add 메서드를 호출하고

InstrumentedHashSet의 add 메서드가 세 번 호출되어 addCount에 3이 더해져 최종 결과는 6이 된다.

> 자식 클래스가 부모 클래스의 메서드를 오버라이딩할 경우 
부모 클래스가 자신의 메서드를 사용하는 방법에 자식 클래스가 결합될 수 있다.

## 부모 클래스와 자식 클래스의 동시 수정 문제

```java
public class Song {
    private String singer;
    private String title;

    public Song(String singer, String title) {
        this.singer = singer;
        this.title = title;
    }

		// getter()
		...
}
```

 음악 목록을 추가할 수 있는 플레이리스트를 구현한다.

```java
public class Playlist {
    private List<Song> tracks = new ArrayList<>();

    public void append(Song song) {
        getTracks().add(song);
    }

    public List<Song> getTracks() {
        return tracks;
    }
}
```

Playlist는 트랙에 노래를 추가할 수 있는 append 메서드를 구현한다.

```java
public class PersonalPlaylist extends Playlist {
    public void remove(Song song) {
        getTracks().remove(song);
    }
}
```

노래를 삭제할 수 있는 기능이 추가된 PersonalPlaylist가 필요하여 가장 빠른 구현 방법인 상속을 통해 Playlist 의 코드를 재사용한다.

- 요구사항이 변경돼서 Playlist에서 노래의 목록뿐만 아니라 가수별 노래의 제목도 함께 관리해야 한다.
- 노래를 추가한 후에 가수의 이름을 키로 노래의 제목을 추가하도록 메서드를 수정한다.

```java
public void append(Song song) {
		tracks.add(song);
		singers.put(song.getSinger(), song.getTitle());
}
```

위 수정 내용이 정상적으로 동작하려면 PersonalPlaylist의 remove 메서드도 함께 수정해야 한다.

```java
public void remove(Song song) {
		getTracks().remove(song);
		getSingers().remove(song.getSinger());
}
```

자식 클래스가 부모 클래스의 메서드를 오버라이딩하거나 불필요한 인터페이스를 상속받지 않았음에도

부모 클래스를 수정할 때 자식 클래스를 함께 수정해야 할 수도 있다.

상속으로 인해 부모 클래스와 자식 클래스가 강하게 결합되어 있기 때문에 위 문제를 해결하기 어렵다

# Phone 다시 살펴보기

## 추상화에 의존하자

코드 중복을 제거하기 위해 상속을 도입할 때 따르는 두 가지 원칙

- 두 메서드가 유사하게 보인다면 차이점을 메서드로 추출하라.

    메서드 추출을 통해 두 메서드를 동일한 형태로 보이도록 만들 수 있다.

- 부모 클래스의 코드를 하위로 내리지 말고 자식 클래스의 코드를 상위로 올려라.

    부모 클래스의 구체적인 메서드를 자식 클래스로 내리는 것보다 

    자식 클래스의 추상적인 메서드를 부모 클래스로 올리는 것이 재사용성과 응집도 측면에서 더 뛰어난 결과를 얻을 수 있다.

## 차이를 메서드로 추출하라

"변하는 것으로부터 변하지 않는 것을 분리하라"

"변하는 부분을 찾고 이를 캡슐화하라"

```java
public class Phone {
    ...
		public Money calculateFee() {
        Money result = Money.ZERO;

        for(Call call : calls) {
            result = result.plus(calculateCallFee(call));
        }
        return result;
    }

    @Override
    private Money calculateCallFee(Call call) {
        return amount.times(call.getDuration().getSeconds() / seconds.getSeconds());
    }
}
```

```java
public class NightlyDiscountPhone {
    ...
		public Money calculateFee() {
        Money result = Money.ZERO;

        for(Call call : calls) {
            result = result.plus(calculateCallFee(call));
        }
        return result;
    }

    @Override
	  private Money calculateCallFee(Call call) {
        if (call.getFrom().getHour() >= LATE_NIGHT_HOUR) {
            return nightlyAmount.times(call.getDuration().getSeconds() / seconds.getSeconds());
        } else {
            return regularAmount.times(call.getDuration().getSeconds() / seconds.getSeconds());
        }
    }
}
```

calculateFee 메서드는 동일해졌고 추출한 calculateCallFee 메서드 안에 서로 다른 부분을 격리시켜 놓았다.

## 중복 코드를 부모 클래스로 올려라

```java
public abstract class AbstractPhone {
    private List<Call> calls = new ArrayList<>();

    **public Money calculateFee() {**
        Money result = Money.ZERO;

        for(Call call : calls) {
            result = result.plus(calculateCallFee(call));
        }

        return result;
    }

    abstract protected Money calculateCallFee(Call call);
}
```

calculateFee 메서드를 AbstractPhone으로 이동시키고

calculateCallFee 메서드는 시그니처는 동일하지만 내부 구현이 서로 다르기 때문에 자식클래스에서 오버라이딩할 수 있도록 protected로 선언한다

![3](https://user-images.githubusercontent.com/61505386/123279070-7a384d00-d542-11eb-9b2b-a493de095997.png)


## 추상화가 핵심이다

공통 코드를 이동시킨 후에 각 클래스는 서로 다른 변경의 이류를 가진다 - 단일 책임 원칙 준수 - 응집도가 높다

- AbstractPhone: 전체 통화 목록을 계산
- Phone: 일반 요금제의 통화 한 건 계산
- NightlyDiscountPhone: 심야 할인 요금제의 통화 한 건을 계산

calculateCallFee 메서드의 시그니처가 변경되지 않는 한 

부모 클래스의 내부 구현이 변경되더라도 자식 클래스는 영향을 받지 않는다 - 낮은 결합도 유지

## 의도를 드러내는 이름 선택하기

NightlyDiscountPhone - 심야 할인 요금제와 관련된 내용 구현 - 명확함

Phone - 일반 요금제와 관련된 내용 - 명시적으로 전달하지 못함

Phone → RegularPhone

AbstractPhone - 전화기를 포괄한다는 의미 명확하지 않음

AbstractPhone - Phone

## 세금 추가하기

```java
public abstract class Phone {
    **private double taxRate;**
    private List<Call> calls = new ArrayList<>();

    public Phone(double taxRate) {
        **this.taxRate = taxRate;**
    }

    public Money calculateFee() {
        Money result = Money.ZERO;

        for(Call call : calls) {
            result = result.plus(calculateCallFee(call));
        }

        **return result.plus(result.times(taxRate));**
    }

    protected abstract Money calculateCallFee(Call call);
}
```

Phone에 인스턴스 변수인 taxRate를 추가하고 인스턴스  변수의 값을 초기화하는 생성자를 추가했다.

자식클래스인 RegularPhone과 NightlyDiscountPhone의 생성자 역시 taxRate를 초기화하기 위해 수정해야 한다

인스턴스 초기화 로직을 변경 > 동일한 세금 계산 코드 중복

8장. 객체 생성 로직에 대한 변경을 막기보다는 핵심 로직의 중복을 막아라.

핵심 로직은 한 곳에 모아 놓고 조심스럽게 캡슐화해야 한다.

# 차이에 의한 프로그래밍(Programming by difference)

기존 코드와 다른 부분만을 추가함으로써 애플리케이션의 기능을 확장하는 방법

중복 코드를 제거하고 코드를 재사용하는 것이 목표다

![4](https://user-images.githubusercontent.com/61505386/123279078-7b697a00-d542-11eb-8b9f-01196b463c6b.png)


- 상속이 코드 재사용이라는 측면에서 매우 강력한 도구인 것은 사실이지만
- 강력한 만큼 잘못 사용할 경우에 돌아오는 피해 역시 크다
- 상속의 오용과 남용은 애플리케이션을 이해하고 확장하기 어렵게 만든다
- 정말로 필요한 경우에만 상속을 사용하라.
