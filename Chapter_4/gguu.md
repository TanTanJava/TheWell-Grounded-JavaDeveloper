# 4. 클래스파일과 바이트 코드

## 4.1 클래스 로딩과 클래스 객체
- .class 파일은 필드, 메서드, 상속 정보, 애너테이션, 기타 메타데이터로 구성된 JVM에서의 타입을 정의한다.

### 4.1.1 로딩과 링킹
- JVM 관점에서 class파일을 실행가능한(바이트코드) 형태로 만들기 위해서 로딩<sub>loading</sub>과 링킹<sub>linking</sub>으로 나눌 수 있다.
#### 로딩
- 클래스 로더가 `.class` 파일을 찾아서 JVM의 메서드영역에 적재

#### 링킹
1. 검증
   2. 자체 일관성이 있고 상수에 대한 기본 동작 규칙을 준수하는지 확인
   3. 해당 검사를 통해 런타임 검사를 건너뛸 수 있다 -> 빠르게 코드를 실행할 수 있다
2. 준비
   3. 메모리를 할당하고 클래스의 정적 변수를 초기화할 수 있도록 준비
   4. 변수를 초기화하거나 JVM 바이트코드를 실행하진 않음
   ```java
   class Test {
    static int a = 10;
    static String str = "Hello";
   }
   ```
   위 코드에서 a 와 str 은 준비 단계에서 메모리에 할당되고, 기본값(int → 0, String → null)으로 설정됨. 값은 초기화 단계에서 설정됨.
3. 해결
   4. 클래스, 메소드, 필드 참조를 실제 메모리 주소로 변경하는 과정 
   5. 예를 들어, System.out.println() 같은 메소드 호출이 실제 java.lang.System 클래스의 out 객체와 연결됨.
4. 초기화
   5. 정적변수 초기화 및 static {} 블록 실행

정리하면, JVM이 클래스를 실행하려면 **로딩 -> 링크 -> 초기화** 단계를 거쳐야한다.
로딩과 링킹의 최종 결과물은 `Class` 객체이다
`Class` 객체는 메서드, 필드, 생성자 등에 대한 간접적인 액세스를 위해 `리플랙션 API`와 함께 사용할 수 있다.


## 4.2 클래스 로더
```csharp
[Bootstrap ClassLoader]    → JDK 기본 클래스(java.base 모듈) 로드
        ↓
[Platform ClassLoader]     → java.sql, java.logging 등의 모듈 로드
        ↓
[Application ClassLoader]  → classpath에 있는 사용자 클래스 로드
```

### 4.2.1 사용자 정의 클래스 로더
- 개발자는 각자의 필요에 맞는 자체 클래스로더를 작성할 수 있다

-  사용자 정의 클래스로더가 필요한 이유 
1. 동적 클래스 로딩
   2. 실행 중에 필요한 클래스 파일을 네트워크, 데이터베이스, 암호화된 파일 등에서 불러올 수 있음.
2. 보안 관리 
   3. 특정 디렉토리에서만 클래스를 로드하거나, 암호화된 클래스를 복호화하여 로드하는 등의 보안 기능 추가 가능.
3. 클래스 격리 (Class Isolation)
   4. 서로 다른 버전의 라이브러리를 동시에 사용해야 할 때, 별도의 클래스로더를 만들어 충돌을 방지할 수 있음.
4. 애플리케이션 서버 및 플러그인 시스템 
   5. Tomcat, Spring Boot, OSGi 등은 사용자 정의 클래스로더를 사용하여 독립적인 애플리케이션 모듈을 관리함.

