# Spring AOP - 2편 : Proxy, Dynamic Proxy

## 프록시 패턴을 통해 AOP 적용하기

---

### 프록시 패턴 ( 런타임 위빙 )

런타임 시에 프록시를 통해 부가 기능이 적용되는 방식

![Untitled](Spring%20AOP%20-%202%E1%84%91%E1%85%A7%E1%86%AB%20Proxy,%20Dynamic%20Proxy%20cc0a51d154734e7e8b1aa395f4db59e6/Untitled.png)

- 구체적인 방식
    - 스프링 애플리케이션 컨텍스트가 생성될 때,  타겟과 같은 인터페이스를 구현하는 프록시 객체가 생성되어 빈으로 등록된다.
    - 이후, 타겟에 대해 클라이언트 요청이 발생하면, 프록시 객체는 타겟의 메소드 호출을 가로채 advice를 적용하고, 타겟의 메소드를 호출한다.
    - 즉, 클라이언트 요청이 발생하면 실제 타겟 객체는 프록시로부터 요청을 위임 받아, 핵심 비즈니스 로직을 실행한다.
- 실제 코드에 Aspect를 통한 부가 기능 호출 코드가 포함되지 않고, 코드는 유지 된 채 프록시를 통해 부가 기능이 적용된다.
- Spring AOP 를 통해 사용할 수 있다.
- Spring AOP가 사용하는 이 프록시 패턴은, 프록시가 핵심 비즈니스 로직 객체의 메소드를 오버라이드하는 방식으로 구현된다. 따라서 메소드에서만 적용할 수 있다.

### 프록시 기반 Spring AOP의 흐름

![Untitled](Spring%20AOP%20-%202%E1%84%91%E1%85%A7%E1%86%AB%20Proxy,%20Dynamic%20Proxy%20cc0a51d154734e7e8b1aa395f4db59e6/Untitled%201.png)

1. 사용하는 Target에 대한 서비스 객체를 상속하여 프록시 객체를 만든다.
2. 이 프록시 객체는 Target 메소드를 재정의한다. 
    1. ex. 트랜잭션 ) 재정의시, AOP 로직에 대한 코드 - 트랜잭션을 여는 코드 를 선언하고, Target 객체를 호출하는 코드를 선언하고, AOP 로직에 대한 코드 - 트랜잭션을 종료하는 코드를 선언한다.
3. 재정의한 메소드가 있는 이 프록시 객체를 스프링 빈으로 등록한다. 

사용하는 프록시에는 두 종류가 있다.

![Untitled](Spring%20AOP%20-%202%E1%84%91%E1%85%A7%E1%86%AB%20Proxy,%20Dynamic%20Proxy%20cc0a51d154734e7e8b1aa395f4db59e6/Untitled%202.png)

### JDK Dynamic Proxy

Spring AOP에 권장되는 방법

타겟이 하나의 인터페이스라도 구현되어 있다면, JDK Dynamic Proxy를 사용한다.

즉, 인터페이스 기반의 프록시이다.

### CGLIB Proxy

타겟이 인터페이스를 구현하지 않았다면, CGLIB Proxy가 사용될 수 있다.

즉, 클래스 기반의 프록시이다.

## 프록시 패턴의 특징

---

어떤 객체를 사용하고자 할 때, 객체를 직접 참고하는 것이 아닌, 해당 객체를 대항하는 객체를 통해 대상 객체에 접근하는 방식

- 해당 객체가 메모리에 존재하지 않아도, 기본적인 정보를 참조하거나 설정할 수 있고, 실제 객체의 기능이 필요한 시점까지 객체의 생성을 미룰 수 있다.
- 실제 객체의 Public, protected 메소드를 숨기고 인터페이스를 통해 노출시킬 수 있다.
- OCP를 지킬 수 있다 : 기존 코드를 변경하지 않고, 새로운 기능을 추가할 수 있다.
- SRP를 지킬 수 있다 : 기존 코드가 해야만 하는 일만 유지할 수 있다.
- 유연한 코드를 만들 수 있다 : 객체 초기화 지연, 로깅, 시간 측정, 캐싱, 흐름제어 등으로 다양하게 활용할 수 있다.

