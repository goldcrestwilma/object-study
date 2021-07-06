합성

- 전체를 표현하는 객체가 부분을 표현하는 객체를 포함해서 부분 객체의 코드를 재사용한다.

상속

- 부모 클래스와 자식 클래스 사이의 의존성은 `컴파일타임`에 해결된다.
- is-a 관계
- 클래스 사이의 정적인 관계

합성

- 두 객체 사이의 의존성은 `런타임`에 해결된다.
- has-a 관계
- 객체 사이의 동적인 관계

> 상속대신 합성을 사용하면 변경하기 쉽고 유연한 설계를 얻을 수 있다.

# 상속을 합성으로 변경하기

코드 재사용을 위해 상속을 남용했을 때 문제

- 불필요한 인터페이스 상속 문제
- 메서드 오버라이딩의 오작용 문제
- 부모 클래스와 자식 클래스의 동시 수정 문제

## 불필요한 인터페이스 상속 문제: java.util.Properties와 java.util.Stack

```java
public class Properties {
    **private Hashtable<String, String> properties = new Hashtable <>();**

    public String setProperty(String key, String value) {
        return properties.put(key, value);
    }

    public String getProperty(String key) {
        return properties.get(key);
    }
}
```

- Hashtable을 Properties의 인스턴스 변수로 포함시키면 합성 관계로 변경할 수 있다.
- Properties의 클라이언트는 모든 타입의 키와 값을 저장할 수 있는 Hashtable의 오퍼레이션을 사용할 수 없기 때문에 String 타입의 키와 값만 허용하는 Properties의 규칙을 어길 위험성은 사라진다.
- Properties는 Hashtable의 내부 구현에 관해 알지 못한다. 단지 Properties는 get와 set 오퍼레이션이 포함된 퍼블릭 인터페이스를 통해서만 Hashtable과 협력할 수 있다.

```java
public class Stack<E> {
    **private Vector<E> elements = new Vector<>();**

    public E push(E item) {
        elements.addElement(item);
        return item;
    }

    public E pop() {
        if (elements.isEmpty()) {
            throw new EmptyStackException();
        }
        return elements.remove(elements.size() - 1);
    }
}
```

- 합성 관계로 변경함으로써 클라이언트가 Stack을 잘못 사용할 수도 있다는 가능성을 깔끔하게 제거했다.

## 메서드 오버라이딩의 오작용 문제: InstrumentedHashSet

```java
public class InstrumentedHashSet<E> implements Set<E> {
    private int addCount = 0;
    private Set<E> set;

    public InstrumentedHashSet(Set<E> set) {
        this.set = set;
    }

    @Override
    public boolean add(E e) {
        addCount++;
        return set.add(e);
    }

    @Override
    public boolean addAll(Collection<? extends E> c) {
        addCount += c.size();
        return set.addAll(c);
    }

    public int getAddCount() {
        return addCount;
    }

    @Override public boolean remove(Object o) {
        return set.remove(o);
    }

    @Override public void clear() {
        set.clear();
    }

    @Override public boolean equals(Object o) {
        return set.equals(o);
    }

    @Override public int hashCode() {
        return set.hashCode();
    }

    @Override public Spliterator<E> spliterator() {
        return set.spliterator();
    }

    @Override public int size() {
        return set.size();
    }

    @Override public boolean isEmpty() {
        return set.isEmpty();
    }

    @Override public boolean contains(Object o) {
        return set.contains(o);
    }

    @Override public Iterator<E> iterator() {
        return set.iterator();
    }

    @Override public Object[] toArray() {
        return set.toArray();
    }

    @Override public <T> T[] toArray(T[] a) {
        return set.toArray(a);
    }

    @Override public boolean containsAll(Collection<?> c) {
        return set.containsAll(c);
    }

    @Override public boolean retainAll(Collection<?> c) {
        return set.retainAll(c);
    }

    @Override public boolean removeAll(Collection<?> c) {
        return set.removeAll(c);
    }
}
```

- InstrumentedHashSet이 Set 인터페이스를 실체화하면서 내부에 HashSet의 인스턴스를 합성하면
- HashSet에 대한 구현 결합도를 제거하면서도 퍼블릭 인터페이스를 그대로 유지할 수 있다.

## 부모 클래스와 자식 클래스의 동시 수정문제: PersonalPlaylist

```java
public class PersonalPlaylist {
    private Playlist playlist = new Playlist();

    public void append(Song song) {
        playlist.append(song);
    }

    public void remove(Song song) {
        playlist.getTracks().remove(song);
        playlist.getSingers().remove(song.getSinger());
    }
}
```

- Playlist의 경우에는 합성으로 변경하더라도 가수별 노래 목록을 유지하기 위해 Playlist와 PersonalPlaylist를 함께 수정해야 하는 문제가 해결되지 않는다.
- 향후에 Playlist의 내부 구현을 변경하더라도 파급효과를 최대한 PersonalPlaylist 내부로 캡슐화할 수 있기 때문이다.

