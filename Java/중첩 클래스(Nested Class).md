
**중첩?? 내부???**

뭔가 겹쳐서 쓰는 클래스같은데..

```java
**정적 중첩 클래스
내부 클래스**
```

내부 클래스 == 비정적 중첩 클래스(non-static nested class)

**중첩 클래스**란 뭘까

### 중첩 클래스란

중첩 클래스는 말 그대로 어떤 클래스의 내부에 존재하는 클래스를 의미한다.

```java
class Outer {     // 외부 클래스
    class Inner { // 내부 클래스
        ...
    }
}
```

중첩 클래스는 컴파일 시 `외부클래스$내부클래스` 형태의 클래스 파일이 생성되게 된다.

- **정적 중첩 클래스**
    
    ```java
    class A {
        static class B {
        }
    }
    ```
    

- **내부 클래스(비정적 중첩 클래스)**
    
    ```java
    class A {
    	  class B {
    	  }
    }
    ```
    
- **지역 내부 클래스**
    - 메소드 내부에 클래스를 정의
    - 마치 메소드 내의 지역변수처럼 쓰임
    
    ```java
    class A {
        void methodA() {
            class B { //지역 이너 클래스
            }
            B b = new B(); //지역 이너 클래스 객체 생성
    	  }
    }
    ```
    
- **익명 내부 클래스**
    
    ```java
    class A {
        C c = new C() {
    
    		    public void bcd() {
    		  	    System.out.println("익명 내부 클래스");
    		    }
    	  };
    
    	  void abc() {
    		    c.bcd();
      	}
    }
    
    interface C {
    	  public abstract void bcd();
    }
    ```
    
    ```java
    class A {
    		...
    		Collections.sort(list, new Comparator<Integer>() {
            @Override
            public int compare(Integer n1, Integer n2) {
                return n1 - n2;
            }
        });
    }
    
    ```
    

### 중첩 클래스는 언제 쓰고, 사용하는 이유는 무엇인가

특정 클래스가 

**1) 다른 클래스와 협력할 일이 적고,** 

**2) 외부 클래스와 밀접한 관련이 있다면,**

하나의 클래스에서 두 개의 클래스를 한 번에 관리하는 논리적인 그룹으로 나타낼 때 사용한다.

가령, B 클래스는 오직 A 클래스에서만 사용된다와 같은 상황이 그 예시이다.

```java
class A {     // 외부 클래스
    class B { // 내부 클래스
        ...
    }
}
```

중첩 클래스를 사용함으로써

1. **외부 클래스와 내부 클래스가 서로의 멤버에 쉽게 접근할 수 있게 된다.**
2. **서로 관련있는 클래스를 논리적으로 묶어서 표현하기 때문에 캡슐화를 향상 시킬 수 있다.**
3. **외부 클래스를 제외한 외부에서는 내부 클래스에 접근할 수 없으므로 복잡성을 줄일 수 있다.**
4. **연관있는 클래스가 하나의 클래스로 묶이기 때문에 가독성이 좋아지고, 유지보수하기 쉬워진다.**

### 정적 중첩 클래스 vs 비정적 중첩 클래스(내부 클래스)

정적 내부 클래스와 비정적 내부 클래스를 나누는 기준은 이름에서도 알 수 있듯 static 예약어가 클래스에 같이 작성되어 있는지로 구분할 수 있다.

- **정적 중첩 클래스**

```java
class Outer {
    private int ov;     // outer variable
    
    static class Inner {
        private int iv; // inner variable
    }
}
```

- 비정적 중첩 클래스

```java
class Outer {
    private int ov;     // outer variable

    class Inner {
        private int iv; // inner variable
    }
}
```

### 중첩 클래스의 생성

- **정적 중첩 클래스**

```java
Outer.Inner inner = new Outer.Inner();
```

- **비정적 중첩 클래스**

```java
Outer.Inner inner = new Outer().new Inner();
```

정적 중첩 클래스는 static 예약어가 있음으로써 독립적으로 생성할 수 있다.

그러나, 비정적 중첩 클래스는 반드시 외부 클래스 객체를 생성한 뒤 해당 객체를 이용해서 생성해야 한다. 즉, **바깥 클래스(Outer)의 참조를 필요로 한다.**

얼핏보기에 생성에서만 다르고, 큰 차이가 없어 보일 수도 있지만,

사실 **내부 클래스(비정적 중첩 클래스)는 문제를 가지고 있다.**

인텔리제이와 같은 IDE에서 내부 클래스를 선언하면 다음과 같이 경고 메시지를 보여준다.

(외부 클래스의 멤버를 사용하지 않을 때)

