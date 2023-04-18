면접 예상 질문에 Java Map의 내부 구현이 어떻게 이루어져있는지 추측해보라는 항목을 보게 돼서 복습할 겸 스터디에서 발표했었던 HashSet의 동작 원리에 대해서 다뤄보려 한다.

 

그때 당시 발표를 준비하게 된 건 다름 아닌 어떤 분의 포스팅을 보고 나서였다.

https://www.blog.ecsimsw.com/entry/%EC%9E%91%EC%84%B1-%EC%A4%91-%EC%9E%90%EB%B0%94-HashSet%EC%9D%98-%EC%9B%90%EB%A6%AC
 

아래와 같은 코드가 존재한다고 했을 때 1부터 10까지 순차적으로 증가하다가 11을 추가하는 순간 11이 첫 번째 출력 결과로 나오는 걸 보게 되었다고 한다.
```java
import java.util.HashSet;

public class HashSetExample1 {
    public static void main(String[] args) {
        HashSet<String> hashSet = new HashSet<>();
        hashSet.add("1");        hashSet.add("2");        hashSet.add("3");
        hashSet.add("4");        hashSet.add("5");        hashSet.add("6");
        hashSet.add("7");        hashSet.add("8");        hashSet.add("9");
        hashSet.add("10");

        for (String str : hashSet) {
            System.out.print(str + " ");
        }
    }
}
// 위 코드 결과
1 2 3 4 5 6 7 8 9 10

// 11을 추가했다고 가정했을 때의 결과
11 1 2 3 4 5 6 7 8 9 10
```


포스팅을 흥미롭게 적어주신 덕분인지 호기심이 생기게 되어 나도 제대로 찾아보기 시작했다.

알아야 할 점이 몇 가지 있는데 첫 번째는 HashSet은 HashMap으로 동작한다는 것이다.

 

Map은 Key와 Value의 쌍으로 값이 저장되는 형태이고, Key Object는 중복을 허용하지 않고, Value Object는 중복을 허용하는 순서를 보장하지 않는 데이터 구조이다.

 

아래는 HashSet 클래스의 일부이다.
```java
public class HashSet<E> {
    private transient HashMap<E,Object> map;
    private static final Object PRESENT = new Object();

    public HashSet() {
        map = new HashMap<>();
    }

    public boolean add(E e) {
        return map.put(e, PRESENT)==null;
    }
}
```
위 코드를 통해 HashSet은 HashMap으로 구현되어 있다는 것과 HashSet에 값이 저장되면 Key Object에 값이 들어가게 된다는 걸 알 수 있다.

 

이것이 가능한 이유는 Map의 Key는 중복된 값이 들어가지 못한다는 점과 Set에는 중복된 값이 들어가지 못하는 공통된 특징 때문이다. (Map의 동작 원리를 추후에 다시 다뤄볼 예정이다.)

 

 

 

두 번째는 HashMap과 HashSet은 데이터를 추가하기 전에 equals와 hashCode가 실행이 된다는 점이다.

자바에는 객체를 식별할 수 있는 hashCode메서드가 존재하는데, hashCode메서드는 기본적으로 객체의 주소를 이용해서 객체의 고유한 값을 정수로 만들어 반환하는 메서드이다.

 

그럼 여기서 가설 하나를 세울 수 있을 것이다.

Q. hashCode 메서드가 실행되어 반환하는 정수로 hashCode의 값에 따라 저장이 되는가?
A. 아니다.
 

처음 문제 상황에서 제기한 11을 추가한 상태로 데이터 각각에 대해 hashCode를 출력하면 다음과 같은 결과값을 얻게 된다.
```java
import java.util.HashSet;

public class HashSetExample1 {
    public static void main(String[] args) {
        HashSet<String> hashSet = new HashSet<>();
        
        hashSet.add("1");        hashSet.add("2");        hashSet.add("3");
        hashSet.add("4");        hashSet.add("5");        hashSet.add("6");
        hashSet.add("7");        hashSet.add("8");        hashSet.add("9");
        hashSet.add("10");        hashSet.add("11");
        
        for (String str : hashSet) {
            System.out.println("key > " + str + ", hashCode > " + str.hashCode());
        }
    }
}
key > 11, hashCode > 1568
key > 1, hashCode > 49
key > 2, hashCode > 50
key > 3, hashCode > 51
key > 4, hashCode > 52
key > 5, hashCode > 53
key > 6, hashCode > 54
key > 7, hashCode > 55
key > 8, hashCode > 56
key > 9, hashCode > 57
key > 10, hashCode > 1567
```
다시 말해 hashCode의 값에 따라 정렬이 되는 것은 아니라는 것을 알 수 있다.

 


 

그렇다면 과연 어떻게 저장되는 것일까?

 

HashSet과 HashMap은 데이터를 저장할 때 배열을 사용한다. 그리고 각각에 대해 아무런 값을 주지 않고 생성하게 되면 'initial capacity'의 값이 16이 된다. 그리고 이 capacity의 값이 배열의 크기라고 생각하면 된다.