```java
import java.io.*;

public class CustomClassLoader extends ClassLoader {
    private String classPath;

    public CustomClassLoader(String classPath) {
        this.classPath = classPath;
    }

    @Override
    protected Class<?> findClass(String name) throws ClassNotFoundException {
        byte[] classData = loadClassData(name);
        if (classData == null) {
            throw new ClassNotFoundException();
        }
        return defineClass(name, classData, 0, classData.length);
    }

    private byte[] loadClassData(String name) {
        try {
            String fileName = classPath + name.replace(".", "/") + ".class";
            InputStream inputStream = new FileInputStream(fileName);
            ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
            byte[] buffer = new byte[1024];
            int bytesRead;
            while ((bytesRead = inputStream.read(buffer)) != -1) {
                outputStream.write(buffer, 0, bytesRead);
            }
            inputStream.close();
            return outputStream.toByteArray();
        } catch (IOException e) {
            return null;
        }
    }

    public static void main(String[] args) {
        try {
            CustomClassLoader loader = new CustomClassLoader("C:/custom_classes/");
            Class<?> clazz = loader.loadClass("com.example.MyClass");
            Object obj = clazz.getDeclaredConstructor().newInstance();
            System.out.println("클래스 로드 성공: " + obj.getClass().getName());
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

- 동작 방식
1. findClass(String name) 메서드를 오버라이딩하여 클래스를 로드할 경로에서 .class 파일을 찾음.
2. loadClassData(String name) 메서드를 사용해 바이너리 데이터를 읽어옴.
3. defineClass()를 사용하여 바이트 배열을 클래스로 변환.
4. main()에서 클래스를 로드하고 인스턴스를 생성하여 정상적으로 로드되었는지 확인.

- 실제 사용 사례
1. 웹 애플리케이션 서버 (Tomcat, Jetty): 웹 애플리케이션마다 독립적인 클래스로더를 사용하여 클래스 격리를 수행.
2. 플러그인 시스템 (Eclipse, IntelliJ, Minecraft Mods): 동적으로 플러그인을 로드하여 확장 기능을 제공.
3. JVM 기반 스크립트 엔진 (Groovy, JRuby, Jython): 실행 중에 동적으로 클래스를 생성하고 로드.


## 4.4 바이트 코드
- 바이트코드는 사람이 읽을 수 있는 소스 코드와 기계 코드의 중간에 있는 프로그램의 중간 표현이다.
- `javac` 명령어 사용함
- 바이트코드는 일반적으로 JIT 방식으로 기계어 코드로 컴파일 할 수 있다.

### 4.4.2 런타임 환경
- 모든 계산과 연산에 스택을 사용하는데, 이를 평가스택이라고 함.
- 메서드가 호출될 때마다 새로운 평가스택이 생성된다.
- JVM은 각 자바스레드마다 호출 스택을 가지고 있으며, 어떤 메서드가 실행 됐는지 기록한다.
- 스레드별 호출스택과 메서드별 평가스택의 차이를 명확하게 아는게 중요하다.

### ~4.4.9 각각 기능별 오퍼레이션 코드가 존재..

## 4.5 리플랙션
- 리플랙션은 객체를 쿼리하거나 내부를 조사해서 런타임에 해당 객체의 기능을 발견하고 사용하는 기능
- 즉, 컴파일타임이 아니라 실행 중에 객체의 정보를 분석하고 조작할 수 있는 강력한 기능
- 콘텍스트에 따라 여러가지로 이해 가능하다.
  - 프로그래밍 언어 API
  - 프로그래밍 스타일이나 기법
  - 기법을 사용할 수 있게 하는 런타임 메커니즘
  - 언어 타입 시스템의 특성

### 4.5.1 리플랙션 소개
- 리플랙션을 통해 사용할 수 있는 기능
  - 클래스의 메타데이터 조회
    - 클래스의 이름, 패키지 정보 가져오기
  - 필드 및 메서드 정보 조회
    - 선언된 필드 및 메서드 목록 확인
  - private 멤버 접근
    - private 필드나 메서드 접근 가능
  - 동적 객체 생성
    - 클래스 이름만 알고 있어도 인스턴스를 생성할 수 있음
  - 동적 메서드 호출
    - 메서드 이름을 문자열로 입력해 실행 가능
  - 애노테이션(Annotation) 정보 조회
    - 클래스, 필드, 메서드에 붙은 애노테이션을 읽을 수 있음
- 사용목적
  - 프레임워크 개발(Spring, Hibernate): 애노테이션을 읽고 동적으로 객체를 생성하는 데 사용됨.
  - IDE 및 디버거: 클래스 정보를 분석하여 코드 보조 기능 제공.
  - 테스트 자동화(JUnit): private 메서드를 호출하여 테스트 가능.

### 4.5.3 리플렉션의 문제점
- 자바 컬렉션 이전에 만들어진 매우 오래된 API로, 배열 타입이 여기저기에 존재한다.
- 어떤 메서드 오버로드를 호출할지 결정하는 것은 쉽지 않다.
- API에는 메서드에 대한 리플렉션적인 접근을 위해 getMethod()와 getDeclaredMethod()라는 서로 다른 두 가지 메서드가 제공된다.
- API는 접근 제어를 무시하는 데 사용할 수 있는 setAccessible() 메서드를 제공한다.
- 리플렉션 호출에 대한 예외 처리가 복잡하다. 체크된 예외가 런타임 예외로 변환된다.
- 원시 타입을 전달하거나 반환하는 리플렉션 호출을 수행하려면 박싱과 언박싱이 필요하다.
- 원시 타입에는 플레이스홀더 클래스 객체가 필요하며, 이는 실제로는 Class 타입이다.
- void 메서드에는 java.lang.Void 타입을 도입해야 한다.
- API의 어려운 부분 외에도 자바 리플렉션은 JVM의 JIT 컴파일러에 대한 호환성 부족 등 여러가지 이유로 인해 항상 성능 저하를 겪어 왔다.



