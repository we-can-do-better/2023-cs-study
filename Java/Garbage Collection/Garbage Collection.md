# Garbage Collection

# JVM의 구조

---

![Untitled](Garbage%20Collection%20306e8bcff8b24eada4dafc83b68681f3/Untitled.png)

## JVM Runtime Data Areas

JVM에 Java Bytecode를 실행하기 위해 사용하는 메모리 공간

![Untitled](Garbage%20Collection%20306e8bcff8b24eada4dafc83b68681f3/Untitled%201.png)

## Per JVM

- 모든 스레드가 공유한다.
- Method area와 Heap area로 이루어져있다.

### Method area ➡️ class data 저장

- Class area, Code area, Static are 등으로도 불린다.
- 클래스 로더가 클래스 파일을 읽어오면, 클래스별로 클래스 정보를 파싱해서 Method Area에 런타임 상수풀, 필드 데이터, 메소드 데이터, 메소드 코드, 생성자 코드 등을 분류해서 저장한다.
- 변수가 어떤게 있는가, 메소드가 어떤게 있는가, 정적 변수는 어떤게 있는가, Bytecode는 어떤가 드의 정보를 저장한다.
- 다른 스레드에서도 접근할 수 있다.
- 전역 변수와 Static 변수가 Method area에 저장되고, 프로그램이 시작부터 종료될 때까지 메모리에 남아있게 된다. ➡️ 전역 변수가 프로그램이 종료될 때까지 어디서든 사용 가능하게 된다.
- final class 변수의 경우 상수로 치환되어 Runtime Constant Pool에 값을 복사한다.

### Method area - Runtime Constant Pool ( 런타임 상수 풀 )

- Method Area에 포함되며 클래스 파일 Constant_pool 테이블에 해당하는 영역이다.
- Type, Field, Method로의 모든 레퍼런스를 저장한다.
- JVM은 Runtime Constant Pool을 통해 실제 메모리상 주소를 찾아 참조한다.

### Heap area ➡️ Object 저장

- 프로그램을 실행하면서 생성할 모든 객체를 Heap에 저장한다.
- JVM이 관리하는 프로그램에서 데이터를 저장하기 위해 런타임시 동적으로 할당하여 사용하는 영역이다.
- New연산자로 생성된 객체, 인스턴스와 배열을 저장한다.
- 힙 영역에 생성된 객체, 배열은 스택 영역의 변수나 다른 객체의 필드에서 참조한다.
- GC가 수행되는 영역
- String Constant Pool
    
    ![Untitled](Garbage%20Collection%20306e8bcff8b24eada4dafc83b68681f3/Untitled%202.png)
    
    - String literal로 생성하면 해당 String 값은 Heap 영역 내 "**String Constant Pool**"에 저장되어 재사용된다.
    - new 연산자로 생성하면 같은 내용이라도 여러 개의 객체가 각각 Heap 영역을 차지한다.
    
    ```java
    String str1 = new String("abc");
    // new 를 통해 객체 주소를 참조하므로 "abc"에 대한 객체를 생성해 Heap에 저장
    String str2 = "abc";
    // = 를 통해 리터럴 주소를 참조하므로, "abc"라는 리터럴을 생성해, contant string pool에 저장
    String str3 = new String("abc");
    // str1과 다른 객체 이므로, 객체를 생성하여 Heap에 저장
    String str4 = "abc";
    // str2의 값과 str4의 값이 같고, 그 값은 Constant pool에 재사용 가능하도록 리터럴로 저장되어 있으므로, str4는 str2와 동일한 주소값을 가진다.
    str2 = "def"
    // "def"라는 리터럴을 생성해 constant string pool에 저장한다.
    ```
    

## Per Thread

