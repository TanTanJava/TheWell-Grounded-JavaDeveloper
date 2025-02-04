# Chapter03 자바 17
> 자바 17에는 개발자가 코드에서 즉시 활용할 수 있는 여러가지 새로운 기능이 도입됨

## 3.1 여러 줄 문자열을 위한 Text 블록
### Text 블록이란
- 여러 줄에 걸쳐 확자되는 문자열 리터럴을 허용
  - 일련의 이스케이프 시퀀스가 필요하지 않게 됨
  - 자바 프로그램에 포함해야 하는 코드 문자열을 읽을 수 있게 됨

```java
String query = """
    SELECT `EMP_ID`, `LAST_NAME` FROM `EMPLOYEE_TB`
    WHERE `CITY` = 'INDIANAPOLIS'
    ORDER BY `EMP_ID`, `LAST_NAME`;
    """;
```
- Text 블록은 세 개의 쌍따옴표로 시작하고 끝남
- Text 블록은 각 줄의 시작 부분에 공백으로 들여쓰기를 할 수 있으며 이 공백은 무시됨

### Text 블록과 문자열 리터럴

#### Text 블록과 문자열 리터럴 공통점 : Text 블록은 문자열 리터럴과 동일하게 상수 표현식임
  - final 키워드가 붙은 String 변수에 Text 블록을 넣을 수 있음
  - 문자열 리터럴처럼 클래스 파일 내부의 상수 풀(Constant Pool) 에 저장됨
    ```java
    final String s1 = "Hello, world!";
    final String s2 = """
        Hello, world!
        """;
    // s1과 s2는 둘 다 컴파일 타임에 결정되는 상수
    ```

#### Text 블록은 문자열 리터럴의 차이점 : Text 블록은 클래스 파일에 상수를 기록하기 전에 javac에서 Text 블록을 처리함
  - 문자열 리터럴은 그냥 있는 그대로 저장되지만, Text 블록은 컴파일 시점에 줄 바꿈과 공백이 정리되고 변환됨
    - 즉, 코드에 적힌 Text 블록과 실제 클래스 파일에 저장된 문자열이 다를 수도 있음


#### 런타임에서는 문자열 리터럴과 Text 블록의 차이가 없음
  - 프로그램이 실행될 때 "Hello, world!"와 """Hello, world!"""는 같은 문자열처럼 동작함 
  - 클래스 파일에는 문자열이 어떻게 입력되었는지가 남아 있지 않고, 변환된 최종 문자열만 저장됨

#### 정리
- 문자열 리터럴: "큰따옴표"로 감싼 문자열 (컴파일러가 그대로 저장)
- Text 블록: """큰따옴표 세 개"""로 감싼 여러 줄 문자열 (컴파일러가 처리 후 저장)
- 둘 다 상수 풀(Constant Pool)에 저장되므로 실행할 때는 차이가 없음
- 하지만 컴파일 시점에 Text 블록은 먼저 가공됨 (공백 정리, 줄바꿈 포함 등)

<br>

## 3.2 switch 표현식
switch 표현식으로 사용하여 값을 생성할 수 있음
```java
String message = switch(month) {
    case 1, 2, 12 -> "Winter";
    case 3, 4, 5  -> "Spring";
    case 6, 7, 8 -> "Summer";
    case 9, 10, 11 -> "Fall";
    default -> {
        throw new IllegalArgumentException("Invalid month");
    }
}
```
- switch 표현식은 입력 유형에 가능한 모든 경우를 처리해야 하며, 그렇지 않으면 코드가 컴파일 되지 않음
  - ex. default 줄을 제거하면 컴파일 오류가 발생하여, 안전장치를 만들어 놓음

<br>

## 3.3 records
### record 클래스란?
- 불변 데이터 클래스(Immutable Data Class) 로, 최소한의 코드로 값을 저장하는 클래스를 만들 수 있음 
- 자동으로 생성자, equals(), hashCode(), toString() 이 제공돼서 불변 객체를 쉽게 만들 수 있음

