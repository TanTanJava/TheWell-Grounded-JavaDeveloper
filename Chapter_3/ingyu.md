### Text 블록

```jsx
String query = """
	SELECT "ORDER_ID", "QUANTITY", "CURRENCY_PAIR" FROM "ORDERS"
	WHERE "CLIENT_ID" = ?
	ORDER BY "DATE_TIME", "STATUS" LIMIT 100;
	""";
```

- TEXT 블록은 문자열 리터럴과 마찬가지다.
- 클래스 파일에 상수로 기록하기 전에 javac 에서 TEXT 블록을 처리한다.

### switch 표현식

```jsx
String message = switch(month) {
	case 12:
	case 1:
	case 2:
		yield "Winter, brrr";
	case 3:
	case 4:
	case 5:
		yield "Spring has sprung!";
	// ... 등등
}
```

- 각 case는 새로운 yield 키워드를 사용해서 원하는 값을 다시 String 변수에 할당한다.

위와 같이 사용하지 않는다면 → switch 표현식에 있음을 나타내므로 이러한 경우 명시적인 yield 가 필요하지 않다. 

```jsx
String message = switch(month) {
	case 1, 2, 12 -> "Winter, brrrr"
	case 3, 4, 5 -> "Spring has sprung!";
	case 6, 7, 8 -> "Summer is here!";
	case 9, 10, 11 -> "Fall has descended";
	default       -> {
		throw new IllegalArgumentException("Ooops, that's not a month");
	}
}
```

### record

- 데이터 전용 집계 모델링을 위한 최고 수준의 수단 제공
- 자바의 타입 시스템에서 발생할 수 있는 격차 해소
- 공통 프로그래밍 패턴을 위한 언어 수준 문법 제공
- 클래스 상용구 감소

```jsx
public record FXOrder (int units, CurrencyPair pair, Side side)
```

- toString(), equals(), hashCode() 메서드는 invokedynamic 기반의 메커니즘을 사용해서 구현된다.

다음 몇가지 질문에 대한 대답

- 하이버네이트에서 그들을 프록시 할 수 있나?
    - JPA/Hibernate 에서 record를 엔티티(@Entity) 로 사용할 수 있는지, 프록시 객체로 다룰 수 있는지에 대한것
    - Hibernate 는 엔티티를 직접 로드하지 않고 프록시 객체를 생성하여 참조
    - Order 클래스가  있으면 Hibernate는 Order$$HibernateProxy 같은 클래스를 내부적으로 생성
    - record는 Hibernate에서 프록시 할 수 있을까? → record 는 final이라 불가능
    - record는 final 클래스이기 때문에 서브 클래스를 만들 수 없음 → 상속 불가능 (private final)
    - Hibernate는 기본 생성자가 NoArgsConstructor 를 필요로 하지만 record는 생성자가 final
- 전통적인 자바 빈과 완전히 호환되나?
    - JavaBean 조건 만족하는 객체
        - 기본 생성자 제공
        - Getter와 Setter 제공
        - 직렬화 가능
    - record는 자동으로 생성자 toString(), equals(), hashCode()를 제공
        - Getter는 있지만 Setter는 없음 → record는 불변 객체
        - 기본 생성자 없음 → record는 모든 필드를 초기화해야함
    - record는 전통적인 JavaBean과 완전히 호환되지 않는다.
- 이름 제거와 형식 유연성을 지원하나?
    - 이름 제거 : 불필요한 상용구 코드 (Boilerplate Code) 줄이는것을 의미
    - 기존 Java 클래스에서는 필드 선언, 생성자, Getter, Setter, equals(), hashCode(), toString() 을 전부 작성해야 하지만 record를 사용하면 자동으로 생성되므로 코드가 단순해진다.
    - 형식 유연성 :
        - 데이터 타입을 변경할 때의 유연성을 의미
        - record의 결정적인 제한점은 final이라서 한번 정해지면 불가능, 상속 불가, 제네릭 지원 가능 (형식 유연성을 어느 정도 제공하지만, 기존 Java 클래스보다 . 더유연하다고 보긴 어렵다.)
