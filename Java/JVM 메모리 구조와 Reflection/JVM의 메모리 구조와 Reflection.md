# JVM의 메모리 구조와 Reflection

## JVM 메모리 구조

---

![Untitled](JVM%E1%84%8B%E1%85%B4%20%E1%84%86%E1%85%A6%E1%84%86%E1%85%A9%E1%84%85%E1%85%B5%20%E1%84%80%E1%85%AE%E1%84%8C%E1%85%A9%E1%84%8B%E1%85%AA%20Reflection%20705f4bc66be6453cadde76777e9cfef3/Untitled.png)

![Untitled](JVM%E1%84%8B%E1%85%B4%20%E1%84%86%E1%85%A6%E1%84%86%E1%85%A9%E1%84%85%E1%85%B5%20%E1%84%80%E1%85%AE%E1%84%8C%E1%85%A9%E1%84%8B%E1%85%AA%20Reflection%20705f4bc66be6453cadde76777e9cfef3/Untitled%201.png)

## Method 영역 / Class 영역

---

Class에 대한 모든 정보가 저장되는 영역

### 특징

- 런타임 중에 변경할 수 없고, 고정된 공간을 차지하고 있다.
- Heap 영역과 함께 모든 스레드가 공유하는 영역이다.
    
    ⇒ 멀티 스레드 환경에서, 동기화에 주의해야하는 영역이다.
    

### 저장되는 정보

1. Type Information : 클래스와 인터페이스의 정보이다.
    - Type명 : Package name + Class name
    - Type의 종류 : Type이 Class인지 Interface인지에 대한 정보
    - Type의 제어자 : 접근 제어자(public, private, default 등), 그 외 제어자 (abstract, final 등)
    - 연관된 Interface 정보 : 사용된 Interface의 정보
2. Runtime Constant Pool : Type의 상수 정보를 저장하는 Pool이다. 각 상수로는 인덱스를 통해 접근 가능하다.
    - Type, Field, Method로 접근하기 위한 Reference 정보 - 객체 접근을 위해 필요하다.
    - 각 클래스와 인터페이스의 상수 뿐만 아니라, 메소드와 필드에 대한 모든 레퍼런스까지 담고 있는 테이블이다.
    - 즉, 어떤 메소드나 필드를 참조할 때 JVM은 런타임 상수 풀을 통해 해당 메소드나 필드의 실제 메모리 상 주소를 찾아서 참조한다.
3. Field Information : 인스턴스 변수의 정보를 저장한다.
    - Type명 : 인스턴스 변수의 타입
    - 제어자 : 접근 제어자(public, private 등), 그 외 제어자(static, final, volatile 등)
4. Method Information : 메서드의 모든 정보를 저장한다.
    - Method 명
    - Method 반환 타입
    - Method parameter수와 각 parameter의 타입 정보
    - 외 필요한 메서드에 대한 정보들
5. Class Variable : static 키워드로 선언된 변수가 저장된다.

## Heap 영역

---

런타임에 생성되는 모든 객체가 저장되는 영역

### 특징

- Class 영역에 로드된 클래스로만 객체의 생성이 가능하다.
- 실제 데이터는 Heap 영역에, 참조값(주소)는 Stack 영역에 저장한다.

![Untitled](JVM%E1%84%8B%E1%85%B4%20%E1%84%86%E1%85%A6%E1%84%86%E1%85%A9%E1%84%85%E1%85%B5%20%E1%84%80%E1%85%AE%E1%84%8C%E1%85%A9%E1%84%8B%E1%85%AA%20Reflection%20705f4bc66be6453cadde76777e9cfef3/Untitled%202.png)

- GC가 이 영역에서 동작한다.
- Method 영역과 함께 모든 스레드가 공유하는 영역이다.
    
    ⇒ 멀티 스레드 환경에서, 동기화에 주의해야하는 영역이다.
    

### 저장되는 정보

1. 객체의 데이터
    1. new 키워드로 생성된 인스턴스 객체의 데이터
2. Reference 자료형 데이터 ( Class, interface, Enum, Array, … )

## JVM Stacks 영역

---

메서드를 실행하기 위한 정보들이 저장되는 영역

- 기본 자료형 ( int, boolean, … ), 지역 변수, 파라미터, 리턴 값, 객체의 **참조 주소값** 등이 저장된다.

### 특징