#### 기존 방식 vs record 방식 비교
- 기존 방식 (일반 클래스) : 불변 객체를 만들려면 코드가 길어짐
```java
class Person {
private final String name;
private final int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String name() { return name; }
    public int age() { return age; }

    @Override
    public boolean equals(Object o) { /* 구현 코드 */ }
    @Override
    public int hashCode() { /* 구현 코드 */ }
    @Override
    public String toString() { /* 구현 코드 */ }
}
```
- record 클래스 사용 → 단 한 줄만으로 동일한 기능 제공!
```java
record Person(String name, int age) {}
```

### record의 핵심 개념
#### 레코드 클래스 (Record Class)
- record 키워드를 사용하여 정의하는 특별한 클래스 
  - record는 final이라 상속 불가능
  - 내부 상태(필드)를 수정할 수 없어서 완전 불변(immutable) 객체가 됨
  - equals(), hashCode(), toString()이 자동 생성됨
  ```java
  record Car(String model, int year) {} // 레코드 클래스 선언
  ```

#### 레코드 컴포넌트 (Record Component)
- record 선언부에서 정의하는 필드 역할을 하는 값들 
  - 일반 클래스의 멤버 변수(필드) 와 비슷하지만, record에서는 자동으로 private final로 선언됨 
  - 컴포넌트 이름을 그대로 게터(getter) 메서드 이름으로 사용
    ```java
    record Book(String title, String author, double price) {}
    // title, author, price가 레코드 컴포넌트
    ```
    - 자동으로 제공되는 메서드
      ```java
      Book book = new Book("자바의 정석", "남궁성", 30000);
      System.out.println(book.title());  // "자바의 정석"
      System.out.println(book.author()); // "남궁성"
      System.out.println(book.price());  // 30000.0
      ```
      - 일반 클래스와 다르게 getTitle()이 아니라 title() 형태의 메서드가 자동 생성됨

#### 상태 설명 (State Description)
- record는 불변 객체라 객체의 상태를 변경할 수 없음 
  - 즉, 한 번 생성된 후에는 모든 필드 값이 유지되어야 함

- 불변 객체라서 setter가 없음
```java
record Car(String model, int year) {}
Car car = new Car("Tesla", 2023);
// car.model = "BMW";  //  ❌ 컴파일 에러 (수정 불가능)
```
- 새로운 객체를 만들어야 변경 가능
```java
Car newCar = new Car("BMW", 2023); // 새 객체 생성
```

#### 명목적 타이핑 (Nominal Typing)
-  같은 데이터 타입이라도 다른 의미를 가지는 경우 record를 사용하면 안전하게 구분할 수 있음 
  - 일반 클래스 사용 (오류 발생 가능)
    ```java
    class Money {
    private final int amount;
    private final String currency;

        public Money(int amount, String currency) {
            this.amount = amount;
            this.currency = currency;
        }
    
        public Money(String currency, int amount) {
            this.amount = amount;
            this.currency = currency;
        }
    }
  
    Money money1 = new Money(1000, "USD");
    Money money2 = new Money("USD", 1000); // ❌ 실수 가능
    ```
    → amount와 currency 순서를 헷갈릴 수 있음

  - record를 사용하면?
    ```java
      record Money(int amount, String currency) {}
      
      Money money1 = new Money(1000, "USD");
      Money money2 = new Money("USD", 1000); // ❌ 타입이 다르면 컴파일 오류 발생!
     ``` 
    → 각 컴포넌트의 타입과 순서가 정확하게 지정되므로 실수 방지 가능

