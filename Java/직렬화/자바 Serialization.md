# 자바 Serialization

# 데이터 직렬화와 역직렬화

- 데이터 직렬화
  - 메모리를 디스크에 저장하거나, 네트워크 통신에 사용하기 위한 형식으로 변환하는 것이다.
- 데이터 역직렬화
  - 디스크에 저장한 데이터를 읽거나, 네트워크 통신으로 받은 데이터를 메모리에 쓸 수 있도록 변환하는 것이다.

직렬화는 데이터를 저장 혹은 통신에 사용하기 위한 것은 알겠는데, 왜 데이터를 그냥 사용하면 안 되고 직렬화라는 과정을 거쳐야 하는 것일까?

## 직렬화가 필요한 이유

개발 언어를 무엇을 선택하든, 사용하는 데이터의 메모리 구조는 크게 2가지로 나뉜다.

- 값 형식 데이터
  - int, float, char 등 값 형식 데이터는 스택에 메모리가 쌓이고 직접 접근이 가능하다.
- 참조 형식 데이터
  - 객체와 같은 참조 형식 변수를 선언하면 힙에 메모리가 할당되고, 스택에서는 이 힙 메모리를 참조하는 구조로 되어 있다.

위 두 가지 데이터 중에서 디스크에 저장하거나 통신할 때는 값 형식 데이터만 사용할 수 있다. 참조 형식 데이터는 실제 데이터 값이 아닌 힙에 할당되어 있는 메모리 번지 주소를 가지고 있기 때문이다.

### **참조 형식 데이터를 사용할 수 없는 이유**

예를 들어, 객체 A를 만들고 주소 값이 0x00045523라고 가정하자. 그리고 이 값을 파일에 포함하여 저장했다고 해보자. 이후 프로그램을 종료하고 다시 실행해서 주소 값 0x00045523을 가져오더라도 기존 A 객체의 데이터를 가져올 수 없다. 프로그램이 종료되면 기존에 할당되었던 메모리(0x00045523)는 해제되고 없어지기 때문이다.

네트워크 통신 또한 마찬가지이다. 각 PC마다 사용하고 있는 메모리 공간 주소는 전혀 다르다. 그러므로 내가 다른 PC로 전송한 A 객체 데이터(0x00045523)는 무의미하다. 이 데이터를 받은 PC의 메모리 주소 0x00045523에는 전혀 다른 값이 존재하기 때문이다.

### **그래서 직렬화를 왜 하는가?**

디스크에 저장하거나 통신할 때 값 형식 데이터만 가능하고, 참조 형식 데이터는 안 된다는 것을 이해하였다. 그러면 직렬화는 도대체 왜 필요한 것인가?

직렬화를 하게 되면 각 주소 값이 가지는 데이터를 전부 끌어 모아서 값 형식 데이터로 변환해 준다. 직렬화가 된 데이터는 언어에 따라서 텍스트 또는 바이너리 등의 형태가 되는데, 이러한 형태가 되었을 때 저장하거나 통신할 때 파싱이 가능한 유의미한 데이터가 된다.

**즉, 직렬화를 하는 이유는 사용하고 있는 데이터를 파일 저장 혹은 데이터 통신에서 파싱할 수 있는 유의미한 데이터를 만들기 위함이다.**

## 데이터 직렬화의 종류

- 문자열 직렬화 : CSV, XML, JSON 직렬화
  - 사람이 읽을 수 있는 형태.
  - 저장 공간의 효율성이 떨어지고, 파싱하는 시간이 오래 걸림.
  - 데이터의 양이 적을 때 주로 사용함.
  - 최근에는 JSON 형태로 통해 데이터를 직렬화를 많이 함.
  - 모든 시스템에서 사용이 가능함.