- 내부에, Frame이라는 자료구조를 포함한다.
    - 메서드가 하나 호출될 때마다, 프레임이 새로 생성된다.
    - 메서드가 끝나거나, 예외가 생성되면 사라진다.
    - 현재 클래스의 Constant Pool에 대한 참조, 지역 변수 배열, Operand Stack으로 구성되어 있다.
- 스레드가 생성될 때마다 같이 생성되고, 서로 다른 스레드가 침범할 수 없는 영역이다.

### 저장되는 정보

1. Heap 영역에 생성되는 객체의 참조값
    - 지역 변수, 파라미터, 리턴 값 등의 참조값이 저장된다.
2. Primitive 타입 데이터 ( int, boolean, … )
    - Primitive 타입의 경우, Stack 영역에 데이터 자체가 저장된다.

## PC Register 영역

---

현재 실행되고 있는 명령어의 주소를 저장하는 영역

- 멀티 스레드 환경에서, 한 스레드가 작업하다가 다른 스레드로 잠시 CPU 점유를 넘겨주고 다시 돌아왔을 때, 이전에 어떤 명령어를 수행하고 있었는지를 PC Register를 통해 확인할 수 있다.
- 스레드가 생성될 때마다 같이 생성되고, 서로 다른 스레드가 침범할 수 없는 영역이다.

## Native Method Stacks 영역

---

Java가 아닌, C나 C++로 작성된 메소드를 실행할 때 사용되는 영역

- 스레드가 생성될 때마다 같이 생성되고, 서로 다른 스레드가 침범할 수 없는 영역이다.

## 자바 코드가 실행되는 순서

---

1. 프로그램이 실행되면 JVM은 OS로부터 이 프로그램이 필요로 하는 메모리를 할당받는다. (JVM은 이 메모리를 용도에 따라 여러 영역으로 나누어 관리한다.)
2. 자바 파일(.java)이 자바 컴파일러에 의해 자바 바이트 코드(.class)로 변환된다.
    1. class 파일에는 클래스 안에 어떤 필드가 있는지, 몇개의 필드가 있는지, 몇개의 메소드가 있는지, 이름이 뭔지 등의 정보를 바이트 코드로써 담고 있다. 
    2. 바이트 코드
        1. JVM이 이해할 수 있는 언어로 변환된 코드
        2. JVM은 바이트코드를 Execution Engine에 전달해 기계어로 번역해, CPU에게 일을 시킨다. 
3. 클래스 로더를 통해 자바 바이트 코드를 JVM으로 필요한 시점에 로딩한다.
4. 해석된 바이트 코드는 JVM의 메모리영역인 **Runtime Data** 영역에 배치되어 실질적인 수행이 이루어지게 된다.
5. 실행시, 실행 엔진 Execution Engine을 거쳐 수행된다. 
실행 엔진은 JVM 메모리에 올라온 바이트 코드를 명령어 단위로 가져와 실행한다. 이때 Interpreter혹은 JIT 컴파일러 방식으로 동작할 수 있다.
    1. Interpreter : 바이트 코드 명령어를 하나씩 읽어 해석하고 실행한다. 전체적인 실행 속도가 느리다.
    2. JIT 컴파일러 : 바이트 코드 영역를 컴파일해, Binary 코드로 변경한다. 이후, 똑같은 명령어 실행을 **다시 요청**받았을 때, Interpreter처럼 하나씩 읽어 해석하지 않고, Binary 코드를 바로 실행한다. 전체적인 실행 속도가 Interpreter에 비해 빠르다.
6. 실행 과정 속에서 JVM은 필요에 따라 GC와 같은 관리 작업을 수행한다.

## Reflection API

---

구체적인 클래스 타입을 알지 못해도 그 클래스의 정보(메서드, 타입, 변수 등등)에 접근할 수 있게 해주는 자바 API

### Reflection 사용법

리플렉션을 사용하기에 앞서, 힙 영역에 로드된 클래스 타입의 객체를 가져와야 한다. 총 3가지 방법이 있다.

- `클래스.class` 로 가져오기
- `인스턴스.getClass()` 로 가져오기
- `Class.forName("클래스명")` 으로 가져오기

### 예제 코드

