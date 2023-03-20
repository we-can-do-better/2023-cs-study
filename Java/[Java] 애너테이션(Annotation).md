```java
@Override
@Getter @Setter @Data
@Controller
...
```

애너테이션(Annotation)은 Java 5부터 등장한 기능으로 **Java 컴파일러 또는 JVM에게 추가 정보를 제공하는 메타데이터의 일종**이다. 


> Annotation의 정의를 살펴보다보면 **메타데이터**라는 용어를 자주 보게 되는데, **메타데이터는 다른 데이터를 설명해 주는 데이터**를 의미한다.
>
>예를 들어 사진은 사진 데이터 자체와 직접 관련이 없는 사진 크기, 해상도, 촬영 당시의 시간 등의 추가 정보를 제공한다. 이때, 사진에 추가적으로 제공되는 정보를 메타데이터라고 한다.



- 컴파일러에게 코드 문법 체크나 경고를 억제하도록 정보를 제공

- **@Override**
    
    메서드 앞에만 붙일 수 있는 애너테이션으로 **부모 클래스의 메서드를 오버라이딩한다**는 걸 컴파일러에게 알려주는 역할을 한다.
    
    만약 오버라이딩할 때 부모 클래스의 이름을 잘못 쓰게되면 컴파일러가 잘못된 것임을 알려준다.
    
    ![image](https://user-images.githubusercontent.com/64416833/226307276-f5de2ad1-590f-4dcc-bd93-385e8ffd13ff.png)

    
    대문자 I가 아닌 소문자 l로 표기
    
    ```java
    method does not override or implement a method from a supertype
    ```
    
    하위 타입으로부터 오버라이딩이 이루어지지 않음의 메시지를 출력한다.


- 소프트웨어 툴(ex) Lombok, MapStruct, …)은 애너테이션 정보로 컴파일 타임이나 배치 시에 코드를 생성할 수 있도록 정보를 제공

- Lombok

