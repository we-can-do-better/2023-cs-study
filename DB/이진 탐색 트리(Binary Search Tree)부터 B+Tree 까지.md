# 이진 탐색 트리(Binary Search Tree)부터 B-Tree까지

이진 탐색 트리(Binary Search Tree)란

**이진 탐색(Binary Search)의 효율적인 탐색**과 **빈번한 자료 입력과 삭제에 용이한 연결 리스트(Linked List)**의 장점을 결합한 자료구조의 일종이다.

### **이진 탐색(Binary Search)**

- 이진탐색이란 데이터가 정렬돼 있는 배열에서 특정한 값을 찾아내는 알고리즘

```
1. 배열의 중간에 있는 임의의 값을 선택하여 찾고자 하는 값 X와 비교
2. X가 중간 값보다 작으면 중간 값을 기준으로 좌측의 데이터들을 대상으로 하고, X가 중간값보다 크면 배열의 우측을 대상으로 다시 탐색한다.
(해당 값을 찾을 때까지 이 과정을 반복)
```

한 가지 예시를 들어보겠다.

```
{ 17, 28, 43, 67, 88, 92, 100 }
```

이 배열에서 이진 탐색을 이용하여 43의 값을 과정은 다음과 같다.

```
찾고자 하는 값: 43
1. 43은 중간 값인 67보다 작으므로 좌측 배열 값들을 대상으로 탐색 **{17, 28, 43}**
2. 43은 좌측 배열의 중간 값인 28보다 크므로 우측 배열 값들을 대상으로 탐색 **{ 43 }**
3. 배열에 값이 하나만 남게 되고, 값을 비교해보면 **43 == 43**으로 원하는 값을 찾게 되어 탐색 종료
```

이렇게 처음에 N개 크기의 배열에서 단계가 하나씩 지나감에 따라 탐색할 배열의 크기가 반씩 줄어들기 때문에 **이진 탐색의 시간 복잡도는 O(log N)**이다.

단, 이진 탐색은 **정렬된 배열에서만 사용**될 수 있다.

### **연결 리스트 (Linked List)**

연결 리스트는 각 노드가 데이터와 포인터를 가지고 한 줄로 연결되어 있는 방식의 자료구조이다.

