## GarbageCollection


> GarbageCollector : 동적으로 할당한 메모리 영역 중 사용하지 않는 영역을 탐지하여 메모리에서 해제하는 JVM의 작업

##### GC 장점
1. 개발자의 실수로 인한 메모리 누수 방지
2. 해제된 메모리를 또 해제하는 이중 해제 예방
3. 해제된 메모리에 접근하는 에러 예방

##### GC 단점
1. GC의 메모리 해제 타이밍을 개발자가 정확히 알기 어렵다.
2. 어떠한 메모리 영역을 해제할지 결정하는데 비용이 든다.
3. GC가 동작하는 동안에는 다른 동작을 멈추기 떄문에 오버헤드가 발생

##### GC에서 사용하는 알고리즘

- Reference Counting : Heap 영역의 객체들이 각각의 reference count를 가지고 있고, 이 count 가 0이되면 해당 객체를 해제한다.
- Mark And Sweep : ```Root Space``` 부터 해당 객체에 접근 가능한지, 아닌지를 메모리 해제의 기준으로 삼는다.

``` Root Space : Stack이나 Static 공간에 선언된 변수들의 모음 ```

#### GC의 실행 방식

- Serial GC
    - Serial GC는 하나의 스레드로 GC를 실행하는 방식, 스레드가 하나이기 때문에 ```Stop The World``` 시간이 길다.
    - GC 실행 이후 메모리 파편화를 막는 Compaction 과정을 수행한다.
- Parallel GC
    - Serial GC를 멀티 스레딩으로 수행한다.
- CMS GC
    - 대부분의 GC 를 애프리케이션 스레드와 동시에 수행하여, Stop The World 시간을 최소화한다.
    - 메모리와 CPU 를 많이 사용하고 Compaction 과정이 기본적으로 제공되지 않는 단점이 있다.

```Stop The World [STW]: GC를 실행하기 위해 JVM이 어플리케이션 실행을 멈추는 것```
- G1 GC
    - Garbage First의 약자로, Garbage만 있는 Region을 먼저 회수한다.
    - JDK9 부터 G1 GC가 기본 GC 방식으로 쓰임

- G1 GC 장점
    - 별도의 STW 없이 여유 메모리 공간을 압축하는 기능을 제공한다.
    - 전체 Young or Old Generation 통째로 Compaction 할 필요 없고 일부분인 Region에 대해서만 Compaction 하면된다.
    - CMS에 비해 개선된 알고리즘을 사용하고 처리 속도가 빠르다.
    - Heap 크기가 클수록 잘 동작한다.

- G1 GC 단점
    - 공간이 부족할 경우 Full GC(Heap 전반적으로 GC가 발생) 가 발생하는데 이 GC는 싱글 쓰레드로 동작한다.
    - 작은 Heap 공간을 가지는 어플리케이션에서는 제 성능을 발휘하지 못하고 Full GC가 발생한다.

- G1 GC의 사이클
    ![img.png](img/img.png)

    - Young-only Phase : Minor GC만 수행하다가 XX:InitiatingHeapOccupancyPercent(Old Generation 비율)에 지정된 값을 초과하는 순간 Major GC가 수행된다. 그림에서 Old gen occupancy exceeds threshold 부분을 말하는 것이다.
    - Space Reclamation Phase : 위의 구간이 끝나고 공간회수 구간이 시작된다.



  