![https://user-images.githubusercontent.com/42285463/198823063-bb06942a-e394-4120-8fa5-3bc5c88caa46.png](https://user-images.githubusercontent.com/42285463/198823063-bb06942a-e394-4120-8fa5-3bc5c88caa46.png)

- 스레드마다 존재한다.
- Program Counter Registers(PC 레지스터), Stack Area, Native Method Stack으로 이루어져있다.

### Stack area

![https://user-images.githubusercontent.com/42285463/198823222-1e1d4a48-4df6-4ebe-a823-58198e505a71.jpeg](https://user-images.githubusercontent.com/42285463/198823222-1e1d4a48-4df6-4ebe-a823-58198e505a71.jpeg)

- 자바 스택은 스레드 별로 1개만 존재한다.
- 스택 프레임은 메소드가 호출될 때마다 생성된다.
- 메소드 실행이 끝나면 스택 프레임은 pop되어 스택에서 제거된다.
- 스레드를 생성하지 않았다면, main 스레드만 존재하고 그 안에 stack frame도 단 한개다.
- 기본/원시 자료형(int, double, long, float)과 참조 자료형(기본 자료형을 제외한 나머지)를 저장한다.
- 참조 자료형 변수의 데이터는 실제로는 Heap의 영역에 존재하고, Stack 영역에서는 참조 자료형의 주소값만 갖는다.
- 메서드 호출이 끝나면 Stack 내 메서드 관련 지역변수도 지워지기 때문에 지역 변수는 전역변수가 저장되는 Method 영역과 달리 메서드 영역 밖에서는 사용할 수 없는 것이다.

**Stack Frame**

- 스택 프레임은 메소드가 호출될 때마다 새로 생겨 스택에 push된다.
- 스택 프레임은 Local Variables array, Operand stack, Frame Data를 갖는다.
    - Local variable array : local 변수가 들어있는 array
    - Operand stack : 메소드 내 계산을 위한 작업 공간
- Frame Data는 Constant pool, 이전 스택 프레임에 대한 정보, 현재 메소드가 속한 클래스 / 객체에 대한 참조 등의 정보를 갖는다.

### Program Counter ( PC )

- 각 스레드는 어떤 메소드를 항상 실행하고 있다.
- 이때, PC는 그 메소드의 몇번째 줄을 실행해야하는지 나타낸다.

### Native Method Stack

- Java Bytecode가 아닌 다른 언어로 작성된 메소드를 의미한다.
- JVM의 성능향상을 목적으로 C나 C++을 사용하는 경우가 있는데, 그 때 사용되는 메소드이다.

### CPU는 레지스터를 써서 피연산자를 저장하고 연산하는데, JVM은 왜 스택을 쓸까?

- Java는 다기종의 디바이스에서 균일하게 동작하는 것을 보장하고 싶었는데, 디바이스마다 레지스터 수가 달라, 레지스터 기반으로 동작할 경우 구현에 관여하게 된다.
- 스택을 쓰면, 계산과정이 복잡해지더라도, 실제 HW의 스펙에 최소한으로 관여할 수 있게된다.
- 그리고 Bytecode가 컴팩트해 네트워크로 전송할때도 용이하다.
- 따라서 이러한 추사아화를 하기 위해 스택을 사용한다.

# GC와 JVM의 Heap 영역

---

![https://user-images.githubusercontent.com/42285463/198823444-e4fb7265-5e9c-4628-8173-a22537a33f2e.png](https://user-images.githubusercontent.com/42285463/198823444-e4fb7265-5e9c-4628-8173-a22537a33f2e.png)

- 동적으로 할당되는 메모리는 최초에 Eden 영역에 할당된다.
- 생존 시간이 길 수록 Old generation으로 옮겨진다.
    - 오랫동안 참조되는 객체는 살아남아 `Eden - Survival 0 - Survival 1 - Old generation` 순으로 옮겨진다.
    - 더 이상 참조되지 않는 객체는 Garbage Collector에 의해 제거된다.

### Yong Generation

- 새롭게 생성된 객체가 할당되는 영역
- 많은 객체가 이 곳에서 도달할 수 없는 상태, 즉 참조되지 않는 상태가 되어 사라진다.
- Eden 영역이 가득차면, Garbage Collection가 실행되고, 이때, 실행되는 Garbage Collection을 Minor GC라고 한다.
- Eden 영역에서 살아남은 객체는 Survival 0 혹은 Survival 1로 이동한다.

### Old Generation

- Young Generation에서 살아남는, 참조값이 유지되는 객체가 위치하는 영역
- Old Generation이 가득차면 Garbage Collection이 실행되고, 이때, 실행되는 Garbage Collection을 Major GC 또는 Full GC라고 한다.

# Garbage Collector

---

## Heap에서의 Garbage Collector 동작 과정

1. 새로운 객체가 Eden 영역에 할당된다. **이 때 두개의 Survival 중 하나는 반드시 비워진 상태로 시작된다.**
2. Eden 영역이 가득차면 **Minor GC** 가 발생한다.
3. Minor GC에서 참조값이 유지된 객체는 Survival 영역으로 이동한다. 참조값이 존재하지 않는 객체는 Minor GC 중 메모리에서 삭제된다.
4. 다음 Minor GC가 발생할 때, Eden 영역에서는 3번과 같은 과정이 반복된다. 기존에 Survival 영역에 위치하던 객체들 중 참조값이 유지된 객체들은 다른 Survival 영역으로 이동하며 이때 객체의 age값이 1 증가한다. Survival 영역에서 참조값이 존재하지 않게 된 객체는 메모리에서 삭제된다.
5. 4번의 과정이 반복되며, 이 과정에서 지속적으로 살아 남은 객체 중 age 값이 특정 값 이상인 객체들은 Old Generation으로 옮겨진다. Young Generation에서 Old Generation으로 객체가 이동하는 단계를 **Promotion** 이라고 이야기한다.
6. Minor GC의 반복과 Promotion 작업의 반복이 계속 일어나다 보면 Old Generation이 가득차게 된다.
7. 이 때 **Major GC** 가 발생한다.

# Garbage Collection( GC )

---

Heap의 영역의 object 중 stack에서 도달 불가능한 객체(stack 영역에서 더 이상 참조하지 않는 객체)를 추적하고 제어한다.

Garbage Collector 라는 프로그램이 Garbage Collection 이라는 행위를 수행한다.

## GC의 동작 방식

1. Stop The World
    - Garbage Collection이 실행될 때, Garbage Collection을 실행하는 특정 스레드를 제외한 모든 스레드의 작업을 중단한다.
    - Garbage Collection이 수행되는 동안 어플리케이션의 모든 동작이 중단된다.
2. Mark and Sweep
    1. Mark
        1. Garbage Collector가 스택의 모든 변수를 스캔하면서 각각 어떤 객체를 참조하고 있는지 찾아서 Marking 한다.
        2. Reachable object가 참조하고 있는 객체도 찾아서 Marking한다.
    2. Sweep
        1. 마킹되지 않은 객체를 힙에서 제거한다.

### Reachable object

![https://user-images.githubusercontent.com/42285463/198823335-084adfb4-3100-4420-bb6c-3566979f326a.png](https://user-images.githubusercontent.com/42285463/198823335-084adfb4-3100-4420-bb6c-3566979f326a.png)

- root set : 유효한 최초 참조
- rootset으로부터 참조를 받거나, 그런 객체의 참조 사슬에 속한 객체
    
    ![https://user-images.githubusercontent.com/42285463/198823344-517a0d09-9a0e-4b31-9c57-4ebda79ee3dd.png](https://user-images.githubusercontent.com/42285463/198823344-517a0d09-9a0e-4b31-9c57-4ebda79ee3dd.png)
    

## Serial GC

- GC를 처리하는 스레드가 1개이다.
- CPU 코어가 1개만 있을때 사용하는 방식이다.
- Mark-Cpmpact Collection 알고리즘을 사용한다.
    - GC후 남은 메모리를 한 곳으로 모아주어서 단편화를 없애준다.

## Parallel GC

- GC를 처리하는 스레드가 여러개이다.
- Serial GC보다 빠르게 처리할 수 있다.
- 메모리와 코어의 개수가 충분할 때 사용하면 좋다.

## Concurrent Mark Sweep GC (CMS)

- Full GC시 자바 어플리케이션이 중단되는 시간을 줄인다.
- Old 영역에서 사용된다.
- 응답 시간이 빨라야 할 때 사용한다.
- 다른 GC보다 메모리와 CPU를 많이 사용한다.

## G1 GC

- 각 영역을 Region 영역으로 나눈다. (Eden, Survival, Old 내부에서 Region으로 또 나눠준다.)
- GC가 일어날 때, 전체 영역(Eden, Sruvival, Old)을 탐색하지 않는다.
- 꽉 찬 Region 영역에 대해서만 GC를 수행한다.
- 자바 어플리케이션이 중단되는 시간이 짧다.

# 출처

---

[https://www.youtube.com/watch?v=vZRmCbl871I](https://www.youtube.com/watch?v=vZRmCbl871I)[https://vcfvct.wordpress.com/2014/10/23/jvm-architecture/](https://vcfvct.wordpress.com/2014/10/23/jvm-architecture/)[https://velog.io/@tkadks123/Java-Garbage-Collector에-대해-알아보자-12](https://velog.io/@tkadks123/Java-Garbage-Collector%EC%97%90-%EB%8C%80%ED%95%B4-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90-12)[https://velog.io/@ddh963963/자바-Garbage-Collection-Garbage-Collector#garbage-collector은-뭐고-garbage-collection는-뭘까](https://velog.io/@ddh963963/%EC%9E%90%EB%B0%94-Garbage-Collection-Garbage-Collector#garbage-collector%EC%9D%80-%EB%AD%90%EA%B3%A0-garbage-collection%EB%8A%94-%EB%AD%98%EA%B9%8C)[https://gyubgyub.tistory.com/68](https://gyubgyub.tistory.com/68)[https://sgcomputer.tistory.com/64](https://sgcomputer.tistory.com/64)