![Untitled](Spring%20AOP%20-%202%E1%84%91%E1%85%A7%E1%86%AB%20Proxy,%20Dynamic%20Proxy%20cc0a51d154734e7e8b1aa395f4db59e6/Untitled%203.png)

### 문제점

1. 객체를 생성할 때 한 단계를 거치게 되므로 빈번한 객체 사용이 필요한 경우 성능을 저하시킬 수 있다.
2. 프록시 내부에서 객체 생성을 위해 스레드가 생성, 동기화가 구현되어야 하는 경우, 성능이 저하될 수 있다.
3. 로직이 난잡해져 가독성이 떨어질 수 있다.
    - Proxy를 직접 구현하려면 인터페이스를 구현해야하고, Proxy 객체를 생성해 주어야한다.
4. 인터페이스를 직접 구현해야한다.
    - 어떤 구현체에 대한 Proxy를 만들려면, 인터페이스를 구현해야 한다.
    - 즉, 그 인터페이스의 모든 메소드를 구현해야한다.
    - 아래와 같은 Hello 인터페이스의 SayHello() 메소드만 기능을 확장하고 싶더라도, SayHi()와 SayThankyou() 메소드도 모두 override 해주어야한다.
        
        ```java
        public interface Hello {
        	String sayHello(String name);
        	String sayHi(String name);
        	String sayThankYou(String name);
        }
        ```
        
5. Proxy 클래스 내에 중복이 발생한다.
    - 모든 구현체에서 원래 타겟으로 위임하는 코드가 중복해서 발생한다.
    - 다른 메소드의 부가 기능이 같은 것일 경우, 부가 기능의 중복이 발생한다.
6. 부가적인 기능추가 마다 별도의 프록시를 만들어야한다.

## Dynamic Proxy

---

Proxy 패턴의 문제점을 해결하기 위해, ( 인터페이스 직접 구현, Proxy 클래스 내 중복 발생 )
Proxy에 해당하는 클래스를 매번 만드는 것이 아닌 
Java Reflection API의 Proxy 클래스 즉 JDK Dynamic Proxy 혹은 CGlib을 이용하여 구현한다.

- Proxy 클래스를 직접 구현하지 않아 코드 복잡도를 해소할 수 있다.

```java
Hello hello = (Hello) Proxy.newProxyInstance(
	Main.class.getClassLoader(),
	new Class[]{Hello.class},
	new UppercaseHandler(new HelloTarget())
);
```

### Java Reflection API

구체적인 클래스 Type을 알지 못해도, 런타임에 클래스의 정보에 접근할 수 있게 해주는 Java API

### Dynamic Proxy의 동작 방식 - 예제

```java
@Transactional
public void saveMember(final Member member){
	memberRepository.save(member);
}
```

위 코드는 프록시 패턴이 적용된다.

1. saveMember 메소드를 호출하는 요청이 들어온다.
2. Proxy 객체를 생성해서, 트랜잭션 처리를 하고, Real Subject인 saveMemeber를 수행한다.
3. 이 때, 정상적으로 트랜잭션 처리가 되지 않는 때가 있는데, 이런 문제를 Self-invocation 문제라고 부른다.

## Self-invocation 문제

아래와 같이, 같은 객체의 자신의 메소드 외의 다른 메소드를 호출할 경우, Self-Invocation 하였다고 한다.

```java
@Component
public class RealSubject implements Subject{
    @Override
    public void realMethod() {
        System.out.println("REAL METHOD");
    }

    @Override
    public void otherMethod() {
        System.out.println("OTHER METHOD");
        this.realMethod(); // 같은 클래스 객체 내에서, 자신 메소드 외에 다른 메소드를 호출했다.
    }
}
```

예를 들어, realMethod()에 Advice를 적용해보자.

realMethod()는 실행 전에, “ASPECT - ADVICE 로직 실행 완료”를 출력하는 Advice가 적용된다.

```java
@Aspect
@Component
public class MyAspect {

    @Pointcut("execution(void com.ming.aop.subjects.Subject*.realMethod(..))")
    public void myPointcut() { }

    @Before("myPointcut()")
    public void before() {
        System.out.println("ASPECT - ADVICE 로직 실행 완료");
    }
}
```