![image](https://user-images.githubusercontent.com/64416833/234759082-8c6c1c9f-865f-409c-94f6-ec416a194311.png)

그와 동시에 **내부 클래스를 static으로 설정하라**는 수정 제안을 하게 된다.

### 왜 이런 경고 메시지를 줄까

그 이유는, **내부 클래스는 외부 참조를 하기 때문**이다.

일반적으로 내부 클래스의 인스턴스를 만들기 위해서는 먼저 **외부 클래스를 초기화한 뒤 내부 클래스를 초기화해야 한다.**

```java
public class InnerExample {
    public static void main(String[] args) {
        Outer.NonStaticInner nonStaticInner = new Outer().new NonStaticInner();
    }
}

class Outer {
    private int out = 10;

    class NonStaticInner { // 내부 클래스
        private int in = 20;
    }
}
```

이러한 단계 과정 때문에 **내부 클래스는 자신을 만들어준 인스턴스에 대한 외부 참조를 갖게 된다.**

컴파일되어 생성된 Outer$NonStaticInner 클래스 파일을 인텔리제이 IDE의 기본 디컴파일러(Java Bytecode Decompiler)를 이용하여 확인해보면 다음과 같은 결과를 볼 수 있다.

```java
class Outer$NonStaticInner {
    private int in;

    Outer$NonStaticInner(Outer this$0) {
        this.this$0 = this$0;
        this.in = 20;
    }
}
```

> *`this$0`은 바이트 코드에서만 보여지는 Outer 클래스의 참조 Hidden 변수*
> 

내부 클래스인 NonStaticInner의 생성자에서 **외부 클래스의 참조를 매개변수로 받아 인스턴스 변수(this)로 저장**함을 볼 수 있다.

따라서 **내부 클래스인 NonStaticInner 클래스는 외부 클래스에 대한 참조를 가진다.**

그래서 내부 클래스의 인스턴스 메서드에서 `'정규화된 this'`를 사용해 외부 클래스의 참조로 외부 클래스의 멤버에 접근할 수 있다.

```java
// 소스 파일
public class InnerExample {
    public static void main(String[] args) {
        Outer.NonStaticInner nonStaticInner = new Outer().new NonStaticInner();
        nonStaticInner.printOuterField();
    }
}

class Outer {
    private int out = 10;

    class NonStaticInner { // 내부 클래스
        private int in = 20;

        void printOuterField() {
            System.out.println("Outer.out = " + Outer.this.out);
        }
    }
}
```

```java
// 클래스 파일
class Outer$NonStaticInner {
    private int in;

    Outer$NonStaticInner(Outer this$0) {
        this.this$0 = this$0;
        this.in = 20;
    }

    void printOuterField() {
        System.out.println("Outer.out = " + this.this$0.out);
    }
}
```

> *정규화된 this
클래스명.this 형태로 클래스의 이름을 명시하는 용법을 말한다.*
> 

이처럼 내부 클래스의 인스턴스는 외부 클래스의 인스턴스와 암묵적으로 연결되는 것을 알 수 있다.

하지만 내부 클래스는 위와 같은 기능적인 장점이 있지만 **‘외부 참조’로 인해 메모리 누수라는 단점이 존재**한다.

만약 외부 클래스는 필요가 없어지고 내부 클래스만 남아 있을 경우 필요하지 않는 외부 클래스는 가비지 컬렉션(GC, Garbage Collection)의 대상이 되어 메모리에서 제거되어야 마땅하다.

그러나 **내부 클래스는 외부 클래스와 외부 참조로 이어져있기 때문에 메모리에서 제거되지 않고 남아있게 되고 이는 곧 메모리 누수로 이어지게 된다.**

JVM으로 구동되는 애플리케이션의 Thread 및 GC 사용량을 모니터링하는 VisualVM과 함께 알아보자.

```java
import java.io.IOException;

public class InnerExample {
    public static void main(String[] args) throws IOException {
        Outer.NonStaticInner nonStaticInner1 = new Outer().new NonStaticInner();
        Outer.NonStaticInner nonStaticInner2 = new Outer().new NonStaticInner();
        Outer.NonStaticInner nonStaticInner3 = new Outer().new NonStaticInner();
        Outer.NonStaticInner nonStaticInner4 = new Outer().new NonStaticInner();
        Outer.NonStaticInner nonStaticInner5 = new Outer().new NonStaticInner();
        Outer.NonStaticInner nonStaticInner6 = new Outer().new NonStaticInner();
        Outer.NonStaticInner nonStaticInner7 = new Outer().new NonStaticInner();
        Outer.NonStaticInner nonStaticInner8 = new Outer().new NonStaticInner();
        Outer.NonStaticInner nonStaticInner9 = new Outer().new NonStaticInner();

        System.gc();
        System.out.println("GC 동작 완료");
        System.in.read(); // VisualVm HeapDump 시점
    }
}

class Outer {
    private int out = 10;

    class NonStaticInner { // 내부 클래스
        private int in = 20;
    }
}
```

내부 클래스의 경우 외부 클래스의 참조 값을 가지고 있기 때문에 외부 클래스인 Outer 객체의 메모리가 GC를 했음에도 해제되지 않음을 확인할 수 있다.'

![image](https://user-images.githubusercontent.com/64416833/234759130-e06468b3-fed5-4782-98f5-ba925d9acbc9.png)

따라서 Outer 객체를 더이상 사용하지도 않는 상황임에도 메모리가 남아있게 되어 메모리 누수가 발생하게 된다.

만약, 내부 클래스에 int 배열의 크기가 1억인 인스턴스를 만든다면??

400MB(=4 byte(int) x 1억) 크기의 인스턴스가 계속해서 생성

```java
import java.io.IOException;
import java.util.ArrayList;
import java.util.List;

public class InnerExample {
    public static void main(String[] args) throws IOException {
        List<Outer.NonStaticInner> list = new ArrayList<>();
        for (int i = 0; i < 100; i++) {
            System.out.println(i);
            list.add(new Outer().new NonStaticInner());
        }
        System.gc();
        System.out.println("GC 동작 완료");
    }
}

class Outer {
    private int out = 10;
    private int[] ints = new int[100000000];

    class NonStaticInner { // 내부 클래스
        private int in = 20;

        void printOuterField() {
            System.out.println("Outer.out = " + Outer.this.out);
        }
    }
}
```

```java
0
1
2
3
4
5
6
7
8
9
10
Exception in thread "main" java.lang.OutOfMemoryError: Java heap space
	at nested.Outer.<init>(InnerExample.java:26)
	at nested.InnerExample.main(InnerExample.java:14)

Process finished with exit code 1
```

외부 클래스인 Outer 객체가 사용되지 않고, GC를 했음에도 외부 참조로 인해 해제되지 않아 메모리 누수가 발생되어 OutOfMemory가 발생하게 된다.

### **반면 정적 중첩 클래스인 경우에는 어떨까**

```java
public class InnerExample {
    public static void main(String[] args) throws IOException {
				Outer.StaticInner staticInner = new Outer.StaticInner();
		}
}

class Outer {
    private int out = 10;

    static class StaticInner { // 정적 중첩 클래스
        private int in = 30;
    }
}
```

내부 클래스와 마찬가지로 인텔리제이 IDE의 기본 디컴파일러로 컴파일된 클래스 파일을 확인해보면 다음의 결과를 얻을 수 있다.

```java
class Outer$StaticInner {
    private int in = 30;

    Outer$StaticInner() {
    }
}
```

**내부 클래스와 달리 정적 중첩 클래스는 외부 참조를 하지 않는 것을 볼 수 있다.**

따라서, `‘정규화된 this’` 기능은 사용하지 못하게 된다.

![image](https://user-images.githubusercontent.com/64416833/234759162-00d8f1a9-16d0-4ee8-927f-7a9bc59a352e.png)

마찬가지로 VisualVM으로 Heap Dump를 떠서 메모리 할당 상태를 확인해보면 다음과 같다.


![image](https://user-images.githubusercontent.com/64416833/234759189-0228d8d9-3a6b-4beb-be40-302f1e6a1513.png)


동일하게 10개의 인스턴스(`외부클래스$정적 중첩 클래스`)를 만들었지만 GC를 실행할 때, 외부 클래스의 인스턴스를 참조하는 객체가 없기 때문에 GC에 의해 정상적으로 수거되어 메모리가 할당 해제된 것을 확인할 수 있다.

이처럼 **메모리 누수가 발생할 수 있는 문제점이 있기 때문에 내부 클래스가 독립적으로 사용되는 경우라면 정적 클래스로 선언하여 사용하는 것이 좋다.**

**정리**

- 중첩 클래스를 선언할 때 비정적 중첩 클래스(inner class)로 만들면 ‘외부 참조’ 현상 때문에 내부 클래스 인스턴스를 생성하기 위해 우선적으로 만들었던 외부 클래스가 GC에 수거가 되지 않아 메모리에 잔존하게 되어 문제를 일으키게 된다.
- 따라서 가능한 static을 붙여 사용하자.

**참고**

- [링크](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EC%9E%90%EB%B0%94%EC%9D%98-%EB%82%B4%EB%B6%80-%ED%81%B4%EB%9E%98%EC%8A%A4%EB%8A%94-static-%EC%9C%BC%EB%A1%9C-%EC%84%A0%EC%96%B8%ED%95%98%EC%9E%90)
- [링크](https://tecoble.techcourse.co.kr/post/2020-11-05-nested-class/)

