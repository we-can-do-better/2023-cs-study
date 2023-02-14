# 이진 탐색 트리(Binary Search Tree)부터 B-Tree까지

### **이진 탐색 트리(Binary Search Tree)**

**이진 탐색(Binary Search)의 효율적인 탐색**과 **빈번한 자료 입력과 삭제에 용이한 연결 리스트(Linked List)**의 장점을 결합한 자료구조의 일종

### **이진 탐색(Binary Search)**

데이터가 **정렬**돼 있는 배열에서 특정한 값을 찾아내는 알고리즘

> **!!주의!!**<br/>
이진 탐색은 **정렬된 배열에서만 사용**될 수 있다.
>

```
1. 배열의 중간에 있는 임의의 값을 선택하여 찾고자 하는 값 X와 비교
2. X가 중간 값보다 작으면 중간 값을 기준으로 좌측의 데이터들을 대상으로 하고, X가 중간값보다 크면 배열의 우측을 대상으로 다시 탐색한다.
>> 해당 값을 찾을 때까지 이 과정을 반복
```

정렬된 배열에서 이진 탐색을 이용하여 43의 값을 과정은 다음과 같다.

```
{ 17, 28, 43, 67, 88, 92, 100 }

찾고자 하는 값: 43
1. 43은 중간 값인 67보다 작으므로 좌측 배열 값들을 대상으로 탐색 **{17, 28, 43}**
2. 43은 좌측 배열의 중간 값인 28보다 크므로 우측 배열 값들을 대상으로 탐색 **{ 43 }**
3. 배열에 값이 하나만 남게 되고, 값을 비교해보면 **43 == 43**으로 원하는 값을 찾게 되어 탐색 종료
```

이렇게 처음에 N개 크기의 배열에서 단계가 하나씩 지나감에 따라 탐색할 배열의 크기가 반씩 줄어들기 때문에 `N, N/2, N/4, N/8, …, 1`로 실행된다. 즉, **이진 탐색의 시간 복잡도는 O(log N)**이다.

### **연결 리스트 (Linked List)**

연결 리스트는 **각 노드가 데이터와 포인터를 가지고 연결**되어 있는 방식의 자료구조