테스트 코드로 출력 결과를 확인해보자.

```java
@RunWith(SpringRunner.class)
@SpringBootTest
@EnableAspectJAutoProxy
public class SelfInvocationTest {
    @Autowired private Subject realSubject;

    @Test
    public void isSelfInvocation() {
				// RealSubject에 대한 프록시가 생성되었는지 확인한다.
        assertTrue(Proxy.isProxyClass(realSubject.getClass())); 
				System.out.println(realSubject.getClass());
				// RealSubject의 otherMethod()를 호출한다.
        realSubject.otherMethod();
    }
}
```

따라서, 우리는 아래와 같은 출력 결과를 기대할 것이다.

```java
class com.sun.proxy.$Proxy48
OTHER METHOD
ASPECT - ADVICE 로직 실행 완료
REAL METHOD
```

하지만, 실제로는 아래와 같이 출력된다. Advice가 적용되지 않는 것이다. 이것이 바로, Self-Invocation의 문제이다.

```java
class com.sun.proxy.$Proxy48
OTHER METHOD
REAL METHOD
```

### Self-Invocation 문제의 원인

SelfInvocationTest 테스트에서는, RealSubject의 otherMethod를 호출한다. 

Spring AOP는 RealSubject에 대한 프록시를 통해 ( realSubject.getClass() → $Proxy48 ), $Proxy48.otherMethod()를 호출한다. 

$Proxy48.otherMethod()와 PointCut을 확인해, Advice를 수행한다. 이때 해당하는 Advice가 없으므로 수행되는 Advice는 없다.

RealSubject 클래스의 otherMethod()가 수행된다. 그리고 realMethod()가 수행된다. 이때, $Proxy48.realMethod()가 아닌, this.realMethod()를 통해 realMethod()가 수행된다.

따라서, 이 예제에서는 realMethod()에는 AOP가 적용될 수 없다.

### Self-Invocation 문제 해결 방법

- AopContext : 현재 AOP 호출에 대한 정보를 얻기 위해 사용되는 추상 클래스
    - 현재 호출된 프록시 객체를 가져와, 프록시객체.realMethod()로 호출한다.
        
        ```java
        @Component
        public class RealSubject implements Subject{
            @Override
            public void realMethod() {
                System.out.println("REAL METHOD");
            }
        
            @Override
            public void otherMethod() {
                System.out.println("OTHER METHOD");
        				// AopContext 클래스를 통해 현재 호출된 프록시 객체를 불러와, 
        				// 자신 메소드 외에 다른 메소드를 호출했다.
                ((Subject) AopContext.currentProxy()).realMethod(); 
            }
        }
        ```
        
- IoC 컨테이너 Bean 활용 : RealSubject에서, 자기 자신의 빈을 주입받아, this.realMethod()로 호출되지 않게 해 프록시를 통해 호출되게 만든다.
- AspectJ Weaving : Spring AOP의 Weaving 방식을 AspectJ Weaving 방식으로 바꾸는 것이다.
    - AspectJ Weaving은 Spring AOP와 달리 바이트 코드를 조작하는 방식이기 때문에 Proxy의 Self-Invocation 이슈가 발생하지 않는다.
    - 또한, 기존의 코드를 변경할 필요가 없다.

## JDK Dynamic Proxy

---

### 특징

- **Interface**를 기반으로 Proxy를 생성한다.
- JDK에서 지원하는 Proxy 생성 방법이다.
- Reflection API를 사용해 느리다.
- Invocation Handler를 통해 중복 코드를 제거할 수 있다.
- Invocation Handler를 재정의한 invoke를 구현해줘야 부가 기능이 추가된다.

### Invocation Handler

