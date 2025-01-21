# Chapter 01 모던 자바 소개

## 1.1 언어와 플랫폼
### JVM의 동작
1. javac로 .class파일 생성
2. 저장소에 있는 .class파일을 JVM 메모리로 로딩
3. .class를 변환작업을 거쳐 새로운 .class 생성
4. 새로 생성된 .class를 JIT컴파일러로 컴파일하여 기계어로 변환
5. 변환된 기계어를 실행
### 자바는 컴파일언어? 인터프리터언어? = 둘다
- .class → 클래스로딩으로 이어지는 바이트코드 과정은 클래스파일 생성과정 (컴파일러)
- 실제적인곳은 JIT(just-in-time) 컴파일러를 이용하는 과정이 실제 컴파일 과정이라고 볼 수 있음 (인터프리터)

## 1.3 향상된 타입 추론(var 키워드)
1. 익명클래스를 명시적으로 선언하지 않아도 자동화됨 (p15예제)
2. for(var item : collection) 에서 특히 효과적임
3. 람다 인자에 애노테이션 넣고 싶을 때 Consumer<Person> f = (@Nonnull var person) -> {..}
### 클래스 로딩 후 바이트코드 조작
1. 자바 : 지역 변수 선언 시점의 컨텍스트만 고려 (var는 필드, 메서드 인수, 반환 타입에는 사용할 수 없음)
2. 코틀린 : 빌더가 되는 람다 내의 내용까지 고려
3. 러스트 : 변수의 스코프가 닿는 모든 곳의 사용을

## 1.5 자바 11에서의 작은 변경 사항
### 컬렉션 팩토리를 통해 불변 컬렉션 간단히 생성
* 자바 9 이상에서는 팩토리 메서드를 통해 불변 컬렉션(Immutable Collection)을 쉽게 생성할 수 있음
  * 배열 리터럴과 유사한, List of() Set of() Map ofEntries() 메서드 추가
#### 참고) 불변 컬렉션의 특징
- 수정 불가 
  - 팩토리 메서드로 생성한 컬렉션은 수정할 수 없음 
    - add(), remove() 등의 메서드를 호출하면 UnsupportedOperationException이 발생함
- 예전에는 불변 리스트를 생성하려면 별도의 라이브러리나 복잡한 코드가 필요했음. 이제 List.of() 등으로 간단히 생성함 
    - cf) 자바 8 이하에서는 불변 컬렉션을 생성하기 위해 직접적으로 지원되는 기능이 없었음 
      - 라이브러리 사용 
        ```
        import com.google.common.collect.ImmutableList;
        List<String> list = ImmutableList.of("A", "B", "C");
        ```        
      - 기존 컬렉션을 감싸 수정 불가능하게 만드는 방식
        ```
        import java.util.Collections;
        List<String> mutableList = new ArrayList<>(List.of("A", "B", "C"));
        List<String> immutableList = Collections.unmodifiableList(mutableList);
        immutableList.add("D"); // UnsupportedOperationException 발생!
        ```        
#### 참고) 런타임에 특정 리스트가 불변인지 파악하기가 어려움 <br>→ List.copyOf() 사용해 안전하게 불변 컬렉션을 생성하므로 불변 여부 확인 과정을 생략하여 간접적으로 해결
- 자바 10이상
  <br>List.copyOf()는 불변 여부와 상관없이 항상 안전하게 불변 컬렉션을 반환함
  <br>불변 컬렉션이 입력으로 주어지면, 동일한 컬렉션을 반환
  <br>가변 컬렉션이 입력으로 주어지면, 불변 컬렉션으로 복사하여 반환

  ```
  List<String> list1 = List.of("a", "b");
  List<String> list2 = List.copyOf(list1); // list2 == list1 (같은 객체)
  
  List<String> mutableList = new ArrayList<>(List.of("a", "b"));
  List<String> list3 = List.copyOf(mutableList); // 새 불변 컬렉션 생성
  ```
  - 자바 10 이상에서는 불변 여부를 확인하지 않고, copyOf()를 사용해 안전하게 불변 컬렉션을 생성하는 것이 권장됨
  - 이 방식은 불변 여부 확인 과정을 생략하고, 항상 불변 컬렉션을 보장하므로 문제를 간접적으로 해결함

- 자바 9이하
  - 자바 9 이하에서 불변 컬렉션 다루기 불변인지 파악하려면 isImmutable() 같은 메서드가 없어서 개발자가 우회적인 방법을 써야 함 
    1. 리플렉션쓰기 : 자바 9 이하에서는 내부 구현에 의존하여 불변 컬렉션을 확인해야 함
       ```
        var name = target.getClass().getName()
        target.contains("ImmutableCollections") || target..contains("Unmodifiable");
       ```
    2. 넣다 빼기 : 컬렉션에 add()나 remove()를 시도한 뒤 예외가 발생하면, 해당 컬렉션이 불변임을 간접적으로 확인할 수 있었음
        ```
        try {
              list.add(null);
              list.remove(list.size() - 1);
        }
        ```
   - 내부 구현에 의존하기 때문에 신뢰성이 낮고, 코드가 비효율적임
### 엔터프라이즈 모듈 제거
자바 EE 모듈을 제거하고 자바 EE 플랫폼을 자바 SE 플랫폼으로 이동(플랫폼 간소화)
### HTTP/2 (자바11)
### 단일 소스 코드 프로그램
* 자바 11에서는 소스 코드는 메모리에서 컴파일된 다음, 디스크에 .class 파일을 생성하지 않고 인터프리터로 실행할 수 있음
