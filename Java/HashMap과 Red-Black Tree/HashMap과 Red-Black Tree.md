# HashMap과 Red-Black Tree

# HashMap

HashMap은 Map이라는 인터페이스를 구현한 구현체 중 하나이다. 

<aside>
💡 HashMap은 Map의 인터페이스를 구현한 구현체로, 아래의 특징을 가진다.

1. HashMap은 해싱함수를 통해 인덱스를 산출한다.
2. HashMap은 인덱스를 통한 접근으로 시간 복잡도 O(1)의 빠른 성능을 자랑한다.
3. key는 무한하지만 인덱스는 한정되어 있어 충돌은 불가피하다.
4. 충돌을 줄이기 위해 HashMap은 버킷의 사이즈를 조절한다.
5. 충돌이 일어날 시, 충돌 수가 적으면 LinkedList 방식으로 충돌된 객체들을 관리하다가, 임계점을 넘으면 Red-Black Tree 방식으로 객체들을 저장한다.
6. 시간 복잡도는 Linked List가 O(n), Red-Black Tree가 O(log n)이다.
</aside>

## hash code

hash code는 객체를 식별하는 하나의 정수값이다.

hashcode() 메소드는 객체의 주소가 담긴 객체 고유의 해시코드를 반환한다.

## HashMap의 데이터 관리 방식

HashMap에서는 데이터를 저장할 때에, 버킷으로 불리는 배열을 가지고 데이터를 저장한다. 아래 table이 그 배열이다.

![Untitled](HashMap%E1%84%80%E1%85%AA%20Red-Black%20Tree%20a1700debd5854c22bfebabc26e5d62e6/Untitled.png)

이 table 배열은 hash값, 키, 데이터, 다음 node 를 필드로 가진다.

![Untitled](HashMap%E1%84%80%E1%85%AA%20Red-Black%20Tree%20a1700debd5854c22bfebabc26e5d62e6/Untitled%201.png)

Map에 저장하는 각 데이터는 고유한 키값으로 구분할 수 있다. 

HashMap에서는 이 키값에 대해, hash code를 추출하고, 그 hash code를 배열의 크기로 나눈 나머지를 내부 배열의 인덱스로 설정해 저장하고, 값을 조회한다.

HashMap은 인덱스를 통해 데이터에 접근하기 때문에, O(1)의 시간 복잡도를 갖는다. 

## 배열 크기 조정을 통한 HashMap의 충돌 관리

hash code의 기준을 잘 설정한다면 각 키에 대한 hash code의 중복, 즉 충돌이 발생하지 않겠지만, 완전히 보장할 수는 없다.

그래서 HashMap은 버킷(배열)의 75%를 초과하여 채워지면, 버킷의 사이즈를 2배씩 늘려 키 값 간 충돌을 방지한다.

## LinkedList와 Red-Black Tree를 통한 HashMap의 충돌 관리

배열 크기를 조정하는 것 뿐으로는 충돌을 관리하기 어렵다. 

만약, 충돌이 발생하여 하나의 인덱스에 여러 데이터가 저장되어야하는 상황이 발생한다면, 같은 인덱스를 가지는 데이터를 LinkedList로 연결하거나 Red-Black Tree로 연결한다.

### LinkedList를 사용한 Seperate Chaining 방식

LinkedList로 같은 인덱스를 가지는 각 노드를 연결한 방식을 Seperate Chaining 방식이라고 부른다.
LinkedList를 사용하기 때문에, 데이터를 조회할 때, O(n)의 시간 복잡도를 가진다.

![Untitled](HashMap%E1%84%80%E1%85%AA%20Red-Black%20Tree%20a1700debd5854c22bfebabc26e5d62e6/Untitled%202.png)

### Red-Black Tree 방식

충돌횟수가 일정 수치( TREEIFY-THRESHOLD -1 )를 초과하면, O(n)으로 데이터를 탐색하는 것이 비효율적이다. 이때, Red-Black Tree 방식을 사용한다. 

Balanced Tree 구조이기 때문에, 데이터를 조회할 때, O(log n)의 시간 복잡도를 가진다. 따라서, 탐색 속도가 LinkedList를 사용한 방식보다 빠르다.