#### 콤팩트 레코드 생성자 (Compact Constructor)
- 일반 record는 생성자를 자동으로 생성하지만, 데이터 유효성 검사가 필요할 때 직접 생성자를 만들 수 있음
<br>이때 콤팩트 생성자(Compact Constructor) 를 사용하면 필드 선언 없이 검증 로직만 작성 가능
  - 일반 생성자 방식
      ```java
      record Person(String name, int age) {
          public Person(String name, int age) {
              if (age < 0) {
                  throw new IllegalArgumentException("나이는 0 이상이어야 합니다.");
              }
              this.name = name;
              this.age = age;
          }
      }
      ```
    - 필드를 직접 this.name = name; 처럼 할당해야 함
  - 콤팩트 생성자 사용
      ```java
      record Person(String name, int age) {
          public Person {
              if (age < 0) {
                  throw new IllegalArgumentException("나이는 0 이상이어야 합니다.");
              }
          }
      }
      ```
    - 자동으로 필드가 할당되므로, 검증 로직만 추가하면 됨
    - 코드가 더 간결하고 가독성이 좋아짐


### 개념
  - 레코드 클래스 (Record Class) : record 키워드로 선언하는 불변 데이터 클래스 
  - 레코드 컴포넌트 (Record Component) : record 선언부에 정의된 필드 역할을 하는 값 
  - 상태 설명 (State Description) : record는 불변이라 값 변경 불가 
  - 명목적 타이핑 (Nominal Typing)	: 같은 데이터 타입이라도 의미를 구분할 수 있도록 타입을 강제 
  - 콤팩트 레코드 생성자 (Compact Constructor) : 필드 할당 없이 검증 로직만 작성하는 생성자

### 정리 : 클래스보다 record가 더 간결하고, 실수 방지 기능이 뛰어남
  - 불변 객체가 필요할 때 record 사용!
  - 자동 생성되는 메서드 활용 (equals(), hashCode(), toString())
  - 필요하면 Compact Constructor로 검증 로직 추가

### record에 대한 또다른 견해
#### 현재는 record를 쓰는 게 최선이지만, 미래에는 value class가 더 좋은 선택이 될 수도 있음!
- record는 값 객체를 간단하게 만들기 위한 도구
- equals(), hashCode(), toString()을 자동 제공하지만 오버라이드 가능 
- sealed interface와 결합 가능하여 타입 안정성을 높일 수 있음
- Rust의 trait처럼 쓰기엔 제한이 많음
- 프로젝트 발할라(Value Class)가 도입되면, record의 필요성이 애매해질 가능성이 있음

<br>

## 3.4 sealed 타입
### sealed 타입이란?
- 특정 클래스(혹은 인터페이스)를 상속(구현)할 수 있는 타입을 제한하는 기능 
  - 자바에서는 기본적으로 누구나 클래스를 상속할 수 있음 → 하지만, 때때로 의도적으로 상속을 제한하고 싶을 때가 있음

- sealed 타입을 사용하면 허용된 클래스만 상속(구현)할 수 있도록 제한할 수 있음
  - 불필요한 확장을 막고, 더 안전한 계층 구조를 설계할 수 있음

#### sealed 타입의 문법
- sealed 키워드 사용
    ```java
    sealed class Shape permits Circle, Rectangle, Triangle {}
    ```
  - Shape 클래스를 sealed로 선언
  - permits 키워드를 사용해 상속할 수 있는 클래스들을 명시적으로 지정
  - Circle, Rectangle, Triangle 세 가지 클래스만 Shape을 상속 가능!

#### sealed 클래스를 상속받는 클래스의 종류
sealed 클래스를 상속하는 클래스는 세 가지 키워드 중 하나를 사용해야 함.

1) final: 더 이상 상속 불가능
  ```java
  sealed class Shape permits Circle, Rectangle, Triangle {}
  final class Circle extends Shape {}  // 더 이상 하위 클래스를 만들 수 없음
  // Circle은 Shape을 상속하지만, 다른 클래스가 Circle을 다시 상속할 수 없음.
  ```

