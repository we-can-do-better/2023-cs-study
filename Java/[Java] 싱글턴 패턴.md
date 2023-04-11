### 싱글턴 패턴이란

애플리케이션이 시작될 때 클래스가 최초 한 번만 메모리를 할당하는(인스턴스가 오직 1개만 생성) 경우에 사용되는 디자인 패턴

### 싱글턴 패턴을 쓰는 이유는

- 한 번의 new로 인스턴스를 사용하기 때문에 메모리 낭비를 방지
- 싱글턴의 인스턴스는 전역 인스턴스이기 때문에 데이터를 공유하기 쉽다

### 싱글턴 패턴은 어떨 때 사용하는 것이 적합할까

- 애플리케이션에서 특정 객체가 단 하나만 존재해야 할 때
- 이 특정 객체를 여러 부분에서 공유하며 사용해야 할 때
- 예시
    - 현실 세계: 사무실에 프린터기가 1대만 존재할 때
    - 객체 세계: JDBC 커넥션 풀, 로그 객체(Logger)

### 싱글턴 패턴의 문제점

**일반적으로 싱글턴 패턴을 구현하는 코드**

```java
public class Bottle {
    private static Bottle instance;

    private Bottle() {
        throw new IllegalStateException("private constructor");
    }

    public static Bottle getInstance() {
        if(instance == null) {
            instance = new Bottle();
        }
        return instance;
    }
}
```

멀티 스레드 환경에서 Thread-Safe을 보장해주지 않는다.

이 말은 **두 개의 스레드가 getInstance의 if 조건문에 동시에 도달할 경우 인스턴스가 두 번 생성되는 문제점이 발생**되게 된다. 즉, 동시성(Concurrency) 문제를 고려해야 한다.

```java
public class BottleTest {
    public static void main(String[] args) {
        Runnable runnable = new Creator();

        new Thread(runnable).start();
        new Thread(runnable).start();
    }
}

class Creator implements Runnable {
    @Override
    public void run() {
        System.out.println("Bottle.hashCode = " + Bottle.getInstance().hashCode());
    }
}
```

```java
// hashCode: 동일한 객체는 동일한 해시 코드를 반환
Bottle.hashCode = 1538421013
Bottle.hashCode = 744423849
```

### 멀티 스레드에서도 안전한(Thread-safe) 싱글톤 클래스를 만드는 방법

멀티 스레드에서도 안전한 싱글톤 클래스를 만드는 테스트는 다음의 코드로 실행한 결과이다.

```java
public class BottleTest {
    public static void main(String[] args) {
        Runnable runnable = new Creator();

        new Thread(runnable).start();
        new Thread(runnable).start();
        new Thread(runnable).start();
        new Thread(runnable).start();
        new Thread(runnable).start();
        new Thread(runnable).start();
        new Thread(runnable).start();
        new Thread(runnable).start();
        new Thread(runnable).start();
        new Thread(runnable).start();
    }
}
```

1. **이른 초기화 (Eager Initialization)**

이른 초기화 방식은 static 키워드의 특징을 이용해서 클래스 로더가 초기화 하는 시점에 정적 바인딩을 통해 해당 인스턴스를 메모리에 등록하는 방법

> 정적 바인딩(static binding): 컴파일 타임에 성격이 결정됨
> 

```java
class Bottle {
    private static Bottle instance = new Bottle();

    private Bottle() {}

    public static Bottle getInstance() {
        return instance;
    }
}
```

즉, 클래스 로더에 의해 클래스가 최초로 로딩될 때 객체가 생성되기 때문에 Thread-safe하다.

```java
Bottle.hashCode = 123687385
Bottle.hashCode = 123687385
Bottle.hashCode = 123687385
Bottle.hashCode = 123687385
Bottle.hashCode = 123687385
Bottle.hashCode = 123687385
Bottle.hashCode = 123687385
Bottle.hashCode = 123687385
Bottle.hashCode = 123687385
Bottle.hashCode = 123687385
```