- invoke() 메소드
    - 부가 기능을 추가할 때, 무조건 호출되어야하는 메소드이다.
    - 어떤 메소드에 기능을 확장할지 결정할 수 있고, 확장된 기능을 구현할 수도 있다.
    - 사용자가 어떤 메소드(realMethod)를 호출했는지에 대한 정보와 메소드(realMethod)에 전달한 인자는 invoke() 메소드의 인자로 전달된다.
    - 타겟을 필드로 반드시 가지고 있어야 부가기능을 수행할 수 있다.
    
    ```java
    @RequiredArgsConstructor
    public class MyHandler implements InvocationHandler {
    	final Object target;
    
    	@Override
    	public Object invoke(Object proxy, Method method, Object[] args) throws Throwable{
    		Object rtn = method.invoke(target, args); // target의 RealMethod 호출
    		return ((String) rtn).toUpperClass();
    	}
    }
    ```
    
    ```java
    Hello hello = (Hello) Proxy.newProxyInstance(
    	Main.class.getClassLoader(),
    	new Class[]{Hello.class},
    	new MyHandler(new HelloTarget())
    );
    ```
    
- InvocationHandler는 invoke() 메소드만 가지고 있는 인터페이스이다.
- Dynamic Proxy의 메소드를 Invocation Handler에서 재정의한 invoke를 통해 부가 기능을 추가하여 타겟에게 다시 반환하는 형태를 가진다.

### JDK Dynamic Proxy를 선언하는 방법

```java
<인터페이스명> exampleService = 
	(<인터페이스명>) 
		Proxy.newProxyInstance( // JDK Proxy를 사용한다.
			<Proxy 클래스명>.class.getClassLoader(), // Proxy 로딩에 사용할, 클래스 로더
			new Class[]{<인터페이스명>.class}, // 타겟의 인터페이스
			new <Invocation Handler 구현 클래스명> ( // 부가 기능과, 위임할 타겟
				new <인터페이스의 구현체>()
			)
		);
```

### Flow

1. 클라이언트가 RealSubject.realMethod()를 호출하고자해, Proxy가 가로채 Proxy의 realMethod를 호출한다. ( 클라이언트가 호출할 때에는 이것이 타겟인지, 프록시인지 알 수 없다. )
2. Proxy는 구현한 Invocation Handler의 객체를 통해 invoke()를 호출한다.
3. 구현한 Invocation Handler의 객체가 Advice를 확인해 있다면 적용하고, 타겟의 realMethod를 실행한 뒤 리턴한다.

## CGLib

---

### 특징

- **Class**를 기반으로 Proxy를 생성한다.
    - Interface에도 강제로 적용할 수 있지만, Class에도 프록시를 적용 시켜야한다.
- 성능면에서 CGlib이 JDK Dynamic Proxy보다 더 우수하고 예외를 덜 발생시킨다.
    - CGlib는 **Byte Code**를 조작해 빠르다.
- Spring boot AOP에서는 Proxy를 생성할 때 CGlib를 기본적으로 사용한다.
- **@Transactional**도 Proxy 객체를 생성할 때 CGlib를 사용한다.
- ~~Enhancer 의존성을 추가해야한다.~~
    - Spring Core 패키지에 포함되어 해결되었다.
- ~~Default 생성자가 필요하다.~~
    - Objenesis 라이브러리를 사용하면 해결할 수 있다.
- ~~타겟의 생성자를 두 번 호출한다.~~
    - Objenesis 라이브러리를 사용하면 해결할 수 있다.
- MethodInterceptor 인터페이스를 구현하여, MethodInterceptor의 intercept 메소드를 구현해야 부가 기능이 추가된다.
- 동적으로 생성을 위한 기본 생성자가 필수로 필요하다.
    - 예외가 발생한다.
- final 클래스는 CGLib이 프록시 클래스로 상속할 수 없어, 적용할 수 없다.
    - 예외가 발생한다.
- final 메소드는 오버라이딩이 불가해, CGLib을 적용할 수 없다.
    - 예외는 발생하지 않지만, 프록시 적용이 되지 않는다.

### CGlib의 최초 메소드 호출 방식

1. 메소드가 처음 호출되었을 때, 동적으로 Target 클래스의 Bytecode 조작
2. 이후 호출시에는 조작된 바이트 코드 재사용

### CGlib의 성능

→ JDK Dynamic Proxy보다 약 3배 가까이 빠르다

### JDK Dynamic Proxy와 CGlib의 처리 구조

![Untitled](Spring%20AOP%20-%202%E1%84%91%E1%85%A7%E1%86%AB%20Proxy,%20Dynamic%20Proxy%20cc0a51d154734e7e8b1aa395f4db59e6/Untitled%204.png)