2) sealed: 추가적인 상속 제한
  ```java
  sealed class Shape permits Circle, Rectangle, Triangle {}
  sealed class Rectangle extends Shape permits FilledRectangle, OutlinedRectangle {}
  
  final class FilledRectangle extends Rectangle {}  //  더 이상 상속 불가능
  final class OutlinedRectangle extends Rectangle {}  //  더 이상 상속 불가능
  // Rectangle 자체도 sealed → 허용된 클래스만 추가적으로 상속 가능
   ```

3) non-sealed: 자유롭게 상속 가능
  ```java
  sealed class Shape permits Circle, Rectangle, Triangle {}
  non-sealed class Triangle extends Shape {}  // 다른 클래스들이 Triangle을 자유롭게 상속 가능
  // Triangle을 non-sealed로 선언 → 누구든지 Triangle을 상속할 수 있음
  ```

### sealed 타입의 핵심 장점
- 불필요한 상속을 방지하여 유지보수성을 높임
- 타입 안정성을 보장 → 허용된 하위 클래스만 존재하므로, 예측 가능한 코드 작성 가능
- 패턴 매칭과 궁합이 좋음 (switch 표현식에서 활용 가능)

### sealed 타입과 switch 패턴 매칭 활용
- sealed 타입을 사용하면 패턴 매칭을 더욱 안전하게 사용할 수 있음
  ```java
  sealed interface Shape permits Circle, Rectangle {}
  
  record Circle(double radius) implements Shape {}
  record Rectangle(double width, double height) implements Shape {}
  
  static double calculateArea(Shape shape) {
      return switch (shape) {
          case Circle c -> Math.PI * c.radius() * c.radius();
          case Rectangle r -> r.width() * r.height();
      };
  }
- sealed 타입 덕분에 switch 문에서 모든 경우를 다루었는지 컴파일러가 체크 가능
- 새로운 서브 클래스가 추가되면 switch에서 빠진 경우를 컴파일러가 알려줌

###  sealed 타입과 enum의 차이점

|             |  sealed 타입 | enum |
|-------------| ------------|------|
| 하위 타입 개수    | 제한 가능하지만 고정 아님 | 완전히 고정 |
| 상태          | 상태값을 가질 수 있음 | 각 값이 정해진 상수 |
| 기능 확장       | 메서드 추가 가능 | 불가능 |
| 패턴 매칭 지원	   | 가능 |	가능 |
- sealed 타입은 더 유연한 타입 계층 구조를 만들 수 있음
- enum은 고정된 값 집합을 정의할 때 적절함
- sealed 타입은 enum과 달리 각 타입이 자체적인 메서드를 가질 수 있음

### 정리 : sealed 타입을 언제 사용하면 좋을까?
- 특정 클래스/인터페이스의 상속을 제한하고 싶을 때
- 예측 가능한 타입 계층을 만들고 싶을 때
- 패턴 매칭과 함께 사용할 때 타입 안정성을 높이고 싶을 때
- 기존 enum으로는 부족한, 좀 더 확장 가능한 구조가 필요할 때

- 즉, sealed 타입은 "열린 enum" 같은 느낌 → 필요한 경우에는 enum보다 더 강력한 기능을 제공

<br>

## 3.5 instanceof의 새로운 형식
### 기존 instanceof의 문제점
자바에서 instanceof를 사용할 때는 타입을 검사한 후, 명시적으로 캐스팅해야 하는 불편함이 있었음
 ```java
  if (obj instanceof String) {
    String str = (String) obj;  // 명시적 캐스팅 필요
    System.out.println(str.length());
  }
  ```
  - obj가 String인지 확인한 후에도 여전히 캐스팅을 직접 해야 함
  - 불필요한 코드 중복이 발생하고, 가독성이 떨어짐

### 개선된 instanceof 문법 (패턴 매칭 도입)
자바 16에서 도입, 자바 17에서 안정화
<br>타입 검사와 동시에 변수를 선언하는 방식으로 변경됨

 ```java
  if (obj instanceof String str) {  // 자동으로 str 변수 생성
      System.out.println(str.length());  // 캐스팅 없이 바로 사용 가능
  }