```java
public class Member {

    private String name;

    protected int age;

    public String hobby;

    public Member() {
    }

    public Member(String name, int age, String hobby) {
        this.name = name;
        this.age = age;
        this.hobby = hobby;
    }

    public void speak(String message) {
        System.out.println(message);
    }

    private void secret() {
        System.out.println("비밀번호는 1234입니다.");
    }

    @Override
    public String toString() {
        return "Member{" +
            "name='" + name + '\'' +
            ", age=" + age +
            ", hobby='" + hobby + '\'' +
            '}';
    }
}

public class Main {

    public static void main(String[] args) throws ClassNotFoundException {
        Class<Member> memberClass = Member.class;
        System.out.println(System.identityHashCode(memberClass));

        Member member = new Member("제이온", 23, "다라쓰 개발");
        Class<? extends Member> memberClass2 = member.getClass();
        System.out.println(System.identityHashCode(memberClass2));

        Class<?> memberClass3 = Class.forName("{패키지명}.Member");
        System.out.println(System.identityHashCode(memberClass3));
    }
}

// 실행 결과
1740000325
1740000325
1740000325
```

3가지 방법으로 가져온 Class 타입의 인스턴스는 모두 같은 것을 확인할 수 있다. 어떤 방식을 사용하든 해시 값이 같으므로 상황에 따라 적절하게 사용하면 된다.

이제, 가져 온 Class 타입을 통해 해당 클래스의 인스턴스를 생성할 수도 있고, 인스턴스의 필드와 메소드를 접근 제어자와 상관 없이 사용할 수 있게 되었다. 우선 해당 클래스의 인스턴스를 생성해 보자.

```java
public class Main {

    public static void main(String[] args) throws Exception {
        // Member의 모든 생성자 출력
        Member member = new Member();
        Class<? extends Member> memberClass = member.getClass();
        Arrays.stream(memberClass.getConstructors()).forEach(System.out::println);

        // Member의 기본 생성자를 통한 인스턴스 생성
        Constructor<? extends Member> constructor = memberClass.getConstructor();
        Member member2 = constructor.newInstance();

        System.out.println("member2 = " + member2);

        // Member의 다른 생성자를 통한 인스턴스 생성
        Constructor<? extends Member> fullConstructor =
            memberClass.getConstructor(String.class, int.class, String.class);
        Member member3 = fullConstructor.newInstance("제이온", 23, "다라쓰 개발");
        System.out.println("member3 = " + member3);
    }
}

// 실행 결과
public Member()
public Member(java.lang.String,int,java.lang.String)
member2 = Member{name='null', age=0, hobby='null'}
member3 = Member{name='제이온', age=23, hobby='다라쓰 개발'}
```

`getConstructor()` 를 통해 생성자를 얻어 오고, `newInstance()` 를 통해 Member 인스턴스를 동적으로 생성해 줄 수 있다.

마지막으로 인스턴스의 필드와 메소드를 접근 제어자와 상관 없이 접근하여 사용해 보자.

```java
public class Main {

    public static void main(String[] args) throws Exception {
        Member member = new Member("제이온", 23, "다라쓰 개발");
        Class<? extends Member> memberClass = member.getClass();

        // 필드 접근
        Field[] fields = memberClass.getDeclaredFields();
        for (Field field : fields) {
            field.setAccessible(true);
            System.out.println(field.get(member));
        }
        fields[0].set(member, "제이온2");
        System.out.println(member);

        // 메소드 접근
        Method speakMethod = memberClass.getDeclaredMethod("speak", String.class);
        speakMethod.invoke(member, "리플렉션 테스트");

        Method secretMethod = memberClass.getDeclaredMethod("secret");
        secretMethod.setAccessible(true);
        secretMethod.invoke(member);
    }
}

// 실행 결과
제이온
23
다라쓰 개발
Member{name='제이온2', age=23, hobby='다라쓰 개발'}
리플렉션 테스트
비밀번호는 1234입니다.
```

`getDeclaredFileds()` 를 통해 클래스의 인스턴스 변수를 모두 가져올 수 있고, `get()` 을 통해 필드 값을 반환받을 수 있고, `set()` 을 통해 필드 값을 수정할 수 있는 것을 알 수 있다. 이때 주의할 점은 private 접근 제어자가 있는 필드에 접근할 때는 `setAccessible()` 의 인자를 true로 넘겨주어야 한다.

메소드도 `getDeclaredMethod()` 를 통해 메소드를 가져올 수 있다. 이때 메소드의 이름과 파라미터의 타입을 같이 인자로 넘겨줘야 한다. 마찬가지로 private 접근 제어자가 있는 메소드에 접근할 때는 `setAccessible()` 의 인자를 true로 설정해야 한다. 마지막으로 `invoke()` 메소드를 통해 리플렉션 API로 얻어 온 메소드를 호출할 수 있다.