![image](https://user-images.githubusercontent.com/64416833/218054995-08a0c8b8-3ae3-41c4-a3d6-6163d74bf1ef.png)

이미지 출처: [https://www.alphacodingskills.com/ds/notes/linked-list.php](https://www.alphacodingskills.com/ds/notes/linked-list.php)

연결 리스트는 포인터로 연결되어 있어서 가리키는 노드만 변경해주면 되기 때문에 삽입과 삭제가 용이하다.

![image](https://user-images.githubusercontent.com/64416833/218055177-cdc285c8-9837-4755-932d-6841b9052e67.png)


이미지 출처: [https://qnaplus.com/insert-element-singly-linked-list/](https://qnaplus.com/insert-element-singly-linked-list/)

그래서 연결 리스트의 맨 앞/뒤에 데이터를 추가/삭제 하는 경우에 시간 복잡도가 O(1)이 소요되게 된다.

단, 연결 리스트의 중간에 추가/삭제(탐색하는 시간) 또는 탐색의 경우에는 시간복잡도 O(n)이 소요된다.

![image](https://user-images.githubusercontent.com/64416833/218055240-fc91512e-87e8-4c37-9d49-07626b3582f0.png)


이미지 출처: [https://qnaplus.com/insert-element-singly-linked-list/](https://qnaplus.com/insert-element-singly-linked-list/)

```
이진탐색
- 장점 : 탐색에 소요되는 시간복잡도는 O(log n)으로 빠름

연결리스트
- 장점 : 자료 입력, 삭제에 필요한 시간복잡도는 O(1)로 빠름
```

### **이진 탐색 트리(Binary Search Tree)**

이진 탐색 트리는 다음과 같은 형태를 띈 트리이다.

![image](https://user-images.githubusercontent.com/64416833/218055342-c3c5ebdf-0f0a-4581-837d-da83179afe17.png)


이진 탐색 트리는 다음과 같은 특징을 갖는 **이진트리*이다.

> **이진 트리(Binary Tree)*: 각 노드의 자식 노드가 최대 2개인 트리
>

```
1. 각 노드에 중복되지 않는 키(key)가 있다.
2. 루트 노드의 왼쪽 서브 트리는 해당 노드의 키보다 작은 키를 갖는 노드들로 이루어져 있다.
3. 루트 노드의 오른쪽 서브 트리는 해당 노드의 키보다 큰 키를 갖는 노드들로 이루어져 있다.
4. 좌우 서브 트리 전부는 이진 탐색 트리여야 한다.

```

이진 탐색 트리의 탐색 연산 시간 복잡도는 트리의 높이를 h(height)라고 한다면 $O(h)$의 시간 복잡도를 갖는다.

아래와 같은 트리가 존재한다고 했을 때, 키가 5인 노드를 찾고자 한다면 다음과 같은 순서를 거치게 된다.

![image](https://user-images.githubusercontent.com/64416833/218055408-56a749a5-d373-44ef-b009-4673285a010b.png)


```
찾고자 하는 값: 5
1. 5는 루트 노드의 키 값인 7보다 작으므로 왼쪽 서브 트리로 탐색
2. 5는 3보다 크므로 오른쪽 서브 트리로 탐색
3. 키가 5인 노드를 찾았으므로 탐색 종료
즉 트리 안의 값을 찾는다면 무조건 트리의 높이(h) 이하의 탐색(O(h))이 이루어진다.
(트리 안에 찾고자 하는 값이 없더라도 최대 h 번 만큼만의 탐색이 진행된다.)
```

**하지만.**

아래 이미의 우측 그림처럼 균형이 잡힌 이진 탐색 트리의 경우 시간 복잡도가 이진 탐색의 시간복잡도인 O(log n)이 되지만, 왼쪽 그림과 같이 균형이 잡히지 않은 이진 탐색의 경우 O(n)에 수렴하게 되면서 이진 탐색의 장점을 가졌다고 보기 어려웠다.

![image](https://user-images.githubusercontent.com/64416833/218055455-e4c4509c-626b-411d-b11d-24e285c32cd2.png)


그래서 이를 해결하기 위한 여러 자료구조가 생겨나게 되었고 그중 하나가 B-tree이다.

### **B-tree (Balanced Tree)**

B-tree는 다음과 같은 형태를 띈 자료구조이다.

![image](https://user-images.githubusercontent.com/64416833/218055508-97b55253-845b-4cbf-a48a-112c84ecba29.png)

이미지 출처: [https://rebro.kr/169](https://rebro.kr/169)

B-Tree는 트리 자료구조의 일종으로 이진 트리를 확장해 하나의 노드가 가질 수 있는 자식 노드의 최대 숫자가 2보다 큰 트리 구조이다.

**B-Tree의 특징**

1. 노드에는 2개 이상의 데이터(key)가 들어갈 수 있으며, 항상 정렬된 상태로 저장된다.

![image](https://user-images.githubusercontent.com/64416833/218055553-9783e50d-1edc-4d52-80db-acafbcd87dc3.png)


1. **최대 M개의 자식**을 가질 수 있는 B 트리를 **M차 B트리**라고 한다.

![image](https://user-images.githubusercontent.com/64416833/218055597-d7ad62da-8388-47c5-8615-c85ced1d0f76.png)

2-1) 이때, 자식 노드는 `[M/2]개` 부터 `M개` 까지의 자식 노드를 가질 수 있다.

위 그림은 3차 B 트리이기 때문에 1~2개의 자식 노드를 가질 수 있다.

![image](https://user-images.githubusercontent.com/64416833/218055645-8ef1176f-b0ee-4360-89b8-77c6f9b1ab27.png)

2-2) 특정 노드의 데이터(key)가 K개라면, 자식 노드의 개수는 K+1개여야 한다.

![image](https://user-images.githubusercontent.com/64416833/218055997-77cb0f6e-e3fc-4efa-8e2d-3f52432cb6be.png)


2-3) 특정 노드의 왼쪽 서브 트리는 해당 노드의 key보다 작은 값들로, 오른쪽 서브 트리는 큰 값들로 구성되어 있다.

![image](https://user-images.githubusercontent.com/64416833/218055712-48aaa61c-9172-4de1-bb9d-ecf3b3e955d4.png)

2-4) 한 노드에는 `floor((M/2)-1)개`부터 `M-1개`의 자식 노드가 포함될 수 있다.

ex) 3차 B 트리 => `0~2` = `1.5 - 1` = `floor(0.5)` = `0`

2-5) 모든 리프 노드(leaf node)들이 같은 레벨에 존재해야 한다.

> 단말 노드(leaf node) : 자식이 없는 노드
노드의 레벨(level) : 트리의 특정 깊이를 가지는 노드의 집합
>

![image](https://user-images.githubusercontent.com/64416833/218055795-87051f15-5db0-4e1a-907c-573f1e33ad7a.png)

이미지 출처: [https://zorba91.tistory.com/293](https://zorba91.tistory.com/293)

[B-Tree Visualization](https://www.cs.usfca.edu/~galles/visualization/BTree.html)

**B-Tree의 탐색 과정**

루트 노드에서 시작하여 **하향식**으로 탐색하는 이진 탐색 트리의 탐색과 비슷하다.

```
1. 루트 노드부터 탐색을 시작한다.
2-1. 노드를 순회하며 찾으려는 값(K)이 존재하면 탐색을 종료한다.
2-2. 찾으려는 값이 존재하지 않는다면 포인터를 통해 자식 노드로 내려간다.
리프 노드(leaf node)까지 2번의 과정을 반복한다.
```

찾으려는 값 K = 14

![image](https://user-images.githubusercontent.com/64416833/218056096-103d1843-3f06-4b52-b9df-2839b765b718.png)

**B-Tree의 삽입 과정**

B-Tree는 균형을 유지해야 하기 때문에 key를 삽입하는 경우 트리의 변형이 발생할 수 있다.

데이터를 삽입하는 과정은 다음과 같다.

```
1. 빈 트리인 경우 루트 노드를 만들어 K를 추가한다. 
루트 노드가 가득찬 경우 노드를 분리하여 리프 노드를 생성한다.

2. K가 들어갈 leaf node를 검색 과정과 동일하게 탐색한다.

3-1. 해당 leaf node에 자리가 남아있다면 정렬을 유지하도록 위치시킨다.
3-2. 해당 leaf node가 꽉 차 있다면 K를 삽입한 후 해당 node를 분할한다.
```

![image](https://user-images.githubusercontent.com/64416833/218056136-ba0ec671-0beb-492d-85d7-00932e603f2b.png)

`13`이라는 값을 삽입하는 과정

1. K가 들어갈 leaf node를 탐색

![image](https://user-images.githubusercontent.com/64416833/218056171-2a14855e-1b83-41d4-b7d3-0cbdc080610e.png)

2. 해당 leaf node에 K값을 삽입했으나 **한 노드가 가질 수 있는 최대 범위**를 벗어났기 때문에 해당 노드를 분할해야 한다.

> **B-Tree의 특징**
>
>
> 2-4) 한 노드에는 `floor((M/2)-1)개`부터 `M-1개`의 데이터를 가질 수 있다.
>
> 3차 B 트리에서 한 노드당 들어갈 수 있는 데이터(key)의 수: `0~2`
> - 최소 `1.5 - 1` = `floor(0.5)` = `0`
> - 최대 `3 - 1` = `M-1` = `2`
>

![image](https://user-images.githubusercontent.com/64416833/218056260-8a9615dc-9510-4e32-a6d1-d351f1feae52.png)


3. leaf node의 중간 값(`13`)이 부모 node와 합쳐지게 되고, 13은 부모 노드인 `11`보다 큰 값이기 때문에 오른쪽에 위치하게 되고, 자식 노드들도 키 값에 따라 각각 분할되게 된다.

![image](https://user-images.githubusercontent.com/64416833/218056385-2230a397-c829-4c86-94e6-059e54fab905.png)


4. 부모 노드 또한 key가 꽉 차있으므로 해당 노드를 분할해야 한다.

![image](https://user-images.githubusercontent.com/64416833/218056415-90cbb625-5530-4ced-9b9e-c0b8aef6ce1a.png)

5. 마찬가지로, 중간 값인 `11` 이 부모 노드와 합쳐지게 된다.

![image](https://user-images.githubusercontent.com/64416833/218056450-989822ef-8a65-48ca-ba1e-d5b280bcfb49.png)


6. 부모 노드(root node)에서도 역시 key가 꽉차있기 때문에 분할이 필요하다.

![image](https://user-images.githubusercontent.com/64416833/218056490-81035e59-2f1f-4039-8027-cc151b0c0881.png)


7. 중간 값인 11이 부모 노드로 올라가게 되면서 결과적으로 다음과 같은 형태를 띄게 된다.

![image](https://user-images.githubusercontent.com/64416833/218056515-e7630a16-5c25-48ab-99ce-e24320b5585d.png)


[참고]

- [링크](https://cjh5414.github.io/binary-search/)
- [링크](https://velog.io/@jewelrykim/Binary-Search-Tree에서-BTree까지Database-Index-추가)