- Binary 직렬화
  - 데이터 변환 및 전송 속도에 최적화하여 별도의 직렬화 방법을 제시하는 구조
  - 사람이 읽을 수 없는 형태.
  - 저장 공간을 효율적으로 사용할 수 있고, 파싱하는 시간이 빠름.
  - 데이터의 양이 많을 때 주로 사용함.
  - 모든 시스템에서 사용이 가능함.
  - ex) 프로토콜 버퍼, Apache Avro 등
  - Protocol Buffer
    - 구글에서 제안한 플랫폼 독립적인 데이터 직렬화 플랫폼
    - 특정 언어 또는 플랫폼에 종속되지 않는 방법을 구현하기 위해 직렬화 하기 위한 데이터를 표현하기 위한 문서가 따로 있다.
    - 이렇게 기술된 `member.proto` 문서를 **프로토콜 버퍼 컴파일러**를 이용해서 개발하기 원하는 언어(여기서는 자바)로 변환해야 한다. (프로토콜 버퍼 컴파일러는 별도로 설치하거나 `Gradle`, `Maven`등 의 빌드 도구를 이용하면 된다.)
      ```protobuf
      message Member {
        required string name = 1;
        optional string email = 2;
        required int32 age = 3;
      }
      ```
      자바로 변환하게 되면 프로토콜 버퍼 형태의 `Member` 클래스가 생성된다.
      ```java
      Member member = Member.newBuilder()
          .setAge(25)
          .setName("김배민")
          .setEmail("deliverykim@baemin.com")
          .build();
      ByteArrayOutputStream baos = new ByteArrayOutputStream()
      member.writeTo(baos);
      // 프로토콜 버퍼 직렬화된 데이터
      byte[] serializedMember = baos.toByteArray();
      ```
    - 자바 직렬화와의 차이점 : 데이터 스펙을 표현하기 위한 문서가 존재하는 부분
- Java 직렬화
  - Java 시스템 간의 데이터 교환이 필요할 때 사용함.

![Untitled](%E1%84%8C%E1%85%A1%E1%84%87%E1%85%A1%20Serialization%2080fbb32170fa48f7bbb3789f7a7ca34e/Untitled.png)

# Java의 직렬화와 역직렬화

---

- Java 직렬화
  - 자바 시스템 내부에서 사용되는 객체 또는 데이터를 외부의 자바 시스템에서도 사용할 수 있도록 바이트 형태로 데이터 변환하는 기술이다.
  - JVM의 메모리에 상주(힙 또는 스택)되어 있는 객체 데이터를 바이트 형태로 변환하는 기술이다.
- Java 역직렬화
  - 바이트로 변환된 데이터를 다시 객체로 변환하는 기술이다.
  - 직렬화된 바이트 형태의 데이터를 객체로 변환해서 JVM으로 상주시키는 기술이다.

## serialVersionUID

---

- Java 직렬화 대상 객체는 동일한 serialVersionUID를 가지고 있어야 한다.
- serialVersionUID를 선언하지 않으면, 내부적으로 클래스의 구조 정보를 이용하여 자동으로 생성된 해시 값이 할당된다.
  - 이 때문에 클래스의 멤버 변수가 추가되거나 삭제되면 serialVersionUID가 달라진다.

```java
public class Member implements Serializable {

	private static final long serialVersionUID = 1L;
```

## 직렬화

---

### **직렬화 조건**

자바 기본(primitive) 타입과 `java.io.Serializable` 인터페이스를 상속받은 객체는 직렬화 할 수 있는 기본 조건을 가진다.

- `java.io.Serializable` 인터페이스를 구현해야 한다.
- 예제 편의상 serialVersionUID가 없다.

```java
/**
    * 직렬 화할 회원 클래스
    */
public class Member implements Serializable {
    private String name;
    private String email;
    private int age;

    public Member(String name, String email, int age) {
        this.name = name;
        this.email = email;
        this.age = age;
    }
    @Override
    public String toString() {
        return String.format("Member{name='%s', email='%s', age='%s'}", name, email, age);
    }
}
```

### **직렬화 방법**

- `java.io.ObjectOutputStream` 를 사용하여 직렬화를 진행한다.

```java
public static void main(String[] args){
    Member member = new Member("김배민", "deliverykim@baemin.com", 25);
    byte[] serializedMember;
    try (ByteArrayOutputStream baos = new ByteArrayOutputStream()) {
        try (ObjectOutputStream oos = new ObjectOutputStream(baos)) {
            oos.writeObject(member);
            // serializedMember -> 직렬화된 member 객체
            serializedMember = baos.toByteArray();
        }
    }
    // 바이트 배열로 생성된 직렬화 데이터를 base64로 변환
    System.out.println(Base64.getEncoder().encodeToString(serializedMember));
}
```

위 예제에서 객체를 직렬화하여 바이트 배열(byte []) 형태로 변환하였다.

## 역직렬화

---

### **역직렬화 조건**

- 직렬화 대상이 된 객체의 클래스가 class path에 존재해야 하며 import되어 있어야 한다.
  - class path란?
    - JVM이 프로그램을 실행할 때, class 파일을 찾는 데 기준이 되는 파일 경로
  - 중요한 점은 직렬화와 역직렬화를 진행하는 시스템이 서로 다를 수 있다는 것을 반드시 고려해야 한다.
