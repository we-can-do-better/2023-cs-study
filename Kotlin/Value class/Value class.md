# Value Class

## 🦮  개요

---

### 만들어진 계기

```kotlin
data class Student(val age: Int)
```

위와 같이 기본형 타입(Primitive type)을 래퍼 클래스로 래핑하여 사용한 경험이 많을 것입니다.

하지만 위와 같은 래퍼 클래스는 런타임 시에 힙 영역에 쓸 때 없는 오버헤드가 생성됩니다.

특히 **기본형 타입은 런타임에 최적화**되어 있는데, 예제와 같이 기본형 타입을 래퍼 클래스로 래핑하면 **성능에 더욱 악영향**을 끼칠 수 있습니다.

해당 문제를 해결하기 위해 코틀린에서는 value class라는 inline class를 제공합니다.

### 정의

**value class**는 **inline class**입니다.

inline class는 값만 보유하는 **값 기반 클래스의 하위 클래스**로, **단일 값을 래핑할 때 사용하면 추가 오버헤드 없이 클래스를 생성**하도록 돕는 클래스입니다.

```kotlin
inline class Name(val s: String)
```

- `값 기반 클래스(Value-based class)` : **객체의 식별성이 아닌 값에 의해 정의되는 클래스**. 여기서 의미하는 **객체의 식별성**은 두 객체를 구별하는 속성으로, 주로 **메모리의 위치**가 이에 해당함. 따라서 값 기반 클래스란, 메모리의 주소 값이 아닌 값 그 자체에 의해서 정의되는 클래스를 의미함.

Kotlin 1.3 버전부터 알파 테스트로 출시된 inline class는, Kotlin 1.5 버전부터 정식 출시되어 value class라는 이름으로 바뀌게 되었습니다. 따라서 기존에 사용되던 inline 정의자는 사용되지 않습니다.

![Untitled](Value%20Class%200417ec27578149eb9a547d9d920cbb28/Untitled%203.png)

value class는 다음과 같은 방식으로 정의할 수 있습니다.

```kotlin
@JvmIline
value class Name(val s: String)
```

- `@JvmInline` : value class가 inline class임을 알려주는 어노테이션으로, JVM 백엔드에서 inline class를 선언할 때 사용됨.
    - `JVM Backend` : Kotlin 코드를 JVM 바이트코드로 컴파일하는 컴파일러의 일부. JVM 프론트엔드에서 소스 코드를 분석하면, JVM 백엔드에서 분석한 소스 코드를 바탕으로 JVM에서 실행 가능한 파일로 변환함.

value class를 통해 초기화한 변수는 **컴파일 타임에 래퍼 클래스의 값으로 바뀌어 저장**됩니다.

실제로 value class로 구현한 코드를 자바 코드로 디컴파일했을 때, **클래스가 아닌 기본형 값 타입으로 초기화**하는 것을 볼 수 있습니다.

```kotlin
value class Student(val age: Int)

fun main() {
		val student = Studnet(age = 2)
}
```

```java
public final class Student {
	  private final int age;

    public static int constructor_impl(int age) {
       return age;
    }

    ...
}

public final class Main {
		public static final void main() {
				// count에는 기본형 타입인 int 2 값이 저장됨
				final int age = Student.constructor-impl(2);
		}
}
```

## 🧰  클래스의 일부 기능 지원

---

### 멤버

value calss도 일반 클래스와 동일하게 클래스 내부에 변수와 함수 및 생성자 블럭을 선언할 수 있습니다.

```kotlin
@JvmInline
value class Name(private val s: String) {
    
    init { ... }

    val length get() = s.length

    fun greet() {
        println("Hi, $s")
    }
}
```

하지만 value class는 뒷받침하는 필드(Backing Field)를 가질 수 없기 때문에 `lateinit`을 선언할 수 없으며, `by lazy`와 같은 Delegated Properties도 선언 할 수 없습니다.

### 상속

value calss는 인터페이스 상속 기능을 지원합니다. 하지만 value class는 항상 `final`로 지정되기 때문에 다른 클래스를 상속하여 확장할 수는 없습니다.

인터페이스를 상속한 코드도 다른 내부 함수와 동일하게 `static` 메서드로 실행됩니다.

```kotlin
interface Foo {
    fun greet()
}

@JvmInline
value class Student(val age: Int) : Foo {

    override fun greet() {
        println("Hello, I'm $age.")
    }
}
```

```java
public final class Student implemnets Foo {
	  private final int age;
		
    public void greet() {
      greet-impl(this.age);
    }

		// static 메서드로 구현됨
		public static void greet_impl(int $this) {
      String var1 = "Hello, I'm " + $this + '.';
      System.out.println(var1);
    }
 
    ...
}
```

## 📦  value class의 boxing

---

사실상 기본형 타입이지만 외부는 클래스의 모양세인 만큼, 어떨 때는 클래스의 형태로 사용해야 하는 경우도 있습니다. 예를 들어, **제네릭 타입**으로 value class가 사용되어야 하는 경우에는 기본형 타입이 아닌 **boxing된 래퍼 타입으로 자동 반환**됩니다.

