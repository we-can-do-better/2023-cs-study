
# UML 클래스 다이어그램

![이미지 출처: [https://velog.io/@zooneon/클래스-다이어그램](https://velog.io/@zooneon/%ED%81%B4%EB%9E%98%EC%8A%A4-%EB%8B%A4%EC%9D%B4%EC%96%B4%EA%B7%B8%EB%9E%A8)](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/508210a2-cb47-497a-a275-97b5f5e7171c/Untitled.png)

이미지 출처: [https://velog.io/@zooneon/클래스-다이어그램](https://velog.io/@zooneon/%ED%81%B4%EB%9E%98%EC%8A%A4-%EB%8B%A4%EC%9D%B4%EC%96%B4%EA%B7%B8%EB%9E%A8)

### UML(Unified Modeling Language)

소프트웨어 시스템을 모델링하고 **문서화하기 위한 표준화**된 모델링 언어로, 객체 지향 프로그래밍 개념을 기반으로 시스템의 구조, 동작, 상호 작용을 모델링할 수 있다.

![이미지 출처: [https://www.nextree.co.kr/p6753/](https://www.nextree.co.kr/p6753/)](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/20a78a69-2d42-439a-938a-1a948e9bc59e/Untitled.png)

이미지 출처: [https://www.nextree.co.kr/p6753/](https://www.nextree.co.kr/p6753/)

- 구조 다이어그램(Structure Diagram)
    - 시스템의 개념, 관계 등의 측면에서 요소들을 나타내는 정적인 면을 보기 위한 것

- 행위 다이어그램(Behavior Diagram)
    - 시스템의 작동 방식과 객체 간의 상호 작용을 나타내는 동적인 면을 보기 위한 것

### 클래스 다이어그램

클래스 다이어그램은 시스템을 구성하는 클래스, 인터페이스, 관계, 상속 등을 보여주는 다이어그램

=> **클래스 내부의 정적인 내용과 클래스간의 의존 관계를 표시하는 다이어그램**

## 클래스 다이어그램의 요소

- 클래스
- 스테레오 타입
- 추상클래스 / 메서드
- 클래스간의 관계

### **클래스**

클래스 다이어그램에서 클래스는 클래스의 이름, 속성(변수), 행위(메서드)로 표현할 수 있다.

- 가장 윗 부분: 클래스 이름
- 중간 부분: 속성(변수)
- 마지막 부분: 행위(메서드)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5d942a27-575d-4bf3-9ccc-ad0bb7593a67/Untitled.png)

클래스의 세부 사항으로 필드와 메서드의 

**접근 제어자(access modifier),** 

**필드명(메서드명),** 

**데이터 타입,** 

**매개변수,** 

**리턴 타입** 등을 나타낼 수 있다.

{readOnly}가 붙으면 final을,

밑줄은 static을 의미한다.

| 접근제어자 | 표시 |
| --- | --- |
| public | + |
| package (default) | ~ |
| protected | # |
| private | - |

| 표시 | 설명 |
| --- | --- |
| {readonly} | final |
| ____ | static |

다만 클래스 다이어그램은 목적에 따라 구분할 수 있다.

도메인안의 개념과 같은 추상적인 개념을 기술하기 위해 나타내는 것과

소프트웨어의 설계 혹은 완성된 소프트웨어의 구현 설명을 목적으로 구분할 수 있다.

![이미지 출처: [https://sabarada.tistory.com/72](https://sabarada.tistory.com/72)](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/cdb2cbf6-bb95-4e1f-8a70-a210d8226240/Untitled.png)

이미지 출처: [https://sabarada.tistory.com/72](https://sabarada.tistory.com/72)

### 스테레오 타입

스테레오 타입은 UML에서 제공하는 기본 요소 외에 추가적인 확장 요소를 나타내는 타입

쌍 꺾쇠와 비슷하게 생긴 길러멧(«») 사이에 적는다. 

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/933912eb-1a7a-41a6-8fe2-7de266d7d4cd/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/207675e6-e44f-4ba3-90b6-79c3c3fc665c/Untitled.png)

위 다이어그램은 인터페이스와 유틸리티 클래스를 표현하고 있다.

스테레오 타입으로 많이 사용되는 것은 «interface», «utility», «abstract», «enumeration» 이 있다.

### 추상 클래스 / 메서드

추상 클래스란 하나 이상의 추상 메서드(구현체가 없는 메서드)를 포함하는 클래스를 말한다.

추상 클래스는 공식적으로 두 가지 방식으로 표기할 수 있다.

1. 추상 클래스의 이름과 추상 메서드를 *italic체*로 표현
2. {abstract} 프로퍼티를 사용하여 표현
3. (공식적이지는 않지만) 스테레오 타입으로 추상 클래스를 표기

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/33b7dc17-1a02-45a5-891a-f1f0545767a4/Untitled.png)

## 클래스간의 관계

클래스 다이어그램의 주 목적은 클래스간의 관계를 한 눈에 보고 의존 관계를 파악하는 것에 있다.

![이미지 출처: [https://en.wikipedia.org/wiki/Class_diagram](https://en.wikipedia.org/wiki/Class_diagram#/media/File:Uml_classes_en.svg)](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5c06d3c0-df2d-4617-9315-a1d067d2cecf/Untitled.png)

이미지 출처: [https://en.wikipedia.org/wiki/Class_diagram](https://en.wikipedia.org/wiki/Class_diagram#/media/File:Uml_classes_en.svg)

### 일반화 관계(Generalization)

일반화(generalization)는 객체 지향 개념에서 부모 클래스와 자식 클래스간의 상속 관계를 나타낸다.

부모 클래스는 자식 클래스를 일반화(Generalize)한 것이고, 자식 클래스는 부모 클래스를 구체화Specitialize)한 것이다. `IS-A 관계`

표기법은 클래스 사이에 실선으로 연결하고 부모 클래스쪽에 비어 있는 삼각형 화살표를 그린다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/46afb78b-77ed-4f79-9ac7-ec1db6cc1059/Untitled.png)

### 실체화(Realization)

interface에 명세(정의)만 되어 있는 메서드를 오버라이딩하여 실제 기능으로 구현하는 것을 나타낸다.

인터페이스와 구현 클래스 사이의 실체화(Realize) 관계는 점선으로 연결하고 인터페이스쪽에 비어있는 삼각형 화살표로 연결한다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/306de3a4-91cf-404a-89f3-3bd23864e14d/Untitled.png)

### 의존 (Dependency)

어떤 클래스가 다른 클래스를 참조하는 것을 의미한다.

클래스간에 점선으로 연결하고 참조할 클래스에 화살표로 연결하여 표현한다.

참조의 형태는 다음과 같다.

- 메서드 내 대상 클래스 객체 생성
- 메서드 내 대상 클래스 객체 사용
- 메서드 내 대상 클래스 메서드 호출
- 메서드 내 대상 클래스 객체 리턴
- 메서드에서 대상 클래스 객체를 매개변수로 받는 것

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8f00c063-e0ca-4765-b9a7-1a7a0dbac6bd/Untitled.png)

이와 같은 객체 참조는 계속 유지하지 않고 메서드의 호출이 끝나면 의존 관계의 클래스와 관계가 끝난다는 것이다.

### 연관 (Association & Directed Association)

클래스 다이어그램에서의 Association은 보통 다른 객체의 참조를 가지는 필드를 의미한다.

여기서는 방향이 있는 실선과 방향이 없는 실선 두 가지로 방향성(navigability)에 따라 연관 관계를 나타낼 수 있다.

- **방향이 있는 실선 (Directed Association)**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/29079360-3fb4-436b-b23a-39f2a1cfab4c/Untitled.png)

연관 관계에서 화살표가 있다는 것은 단방향 연관 관계를 의미한다. 

이 말은 한 쪽은 알지만, 다른 쪽은 상대방의 존재를 모른다는 관계를 나타낸 것이다.

여기서 `- comments` 는 역할 이름을 나타내며, 코드 내에서 ‘Comment가 Board에 참조될 때 어떤 참조 속성의 이름’인 지를 의미한다.

- **방향이 없는 실선 (Association)**

만약 우측 이미지처럼 실선으로만 이어져있다면

- Board가 Comment를 참조할 수도,
- Comment가 Board를 참조할 수도,
- 둘 다 참조할 수도 있다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6952c1bd-a7f2-4d01-b1b8-735613a85f9d/Untitled.png)

연관 관계가 어떻게 되는지 숫자로 표현할 수도 있다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d964482d-e0ba-42ab-89ce-800af31b1aa9/Untitled.png)

- 개수(Multiplicity)
    - 대상 클래스의 가질 수 있는 인스턴스 개수 범위를 의미한다.
    - 다중성 표기 방법
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d0d9c71a-4c82-4921-b27b-8e7886594dde/Untitled.png)
        
        - 사용자는 휴대폰을 0개 또는 그 이상을 가질 수 있다.
        - 학생은 1개 이상의 강좌를 가져야 하고, 강좌는 1명 이상의 학생을 가져야 한다.

Assocication은 다음과 같은 형태로도 작성이 가능하다.

```java
public class Creator {
    @Embedded
    private Platform platform;
}

@Embeddable
class Platform {
    @Embedded
    private PlatformUrl platformUrl;

    @Embedded
    private PlatformType platformType;
}
```

```java
public class Collegian {
    private List<Grade> grades
}

class Grade {
    private Subject subject;
}

class Subject {}
```

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/847e1f38-3d8d-4397-ad29-5efe3d406af5/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f74e98ad-277b-4efa-9bfc-b6fb52369d30/Untitled.png)