[TIP]
Java의 Collection을 보게 되면 capacity와 load factor라는 용어가 정말 자주 나온다..!
 

Java 8 레퍼런스 HashSet의 기본 생성자에 대해 이렇게 설명되어 있다.

Constructs an empty HashMap with the default initial capacity (16) and the default load factor (0.75).
번역하면, 기본 초기 용량(16)과 기본 로드 팩터(0.75)를 사용하여 빈 해시맵을 구성한다.
 

그럼 대충..... HashSet과 HashMap을 생성하면 배열의 크기가 16인 걸 알게 되었다.

 

그럼 이제 load factor만 이해한다면 HashSet의 동작 원리에 대해 감이 올 것이다.

load factor를 설명하기전에 HashSet과 HashMap은 데이터 저장시에 배열을 사용한다고 했던 게 기억이 나는가?

 

HashSet과 HashMap은 데이터를 저장할 때 hashCode를 배열의 크기만큼 나눈 나머지를 배열의 index로 사용한다.

 

아까 hashCode의 값을 출력한 코드에 배열의 크기만큼 나눈 항목을 추가하여 확인을 해보면 다음과 같다.
```java
import java.util.HashSet;

public class HashSetExample1 {

    public static void main(String[] args) {
        HashSet<String> hashSet = new HashSet<>();
        
        hashSet.add("1");        hashSet.add("2");        hashSet.add("3");
        hashSet.add("4");        hashSet.add("5");        hashSet.add("6");
        hashSet.add("7");        hashSet.add("8");        hashSet.add("9");
        hashSet.add("10");        hashSet.add("11");
        
        for (String str : hashSet) {
            System.out.println("key > \t" + str + "\t | hashCode > \t" + str.hashCode()
                                + "\t | hashCode % 16 > " + str.hashCode() % 16);
        }
    }
}
```
```java
key > 	11	 | hashCode > 	1568	     | hashCode % 16 > 0
key > 	1	 | hashCode > 	49	     | hashCode % 16 > 1
key > 	2	 | hashCode > 	50	     | hashCode % 16 > 2
key > 	3	 | hashCode > 	51	     | hashCode % 16 > 3
key > 	4	 | hashCode > 	52	     | hashCode % 16 > 4
key > 	5	 | hashCode > 	53	     | hashCode % 16 > 5
key > 	6	 | hashCode > 	54	     | hashCode % 16 > 6
key > 	7	 | hashCode > 	55	     | hashCode % 16 > 7
key > 	8	 | hashCode > 	56	     | hashCode % 16 > 8
key > 	9	 | hashCode > 	57	     | hashCode % 16 > 9
key > 	10	 | hashCode > 	1567	     | hashCode % 16 > 15
```
HashSet과 HashMap의 기본 배열의 크기가 16이라 hashCode에 16을 나눈 나머지의 값을 출력하게 될 경우 배열의 INDEX 값이 결과로 나타나게 되고, 해당 INDEX의 값에 따라 값이 저장되기 때문에 11의 값이 추가됐을 때 가장 첫 번째 결과로 나오게 된 것이다.

 

그렇다면 또 다른 가설을 세울 수 있다.

Q. Set은 비선형 자료구조이기 때문에 저장한 순서대로 값이 저장되지 않고, 따라서 출력 또한 저장된 순서대로 출력되지 않는 특징을 가지고 있는데 이렇게 되면 순서를 보장하게 되는 것이 아닌가요?!
A. 아니다.

Q. 아 참... 데이터가 16개가 넘어가게 되는 경우에는 어떻게 되나요?!
 

이걸 설명하기 위한 것이 바로 로드 팩터(load factor)이다.

앞전에 가져온 HashSet의 생성자를 다시 가져와보겠다.

Constructs an empty HashMap with the default initial capacity (16) and the default load factor (0.75).
번역하면, 기본 초기 용량(16)과 기본 로드 팩터(0.75)를 사용하여 빈 해시맵을 구성한다.
로드 팩터는 추가적인 공간을 확보하기 위한 수치라고 보면 된다. 그리고 이 수치는 기본적으로 0.75로 설정이 된다.

배열의 크기(16)가 75% 사용되면 현재 배열의 크기의 2배만큼 늘리게 된다.

 

이러한 이유로 초기에는 순서가 보장될 수 있을지언정 크기가 늘어날수록 HashMap과 HashSet의 순서를 보장하지 않게 된다.

 

 


이제 지금까지의 내용들을 토대로 예시를 보도록 한다.

참고로 위 내용까지는 이해를 위해 HashSet<String>을 사용하였지만, 아래 내용부터는 가시적인 결과를 위해 HashSet<Integer>를 사용하였습니다.
 

초기 배열 크기가 16이고 75%에 해당하는 데이터가 12이기 때문에 두 가지 예시를 들 것이다.