```kotlin
interface I

@JvmInline
value class Foo(val i: Int) : I

fun asInline(f: Foo) {}
fun <T> asGeneric(x: T) {}
fun asInterface(i: I) {}
fun asNullable(i: Foo?) {}

fun <T> id(x: T): T = x

fun main() {
    val f = Foo(42)

    asInline(f) // unboxed
    asGeneric(f) // boxed
    asInterface(f) // boxed
    asNullable(f) // boxed 
    
    // f는 처음 boxing되어 제네릭 값으로 전달되고, c에 반환될 때 unboxing되어 42를 반환함
    val c = id(f) // val c = 42
}
```

```java
public static final void main() {
    int f = Foo.constructor-impl(42);
    asInline-9qDqOCk(f); // unboxed
    asGeneric(Foo.box-impl(f)); // boxed
    asInterface(Foo.box-impl(f)); // boxed
    asNullable-6aipi8o(Foo.box-impl(f)); // boxed
    int c = ((Foo)id(Foo.box-impl(f))).unbox-impl(); // boxed -> unboxed
 }
```

value class는 참조 평등 연산자(`===`)를 지원하지 않습니다. 기본 값과 래퍼로 모두 표현될 수 있기 때문에 객체의 주소 값을 비교하는 참조 평등 비교는 무의미하기 때문입니다.

### 맹글링 (Mangling)

맹글링은 오버로딩과 같이 동일한 이름의 함수를 정의하였을 때, 컴파일러가 함수를 구분하기 위해 함수에 특정 해시코드를 붙이는 것을 말합니다.

value class는 컴파일 타임에 기본 타입으로 컴파일되기 때문에 아래와 같이 같은 타입의 파라미터로 충돌을 야기할 수 있습니다.

```kotlin
@JvmInline
value class UInt(val value: Int)

fun compute(x: Int) { ... }

// compute(x: Int)로 컴파일되기 때문에 오류!
fun compute(x: UInt) { ... }
```

이러한 문제를 해결하기 위해서 코틀린 1.4.3부터는 value class의 맹글링을 지원합니다. 위 코드를 디컴파일해보면 아래와 같이 함수명 뒤에 임의의 해시코드가 붙여진 것을 확인할 수 있습니다.

```java
public static final void compute(int x) { ... }

// 함수명 뒤에 해시코드가 붙음!
public static final void **compute_hUb9Lp8(int x) { ... }
```

### 자바 코드에서 이름 변경하기

Kotlin 1.4.3 이전 버전에서 오버로딩 기능을 사용하고자 한다면, `@JvmName` 어노테이션을 통해 함수명을 변경해주어야 합니다. `@JvmName`으로 이름을 지정한 함수는 자바 코드로 디컴파일 될 때 해당 이름으로 바뀌어 컴파일됩니다.

```kotlin
fun compute(x: Int) { ... }

@JvmName("computeUInt")
fun compute(x: UInt) { ... }
```

```java
public static final void compute(int x) { ... }

@JvmName(name = "computeUInt")
public static final void **computeUInt**(int x) { ... }
```

## 💬  다른 타입 클래스와의 차이점 알아보기

---

### data class vs value class

얼핏보면 데이터를 래핑하고 생성한다는 점에서 유사한 data class와 value class! 하지만 이 둘은 아래와 같은 차이점이 있습니다.

1. **파라미터 지원**

   먼저, **value class**는 값 기반의 클래스이므로, **단일 파라미터만 선언 가능**합니다. 또한, value class는 컴파일 타임에 기본형 타입으로 바뀌어 저장되기 때문에 **Immutable한 값만 허용**합니다. (`var` X, `val` O)

   mutable한 값 선언을 허용하게 된다면, 해당 값에 접근하기 위해 힙 메모리로부터 주소 값을 할당 받아야 하므로, value class의 의의를 잃게 됩니다.

   반대로, **data class**는 **여러 개의 파라미터를 선언할 수 있고, mutable한 값도 허용**합니다.

2. **기본 함수 지원 여부**

   **value class는 data class에서 지원하는 여러 기본 함수들을 지원하지 않습니다.** `toString()`, `equals()`, `hashCode()`와 같은 메서드는 공통적으로 지원하지만, 값의 복제를 도와주는 `copy()`나 값의 접근을 돕는 `componentN()`과 같은 함수는 지원하지 않습니다.

   애초에 value class는 단일 값만 지원하니 필요없죠!

3. **인라이닝**

   앞의 내용에서 언급했다시피, value class는 인라이닝 기능을 지원합니다. 하지만 데이터 클래스는 단순 클래스로써의 기능만을 제공합니다.


사용 목적에 따라 value class와 data class를 구분해서 사용하면 될 듯합니다.

단순 래퍼 클래스가 필요한 경우라면 value class를 사용하고, 파라미터 값이 여러 개이거나 data class가 지원하는 기능을 사용하고자 한다면 data class를 사용하면 될 것 같습니다.

## 🧹  정리

---

> 기본형 타입을 래퍼 클래스로 래핑하면 쓸 때 없는 오버헤드를 생성하기 때문에,
값의 인라이닝을 돕는 value class를 사용하여 메모리 낭비를 줄이자!
>

---

- [Inline classes | Kotlin Documentation](https://kotlinlang.org/docs/inline-classes.html)
- [Object lifetime - Wikipedia](https://en.wikipedia.org/wiki/Object_lifetime)
- [C++ 상에서 발생하는 name mangling에 관한 내용](https://spikez.tistory.com/m/19)
- [Value-based Classes (Java SE 17 & JDK 17) - Oracle](https://docs.oracle.com/en/java/javase/19/docs/api/java.base/java/lang/doc-files/ValueBased.html)