집합 관계(Aggregation)와 합성 관계(Composition)는 연관 관계(Association)를 특수하게 나타낸 것으로 전체(whole)와 부분(part)으로 나타낸다.

### 집합 (Aggregation, Shared Aggregation)

집합 관계는 연관 관계의 집합 관계를 나타내는 것으로 Collection이나 Array를 이용하는 관계이다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/56cf72f0-4087-420a-b19b-550562d8b9d2/Untitled.png)

위 예시에서 전체(Whole)는 Vulture이게 되고, 부분(Part)은 Mine가 된다.

표기법은 전체쪽에 비어있는 다이아몬드로 표기하고, 부분쪽에는 화살표를 명시하여도 되고, 명시하지 않아도 된다.

```java
?? 연관관계와 비슷한뎁쇼..?
```

지금까지 봐온 다른 관계들과 달리 **집합 관계는 연관 관계의 형태와 비슷해보인다.**

심지어 코드도 연관 관계(Association)의 코드와도 동일하다.

- **연관 관계**: 다른 객체의 참조를 가진다. 집합이라는 의미를 내포하고 있지 않다.
- **집합 관계**: 다른 객체의 참조를 가진다. 집합이라는 의미를 내포한다.

연관 관계와 집합 관계의 차이는 **집합이라는 개념적인 차이**만 존재하는데, 모델링 언어의 표준화를 제공하는 단체에서 집합 관계의 명확한 정의를 제공하지 않아서 현재까지도 갑론을박이 계속된다고 한다.

