상속의 목적은 코드 재사용이 아니다.

상속은 타입 계층을 구조화하기 위해 사용해야 한다.

# 다형성

다형성(Polymorphism)

- 그리스어에서 '많은'을 의미하는 'poly'와 '형태'를 의미하는 'morph'의 합성어로
- 많은 형태를 가질 수 있는 능력을 의미한다.
- 컴퓨터 과학에서는 하나의 추상 인터페이스에 대해 코드를 작성하고 이 추상 인터페이스에 대해 서로 다른 구현을 연결할 수 있는 능력으로 정의한다.

객체지향 프로그래밍에서 사용되는 다형성

![1](https://user-images.githubusercontent.com/61505386/126667064-d4fc9679-1502-49f6-9316-4c9bfdef7c73.png)

**오버로딩 다형성**

- 하나의 클래스 안에 동일한 이름의 메서드가 존재하는 경우
- 하나의 plus 메서드 이름만 기억하면 유사한 작업을 수행하는 메서드의 이름만 통일할 수 있다.

```java
public class Money {
		public Money plus(Money amount) { ... }
		public Money plus(BigDecimal amount) { ... }
		public Money plus(long amount) { ... }
}
```

**강제 다형성**

- 언어가 지원하는 자동적인 타입 변환이나
- 사용자가 직접 구현한 타입 변환을 이용해 동일한 연산자를 다양한 타입에 사용할 수 있는 방식
- 자바에서 이항 연산자 '+'는 `피연산자가 모두 정수일 경우`에는 정수에 대한 `덧셈 연산자`로 동작하지만
- 하나는 `정수형이고 다른 하나는 문자열인 경우`에는 `연결 연산자`로 동작한다.

```java
int sum = 1 + 2
String info = "number: " + 1
```

- 이때 정수형 피연산자는 문자열 타입으로 강제 형변환된다.
- 일반적으로 오버로딩 다형성과 강제 다형성을 함께 사용하면 모호해질 수 있는데 실제로 어떤 메서드가 호출될지를 판단하기가 어려워지기 때문이다.

**매개변수 다형성**

- 제네릭 프로그래밍과 관련이 높다.
- 클래스의 인스턴스 변수나 메서드의 매개변수 타입을 임의의 타입으로 선언한 후
- 사용하는 시점에 구체적인 타입으로 지정하는 방식
- 자바의 List 인터페이스는 컬렉션에 보관할 요소의 타입을 임의의 타입 T로 지정하고 있으며
- 실제 인스턴스를 생성하는 시점에 T를 구체적인 타입으로 지정할 수 있게 하고 있다.
- 따라서 List 인터페이스는 다양한 타입의 요소를 다루기 위해 동일한 오퍼레이션으로 사용할 수 있다.

**포함 다형성**

- 메시지가 동일하더라도 수신한 객체의 타입에 따라 실제로 수행되는 행동이 달라지는 능력
- **서브타입(Subtype) 다형성**이라고도 부른다.
- 객체지향 프로그래밍에서 특별한 언급 없이 다형성이라고 할 때는 포함 다형성을 의미하는 것이 일반적이다.

```java
public class Movie {
		private DiscountPolicy discountPolicy;

		public Money calculateMovieFee(Screening screening) {
				return fee.minus(discountPolicy.calculateDiscountAmount(screening));
		}
}
```

- Moive 클래스는 discountPolicy 에게 calculateDiscountAmount 메시지를 전송하지만
- 실제로 실행되는 메서드는 메시지를 수신한 객체의 타입에 따라 달라진다.
- 포함 다형성을 위한 전제 조건은 자식 클래스가 부모 클래스의 서브타입이어야 한다는 것이다.

상속의 진정한 목적은 코드 재사용이 아니라 다형성을 위한 서브타입 계층을 구축하는 것이다.

# 상속의 양면성

객체지향 패러다임의 근간을 이루는 아이디어는 

- `데이터`와 `행동`을 객체라고 불리는 하나의 실행 단위안으로 통합하는 것
- 객체지향 프로그램을 작성하기 위해서는 항상 `데이터`와 `행동`이라는 두 가지 관점을 함께 고려해야 한다.

**데이터 관점의 상속**

- 부모 클래스에서 정의한 모든 데이터를 자식 클래스의 인스턴스에 자동으로 포함시킬 수 있다.

**행동 관점의 상속**

- 데이터뿐만 아니라 부모 클래스에서 정의한 일부 메서드 역시 자동으로 자식 클래스에 포함시킬 수 있다.

상속의 매커니즘을 이해하는 데 필요한 몇 가지 개념

- 업캐스팅
- 동적 메서드 탐색
- 동적 바인딩
- self 참조
- super 참조

## 상속을 사용한 강의 평가

### Lecture 클래스 살펴보기

```java
public class Lecture {
    private int pass;
    private String title;
    private List<Integer> scores = new ArrayList<>();

    public Lecture(String title, int pass, List<Integer> scores) {
        this.title = title;
        this.pass = pass;
        this.scores = scores;
    }

    public double average() {
        return scores.stream().mapToInt(Integer::intValue).average().orElse(0);
    }

    public List<Integer> getScores() {
        return Collections.unmodifiableList(scores);
    }

    public String evaluate() {
        return String.format("Pass:%d Fail:%d", passCount(), failCount());
    }

    private long passCount() {
        return scores.stream().filter(score -> score >= pass).count();
    }

    private long failCount() {
        return scores.size() - passCount();
    }
}
```

```java
Lecture lecture = new Lecture("객체지향 프로그래밍", 70, Arrays.asList(81, 95, 75, 50, 45));
String evaluration = lecture.evaluate();
```

### 상속을 이용해 Lecture 클래스 재사용하기

- Lecture의 출력 결과에 등급별 통계를 추가하기

```java
public class Grade {
    private String name;
    private int upper,lower;

    private Grade(String name, int upper, int lower) {
        this.name = name;
        this.upper = upper;
        this.lower = lower;
    }

    public String getName() {
        return name;
    }

    public boolean isName(String name) {
        return this.name.equals(name);
    }

    public boolean include(int score) {
        return score >= lower && score <= upper;
    }
}
```

```java
public class GradeLecture extends Lecture {
    private List<Grade> grades;

    public GradeLecture(String name, int pass, List<Grade> grades, List<Integer> scores) {
        super(name, pass, scores);
        this.grades = grades;
    }

    @Override
    public String evaluate() {
        return super.evaluate() + ", " + gradesStatistics();
    }

    private String gradesStatistics() {
        return grades.stream().map(grade -> format(grade)).collect(joining(" "));
    }

    private String format(Grade grade) {
        return String.format("%s:%d", grade.getName(), gradeCount(grade));
    }

    private long gradeCount(Grade grade) {
        return getScores().stream().filter(grade::include).count();
    }

    public double average(String gradeName) {
        return grades.stream()
                .filter(each -> each.isName(gradeName))
                .findFirst()
                .map(this::gradeAverage)
                .orElse(0d);
    }

    private double gradeAverage(Grade grade) {
        return getScores().stream()
                .filter(grade::include)
                .mapToInt(Integer::intValue)
                .average()
                .orElse(0);
    }
}
```

- GradeLecture의 evaluate 메서드에서는 예약어 super를 이용해 Lecture 클래스의 evaluate 메서드를 먼저 실행한다.
- GradeLecture와 Lecture에 구현된 두 evaluate 메서드의 시그니처가 완전히 동일하다
- 부모 클래스와 자식 클래스에 동일한 시그니처를 가진 메서드가 존재할 경우 자식 클래스의 메서드가 우선순위가 더 높다.
- = 메시지를 수신했을 때 자식 클래스의 메서드가 실행된다.

**메서드 오버라이딩**

- 자식 클래스 안에 상속받은 메서드와 동일한 시그니처의 메서드를 재정의해서
- 부모클래스의 구현을 새로운 구현으로 대체하는 것

메서드 오버로딩

- 부모 클래스에서 정의한 메서드와 이름은 동일하지만
- 시그니처는 다른 메서드를 자식 클래스에 추가하는 것

## 데이터 관점의 상속

```java
Lecture lecture = new Lecture("OOP", 70, Arrays.asList(81, 95, 75, 50, 45));
```

- Lecture의 인스턴스를 생성하면
1. 시스템은 인스턴스 변수 title, pass, scores를 저장할 수 있는 메모리 공간을 할당하고
2. 생성자의 매개변수를 이용해 값을 설정한 후 
3. 생성된 인스턴스의 주소를 lecture라는 이름의 변수에 대입한다.

메모리 상에 생성된 객체의 모습을 개념적으로 표현

![2](https://user-images.githubusercontent.com/61505386/126667083-367b9a40-29eb-4650-ae0b-b603d16135bf.png)

```java
Lecture lecture = new GradeLecture("OOP", 
																			70, 
																			Arrays.asList(new Grade("A", 100, 95),
																										new Grade("A", 94, 80),
																										new Grade("A", 79, 70),
																										new Grade("A", 69, 50),
																										new Grade("A", 49, 0)),
																			Arrays.asList(81, 95, 75, 50, 45));
```

- 상속을 인스턴스 관점에서 바라볼 때는 개념적으로
- 자식 클래스의 인스턴스 안에 부모 클래스의 인스턴스가 포함되는 것으로 생각하는 것이 유용하다.

![3](https://user-images.githubusercontent.com/61505386/126667086-2e3b056a-12ec-444d-a72d-b0fd962d5c81.png)

자식 클래스의 인스턴스에서 부모 클래스의 인스턴스로 접근 가능한 링크가 존재하는 것처럼 생각해도 무방하다

![4](https://user-images.githubusercontent.com/61505386/126667091-89e580a6-9627-4044-ab68-88528026da3c.png)

데이터 관점에서 상속은 자식 클래스의 인스턴스 안에 부모 클래스의 인스턴스를 포함하는 것으로 볼 수 있다.

## 행동 관점의 상속

부모 클래스가 정의한 일부 메서드를 자식 클래스의 메서드로 포함시키는 것

어떻게 부모 클래스에서 구현한 메서드를 자식 클래스의 인스턴스에서 수행할 수 있을까?

- 런타임에 시스템이 자식 클래스에 정의되지 않은 메서드가 있을 경우
- 이 메서드를 부모 클래스 안에서 탐색하기 때문이다.

`객체`의 경우 서로 다른 상태를 저장할 수 있도록 `각 인스턴스별`로 `독립적인 메모리`를 할당받아야 한다.

하지만 메서드의 경우에는 동일한 클래스의 인스턴스끼리 공유가 가능하기 때문에 

클래스는 한 번만 메모리에 로드하고 각 인스턴스별로 클래스를 가리키는 포인터를 갖게 하는 것이 경제적이다. 

![5](https://user-images.githubusercontent.com/61505386/126667094-78edfa19-64b9-420a-b467-b47cefb1c461.png)

- 인스턴스는 두 개가 생성됐지만 클래스는 단 하나만 메모리에 로드됐다
- 각 객체는 자신의 클래스인 Lecture의 위치를 가리키는 class라는 이름의 포인터를 가지며
- 이 포인터를 이용해 자신의 클래스 정보에 접근할 수 있다.
- Lecture 클래스가 자신의 부모 클래스인 Object의 위치를 가리키는 parent라는 이름의 포인터를 가진다.
- 이 포인터를 이용하면 클래스의 상속 게층을 따라 부모 클래스의 정의로 이동하는 것이 가능하다.

1. 메시지를 수신한 객체는 class 포인터로 연결된 자신의 클래스에서 적절한 메서드가 존재하는지를 찾는다.
2. 만약 메서드가 존재하지 않으면 클래스의 parent 포인터를 따라 부모 클래스를 차례대로 훑어가면서 적절한 메서드가 존재하는지를 검색한다.