그러나, static 멤버를 지금 당장 객체를 사용하지 않더라도 메모리에 적재하기 때문에 리소스가 크다면 공간, 자원 낭비로 이어질 수 있다.

1. **게으른 초기화** **(Lazy Initialization With Synchronized)**

게으른 초기화라는 이름처럼 컴파일 시점에 인스턴스를 생성하는 것이 아닌 인스턴스가 필요한 시점에 요청하여 동적 바인딩을 통해 인스턴스를 생성하는 방식

> 동적 바인딩(dynamic binding): 런타임에 성격이 결정됨
> 

`synchronized`키워드를 이용한 게으른 초기화 방식으로 메서드에 동기화 블럭을 지정해 Thread-safe를 보장한다.

```java
class Bottle {
    private static Bottle instance;

    private Bottle() {}

    public static synchronized Bottle getInstance() {
        if(instance == null) {
            instance = new Bottle();
        }
        return instance;
    }
}
```

```java
Bottle.hashCode = 891375376
Bottle.hashCode = 891375376
Bottle.hashCode = 891375376
Bottle.hashCode = 891375376
Bottle.hashCode = 891375376
Bottle.hashCode = 891375376
Bottle.hashCode = 891375376
Bottle.hashCode = 891375376
Bottle.hashCode = 891375376
Bottle.hashCode = 891375376
```

> **synchronized**
쓰레드의 동기화를 하는 기능으로 공유 데이터를 사용하는 코드 영역을 임계 영역으로 지정하여 하나의 쓰레드만 해당 영역에 접근 가능하게 한다.

**적용 방법**
1. 메서드 전체에 임계 영역으로 지정
2. 특정 영역을 임계 영역으로 지정
> 

그러나 동기화 블럭을 지정해서 Thread-Safe 하도록 만들었지만 인스턴스가 생성되었든, 안되었든 무조건 동기화 블럭을 거치게 된다.

하지만 synchronized 특성상 잠금을 획득해야 하기 때문에 비교적 큰 성능저하가 발생하므로 권장하지는 않는다.

1. **게으른 초기화 + DCL(Double-Checked Locking)**

2번의 게으른 초기화 방식에서 동기화 블럭을 개선한 것으로 인스턴스가 생성되지 않은 경우에만 동기화 블럭이 실행되게끔 구현하는 방식이다.

```java
class Bottle {
    private static volatile Bottle instance;

    private Bottle() {}

    public static Bottle getInstance() {
        if(instance == null) {
            synchronized (Bottle.class) {
                if(instance == null) {
                    instance = new Bottle();
                }
            }
        }
        return instance;
    }
}
```

메서드 전체를 임계 영역으로 지정하는 것이 아닌 첫 번째 if 조건문에서 인스턴스의 존재 여부를 확인하고, 두 번째 if 조건문에서 다시 한 번 체크하는 영역을 임계 영역으로 지정해서 인스턴스를 생성하는 방식이다.

따라서 처음 생성 이후에 synchronized 블럭을 타지 않기 때문에 성능 저하를 완화했다.

```java
Bottle.hashCode = 296740810
Bottle.hashCode = 296740810
Bottle.hashCode = 296740810
Bottle.hashCode = 296740810
Bottle.hashCode = 296740810
Bottle.hashCode = 296740810
Bottle.hashCode = 296740810
Bottle.hashCode = 296740810
Bottle.hashCode = 296740810
Bottle.hashCode = 296740810
```

---

**volatile??**

캐시와 메모리간에 값의 불일치를 해결하는 키워드 (Java 1.4~)

요즘 대부분의 컴퓨터에서는 멀티 코어 프로세서가 장착된 컴퓨터이기 때문에 문제가 될 수 있다.

코어마다 별도의 캐시 공간을 가지고 있는데, 코어는 메모리에서 읽어온 값을 캐시에 저장하고 캐시에서 값을 읽어서 작업한다.