![image](https://user-images.githubusercontent.com/64416833/226307559-fe38b0a1-e018-4501-814f-a0378fc34cf9.png)

즉, 애너테이션을 어떻게 활용하냐에 따라 보일러 플레이트 코드처럼 반복 코드를 줄여주기 때문에 코드가 간결해지고 읽어야 할 코드의 수가 줄어들게 된다.

그러나 이처럼 애너테이션이 의미를 함축하고 있기 때문에 어떤 동작을 하는지 명확히 알 수 없게 되고, 리팩터링을 하기 위해서는 어떠한 동작과 의미를 담고 있는지 알아야 하기 때문에 어려움이 있다.

이처럼 당장에는 이점이 좋을 순 있어도 멀리 보았을 때 적절할 지를 따져보고 사용하는 것이 바람직하다.

### **애너테이션 정의**

애너테이션은 인터페이스를 만들듯이 정의할 수 있는데 interface앞에 ‘@’기호를 붙여 선언할 수 있다.

```java
public @interface 애너테이션이름 {
		타입 요소이름() // 애너테이션의 요소를 선언한다
}
```

```java
public @interface CustomAnnotation {
		String name() default "기본값";
}
```

애너테이션 내에 선언된 메서드를 **애너테이션의 요소**라고 하며, 애너테이션의 요소는 반환값이 있고 매개변수는 없는 형태로 되어있다.

애너테이션을 적용할 때 이 요소들의 값을 빠짐없이 지정해주어야 하는데, 위와 같이 기본값이 있는 요소는 적어주지 않을 경우 자동으로 기본값이 설정된다.

만약, 기본값 설정이나 요소의 값을 설정하지 않을 경우 컴파일 에러가 발생하게 된다.

### 애너테이션 요소의 규칙

- 요소의 타입은 기본형, String, enum, 애너테이션, Class만 허용된다.
- 애너테이션의 요소에는 매개변수를 지정할 수 없다.
- 예외를 선언할 수 없다.
- 애너테이션 요소의 타입에 타입 매개변수를 정의할 수 없다.

직접 만든 CustomAnnotation을 사용하기 위해 JDK에서 제공하는 표준 애너테이션 중 애너테이션을 정의할 때 사용하는 **메타 애너테이션**을 알아보자.

**표준 애너테이션**

![image](https://user-images.githubusercontent.com/64416833/226307719-66ec53fb-fda0-4aa7-808e-50af4453803b.png)

**@Target**

**애너테이션을 적용할 수 있는 범위를 지정**

만약, 여러 개의 값을 지정할 때는 배열처럼 괄호({ })를 사용해야 한다.

![image](https://user-images.githubusercontent.com/64416833/226307796-323a02e1-fdb9-43b5-97a6-717f16c6b48d.png)

```java
@Target({ElementType.FIELD, ElementType.TYPE, ElementType.TYPE_USE})
public @interface MyAnnotation {}
```

```java
@MyAnnotation     // 적용 대상이 TYPE
public class MyClass {
    @MyAnnotation // 적용 대상이 FIELD
    int i;
    
    @MyAnnotation
    MyClass mc;   // 적용 대상이 TYPE_USE
}
```

**@Retention**

**애너테이션이 유지되는 기간을 지정**

| 유지 정책(RetentionPolicy) | 의미 |
| --- | --- |
| SOURCE | 소스 파일에만 존재. 이 말은 사실상 주석으로 사용한다는 것을 의미한다. 컴파일러가 컴파일할때 해당 애너테이션의 메모리를 버리게 된다. |
| CLASS | 클래스 파일에 존재. 실행시에 사용불가 (기본값). 컴파일러가 컴파일 타임에는 애너테이션의 메모리를 가져가지만 런타임시에는 사라지게 된다. “런타임시에 사라진다.”라는 말은 리플렉션으로 선언된 애너테이션의 데이터를 가져올 수 없음을 의미한다. |
| RUNTIME | 클래스(*.class) 파일에 존재. 실행시에 사용가능. JVM이 자바 바이트코드가 담긴 클래스 파일을 런타임환경으로 구성하고, 런타임 종료시까지 메모리에 살아있다. |

### SOURCE

```java
package annotation;

public class RetentionTest {
    public RetentionTest() {
    }

    void printClass() {
    }
}
```

### CLASS

```java
package annotation;

public class RetentionTest {
    public RetentionTest() {
    }

    @RetentionAnnotation
    void printClass() {
    }
}
```

클래스 파일에는 존재하기 때문에 RetentionTest.class 파일에 존재하게 된다. 

그러면 **런타임에 사라진다는 건 어디서 알 수 있을까**

디컴파일 되지 않은 클래스 파일을 직접 열어보면 바이트 코드로 작성된 부분중에 `RuntimeInVisibleAnnotation` 라는 것으로 구분지어놨다. 

즉, 유지 정책 중 CLASS는 단지 바이트코드에서 확인할 수 있는 수준으로만 정의해 놓았을 뿐 런타임시에 애너테이션의 정보가 버려지게 된다.

![image](https://user-images.githubusercontent.com/64416833/226308169-7cc64e19-6840-4d4b-af9d-dcb012c29969.png)

### RUNTIME

```java
package annotation;

public class RetentionTest {
    public RetentionTest() {
    }

    @RetentionAnnotation
    void printClass() {
    }
}
```

![image](https://user-images.githubusercontent.com/64416833/226308226-5c6f68ac-5ec4-4d60-9670-eea7bb8866ac.png)


**@Inherited**

**애너테이션은 기본적으로 상속되지 않는데, 이 애너테이션을 사용하면 상속이 가능하도록 설정 가능**

만약, @Inherited가 붙은 애너테이션을 부모 클래스에 붙이면 자식 클래스도 이 애너테이션이 붙은 것과 같이 인식된다.

```java
@Target({ElementType.FIELD, ElementType.TYPE, ElementType.TYPE_USE})
@interface MyAnnotation {}

@MyAnnotation
class Parent {}

// @MyAnnotation을 붙인 것과 동일
class Child extends Parent {}
```

**@Repeatable**

**Java 1.8에 추가되었으며, 단일 항목에 애너테이션을 두 번 이상 적용할 수 있게 한다.**

**@Document**

**해당 애너테이션을 Javadoc에 포함되도록 한다.**

> @Retention과 @Target은 필수적으로 적어주고 그 외의 애너테이션은 필요에 따라 사용하면 된다.

### 커스텀 애너테이션

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
public @interface CustomAnnotation {
    String name() default "기본값";
}
```

```java
public class AnnotationTest {

    @CustomAnnotation(name = "스터디")
    static class MyClass {}

    @CustomAnnotation
    static class DefaultClass {}

    @Test
    @DisplayName("애너테이션 예제")
    void annotationTest() throws Exception {
        AnnotationExample.DefaultClass defaultClass = new AnnotationExample.DefaultClass();
        CustomAnnotation customAnnotation = defaultClass.getClass().getAnnotation(CustomAnnotation.class);
        String 기본값 = customAnnotation.name();
        System.out.println("[Default] customAnnotation.name() = " + 기본값);

        AnnotationExample.MyClass myClass = new AnnotationExample.MyClass();
        customAnnotation = myClass.getClass().getAnnotation(CustomAnnotation.class);
        String 스터디 = customAnnotation.name();
        System.out.println("[Assign]  customAnnotation.name() = " + 스터디);

        assertThat("기본값").isEqualTo(기본값);
        assertThat("스터디").isEqualTo(스터디);
    }
}
```

```java
[Default] customAnnotation.name() = 기본값
[Assign]  customAnnotation.name() = 스터디
```

### 애너테이션 응용

- AOP + Reflection + Custom Annotation

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface Sorter {
    String order() default "ASC";
}
```

```java
@Sorter(order = "DESC")
@GetMapping("/list/desc")
public List<Integer> findAllDESC() {
    List<Integer> rst = Arrays.asList(100, 1, 50);
    return rst;
}

@Sorter(order = "ASC")
@GetMapping("/list/asc")
public List<Integer> findAllASC() {
    List<Integer> rst = Arrays.asList(100, 1, 50);
    return rst;
}
```

```java
@Aspect
@Component
@Slf4j
public class SorterAspect {

    @Around("@annotation(springbox.annotationsample.annotation.Sorter) && @annotation(annotation)")
    public Object parameterAppender(ProceedingJoinPoint joinPoint, Sorter annotation) throws Throwable {
        Sorter sorter = ((MethodSignature) joinPoint.getSignature())
                .getMethod()
                .getAnnotation(Sorter.class);
        log.info("sorter.order = {}", sorter.order());

        Object proceed = null;
        try {
            proceed = joinPoint.proceed();
        } catch (Throwable e) {
            log.info(e.getMessage());
        }

        if(proceed instanceof List) {
            if (sorter.order().equals("ASC")) {
                Collections.sort((List) proceed);
            } else if (sorter.order().equals("DESC")) {
                Collections.sort((List) proceed, Collections.reverseOrder());
            }
        }
        return proceed;
    }
}
```

**참고**

- [링크](https://www.baeldung.com/spring-enum-request-param)
- [링크](https://www.baeldung.com/jackson-serialize-enums)
- [링크](https://sas-study.tistory.com/329)
- 자바의 정석 3rd Edition