```
- instanceof 연산자 뒤에 바로 변수를 선언 → 자동으로 타입 캐스팅
- 코드가 더 간결하고 가독성이 좋아짐

### instanceof 패턴 매칭의 장점
- 불필요한 명시적 캐스팅 제거 → 코드 간결화
- 가독성 및 유지보수성 향상
- 패턴 매칭을 활용한 직관적인 코드 작성 가능

### 정리
switch 패턴 매칭과 함께 더 간결한 코드 작성이 필요할 때 사용할 수 있으므로,
<br> 이제 instanceof를 사용할 때는 새로운 방식으로 작성하는 것이 더 효율적! 

<br>

## 3.6 패턴 매칭과 프리뷰 기능
자바 17에서는 아직 완전히 제공하지는 않지만 향후 버전에서 언어가 나아갈 방향을 명확히 보여줌

### 패턴 매칭(Pattern Matching)이란?
- 패턴 매칭
<br> 패턴 매칭(Pattern Matching)은 객체의 타입을 검사하고, 자동으로 변수에 할당하는 기능을 의미함
<br> 쉽게 말해, instanceof 검사 후 명시적 캐스팅 없이 바로 사용할 수 있도록 해주는 기능

###  패턴 매칭 적용 사례
#### instanceof에서의  패턴 매칭
- 기존에는 instanceof 사용 후 수동으로 타입 캐스팅해야 했음

 ```java
  if (obj instanceof String str) {  // 자동으로 str 변수 생성
      System.out.println(str.length());  // 캐스팅 없이 바로 사용 가능
  }
  //  타입 체크 + 변수 할당을 한 번에 처리 → 코드가 더 간결해짐
```

#### switch 문에서의 패턴 매칭 (프리뷰 기능)
자바 17에서는 switch 문에서도 패턴 매칭을 지원하는 기능이 프리뷰로 제공됨
 ```java
    static void printType(Object obj) {
    switch (obj) {
        case Integer i -> System.out.println("정수: " + i);
        case String s -> System.out.println("문자열: " + s);
        case null -> System.out.println("null 값");
       default -> System.out.println("알 수 없는 타입");
    }
 }
 ```
- case 문에서 타입을 검사하고 변수에 자동 할당!
- 불필요한 instanceof + 캐스팅 코드가 필요 없음

### 프리뷰 기능(Preview Features)이란?
- 자바에서는 새로운 기능을 바로 정식 기능으로 추가하지 않고, 프리뷰(Preview) 기능으로 먼저 제공함
- 프리뷰 기능은 실험적인 기능으로, 실제 사용 피드백을 반영하여 개선될 수 있음
- 프리뷰 기능을 사용하려면 컴파일 옵션을 추가해야 함

#### 프리뷰 기능 활성화 방법
```
javac --enable-preview --release 17 Main.java
java --enable-preview Main
// --enable-preview 옵션을 사용해야 실행 가능
```

#### 자바 17의 주요 프리뷰 기능
- switch 문에서 패턴 매칭 지원
- sealed class 기능
- Foreign Function & Memory API (네이티브 코드 연동)

### 패턴 매칭과 프리뷰 기능이 왜 중요할까?
- 패턴 매칭은 불필요한 타입 변환을 줄여 코드 가독성을 높여줌 
- switch 문에서 패턴 매칭을 사용하면 더 간결하고 강력한 코드 작성 가능
- 프리뷰 기능을 통해 실험적인 기능을 미리 경험하고, 향후 변경 가능성도 고려해야 함

## 내 생각
- 자바는 점점 더 간결하고 타입 안전성을 강화하는 방향으로 발전 중임
- dto, command 같은 경우는 record를 사용하면 값 안전하게 사용하고, 성능도 높일 것 같음

## 공부해볼만한 부분
- [invoke dynamic](https://blog.hexabrain.net/400)
- [Record 성능](https://gree-me.tistory.com/20)
