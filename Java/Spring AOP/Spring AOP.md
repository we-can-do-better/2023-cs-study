# Spring AOP - 1편

AOP는 여러 오브젝트에 나타나는 공통적인 부가 기능을 모듈화하여 재사용하는 기법이다.

SRP에 따르면, 클래스를 변경하는 이유은 하나다.

만약, 어떤 로그인 서비스의 수행시간을 측정하기 위해 측정 로직은 서비스 내에 넣는다면, 서비스 로직 말고 부가 기능이 비즈니스 로직을 위한 서비스 코드에 포함된다. 맞는 접근일까?

수행시간을 측정하고, 트랜잭션을 처리하고, 권한을 체크하는 등의 부가 기능 작업은 모두 일종의 인프라 로직이다.

## 인프라 로직

---

- 어플리케이션의 전 영역에서 나타날 수 있다.
- 중복 코드를 만들어낼 가능성 때문에 유지보수가 힘들어진다.
- 비즈니스 로직과 섞여 있으면, 비즈니스 로직을 이해하기가 어려워진다.
- 인프라 로직은 각자의 관심사가 있다.
- 인프라 로직의 중복이 횡단으로 나타나고 이를 횡단 관심사라고 부른다.

![Untitled](Spring%20AOP%20-%201%E1%84%91%E1%85%A7%E1%86%AB%2039580202c83645bc88a68d9074e263f3/Untitled.png)

## AOP ( Aspect - Oriented - Programming )

---

관점(관심) 지향 프로그래밍

![Untitled](Spring%20AOP%20-%201%E1%84%91%E1%85%A7%E1%86%AB%2039580202c83645bc88a68d9074e263f3/Untitled%201.png)

### **Aspect**

**흩어진 관심사**를 **모듈화** 한 것

### AspectJ

AOP를 구현한 Java의 구현체

### **Target**

**Aspect**를 **적용**하는 곳

- 클래스, 메서드 등
- ex ) MemberService의 hello()라는 메소드 실행 전,후에 hello랑 bye를 출력하는 일을 하고자 한다.
  - Target : MemberService Bean

### **Advice**

실질적인 **부가기능**을 담은 **구현체**

- ex ) MemberService의 hello()라는 메소드 실행 전,후에 hello랑 bye를 출력하는 일을 하고자 한다.
  - Advice : hello랑 bye를 출력하는 일

### **Join Point**

Advice가 **적용될 위치**

- AspectJ는 메소드, 필드, 객체, 생성자 등에 다양하게 적용하능하다.
- 하지만, Spring AOP는 메소드가 실행될 때만 적용가능하므로, Spring AOP의 Join Point는 언제나 **메서드 실행 시점**을 의미한다.
- ex ) MemberService의 hello()라는 메소드 실행 전,후에 hello랑 bye를 출력하는 일을 하고자 한다.
  - Join Point : 메소드 실행 전, 후

### **Point Cut**

Join Point의 상세한 스펙을 정의한 것

- "A란 메서드의 진입 시점에 호출할 것"처럼 구체적으로 Advice가 실행될 시점을 정함
- JoinPoint가 메타적인 정보라면, Point Cut은 더 구체적인 적용 지점이다.
- ex ) MemberService의 hello()라는 메소드 실행 전,후에 hello랑 bye를 출력하는 일을 하고자 한다.
  - Point Cut : hello() 메소드 실행 전, 후

### 위빙 weaving

원본 로직에 부가 기능 로직이 추가된 것

## Spring AOP - Advice의 종류

---

### Before

메소드 실행 전에 Advice를 실행하겠다.

```java
@Aspect
public class BeforeExample {

    @Before("execution(* com.example.service.UserService.*(..))")
    public void doAccessCheck() {
        // ...
    }
}
```

Point Cut : **execution(_ com.example.service.UserService._(..))**

→ UserService 클래스의 모든 메서드를 대상으로 AOP를 적용하겠다.

⇒ UserService 클래스의 모든 메서드를 대상으로, 그 메소드 호출 전에 doAccessCheck()이라는 Advice를 실행하는 AOP를 적용하겠다.

### After Returning

메소드가 에러 없이 성공적으로 실행된 후 Advice를 실행하겠다.

```java
@Aspect
public class AfterReturningExample {

    @AfterReturning("execution(* com.example.service.UserService.*(..))")
    public void doAccessCheck() {
        // ...
    }
}
```

⇒ UserService 클래스의 모든 메서드를 대상으로, 그 메소드가 에러 없이 성공적으로 실행된 후에 doAccessCheck()이라는 Advice를 실행하는 AOP를 적용하겠다.

### After Throwing

메소드가 에러가 발생하여 Exception을 던지는 시점에 Advice를 실행하겠다.

```java
@Aspect
public class AfterThrowingExample {

    @AfterThrowing("execution(* com.example.service.UserService.*(..))")
    public void doRecoveryActions() {
        // ...
    }
}
```