- 자바 직렬화 대상 객체는 동일한 serialVersionID를 가지고 있어야 한다.

### **역직렬화 방법**

- `java.io.ObjectInputStream` 을 사용하여 역직렬화를 진행한다.

```java
public static void main(String[] args){
    // 직렬화 예제에서 생성된 base64 데이터
    String base64Member = "...생략";
    byte[] serializedMember = Base64.getDecoder().decode(base64Member);
    try (ByteArrayInputStream bais = new ByteArrayInputStream(serializedMember)) {
        try (ObjectInputStream ois = new ObjectInputStream(bais)) {
            // 역직렬화된 Member 객체를 읽어온다.
            Object objectMember = ois.readObject();
            Member member = (Member) objectMember;
            System.out.println(member);
        }
    }
}
```

# 자바에서도 CSV, JSON 등을 쓰면 되지, 왜 자바 직렬화를 사용하는가?

---

- 목적에 따라 적절하게 써야 한다.
- 아래에서 자바 직렬화의 장점과 단점을 살펴 보자.
- **자바 직렬화 형태의 데이터 교환은 자바 시스템 간의 데이터 교환을 위해서 존재한다.**

## Java 직렬화의 장점

- 자바 직렬화는 자바 시스템에서 개발에 최적화되어 있다.
- 복잡한 데이터 구조의 클래스의 객체라도 직렬화 기본 조건만 지키면 큰 작업 없이 바로 직렬화, 역직렬화가 가능하다.
- 데이터 타입이 자동으로 맞춰지기 때문에 역직렬화가 되면 기존 객체처럼 바로 사용이 가능하다.

## Java 역직렬화의 단점

### **역직렬화 시, 클래스 구조 변경 문제**

역직렬화를 할 때 클래스 구조가 변경되면 문제가 발생한다.

```java
public class Member implements Serializable {
      private String name;
      private String email;
      private int age;
    // 생략
  }
```

위와 같이 기존 멤버 클래스를 직렬화하면 아래와 같은 직렬화된 데이터를 얻을 수 있다.

```java
rO0ABXNyABp3b293YWhhbi5ibG9nLmV4YW0xLk1lbWJlcgAAAAAAAAABAgAESQADYWdlSQAEYWdlMkwABWVtYWlsdAASTGphdmEvbGFuZy9TdHJpbmc7TAAEbmFtZXEAfgABeHAAAAAZAAAAAHQAFmRlbGl2ZXJ5a2ltQGJhZW1pbi5jb210AAnquYDrsLDrr7w=
```

그런데, 기능이 추가되어 기존 멤버 클래스 구조의 변화가 생겼다.

```java
public class Member implements Serializable {
      private String name;
      private String email;
      private int age;
      // phone 속성을 추가
      private String phone;
  }
```

이 상태에서 아까 직렬화한 데이터를 역직렬화하면 어떻게 될까? 결과는 `java.io.InvalidClassException` 이 발생한다. 이것은 각 시스템에서 사용하고 있는 모델의 버전 차이로 인해 생기는 문제이다.

이를 해결하기 위해서는 모델의 버전 간이 호환성을 유지하는 `SUID(serialVersionUID)` 를 정의해야 한다.

### **너무 엄격한 타입 체크**

객체의 변수 명은 같은데 객체의 변수 타입이 달라지면 타입 예외가 발생한다. 가령, 직렬화한 데이터의 객체의 변수 타입이 String인데, 추후 개발하면서 StringBuilder로 바꿨다면 역직렬화가 불가능하다. int를 long으로 바꾸는 것도 마찬가지이다.

### **용량 문제**

```java
{"name":"김배민","email":"deliverykim@baemin.com","age":25}
serializedMember (byte size = 146)
json (byte size = 62)
```

아주 간단한 객체의 내용도 2배 이상의 용량 차이가 발생한다. 일반적인 메모리 기반의 Cache에서는 Data를 저장할 수 있는 용량의 한계가 있으므로 json 형태와 같은 경량화된 형태로 직렬화하는 것이 좋다.

# 자바 직렬화는 어디서 사용되는가?

---

- 서블릿 세션
  - 세션을 서블릿 메모리 위에서 운용한다면 직렬화를 필요로 하지 않지만, 파일로 저장하거나 세션 클러스터링, DB를 저장하는 옵션 등을 선택하게 되면 세션 자체가 직렬화가 되어 전달된다. 그래서 세션에 필요한 객체는 `java.io.Serializable` 인터페이스를 구현하는 것이 좋다.
