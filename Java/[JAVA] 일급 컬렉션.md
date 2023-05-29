# 일급 컬렉션

일급 컬렉션은 **컬렉션을 감싼(Wrapping) 컬렉션 객체**를 말한다.

컬렉션을 감싼다..?

- **일급 컬렉션을 사용하지 않은 코드**

```java
public class Board {
    private List<Tag> tags = new ArrayList<>();
		private String title;
}

class Tag {
    private String tagName;
}
```

- **일급 컬렉션을 사용한 코드**

```java
public class Board {
    private Tags tags;
    private String title;
}

class Tags {
    private List<Tag> tags = new ArrayList<>();
}

class Tag {
    private String tagName;
}
```

단순히 컬렉션 객체를 별도의 클래스에 담았을 뿐인데,

**왜 일급 컬렉션을 사용하라**고 조언할까

- 유효성 검증에 용이하고 비즈니스에 종속되는 자료구조를 만들 수 있다.
- 상태와 행위를 한 곳에서 관리할 수 있다.
- 가독성과 유지보수가 향상된다.
- *~~Collection의 불변을 보장하게 할 수도 있다.~~*
    - *그러나, 일급 컬렉션이 주는 기능의 핵심은 불변이 아니다.*

### 1. 유효성 검증에 용이하고, 비즈니스에 종속되는 자료구조를 만들 수 있다.

게시판을 만들 때 게시물을 설명하는 태그들을 붙일 수 있다고 가정해보자.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1c702ed3-cdc6-4c1e-bc8c-6358cfd652a8/Untitled.png)

이때 태그에 아래와 같은 조건이 존재하고 있다.

- **태그는 널(Null) 또는 빈 문자열(””)이 불가능하다.**
- **태그의 글자 수는 최대 10글자까지 가능하다.**
- **태그는 최대 5개까지 가능하다.**

만약, 이러한 유효성 검증을 Service Layer에서 처리를 한다면?

**문제점 => 태그를 사용하는 모든 곳에서 검증 로직이 필요할 것이다.**

```java
public class BoardService {
    public void save(Request request) {
        List<Tag> tagList = request.getTagList();
        validateTagList(tagList);
        validateTagLength(tagList);
        validateSize(tagList);        
        // business logic
    }
}
```

그러나, 일급 컬렉션을 사용한다면 비즈니스 정책에 따른 로직을 일급 컬렉션에 몰아넣음으로써 

- **비즈니스 정책에 종속적이게 만들 수 있고,**
- **여러 곳에서 검증 로직이 존재하지 않아도 되고,**
- **변경을 최소화하게 만들 수 있다.**

```java
public class BoardService {
    public void save(Request request) {
        List<Tag> tagList = request.getTagList();
        Tags tags = new Tags(tagList);

        // business login
    }
}
```

```java
public class Board {
    private Tags tags;
    private String title;
}

class Tags {
    private List<Tag> tags = new ArrayList<>();

    public Tags(List<Tag> tags) {
        validateTagList(tags);   // validate Null 또는 빈 문자열
        validateTagLength(tags); // validate TagName의 최대 길이가 10
        validateSize(tags);      // validate 태그가 5개인지

        this.tags = tags;
    }
}

class Tag {
    private String tagName;
}
```

### 2. 상태와 행위를 한 곳에서 관리할 수 있다.

일급 컬렉션의 또 다른 장점은 상태와 행위를 한 곳에서 관리할 수 있다는 것이다.

- 중복되는 태그는 제외한다.
- 태그앞에 ‘#’을 붙인다.

```java
class Tags {
    private List<Tag> tags = new ArrayList<>();

    public Tags(List<Tag> tags) {
        validateTagList(tags);
        validateTagLength(tags);
        validateSize(tags);

        this.tags = tags;
    }

    public List<Tag> getDistinctTags(List<Tag> tags) {
        return tags.stream()
                .distinct()
                .collect(Collectors.toList());
    }

    public List<Tag> appendDelimiter(List<Tag> tags) {
        return tags.stream()
                .map(tag -> new Tag("# " + tag.getTagName()))
                .collect(Collectors.toList());
    }
}
```

### 3. 가독성과 유지보수가 향상된다.

일급 컬렉션으로 만들어진 컬렉션 객체는 타입(클래스 이름)만으로도 역할과 의미를 바로 파악할 수 있다.

```java
public class Creator {
    private Followers followers;
		...
}

public class Followers {
    private List<Follow> followers = new ArrayList<>();
		...
}
```

위 코드와 같이 정의하게 될 경우 크리에이터의 팔로워와 관련된 상태와 행위가 Followers 클래스에 **캡슐화되어 있기 때문에 코드의 가독성이 올라가고, 유지보수가 용이해지게 된다.**

만약, 상속 관계를 갖는 형태라면 더 큰 이점을 얻게 된다.

```java
public class Ducks {
    List<Duck> ducks = new ArrayList<>();

		public List<Duck> createMaDuck() {
        // create logic
    }

    public List<Duck> createJungDuck() {
        // create logic
    }

    public List<Duck> createJeonDuck() {
        // create logic
    }
}

-------------------------

interface Duck {
    void quack();
}

-------------------------

class MaDuck implements Duck {
    @Override
    public void quack() {
        System.out.println("마마~");
    }
}

class JungDuck implements Duck {
    @Override
    public void quack() {
        System.out.println("정정~");
    }
}

class JeonDuck implements Duck {
    @Override
    public void quack() {
        System.out.println("전전~");
    }
}
```

만약 `AhnDuck` 이 추가되더라도 Ducks 클래스에 추가만 하면 되기 때문에 유지보수에도 용이해진다.

---

## 나는 어떻게 적용하고 있을까

### 1. JPA 연관 관계에도 일급 컬렉션을 적용할 수 있다.

```java
@Embeddable
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class Followers {

    @OneToMany(fetch = FetchType.LAZY, mappedBy = "target", cascade = CascadeType.PERSIST, orphanRemoval = true)
    private List<Follow> followers = new ArrayList<>();

    private void validateFollow(Follow follow) {
        if(!followers.contains(follow)) {
            throw new InvalidFollowException(ErrorCode.INVALID_FOLLOW);
        }
    }
}
```

### 2. 정적 팩터리 메서드를 활용하여 유효성 검증을 명확히 할 수 있다.

```java
public class Tags {

    private List<Tag> tags;

    private Tags(List<Tag> tags) {
        validateTagsSize(tags);
        validateTagLength(tags);
        this.tags = tags;
    }

    public static Tags from(List<Tag> tags) {
        return new Tags(tags);
    }
		...
}
```

**참고**

- [링크](https://jojoldu.tistory.com/412)
- [링크](https://dkswnkk.tistory.com/696)