## Proxy Factory Bean

---

스프링 프레임워크에서는 Dynamic Proxy를 편리하게 사용하기 위해서 ProxyFactory 클래스로 추상화하여 사용한다. ProxyFactory 는 JDK Dynamic Proxy와 CGLIB을 사용하지만 이러한 구체적인 기술을 전혀 모른 채로 사용할 수 있다.

- Spring에서 프록시를, Bean으로 만들어 주는 것
- Target의 인터페이스 정보가 필요없다 : CGlib으로 프록시를 생성한다.(Spring Boot)
    - ProxyFactory 는 내부적으로 타겟 클래스의 타입이 인터페이스면 JDK Dynamic Proxy를 사용하고, 구체 클래스면 CGLIB을 사용한다.
    - Spring Boot는 모든 경우에 CGLib을 사용하는 것이 기본값이다.
- Proxy Bean을 생성한다.
- 부가 기능을 MethodInterceptor로 구현한다.
    - 프록시 기능을 설정하기 위해 구현하던 인터페이스가 MethodInterceptor로 통일된 것이다.
    - 단, CGLib의 MethodInterceptor가 아닌 다른 패키지의 MethodInterceptor 인터페이스이다.
- invoke() 메소드가 파라미터로 MethodInvocation 만을 받는다.
    - JDK 동적 프록시와 CGLib에서는 메서드 호출과 프록시 인스턴스, 매개변수 정보 등이 분리되어 매개변수로 선언되어 있었다.
    - 하지만, ProxyFactory의 MethodInterceptor 인터페이스에서 사용하는 `MethodInvocation` 클래스는, 클래스 내부에 모두 포함되어있어 별도로 선언할 필요가 없다.

```java
import org.aopalliance.intercept.MethodInterceptor;
import org.aopalliance.intercept.MethodInvocation;

@Slf4j
public class TransactionAdvice implements MethodInterceptor {

    @Override
    public Object invoke(MethodInvocation invocation) throws Throwable {
        log.info("Advice parameter: invocation={}", invocation);

        try {
            log.info("--- 트랜잭션 커밋 시작 ---");

            Object result = invocation.proceed();

            log.info("--- 트랜잭션 커밋 완료 ---");
            return result;
        } catch (Exception e) {
            log.info("--- 트랜잭션 롤백 ---");
            throw e;
        } finally {
            log.info("--- DB 커넥션 자원 반환 ---");
        }
    }
}
```

```java
@Test
void save_normal_member() {
    String normalMemberName = "PARKER";
    MemberRepository target = new NormalMemberRepository();

    ProxyFactory proxyFactory = new ProxyFactory(target);
    proxyFactory.addAdvice(new TransactionAdvice());
    MemberRepository proxy = (MemberRepository) proxyFactory.getProxy();

    log.info("targetClass={}", target.getClass());
    log.info("proxyClass={}", proxy.getClass());
    proxy.save(normalMemberName);
}
```

### ProxyFactoryBean이 MethodInterceptor를 쓰는 이유

- Invocation Handler를 쓸 경우, 타겟이 늘어남에 따라, 같은 기능을 매번 Bean으로 등록하고, 객체를 생성해줘야한다.
- 반면, MethodInterceptor는 타겟을 가지지 않아, 부가 기능을 독립적으로 유지할 수 있다.
- 즉, 부가 기능을 Singleton으로 공유하여 사용가능하다.

## 레퍼런스

[Self Invocation은 왜 발생할까?](https://gmoon92.github.io/spring/aop/2019/04/01/spring-aop-mechanism-with-self-invocation.html)

[스프링 AOP : 동적 프록시 적용(JDK 동적 프록시, CGLIB)](https://ojt90902.tistory.com/700)

[자바를 통해 다이나믹 프록시(Dynamic Proxy)를 구현하는 방법](https://live-everyday.tistory.com/217)

[동적 프록시(Dynamic Proxy) with Spring](https://velog.io/@codemcd/동적-프록시Dynamic-Proxy-with-Spring#jdk-동적-프록시-vs-cglib)