⇒ UserService 클래스의 모든 메서드를 대상으로, 그 메소드에서 에러가 발생하여 Exception을 던질 때에 doAccessCheck()이라는 Advice를 실행하는 AOP를 적용하겠다.

### After

메소드 실패 여부와 관계없이 실행된 후에 Advice를 실행하겠다.

```java
@Aspect
public class AfterFinallyExample {

    @After("execution(* com.example.service.UserService.*(..))")
    public void doReleaseLock() {
        // ...
    }
}
```

⇒ UserService 클래스의 모든 메서드를 대상으로, 그 메소드의 실패 여부와 상관 없이 호출 후에는 무조건 doAccessCheck()이라는 Advice를 실행하는 AOP를 적용하겠다.

### Around

메소드 실패 여부와 관계없이 \*\*\*\*전 후로 Advice를 실행하겠다.

```java
@Aspect
public class AroundExample {

    @Around("execution(* com.example.service.UserService.*(..))")
    public Object doBasicProfiling(ProceedingJoinPoint pjp) throws Throwable {
        // start stopwatch
        Object retVal = pjp.proceed();
        // stop stopwatch
        return retVal;
    }
}
```

⇒ UserService 클래스의 모든 메서드를 대상으로, 그 메소드의 **호출 전** 그리고 메소드의 실패 여부와 상관 없이 **호출 후**에는 무조건 doAccessCheck()이라는 Advice를 실행하는 AOP를 적용하겠다.

## AOP의 적용 방식

---

### 1. 컴파일 ( 컴파일 타임 위빙 )

.java → .class 파일로 컴파일하는 과정에서, AspectJ 컴파일러가 부가 기능 로직을 붙이는(위빙) 방식

- 실제 코드에 Aspect를 통한 부가 기능 호출 코드가 포함된다.
- AspectJ 를 통해 사용할 수 있다.

### 2. 클래스 로드시 ( 로드 타임 위빙 )

자바 코드가 컴파일 될 때에는 위빙되지 않았지만, 컴파일된 클래스 파일이 JVM에 로드될 때, 위빙하는 방식

- 실제 코드에 Aspect를 통한 부가 기능 호출 코드가 포함된다.
- AspectJ 를 통해 사용할 수 있다.

### 3. 프록시 패턴 ( 런타임 위빙 )

런타임 시에 프록시를 통해 부가 기능이 적용되는 방식

![Untitled](Spring%20AOP%20-%201%E1%84%91%E1%85%A7%E1%86%AB%2039580202c83645bc88a68d9074e263f3/Untitled%202.png)

- 구체적인 방식
  - 스프링 애플리케이션 컨텍스트가 생성될 때, 타겟과 같은 인터페이스를 구현하는 프록시 객체가 생성되어 빈으로 등록된다.
  - 이후, 타겟에 대해 클라이언트 요청이 발생하면, 프록시 객체는 타겟의 메소드 호출을 가로채 advice를 적용하고, 타겟의 메소드를 호출한다.
  - 즉, 클라이언트 요청이 발생하면 실제 타겟 객체는 프록시로부터 요청을 위임 받아, 핵심 비즈니스 로직을 실행한다.
- 실제 코드에 Aspect를 통한 부가 기능 호출 코드가 포함되지 않고, 코드는 유지 된 채 프록시를 통해 부가 기능이 적용된다.
- Spring AOP 를 통해 사용할 수 있다.
- Spring AOP가 사용하는 이 프록시 패턴은, 프록시가 핵심 비즈니스 로직 객체의 메소드를 오버라이드하는 방식으로 구현된다. 따라서 메소드에서만 적용할 수 있다.

## 프록시 기반 Spring AOP의 흐름

---

![Untitled](Spring%20AOP%20-%201%E1%84%91%E1%85%A7%E1%86%AB%2039580202c83645bc88a68d9074e263f3/Untitled%203.png)

사용하는 프록시에는 두 종류가 있다. ( 자세한 내용은 다음 글에 )

![Untitled](Spring%20AOP%20-%201%E1%84%91%E1%85%A7%E1%86%AB%2039580202c83645bc88a68d9074e263f3/Untitled%204.png)

### JDK Dynamic Proxy

Spring AOP에 권장되는 방법

타겟이 하나의 인터페이스라도 구현되어 있다면, JDK Dynamic Proxy를 사용한다.

즉, 인터페이스 기반의 프록시이다.

### CGLIB Proxy

타겟이 인터페이스를 구현하지 않았다면, CGLIB Proxy가 사용될 수 있다.

즉, 클래스 기반의 프록시이다.

## AspectJ와 Spring AOP의 비교

---

Spring AOP가 별도의 컴파일러나 코드를 삽입하는 과정이 필요 없기 때문에 AspectJ에 비해 간단한 구조를 가지지만, Spring에 관리되는 빈에만 ( 특히 메소드 ) 적용될 수 있다.