### 왜 다른 Balanced Search Tree - AVL Tree가 아닌 Red-Black Tree를 사용한걸까?

Red-Black Tree와 같은 Balanced Search Tree 인 AVL Tree도 사용할 수 있을 텐데, 왜 HashMap은 Red-Black Tree를 사용한 걸까? 게다가, AVL Tree가 더 엄격하게 균형있는 트리로 관리하기 때문에, 탐색 속도가 빠르다.

1. HashMap은 삽입, 삭제가 자주 일어난다. 
    1. AVL Tree는 더 엄격한 균형의 트리인 만큼, 삽입과 삭제에 대해 Red-Black Tree보다 느리다.
    2. Red-Black Tree는 rebalance 시에, color flip과 rotate로 비교적 빠르게 삽입과 삭제를 수행할 수 있다.
2. Red-Black Tree는 각 노드를 1개의 비트만으로 표현한다.
    1. AVL Tree는 2개의 비트로 표현한다.
    2. 매우 큰 크기의 데이터 셋에 대해서 처리한다고 한다면, Red-Black Tree가 메모리 관점에서 더 유리하다.

# Red-Black Tree

## 규칙

1. 모든 노드는 빨강색 혹은 검정색이다.
2. Root 노드는 검정색이다.
3. 새롭게 노드를 추가할 때에는, 빨강색 노드로 추가한다.
4. Root 노드 부터 Leaf 노드까지의 모든 경로는 같은 개수의 검정색 노드를 가지고 있어야한다.
5. 어떠한 경로도, 연속으로 빨강색 노드를 가질 수 없다.
6. Null인 경우는 검정색 노드로 생각할 수 있다.

## Rebalance ( 트리를 균형있게 만드는 방법 )

1. **Rotate** : Black Aunt를 가질 경우
    1. Rotation 후에는, 부모 노드는 검정색, 자식 노드는 빨강색 노드의 구조이다.
2. **Color Flip** : Red Aunt를 가질 경우
    1. Color Flip 후에는, 부모 노드는 빨강색, 자식 노드는 검정색 노드의 구조이다.

![Untitled](HashMap%E1%84%80%E1%85%AA%20Red-Black%20Tree%20a1700debd5854c22bfebabc26e5d62e6/Untitled%203.png)

## Red-Black Tree Rebalance 예제

파랑색을 검정색 노드로, 핑크색을 빨강색 노드로 생각한다.

### 1번 예제

1. 올바른 균형의 Red-Black Tree에 7을 추가한다.
2. 5 노드와 7 노드에 연속적인 빨강색 노드가 있는 문제가 발생했다. 이때, Aunt 노드인 1번 노드가 빨강색이기 때문에, Color Flip 방법을 사용한다. 
3. 1, 5번 노드는 Flip되어 검정색 노드로 색깔을 변경했다. 3번 노드는 Flip되어야하나, 루트 노드이므로 검정색 노드로 유지한다.

![Untitled](HashMap%E1%84%80%E1%85%AA%20Red-Black%20Tree%20a1700debd5854c22bfebabc26e5d62e6/Untitled%204.png)

### 2번 예제

1. 올바른 균형의 Red-Black Tree에 6을 추가한다.
2. 7 노드와 6 노드에 연속적으로 빨강색 노드가 있는 문제가 발생했다. 이때, Aunt 노드는 없기 때문에(null) 검정색 노드라고 생각한다. Black Aunt의 상황에는 Rotate 방법을 사용한다. 
3. 5, 7, 6 노드를 Rotate 하여 6을 부모 노드로, 5와 7을 자식 노드로 가진다. 
4. Rotate 방법을 적용한 이후에는 부모 노드는 검정색 노드, 자식 노드를 빨강색 노드를 가져야하므로 6 노드와 5 노드의 색깔을 변경한다.

![Untitled](HashMap%E1%84%80%E1%85%AA%20Red-Black%20Tree%20a1700debd5854c22bfebabc26e5d62e6/Untitled%205.png)

### 3번 예제