> 몽키 패치(Monkey Patch)
현재 실행 중인 환경에만 영향을 미치도록 지역적으로 코드를 수정하거나 확장하는 것을 가리킨다. Playlist의 코드를 수정할 권한이 없거나 소스코드가 존재하지 않는다고 하더라도 몽키 패치가 지원되는 환경이라면 Playlist에 직접 remove 메서드를 추가하는 것이 가능하다.
루비 - Open Class
C# - 확장 메서드
스칼라 - 암시적 변환
자바는 언어 차원에서 몽키 패치를 지원하지 않기 때문에 바이트코드를 직접 변환하거나 AOP를 이용해 몽키 패치를 구현하고 있다.

# 상속으로 인한 조합의 폭발적인 증가

## 기존 정책과 부가 정책 조합하기

10장에서 소개했던 핸드폰 과금 시스템에 새로운 요구사항 추가

- 현재 시스템에는 일반 요금제와 심야 할인 요금제 존재
- 두 요금제에 부가 정책을 추가하는 것

![1](https://user-images.githubusercontent.com/61505386/124615873-97f5a280-deb0-11eb-8416-be3a007a66f2.png)

클래스 폭발(Class explosion)

- 상속의 남용으로 하나의 기능을 추가하기 위해 필요 이상으로 많은 수의 클래스가 추가해야 하는 경우
- 조합의 폭발(combinational explosion)

# 합성 관계로 변경하기

기본 정책과 부가 정책을 포괄하는 RatePolicy 인터페이스

- Phone을 인자로 받아 계산된 요금을 반환하는 calculateFee 오퍼레이션을 포함하는 간단한 인터페이스

```java
public interface RatePolicy {
    Money calculateFee(Phone phone);
}
```

기본 정책 구현

```java
public abstract class BasicRatePolicy implements RatePolicy {
    @Override
    public Money calculateFee(Phone phone) {
        Money result = Money.ZERO;

        for(Call call : phone.getCalls()) {
            result.plus(calculateCallFee(call));
        }

        return result;
    }

    protected abstract Money calculateCallFee(Call call);
}
```

일반 요금제 구현

```java
public class RegularPolicy extends BasicRatePolicy {
    private Money amount;
    private Duration seconds;

    public RegularPolicy(Money amount, Duration seconds) {
        this.amount = amount;
        this.seconds = seconds;
    }

    @Override
    protected Money calculateCallFee(Call call) {
        return amount.times(call.getDuration().getSeconds() / seconds.getSeconds());
    }
}
```

심야 요금제 구현

```java
public class NightlyDiscountPolicy extends BasicRatePolicy {
    private static final int LATE_NIGHT_HOUR = 22;

    private Money nightlyAmount;
    private Money regularAmount;
    private Duration seconds;

    public NightlyDiscountPolicy(Money nightlyAmount, Money regularAmount, Duration seconds) {
        this.nightlyAmount = nightlyAmount;
        this.regularAmount = regularAmount;
        this.seconds = seconds;
    }

    @Override
    protected Money calculateCallFee(Call call) {
        if (call.getFrom().getHour() >= LATE_NIGHT_HOUR) {
            return nightlyAmount.times(call.getDuration().getSeconds() / seconds.getSeconds());
        }

        return regularAmount.times(call.getDuration().getSeconds() / seconds.getSeconds());
    }
}
```

Phone 클래스 수정

```java
public class Phone {
    **private RatePolicy ratePolicy; // 합성**
    private List<Call> calls = new ArrayList<>();

    public Phone(RatePolicy ratePolicy) {
        this.ratePolicy = ratePolicy;
    }

    public List<Call> getCalls() {
        return Collections.unmodifiableList(calls);
    }

    public Money calculateFee() {
        return ratePolicy.calculateFee(this);
    }
}
```

- Phone은 컴파일타임 의존성을 구체적인 `런타임 의존성`으로 대체하기 위해
- 생성자를 통해 RatePolicy의 인스턴스에 대한 의존성을 주입 받는다.

## 부가 정책 적용하기

```java
public abstract class AdditionalRatePolicy implements RatePolicy {
    private RatePolicy next;

    public AdditionalRatePolicy(RatePolicy next) {
        this.next = next;
    }

    @Override
    public Money calculateFee(Phone phone) {
        Money fee = next.calculateFee(phone);
        return afterCalculated(fee) ;
    }

    abstract protected Money afterCalculated(Money fee);
}
```

- 다른 요금 정책과 조합될 수 있도록 RatePolicy 타입의 next라는 이름을 가진 인스턴스 변수를 내부에 포함한다.

세금 정책 구현

```java
public class TaxablePolicy extends AdditionalRatePolicy {
    private double taxRatio;

    public TaxablePolicy(double taxRatio, RatePolicy next) {
        super(next);
        this.taxRatio = taxRatio;
    }

    @Override
    protected Money afterCalculated(Money fee) {
        return fee.plus(fee.times(taxRatio));
    }
}
```

기본 요금 할인 정책 추가

```java
public class RateDiscountablePolicy extends AdditionalRatePolicy {
    private Money discountAmount;

    public RateDiscountablePolicy(Money discountAmount, RatePolicy next) {
        super(next);
        this.discountAmount = discountAmount;
    }

    @Override
    protected Money afterCalculated(Money fee) {
        return fee.minus(discountAmount);
    }
}
```

![2](https://user-images.githubusercontent.com/61505386/124615899-9c21c000-deb0-11eb-818a-99cf2d57d812.png)