- 패턴 매칭과 구조 분해가 지원될까?
    - 패턴 매칭 : instanceof 별도의 변수를 선언하지 않아도 자동으로 형변환 수행 - 자바 16부터 지원
    
    ```jsx
    public record FXOrder(int units, CurrencyPair pair, Side side) {}
    
    void processOrder(Object obj) {
        if (obj instanceof FXOrder fxOrder) { // 패턴 매칭 적용
            System.out.println("Order units: " + fxOrder.units()); // 자동 형변환
        }
    }
    ```
    
    - 구조 분해 : 객체에서 개별 필드를 변수로 추출하는 기능
    
    ```jsx
    FXOrder order = new FXOrder(10, CurrencyPair.USD_EUR, Side.BUY);
    int units, CurrencyPair pair, Side side;
    (units, pair, side) = order; // ❌ 컴파일 오류! Java는 구조 분해를 지원하지 않음.
    ```
    

### 명목적 타이핑

- 모든 자바 저장소 에는 명확한 타입이 있다.
- 자바 익명 클래스에도 이름이 있고 컴파일러가 할당해준다.
- 자바 빈은 변경 가능하지만 record는 변경 불가능하고 접근자에 대한 규칙이 다르다.

### 콤팩트 레코드 생성자

- 레코드 클래스의 콤팩트 생성자의 형식 매개변수는 암묵적으로 선언된다.
- 레코드 생성자 본문에서 레코드가 생성될 떄 코드를 실행할 수 있다는 점이다.

### sealed 타입

- 하위 타입들에 의해서만 확장할 수 있고, 다른 하위 타입들은 확장할 수 없다는 개념이다.
- sealed로 선언된 경우 해당 클래스는 현재의 컴파일 유닛 내에서만 확장될 수 있다.
- Pet 이 abstract 로 되어 있는 이유는 Pet.Cat, [Pet.Dog](http://Pet.Dog) 객체만을 원하기 때문이다.

```jsx
public abstract sealed class Pet {
	private final String name;
	
	protected Per(String name) {
		this.name = name;
	}
	
	public String name() {
		return name;
	}
	
	public static final class Cat extends Pet {
		public Cat(String name) {
			super(name);
		}
		
		void meow() {
			System.out.println(name() + " meows");
		}
	}

 public static final class Dog extends Pet {
	 public Dog(String name) {
		 super(name);
	 }
	 
	 void bark() {
		 System.out.println(name() + " barks");
	 }
}
```

### instanceof의 새로운 형식

```jsx
if (o instanceof String s) {
	System.out.println(s.length()); // s가 이 분기의 범위 내에 존재한다.
} else {
	System.out.println("Not a String"); //s가 else 브랜치에서 범위 내에 존재하지 않는다.
}
```

### 패턴 매칭과 프리뷰 기능

```jsx
var msg = switch (o) {
	case String s -> "String of length:" + s.length();
	case Integer i -> "Integer:" + i;
	case null, default -> "Not a String or Ineger";
	//이제 null을 case 레이블로 사용해 npe를 방지할 수 있게된다. 
}; 
```

- 컴팡일러에서 프리뷰 기능을 활성화 해야 사용할 수 있따.

```jsx
$ javac --enable-preview -source 17 ch3/Java17Examples.java 
```

- 프리뷰 기능을 계속 활성화해야 하는 것은 번거롭지만, 미완성된 기능을 사용하는 코드가 프로덕션 환경으로 빠져나가 문제를 일으키지 않도록 개발자를 보호하기 위한 조치다.

### 요약

- 자바 17에는 개발자가 코드에서 즉시 활용할 수 있는 여러 가지 새로운 기능이 도입됐다.
    - 여러 줄 문자열을 위한 Text 블록
    - records
    - sealed 타입 : 객체지향 모델링 개념
    - 패턴 매칭 : 자바 17에서 아직 완전히 제공하지 않았지만 향후 버전에서 언어가 나아가야할 방향을 명확하게 보여준다.