이때, 메모리에 저장된 변수의 값이 변경되었는데도 캐시에 저장된 값이 갱신되지 않아서 메모리에 저장된 값이 다른 경우가 발생한다. 

만약 volatile 키워드를 붙이지 않는다면 low-level에서 문제가 발생하게 되고, 예시는 다음과 같다.

1. **쓰레드 A가 인스턴스를 생성하고 synchronized 블럭을 나온다**
2. **쓰레드 B가 synchronized 블럭에 들어와서 두 번째 if조건문에서 인스턴스 존재 여부를 확인**
3. **만약 쓰레드 A에서 만든 인스턴스가 CPU Cache에만 존재하고, 메모리에 없으면**
4. **쓰레드 A에서 만든 인스턴스와 다른 새로운 인스턴스를 생성한다.**

즉 두 메모리간 완전히 동기화가 이루어지지 않았기 때문에 문제가 생기게 된다.

volatile 키워드가 이러한 문제를 해결해주는데 valatile 변수는 값을 읽어올 때 캐시가 아닌 메모리에서 읽어오기 때문에 캐시와 메모리간의 값의 불일치가 해결된다.

---

1. **게으른 홀더 (LazyHolder, Initialization on demand holder idion,  static holder pattern, Bill Pugh Solution)** 

싱글턴 구현 방식으로 가장 많이 사용되는 방식으로 volatile이나 synchronized 키워드 없이도 동시성 문제를 해결하기 때문에 성능이 뛰어나다.

클래스안에 클래스(Holder)를 두어, **JVM의 Class loader(static initializer) 매커니즘**과 클래스가 Load되는 시점을 이용한 **Lazy Loading 방식**이다.

```java
class Bottle {
    private static class BottleHolder {
        private static volatile Bottle instance = new Bottle();
    }

    private Bottle() {}

    public static Bottle getInstance() {
        return BottleHolder.instance;
    }
}
```

따라서 HolderBottle안에 선언된 인스턴스가 static이기 때문에 클래스 로딩시점에 한 번만 호출되게 되고, final을 사용해 다시 값이 할당되지 않게 된다.

이 방법이 위 방법 중 가장 쓸만한(?) 방법이다.

- 자바 버전을 타지 않으며,
- 성능도 준수하다.

그러나 Reflection 공격이나 직렬화된 인스턴스를 역직렬화할 때마다 새로운 인스턴스가 만들어지는 문제가 존재있다.

```java
Bottle getBottle = Bottle.getInstance();

Constructor<? extends Bottle> bottleConstructor = getBottle.getClass().getDeclaredConstructor();
bottleConstructor.setAccessible(true);

Bottle newBottle = bottleConstructor.newInstance();

System.out.println("getBottle.hashCode() = " + getBottle.hashCode());
System.out.println("newBottle.hashCode() = " + newBottle.hashCode());
```

```java
[실행결과]
getBottle.hashCode() = 775522127
newBottle.hashCode() = 762218386
```

이럴 때에는 Joshua Bloch의 Effective Java에서 소개된 방법으로 Enum을 적용하면 된다.

이 방법도 좋은 방법 중 하나이고 직렬화/역직렬화 문제와 Reflection 공격에도 안전하다.

### **💥** **중요 포인트 💥**

1. **BottleHolder는 Bottle이 클래스 로딩될 때 호출될까**

**아니면 getInstance에서 BottleHolder에 접근할 때 호출될까**

```java
public class LoadTest {
    public static void main(String[] args) {
        OuterClass outerClass = new OuterClass();
    }
}

class OuterClass {
    public OuterClass() {
        System.out.println("OuterClass!!");
    }

    private static class InnerClass {
        public InnerClass() {
            System.out.println("NestedClass!!");
        }
    }
}
```

```java
[실행결과]
OuterClass!!
```

왜 그럴까???

