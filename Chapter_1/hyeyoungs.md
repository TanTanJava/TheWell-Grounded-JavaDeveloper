# Chapter 01 모던 자바 소개

## 1.1 언어와 플랫폼
### JVM의 동작
1. javac로 .class파일 생성
2. 저장소에 있는 .class파일을 JVM 메모리로 로딩
3. .class를 변환작업을 거쳐 새로운 .class 생성
4. 새로 생성된 .class를 JIT컴파일러로 컴파일하여 기계어로 변환
5. 변환된 기계어를 실행

- .class → 클래스로딩으로 이어지는 바이트코드 과정은 클래스파일 생성과정 (컴파일러)
- 실제적인곳은 JIT(just-in-time) 컴파일러를 이용하는 과정이 실제 컴파일 과정이라고 볼 수 있음 (인터프리터)

## 1.3 향상된 타입 추론(var 키워드)
1. 익명클래스를 명시적으로 선언하지 않아도 자동화됨 (p15예제)
2. for(var item : collection) 에서 특히 효과적임
3. 람다 인자에 애노테이션 넣고 싶을 때 Consumer<Person> f = (@Nonnull var person) -> {..}
### 클래스 로딩 후 바이트코드 조작
1. 자바 : 지역 변수 선언 시점의 컨텍스트만 고려 (var는 필드, 메서드 인수, 반환 타입에는 사용할 수 없다.)
2. 코틀린 : 빌더가 되는 람다 내의 내용까지 고려
3. 러스트 : 변수의 스코프가 닿는 모든 곳의 사용을

## 1.5 자바 11에서의 작은 변경 사항
### 컬렉션 팩토리
* 배열 리터럴과 유사한, List of() Set of() Map ofEntries() 메서드 추가
* 팩토리 메서드는 immutable 타입의 인스턴스를 생성
#### 불변 컬렉션
런타임에 그 리스트가 불변인지 파악하기가 지옥
- 자바 10이상
<br>If the given collection is already an unmodifiable list, it is returned as is.
<br>Otherwise, a new list containing the elements of the given collection is returned 
  - list == List.copyOf(list)
  - set == Set.copyOf(set)
  - map == Map.copyOf(map)
- 자바 9이하
1. 리플렉션쓰기
   <br>var name = target.getClass().getName()
   <br>target.contains("ImmutableCollections") || target..contains("Unmodifiable");
2. 넣다 빼기
```
try {
      list.add(null);
      list.remove(list.size() - 1);
    }
```
### 엔터프라이즈 모듈 제거
### HTTP/2(자바11)
### 단일 소스 코드 프로그램
* 자바 11에서는 소스 코드는 메모리에서 컴파일된 다음, 디스크에 .class 파일을 생성하지 않고 인터프리터로 실행할 수 있음
* 이 기능의 제약사항
    * 단일 소스 파일에 있는 코드로 제한됨
    * 동일한 실행에서 추가적인 소스 컴파일을 할 수 없음
    * 소스 파일에 여러 클래스를 포함할 수 있음
    * 소스 파일에서 첫 번째 클래스를 진입점으로 선언해야 함
    * 진입점 클래스에서 main 메서드를 정의해야 함