![image](https://user-images.githubusercontent.com/64416833/218458301-875ec591-8c18-4929-816f-34afae46ce3e.png)


이미지 출처: [https://www.alphacodingskills.com/ds/notes/linked-list.php](https://www.alphacodingskills.com/ds/notes/linked-list.php)

연결 리스트는 포인터로 연결되어 있어서 가리키는 노드만 변경해주면 되기 때문에 삽입과 삭제가 용이

![img_1.png](../image/이진%20탐색%20트리(Binary%20Search%20Tree)부터%20B-Tree%20까지/img_1.png)

이미지 출처:[https://qnaplus.com/insert-element-singly-linked-list/](https://qnaplus.com/insert-element-singly-linked-list/)

그래서 연결 리스트의 **(맨 앞(= head)/뒤(= tail)에)** 데이터를 추가/삭제 하는 경우에
**시간 복잡도가 O(1)**이 소요되게 된다.


> 💡 삽입과 삭제는 특정 노드 다음에 삽입/삭제가 이루어지는 경우가 종종 존재한다.<br/>
이런 경우 원하는 노드에 탐색(접근) + 삽입or삭제 를 해야하기 때문에 
**시간 복잡도가 O(n+1)**이 되게 된다.
>
> ![img_4.png](../image/이진%20탐색%20트리(Binary%20Search%20Tree)부터%20B-Tree%20까지/img_4.png)
> 
> 이미지 출처: [https://qnaplus.com/insert-element-singly-linked-list/](https://qnaplus.com/insert-element-singly-linked-list/)


### **이진 탐색 트리(Binary Search Tree)**


> 📢 Q. 이진 탐색 트리도 이진 탐색을 사용하는 배열처럼 데이터를 똑같이 정렬된 형태로 지니고 있어야 하는거 아닌가?? 어떤 점이 더 좋은거지?
>
> A. `배열`은 아시다시피 **인덱스(index)**를 이용하기 때문에 **검색(조회)에 강점**을 가지는 자료구조이다. 만약, 검색만 한다고 하면 `트리` 구조가 굳이 필요하지 않다. <br/>
> 하지만, **자료의 삽입과 삭제 시**에는 해당 위치의 다음 데이터들을 이동시켜야 하기 때문에 자료의 수가 많아질수록 비효율적이게 되므로 **성능이 떨어지게 된다.**<br/>
반면, `트리` 구조는 데이터의 삽입과 삭제시에 포인터만 변경해주면 되므로 배열에 비해 나은 성능을 보인다.
>
> 그렇다보니, **삽입**/**삭제** 연산이 한번이라도 존재한다면, 이는 삽입이 연속적으로 N번 발생할 수도 있다는 의미이므로 굳이 `배열` 자료구조를 사용할 필요가 없다.

</aside>

- 이진 탐색을 사용하는 배열

 ![img_5.png](../image/이진%20탐색%20트리(Binary%20Search%20Tree)부터%20B-Tree%20까지/img_5.png)
  이미지 출처: [https://velog.io/@crystalhwang16/이진-탐색-binary-search](https://velog.io/@crystalhwang16/%EC%9D%B4%EC%A7%84-%ED%83%90%EC%83%89-binary-search)

- 이진 탐색 트리

![img_6.png](../image/이진%20탐색%20트리(Binary%20Search%20Tree)부터%20B-Tree%20까지/img_6.png)

  이미지 출처: [https://ratsgo.github.io/data structure&algorithm/2017/10/22/bst/](https://ratsgo.github.io/data%20structure&algorithm/2017/10/22/bst/)


이진 탐색 트리는 다음과 같은 형태를 띈 이진 트리

> **이진 트리(Binary Tree)*: 각 노드의 자식 노드가 최대 2개인 트리
>

```
1. 각 노드에 중복되지 않는 키(key)가 있다.
2. 루트 노드의 왼쪽 서브 트리는 해당 노드의 키보다 작은 키를 갖는 노드들로 이루어져 있다.
3. 루트 노드의 오른쪽 서브 트리는 해당 노드의 키보다 큰 키를 갖는 노드들로 이루어져 있다.
4. 좌우 서브 트리 전부는 이진 탐색 트리여야 한다.
```

![img_7.png](../image/이진%20탐색%20트리(Binary%20Search%20Tree)부터%20B-Tree%20까지/img_7.png)

이진 탐색 트리의 탐색/삽입/삭제 시간 복잡도는 트리의 높이를 h(height)라고 했을 때 $O(log(n))$의 시간 복잡도를 갖는다.

![img_8.png](../image/이진%20탐색%20트리(Binary%20Search%20Tree)부터%20B-Tree%20까지/img_8.png)

```
키가 5인 노드를 찾고자 한다면??

찾고자 하는 값: 5
1. 5는 루트 노드의 키 값인 7보다 작으므로 왼쪽 서브 트리로 탐색
2. 5는 3보다 크므로 오른쪽 서브 트리로 탐색
3. 키가 5인 노드를 찾았으므로 탐색 종료
즉 트리 안의 값을 찾는다면 무조건 트리의 높이(h) 이하의 탐색(O(h))이 이루어진다.
(트리 안에 찾고자 하는 값이 없더라도 최대 (O(h))번 만큼만의 탐색이 진행된다.)
```

**하지만.**

아래 우측 그림처럼 **균형이 잡힌 이진 탐색 트리**의 경우 시간 복잡도가 `O(log n)`이 되지만, 왼쪽 그림과 같이 **균형이 잡히지 않은 이진 탐색**(최악의 경우)의 경우 `O(n)`이 되기 때문에 이진 탐색의 장점을 가졌다고 보기 어렵다.

![img_9.png](../image/이진%20탐색%20트리(Binary%20Search%20Tree)부터%20B-Tree%20까지/img_9.png)

그래서 이를 해결하기 위한 여러 자료구조(`AVL 트리`, `레드-블랙 트리`, …)가 생겨나게 되었고 그중 하나가 `B-tree`이다.

### **B-tree (Balanced Tree)**

B-tree는 이진 탐색 트리의 일종으로 탐색 성능을 높이기 위해 **균형있게 높이를 유지하는 트리**이다.

(균형 이진 탐색 트리는 대표적으로 `AVL 트리`, `레드-블랙 트리`같은 것이 존재)

특이한 점은,

1. `이진 트리`가 아니라는 점
2. 규칙에 따라 노드의 `자식 노드 개수는 2개 이상일 수 있다`는 점

![img_10.png](../image/이진%20탐색%20트리(Binary%20Search%20Tree)부터%20B-Tree%20까지/img_10.png)

이미지 출처: [https://rebro.kr/169](https://rebro.kr/169)

**B-Tree의 특징**

> **!!주의!!**
`[ … ]` 은 전부 내림 처리
>


1) 노드에는 2개 이상의 데이터(key)가 들어갈 수 있으며, 항상 정렬된 상태로 저장된다.

![img_11.png](../image/이진%20탐색%20트리(Binary%20Search%20Tree)부터%20B-Tree%20까지/img_11.png)

2) **M차 B트리는 최대 M개의 자식을 가질 수 있는 B 트리**이다.

![img_12.png](../image/이진%20탐색%20트리(Binary%20Search%20Tree)부터%20B-Tree%20까지/img_12.png)

3) 이때, 자식 노드는 `[M/2]개` 부터 `M개` 까지의 자식 노드를 가질 수 있다.

위 그림은 3차 B 트리이기 때문에 1~3개의 자식 노드를 가질 수 있다.

![img_13.png](../image/이진%20탐색%20트리(Binary%20Search%20Tree)부터%20B-Tree%20까지/img_13.png)

4) 특정 노드의 데이터(key)가 K개라면, 자식 노드의 개수는 K+1개여야 한다.

![img_14.png](../image/이진%20탐색%20트리(Binary%20Search%20Tree)부터%20B-Tree%20까지/img_14.png)

5) 특정 노드의 왼쪽 서브 트리는 해당 노드의 key보다 작은 값들로, 오른쪽 서브 트리는 큰 값들로 구성되어 있다.

![img_15.png](../image/이진%20탐색%20트리(Binary%20Search%20Tree)부터%20B-Tree%20까지/img_15.png)

6) 한 노드에는 `floor([M/2]-1)개`부터 `M-1개`의 자식 노드가 포함될 수 있다.

ex) 3차 B 트리 => `0~2` = `1.5 - 1` = `floor(0.5)` = `0`

7) 모든 리프 노드(leaf node)들이 같은 레벨에 존재해야 한다.

> 단말 노드(leaf node) : 자식이 없는 노드<br/>
노드의 레벨(level) : 트리의 특정 깊이를 가지는 노드의 집합
>

![img_16.png](../image/이진%20탐색%20트리(Binary%20Search%20Tree)부터%20B-Tree%20까지/img_16.png)

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

![img_17.png](../image/이진%20탐색%20트리(Binary%20Search%20Tree)부터%20B-Tree%20까지/img_17.png)

**B-Tree의 삽입 과정**

B-Tree는 균형을 유지해야 하기 때문에 key를 삽입하는 경우 트리의 변형이 발생할 수 있다.

**데이터를 삽입하는 과정**은 다음과 같다.

```
1. 빈 트리인 경우 루트 노드를 만들어 K를 추가한다.
루트 노드가 가득찬 경우 노드를 분리하여 리프 노드를 생성한다.

2. K가 들어갈 leaf node를 검색 과정과 동일하게 탐색한다.

3-1. 해당 leaf node에 자리가 남아있다면 정렬을 유지하도록 위치시킨다.
3-2. 해당 leaf node가 꽉 차 있다면 K를 삽입한 후 해당 node를 분할한다.
```

![img_18.png](../image/이진%20탐색%20트리(Binary%20Search%20Tree)부터%20B-Tree%20까지/img_18.png)

`**13`이라는 값을 삽입하는 과정 (노드 분할)**

1. (탐색 과정 생략) K가 들어갈 leaf node를 탐색

![img_19.png](../image/이진%20탐색%20트리(Binary%20Search%20Tree)부터%20B-Tree%20까지/img_19.png)

2. 해당 leaf node에 K값을 삽입했으나 **한 노드가 가질 수 있는 최대 범위**를 벗어났기 때문에 해당 노드를 `분할`해야 한다.

> **B-Tree의 특징**
>
>
> 6) 한 노드에는 `floor((M/2)-1)개`부터 `M-1개`의 데이터를 가질 수 있다.
>
> 3차 B 트리에서 한 노드당 들어갈 수 있는 데이터(key)의 수: `0~2`
> - 최소 `1.5 - 1` = `floor(0.5)` = `0`
> - 최대 `3 - 1` = `M-1` = `2`
>

![img_20.png](../image/이진%20탐색%20트리(Binary%20Search%20Tree)부터%20B-Tree%20까지/img_20.png)


3. leaf node의 중간 값(`13`)이 부모 node와 합쳐지게 되고, 13은 부모 노드인 `11`보다 큰 값이기 때문에 오른쪽에 위치하게 되고, 자식 노드들도 키 값에 따라 각각 분할되게 된다.
    
![img_21.png](../image/이진%20탐색%20트리(Binary%20Search%20Tree)부터%20B-Tree%20까지/img_21.png)
    

4. 부모 노드 또한 key가 꽉 차있으므로 해당 노드를 `분할`해야 한다. 
    
![img_22.png](../image/이진%20탐색%20트리(Binary%20Search%20Tree)부터%20B-Tree%20까지/img_22.png)
    
5. 마찬가지로, 중간 값인 `11` 이 부모 노드와 합쳐지게 된다. 
    
![img_23.png](../image/이진%20탐색%20트리(Binary%20Search%20Tree)부터%20B-Tree%20까지/img_23.png)
    

6. 부모 노드(root node)에서도 역시 key가 꽉차있기 때문에 `분할`이 필요하다.

![img_24.png](../image/이진%20탐색%20트리(Binary%20Search%20Tree)부터%20B-Tree%20까지/img_24.png)


7. 중간 값인 11이 부모 노드로 올라가게 되면서 결과적으로 다음과 같은 형태를 띄게 된다.

![img_25.png](../image/이진%20탐색%20트리(Binary%20Search%20Tree)부터%20B-Tree%20까지/img_25.png)


[참고]

- [링크](https://cjh5414.github.io/binary-search/)
- [링크](https://velog.io/@jewelrykim/Binary-Search-Tree에서-BTree까지Database-Index-추가)
- [링크](https://codingstudyroom.tistory.com/24)
- [링크](https://mommoo.tistory.com/101)