컴파일 타임 위빙이 런타임 위빙보다 훨씬 빠르다. 런타임 위빙은 프록시 기반이기에 어플리케이션이 시작될 때 프록시를 생성해두어야되고 성능에 영향을 미친다. 컴파일 타임 위빙은 미리 코드에 aspect를 넣어두기 때문에 런타임 오버헤드가 없다.

![스크린샷 2023-04-18 오후 6.59.14.png](Spring%20AOP%20-%201%E1%84%91%E1%85%A7%E1%86%AB%2039580202c83645bc88a68d9074e263f3/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2023-04-18_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_6.59.14.png)

## Transactional

---

`@Transactional` 어노테이션은 Spring AOP의 대표적인 예다.

- `@Transactional` 이 붙어있으면 OwnerRepository 타입의 **프록시**가 새로 만들어지고 Spring AOP에 의해 자동으로 생성되는 OwnerRepository의 프록시에는 `@Transactional` 이 지시하는 코드가 삽입된다.
- `@Transactional`은 Proxy 기반이므로 private 메소드에는 적용되지 않는다.
  - Proxy는 타겟 클래스를 상속해서 프록시 객체를 만드는데, private 메소드를 가지고 있다면 상속이 불가능하기 때문이다.
  - 따라서 이러한 경우에는 AspectJ를 이용해야한다.
- `@Transactional`은 Proxy 기반이므로 동일 클래스의 내부 메서드에 적용된 `@Transactional`은 적용되지 않는다.

  - Proxy는 프록시를 통해 들어오는 외부 메소드의 호출에서 가로챌 수 있기 때문에, 내부 메서드의 호출을 프록시가 가로채서 Advice를 수행하지 못한다.
  - 따라서, 대상 객체 내의 메서드 내부에서의 다른 메서드 호출은 호출된 메서드가 `@Transctional`로 표시되어있더라고 런타임 시 실제 트랜잭션으로 이어지지 않는다.

  ```java
  public class Bean {
     public void doStuff() {
        doTransactionStuff();
     }

     @Transactional
     public void doTransactionStuff() {
        //...
     }
  }

  //트랜잭션 동작함
  Bean bean = (Bean)appContext.getBean("bean");
  bean.doTransactionStuff();

  //트랜잭션 동작 안함
  Bean bean = (Bean)appContext.getBean("bean");
  bean.doStuff();
  ```

## Spring AOP의 예시

---

Transactional, Interceptor, Filter 등이 모두 Spring AOP의 예이다.

### **커스텀 어노테이션(`@LogExecutionTime`) 정의**

```java
package org.springframework.samples.petclinic.owner;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target(ElementType.**METHOD**)
@Retention(RetentionPolicy.**RUNTIME**)
public @interface **LogExecutionTime** {
}
```

### **부가 기능을 갖는 Class(Aspect) 생성**

```java
package org.springframework.samples.petclinic.owner;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;
import org.springframework.util.StopWatch;

@Component
**@Aspect**
public class LogAspect {

    Logger logger = LoggerFactory.getLogger(LogAspect.class);

    **@Around("@annotation(LogExecutionTime)")**
    public Object logExecutionTime(ProceedingJoinPoint joinPoint) throws Throwable {
        StopWatch stopWatch = new StopWatch();
        stopWatch.start();

        // @LogExecutionTime이 붙어있는 **타겟 메소드**를 실행
        Object proceed = joinPoint.proceed();

        stopWatch.stop();
        logger.info(stopWatch.prettyPrint());

        return proceed;
    }
}
```

joinPoint는 @LogExecutionTime를 붙인 타겟 메소드를 의미한다.

### **AOP를 사용할 메소드 지정**

![Untitled](Spring%20AOP%20-%201%E1%84%91%E1%85%A7%E1%86%AB%2039580202c83645bc88a68d9074e263f3/Untitled%205.png)

성능을 측정하기 위해 `@LogExecutionTime` 생성

### **결과**

![Untitled](Spring%20AOP%20-%201%E1%84%91%E1%85%A7%E1%86%AB%2039580202c83645bc88a68d9074e263f3/Untitled%206.png)

## 다음 내용

---

> `@Transactional`은 Proxy 기반이므로 동일 클래스의 내부 메서드에 적용된 `@Transactional`은 적용되지 않는다.

위 개념의 자세한 내용을 살펴보고,

JDK Dynamic Proxy와 CGLIB Proxy의 개념을 살펴본다.

## 레퍼런스

---

[Comparing Spring AOP and AspectJ | Baeldung](https://www.baeldung.com/spring-aop-vs-aspectj)

[Core Technologies](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html)

[[Spring] AOP에 대해 (6)](https://bepoz-study-diary.tistory.com/408)

[Spring AOP](https://velog.io/@ljo_0920/Spring-AOP)