## Reflection API의 장단점

---

### 장점

- 런타임 시점에서 클래스의 인스턴스를 생성하고, 접근 제어자와 관계 없이 필드와 메소드에 접근하여 필요한 작업을 수행할 수 있는 유연성을 가지고 있다.

### 단점

- 캡슐화를 저해한다.
- 런타임 시점에서 인스턴스를 생성하므로 컴파일 시점에서 해당 타입을 체크할 수 없다.
- 런타임 시점에서 인스턴스를 생성하므로 구체적인 동작 흐름을 파악하기 어렵다.
- 단순히 필드 및 메소드를 접근할 때보다 리플렉션을 사용하여 접근할 때 성능이 느리다. (모든 상황에서 성능이 느리지는 않음.)

## Reflection API가 사용되는 곳

---

### 프레임워크, 라이브러리

Reflection은 애플리케이션 개발보다는 프레임워크나 라이브러리에서 많이 사용된다. 프레임워크나 라이브러리는 사용자가 어떤 클래스를 만들지 예측할 수 없기 때문에 동적으로 해결해주기 위해 Reflection을 사용한다.

- intellij의 자동완성
- jackson 라이브러리
- Hibernate
- Spring Framework
    - BeanFactory
- JPA
    - Entity에 기본 생성자가 필요한 이유

### Spring의 BeanFactory와 Reflection

Spring의 Bean Factory를 보면, @Controller, @Service, @Repository 등의 어노테이션만 붙이면 Bean Factory에서 알아서 해당 어노테이션이 붙은 클래스를 생성하고 관리해 주는 것을 알 수 있다. 

개발자는 Bean Factory에 해당 클래스를 알려준 적이 없는데, 이것이 가능한 이유는 바로 Reflection 덕분이다. 런타임에 해당 어노테이션이 붙은 클래스를 탐색하고 발견한다면, Reflection을 통해 해당 클래스의 인스턴스를 생성하고 필요한 필드를 주입하여 Bean Factory에 저장하는 식으로 사용이 된다.

### JPA의 Entity에 기본 생성자가 필요한 이유

JPA는 Entity에 public / protected 접근제어자의 기본 생성자가 필요하도록 공식 문서에 명세되어 있다.

그렇다면 왜 기본 생성자가 필요한 것이고, private 기본 생성자여서는 안되는 걸까?

1. JPA는 DB의 데이터를 엔티티 객체에 매핑할 때 Reflection을 사용한다.
    1. **Reflection**은 클래스 이름만 안다면, 클래스의 정보에 접근할 수 있다. 하지만, **메소드의 파라미터는 알 수 없다**. 따라서, 기본 생성자가 없다면 Reflection은 이 객체를 만들때 쓸 생성자가 어떤 파라미터가 필요한지 알 수 없기 때문에 객체를 생성할 수 없다.
    2. 비단 JPA의 엔티티 객체 매핑 시 뿐만이 아니다. 
    스프링에서, 웹 요청의 **@RequestBody를 DTO 객체로 바인딩할 때에도 Reflection을 사용**하여 객체로 만든다.
2. 지연 로딩으로 인해 생성되는 **프록시 객체는 원본 엔티티를 상속한 프록시 엔티티 객체를 생성한**다.
    1. JPA를 통해, DB로부터 어떤 데이터를 가져올 때, **연관된 데이터**는 **지연 로딩**으로 가져온다면, **프록시 객체**의 형태로 가져오고, 필요할 때에 실제 데이터를 가져온다.
    2. 이 때, 프록시 객체는 원본 엔티티를 상속한 프록시 엔티티를 만들어 생성되었다. 
    이 상속한 프록시 엔티티의 생성자는 반드시 부모 엔티티인 원본 엔티티의 생성자를 super를 통해 호출할 것이다.
    3. 이때, 호출할 생성자는 프록시 객체이므로 데이터가 아직 없어 기본 생성자를 쓴다. 
    4. 하지만 이때, 호출해야하는 기본 생성자가 private이라면 호출할 수 없다.
    

## 레퍼런스

---

[[메모리] JVM의 메모리 사용 방식 : JVM의 Static Area와 Heap Area를 중심으로 Kotlin/JVM의 메모리 사용방식을 이해하기](https://kotlinworld.com/3)