### 합성 (Composition, Composite Aggregation)

합성 관계는 집합 관계처럼 전체(Whole)와 부분(Part)으로 이루어져있지만, 개념적으로 집합 관계보다 **더 강한 집합**을 의미한다.

표기법은 집합 관계와 표기법은 거의 비슷하고, 다이아몬드의 내부가 채워져있다는 점만 다르다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a32e8102-8478-46b2-9612-7c967f15a905/Untitled.png)

- 집합 관계: 부분이 전체에 독립적이다.
    - **전체(Whole) 객체의 라이프 사이클과 부분(Part) 객체의 라이프 사이클이 독립적이다.**
    - **즉, 전체 객체가 메모리에 존재하지 않아도, 부분 객체는 메모리에 유지되고 있다.**
    - ex) 스타크래프트 벌처의 마인
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/cad41bb7-af77-437b-8803-4b3efd60e096/Untitled.png)
        

- 합성 관계: 부분이 전체에 종속적이다.
    - **부분 객체의 라이프 사이클이 전체 객체의 라이프 사이클에 의존적이다.**
    - **즉, 전체 객체가 메모리에서 해제되면 부분 객체도 메모리에서 해제된다.**
    - ex) 스타크래프트 캐리어의 인터셉터
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/87fb1fb2-8f1e-45be-b0ea-1b8322ab7fd4/Untitled.png)
        

### 중첩 (Nested, Nested Class)

`class Outer` 안에 `class Inner` 가 내부 클래스로 존재하는 경우

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0fec44bf-141d-427d-b4b4-dfb2972dd795/Untitled.png)

**예시 코드**

[blog-source/java-practice/src/uml at master · ahn-sj/blog-source](https://github.com/ahn-sj/blog-source/tree/master/java-practice/src/uml)

**참고**

- [링크](https://www.nextree.co.kr/p6753/)
- [링크](https://gmlwjd9405.github.io/2018/07/04/class-diagram.html)
- [링크](https://brownbears.tistory.com/577)
- [링크](https://sabarada.tistory.com/72)
- [링크](https://morm.tistory.com/88)