클래스 로더가 클래스(xxx.class)파일을 로딩하는 순서는 다음과 같이 3단계로 구성된다.

1. **Loading(로드) : 클래스 파일을 가져와서 JVM의 메모리에 로드한다.**
2. **Linking(링크) : 클래스 파일을 사용하기 위해 검증하는 과정이다.**
3. **Initialization(초기화) : 클래스 변수들을 적절한 값으로 초기화한다.**

그런데

클래스 파일을 메모리에 올리는 **Loading 기능은 한번에 메모리에 올리지 않고, 애플리케이션에서 필요한 경우 동적으로 메모리에 적재한다는 것**이다.

**초기화 시점**

```java
class Init {
		static int x = 10;
		int y = 5;

		static {  // 클래스 초기화 블럭
				...
		}

		{         // 인스턴스 초기화 블럭
				...
		}

		Init() {...}
}
```

클래스 초기화

1. 기본값 
    
    ex) `static int x = 10` >>> `x = 0`
    
2. 명시적 초기화
    
    ex) `x = 10`
    
3. 클래스 초기화 블럭
    
    ex) static {…}
    

인스턴스 초기화

1. 인스턴스 기본 값
    
    ex) `int y = 5` >> `y = 0`
    
2. 명시적 초기화
    
    ex) `y = 5`
    
3. 인스턴스 초기화 블럭
    
    ex) {…}
    
4. 생성자

```java
public class LoadTest {
    public static void main(String[] args) {
        OuterClass outerClass = new OuterClass();
        System.out.println("=================");
        outerClass.getInnerClass();
    }
}

class OuterClass {                                        // 1
    private static final String SEQ01 = "SEQ01 VARIABLE"; // 2

    static {
        System.out.println("SEQ01 >>>>>>> " + SEQ01);     // 3
    }

    public OuterClass() {                                 // 4
        System.out.println("SEQ02 >>>>>>> SEQ02");
    }

    // -------------------------------------------------------
    // | 현재 InnerClass 는 Load, Initialization 이 되지 않음 |
    // -------------------------------------------------------

    public void getInnerClass() {
        System.out.println("SEQ03 >>>>>>> " + InnerClass.class);      // 5, 로드 O, 초기화 X
        System.out.println("================================");
        System.out.println("SEQ05 >>>>>>> " + InnerClass.getSeq05()); // 6. getSeq03 을 호출 할 때 클래스가 초기화된다.
                                                                      // 7. 이때, static 초기화 블럭이 먼저 호출되고
                                                                      // 8. getSeq05 를 반환한다.
    }

    private static class InnerClass {
        private static final String SEQ03 = "SEQ03";

        static {                                                      // 7. InnerClass.getSeq05 가 호출될 때 실행
            System.out.println("SEQ04 >>>>>>> " + SEQ03);
        }

        public static String getSeq05() {                             // 8. static 초기화 블럭 실행 후에 동작
            return "SEQ04";
        }
    }
}
```

1. **BottleHolder는 정적 중첩 클래스(static nested class)일까 내부 클래스(Inner Class)일까**

[☕ 내부 클래스는 static 으로 선언 안하면 큰일 난다](https://inpa.tistory.com/entry/JAVA-☕-자바의-내부-클래스는-static-으로-선언하자)

1. **초기화와 로드는 같은걸까??**

[☕ 클래스는 언제 메모리에 로딩 & 초기화 되는가 ❓](https://inpa.tistory.com/entry/JAVA-☕-클래스는-언제-메모리에-로딩-초기화-되는가-❓)

이 경우에는 getInstance를 호출하면 BottleHolde에 처음 엑세스할 때 인스턴스를 할당한다.

**참고**

- [링크](https://jeong-pro.tistory.com/86)
- [링크](https://herdin.github.io/2020/12/25/about-double-check-locking)
- [링크](https://hyesun03.github.io/2019/08/22/gof-design-pattern-05-singleton-02/)
- 자바의 정석 3rd Edition