- 캐시
  - Encache, Redis, memcached 등의 라이브러리 시스템에서 캐시 할 부분을 자바 직렬화된 데이터를 저장해서 사용한다. 다른 직렬화 방식도 사용되지만, 자바 직렬화가 가장 간편해서 많이 사용된다.
- 자바 RMI
  - 원격 시스템 간의 메시지 교환을 위해서 사용하는 자바에서 지원하는 기술이다. 원격 시스템의 메소드를 호출 시에 전달하는 메시지를 작동으로 직렬화하고, 전달 받는 원격 시스템에서는 메시지를 역직렬화하여 사용한다.

# 자바 직렬화 사용 시 주의할 점

---

- 외부 저장소로 저장되는 데이터는 짧은 만료 시간이 아니라면 자바 직렬화 사용을 지양한다.
- 역직렬화 시 반드시 예외가 생길 수 있다는 점을 인지하고 개발한다.
- 자주 변경되는 비즈니스적인 데이터에 대해 자바 직렬화 사용을 지양한다.
- 긴 만료 시간을 가지는 데이터는 JSON 등 다른 포맷을 사용하여 저장한다.

## 출처

- [https://hub1234.tistory.com/26](https://hub1234.tistory.com/26)
- [https://nesoy.github.io/articles/2018-04/Java-Serialize](https://nesoy.github.io/articles/2018-04/Java-Serialize)
- [https://techblog.woowahan.com/2550/](https://techblog.woowahan.com/2550/)
- [https://techblog.woowahan.com/2551/](https://techblog.woowahan.com/2551/)
- [https://machine-geon.tistory.com/166](https://machine-geon.tistory.com/166)
- [https://effectivesquid.tistory.com/entry/자바-클래스패스classpath란](https://effectivesquid.tistory.com/entry/%EC%9E%90%EB%B0%94-%ED%81%B4%EB%9E%98%EC%8A%A4%ED%8C%A8%EC%8A%A4classpath%EB%9E%80)

## 예상 면접 질문 및 답변

### 직렬화란?

메모리를 디스크에 저장하거나, 네트워크 통신에 사용하기 위한 형식으로 변환하는 것이다. 특별히, 자바 직렬화는 자바 시스템 내부에서 사용되는 객체 또는 데이터를 외부의 자바 시스템에서도 사용할 수 있도록 바이트 형태로 데이터 변환하는 기술을 뜻한다.

### 직렬화가 왜 필요한가?

디스크에 저장하거나 통신할 때는 값 형식 데이터만 사용할 수 있다. 참조 형식 데이터는 실제 데이터 값이 아닌 힙에 할당되어 있는 메모리 번지 주소를 가지고 있기 때문이다.

이때 직렬화를 하게 되면 각 주소 값이 가지는 데이터를 전부 끌어 모아서 값 형식 데이터로 변환해 준다. 직렬화가 된 데이터는 언어에 따라서 텍스트 또는 바이너리 등의 형태가 되는데, 이러한 형태가 되었을 때 저장하거나 통신할 때 파싱이 가능한 유의미한 데이터가 된다.

### 역직렬화란?

디스크에 저장한 데이터를 읽거나, 네트워크 통신으로 받은 데이터를 메모리에 쓸 수 있도록 변환하는 것이다. 특별히, 자바 역직렬화는 바이트로 변환된 데이터를 다시 객체로 변환하는 기술을 뜻한다.

### 자바 직렬화는 언제 쓰는가?

- 짧은 만료 시간을 지니는 데이터를 가지고 있는 객체를 직렬화 할 때
- 거의 변경되지 않는 데이터를 지닌 객체를 직렬화 할 때

### 자바 직렬화를 사용하는 기술은 무엇이 있는가?

- 서블릿 세션
- 캐시
- 자바 RMI

### 자바 직렬화의 장점은?

- 자바 직렬화는 자바 시스템에서 개발에 최적화되어 있다.
- 복잡한 데이터 구조의 클래스의 객체라도 직렬화 기본 조건만 지키면 큰 작업 없이 바로 직렬화, 역직렬화가 가능하다.
- 데이터 타입이 자동으로 맞춰지기 때문에 역직렬화가 되면 기존 객체처럼 바로 사용이 가능하다.

### 자바 직렬화의 단점은?

- 직렬화한 객체의 구조와 역직렬화 할 현재 객체의 구조가 다르면 에러가 발생한다.
- 타입 체크가 매우 엄격하다.
- 타 직렬화에 비해 직렬화 된 데이터의 용량이 크다.
