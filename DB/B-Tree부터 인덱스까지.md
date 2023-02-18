### 이진 탐색 트리

- **이진 검색의 효율적인 검색의 장점**과 **데이터를 빈번하게 삽입하고 삭제하기 용이한 연결 리스트의 장점을 결합한 정렬된 이진트리**

**이진트리**

노드의 최대 차수가 2인 트리

> *차수(Degree): 어떤 노드가 가지고 있는 자식 노드의 개수*
>

![이미지 출처: [https://galid1.tistory.com/176](https://galid1.tistory.com/176)](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1e53bd86-bddd-4d77-a16a-05ab01a531a3/Untitled.png)

이미지 출처: [https://galid1.tistory.com/176](https://galid1.tistory.com/176)

**이진 탐색 트리**

이진 트리에서 조건이 추가된 이진 트리(= 정렬된 이진트리)

- 조건
    1. 루트 노드의 왼쪽 자식 노드는 루트 노드보다 크기가 작다.
    2. 루트 노드의 오른쪽 자식 노드는 루트 노드보다 크기가 크다.

- **균형 잡힌 이진 트리**와 **균형 잡히지 않은 이진 트리**

  ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8891b0a7-6342-4a7d-a51b-8b3d4f468027/Untitled.png)

    - **균형이 잡히지 않은 이진 탐색**(최악의 경우)의 경우 `O(n)`
    - **균형이 잡힌 이진 탐색 트리**의 경우 시간 복잡도가 `O(log n)`

  **= 이진 탐색의 장점을 가졌다고 보기 어렵다.**


<aside>
📢 Java Collection
- TreeSet은 TreeMap으로 동작
- TreeMap은 **Red-Black 트리를 사용**

</aside>

### B-Tree

- B-Tree는 이진 탐색 트리의 최악인 경우(`O(n)`)의 문제를 해결하기 위해 나온 **self-balancing tree**
- 효율적인 검색, 삽입 및 삭제 작업에 사용되는 균형 잡힌 트리

> **Q. B-Tree는 왜 이진 트리가 아닐까?**
**이진 트리**는 `각 노드의 최대 차수가 2인 트리`
반면, **B-Tree**는 `규칙에 따라 특정 노드의 차수가 2개 이상일 수 있기 때문`
>
>
> *차수(Degree): 어떤 노드가 가지고 있는 자식 노드의 개수*
>
- 최대 M개의 자식을 가질 수 있는 B-Tree를 `M차 B-Tree`

![이미지 출처: [https://rebro.kr/169](https://rebro.kr/169)](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/075cc390-0016-4b17-9db8-ccd8bd18f336/Untitled.png)

이미지 출처: [https://rebro.kr/169](https://rebro.kr/169)

1. 최상위 노드를 `루트 노드`, 최하위 노드를 `리프 노드`, 그 사이 노드들을 `브랜치 노드`라 부른다.
2. 특정 노드의 키(노드의 수)의 수가 `K개`라면, 자식 노드의 수는 `K+1개`이다.
3. 노드의 키는 반드시 `정렬된 상태`여야 한다.

   [B-Tree Visualization](https://www.cs.usfca.edu/~galles/visualization/BTree.html)

    - 2 ->1 -> 3 예시
4. 자식 노드들의 키는 현재 노드의 키를 기준으로 나뉘게 된다. `작으면 왼쪽, 크면 오른쪽`
5. 루트 노드는 `항상 2개 이상의 자식 노드`를 갖는다. (root node가 leaf node인 경우 제외)
6. M차 트리일 때, 루트 노드와 리프 노드를 제외한 모든 노드는 `최소 [M/2], 최대 M개의 서브 트리를 갖는다.`
7. INSERT, DELETE와 같은 작업이 일어나도 `항상 균형을 유지`한다. (B-Tree의 B는 Balanced)
8. 모든 리프 노드(leaf node)들은 `같은 레벨(level)`에 있어야 한다.

> **Q. B-Tree에서는 중복된 키 값을 허용하지 않는다…… 라는 글을 많이 보게 되는데 만약 중복값이 들어간다면?
>> 중복 데이터가 B-Tree에 삽입될 때, 알고리즘의 특정 구현에 따라 달라진다.

1.** 중복된 값을 가진 키를 삽입할 때, 비중복 키와 마찬가지로 키가 삽입되어야 하는 적절한 리프 노드를 검색
2. 리프 노드에 동일한 값을 가진 키가 이미 포함되어 있으면 B-Tree는 기존 키를 **새 키로 덮어쓰거나 기존 노드에 연결된 별도의 노드에 새 키를 삽입**
>
>
> (기존 키를 덮어쓸지 아니면 별도의 노드에 새 키를 삽입할지에 대한 선택은 B-Tree의 구현과 애플리케이션의 특정 요구 사항에 따라 달라진다. 따라서, 경우에 따라 중복 키를 허용하는 것이 바람직할 수 있지만 다른 경우에는 고유 키를 적용해야 할 수도 있다.)
>

### B+Tree

### 인덱스

**참고**

- [링크](https://velog.io/@main_door/이진-트리와-이진-탐색-트리)