1. 올바른 균형의 Red-Black Tree에 8을 추가한다.
2. 7 노드와 8 노드가 연속적으로 빨강색 노드를 가져 Rebalancing 해야한다. 이때, Aunt Node인 5 노드가 빨강색 노드이므로, Color Flip 방법을 사용한다.
3. 6 노드는 빨강색 노드로, 5, 7 노드는 검정색으로 변경하여, 트리가 올바르게 Rebalancing 되었다. 이 트리에 9를 추가한다.
4. 8 노드와 9 노드가 연속적으로 빨강색 노드를 가져 Rebalancing 해야한다. 이때, Aunt Node가 null이므로 검정색 노드라고 생각하므로, Rotate 방법을 사용한다. 
5. 7, 8, 9 노드에 대해 Rotate를 적용하여 8을 부모 노드로, 7, 9 를 자식 노드로 갖는다. 올바른 트리로 Rebalancing 되었다.
이 때, 루트 - 리프 노드의 각 경로를 살펴 보면 모두 2개씩 검정색 노드를 가지는, 올바른 Red-Black Tree임을 알 수 있다.

![Untitled](HashMap%E1%84%80%E1%85%AA%20Red-Black%20Tree%20a1700debd5854c22bfebabc26e5d62e6/Untitled%206.png)

## AVL Tree와의 차이점

### AVL Tree의 규칙

1. 왼쪽 Sub Tree와 오른쪽 Sub Tree간 높이의 차이는 0 또는 1 이어야한다. 
2. 1번 조건을 위반할 경우( 높이 차이가 2이상일 경우 ), Rebalancing 한다.

### Red-Black Tree VS AVL Tree

| 비교 대상 | Red Black Tree | AVL Tree |
| --- | --- | --- |
| 조회(탐색) | 엄격히 균형 잡힌 것은 아니기 때문에, 비교적 탐색이 빠르지 않다. | 엄격히 균형잡힌 트리이기 때문에, 빠르게 조회할 수 있다. |
| 색깔 | 빨강색과 검정색 노드로 이루어져있다. | 해당 없음 |
| 삽입, 삭제 | Red Black Tree는 AVL Tree에 비해 균형이 엄격하지 않아 rotation 작업이 적기 때문에, 더 빨리 삽입과 삭제를 수행한다. | AVL tree는 엄격히 균형잡힌 트리이므로, 삽입과 삭제가 복잡하다. |
| 저장 용량 | 노드당 1비트 | 각 노드의 balance factor 또는 높이를 저장하므로, 노드당 정수값을 저장한다. |
| 사용 | map, multimap, multiset등 | 빠른 탐색을 요하는 DB |
| Balance Factor | 해당 없음 | 각 노드는 1, 0, -1 값 중 하나를 가지는 balance factor를 가지고 있다. |
| Balancing | balancing을 위한 처리량이 적다. Rotation도 왼쪽, 오른쪽으로 최대 두가지 뿐이다. | Left Left, Right Right, Left Right, Right Left의 Rotation을 할 수 있어, balancing을 위해 비교적 처리량이 많다. |

## 레퍼런스

[Figure 2. Example of Separate Chaining Method](https://www.google.com/url?sa=i&url=https://www.researchgate.net/figure/Example-of-Separate-Chaining-Method_fig2_283760058&psig=AOvVaw0HtV0NVZXl62PqIxqHGFiT&ust=1678340390668000&source=images&cd=vfe&ved=0CA4QjhxqFwoTCPCHk7HPy_0CFQAAAAAdAAAAABAD)

[Red Black Tree vs AVL Tree - GeeksforGeeks](https://www.geeksforgeeks.org/red-black-tree-vs-avl-tree/)

[[자료구조] HashMap 파헤치기 1 (Linked List + Red Black Tree)](https://lordofkangs.tistory.com/78)

[Red Black Tree Rotations and Color Flips](https://medium.com/swlh/red-black-tree-rotations-and-color-flips-10e87f72b142)

[Red Black Tree 1 The  Rules](https://www.youtube.com/watch?v=nMExd4DthdA)

[Red Black Tree vs AVL Tree - GeeksforGeeks](https://www.geeksforgeeks.org/red-black-tree-vs-avl-tree/)