1. 데이터가 12개 이하인 경우
2. 데이터가 13개 이상인 경우
 

1. 데이터가 12개 이상인 경우
```java
import java.util.HashSet;

public class HashSetExample1 {

    public static void main(String[] args) {
        HashSet<Integer> hashSet = new HashSet<>();
        hashSet.add(16);	hashSet.add(1);		hashSet.add(2);
        hashSet.add(3);		hashSet.add(4);		hashSet.add(5);
        hashSet.add(6);		hashSet.add(7);		hashSet.add(8);
        hashSet.add(9);		hashSet.add(10);	hashSet.add(11);
        
        // hashSet.add(12);

        System.out.println(hashSet);
        System.out.println("hashSet.size() > " + hashSet.size());
        
        System.out.println("------------------------------------");
        
        for (Integer i : hashSet) {
            System.out.println("key > \t" + i + "\t | hashCode > \t" + i.hashCode()
                                + "\t | hashCode % 16 > " + i.hashCode() % 16);
        }
    }
}
```
```java
[16, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11] 
hashSet.size() > 12
------------------------------------
key > 	16	 | hashCode > 	16	 | hashCode % 16 > 0
key > 	1	 | hashCode > 	1	 | hashCode % 16 > 1
key > 	2	 | hashCode > 	2	 | hashCode % 16 > 2
key > 	3	 | hashCode > 	3	 | hashCode % 16 > 3
key > 	4	 | hashCode > 	4	 | hashCode % 16 > 4
key > 	5	 | hashCode > 	5	 | hashCode % 16 > 5
key > 	6	 | hashCode > 	6	 | hashCode % 16 > 6
key > 	7	 | hashCode > 	7	 | hashCode % 16 > 7
key > 	8	 | hashCode > 	8	 | hashCode % 16 > 8
key > 	9	 | hashCode > 	9	 | hashCode % 16 > 9
key > 	10	 | hashCode > 	10	 | hashCode % 16 > 10
key > 	11	 | hashCode > 	11	 | hashCode % 16 > 11
```

2. 데이터가 13개 이상인 경우
```java
import java.util.HashSet;

public class HashSetExample1 {

    public static void main(String[] args) {
        HashSet<Integer> hashSet = new HashSet<>();
        hashSet.add(16);	hashSet.add(1);		hashSet.add(2);
        hashSet.add(3);		hashSet.add(4);		hashSet.add(5);
        hashSet.add(6);		hashSet.add(7);		hashSet.add(8);
        hashSet.add(9);		hashSet.add(10);	hashSet.add(11);
        
        hashSet.add(12);

        System.out.println(hashSet);
        System.out.println("hashSet.size() > " + hashSet.size());
        
        System.out.println("------------------------------------");
        
        for (Integer i : hashSet) {
            System.out.println("key > \t" + i + "\t | hashCode > \t" + i.hashCode()
                                + "\t | hashCode % 16 > " + i.hashCode() % 16);
        }
    }
}
```
```java
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 16]
hashSet.size() > 13
------------------------------------
key > 	1	 | hashCode > 	1	 | hashCode % 16 > 1
key > 	2	 | hashCode > 	2	 | hashCode % 16 > 2
key > 	3	 | hashCode > 	3	 | hashCode % 16 > 3
key > 	4	 | hashCode > 	4	 | hashCode % 16 > 4
key > 	5	 | hashCode > 	5	 | hashCode % 16 > 5
key > 	6	 | hashCode > 	6	 | hashCode % 16 > 6
key > 	7	 | hashCode > 	7	 | hashCode % 16 > 7
key > 	8	 | hashCode > 	8	 | hashCode % 16 > 8
key > 	9	 | hashCode > 	9	 | hashCode % 16 > 9
key > 	10	 | hashCode > 	10	 | hashCode % 16 > 10
key > 	11	 | hashCode > 	11	 | hashCode % 16 > 11
key > 	12	 | hashCode > 	12	 | hashCode % 16 > 12
key > 	16	 | hashCode > 	16	 | hashCode % 16 > 0
```

위 결과를 통해 HashSet의 데이터가 13개가 되는 순간 사용 데이터 수가 배열의 크기 75%를 넘기게 되면서, 현재 크기에 2배인 32가 되게 되고 제일 마지막으로 이동한 것을 확인할 수 있다.

 

이러한 이유때문에 아래와 같은 코드를 작성했을 때 Set과 Map의 특징과는 맞지 않게 정렬되어 출력된 것이 이러한 이유 때문이다.
```java
import java.util.HashSet;

public class HashSetExample1 {
    public static void main(String[] args) {
        HashSet<String> hashSet = new HashSet<>();
        
        hashSet.add("1");            hashSet.add("3");
        hashSet.add("0");            hashSet.add("2");
        
        System.out.println(hashSet);
    }
}
[0, 1, 2, 3]
```


**참고**
- [링크](https://papimon.tistory.com/74)
