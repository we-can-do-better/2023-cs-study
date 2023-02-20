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

    <aside>
    ❓ **Q. B-Tree는 왜 이진 트리가 아닐까?**
    **이진 트리**는 `각 노드의 최대 차수가 2인 트리`
    반면, **B-Tree**는 `규칙에 따라 특정 노드의 차수가 2개 이상일 수 있기 때문`

  ![이미지 출처: [https://techdifferences.com/difference-between-b-tree-and-binary-tree.html](https://techdifferences.com/difference-between-b-tree-and-binary-tree.html)](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ab29178d-7cee-4d04-9395-4c5b187a6357/Untitled.png)

  이미지 출처: [https://techdifferences.com/difference-between-b-tree-and-binary-tree.html](https://techdifferences.com/difference-between-b-tree-and-binary-tree.html)

  *차수(Degree): 어떤 노드가 가지고 있는 자식 노드의 개수*

    </aside>

- 하나의 노드에 여러 개의 값(키)를 가질 수 있고, 특정 노드의 차수(자식 노드의 수)가 2개 이상일 수 있다.
- 하나의 노드에 `최대 자식 노드의 수가 M개`인 B-Tree를 `M차 B-Tree`

  ![이미지 출처: [https://rebro.kr/169](https://rebro.kr/169)](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/075cc390-0016-4b17-9db8-ccd8bd18f336/Untitled.png)

  이미지 출처: [https://rebro.kr/169](https://rebro.kr/169)

    - 하나의 노드에 2개의 값이 존재 `* 7 * 15 *` , `* 9 * 11 *` , …
    - 하나의 노드에 최대 자식의 개수가 3이므로 위 이미지의 B-Tree는 `3차 B-Tree`
    - M차 B-Tree는 하나의 노드가 가질 수 있는 최대의 키 개수는 `M-1개`이다.(`최대 키의 수 = 2`)

    <aside>
    ❓ * 는 포인터를 의미

    </aside>


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

   ![이미지 출처: [https://escapefromcoding.tistory.com/731](https://escapefromcoding.tistory.com/731)](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c6821b98-efaa-4cee-89a7-1a65dcb60c00/Untitled.png)

   이미지 출처: [https://escapefromcoding.tistory.com/731](https://escapefromcoding.tistory.com/731)


<aside>
❓ **Q. B-Tree에서는 중복된 키 값을 허용하지 않는다…… 라는 글을 많이 보게 되는데 만약 중복값이 들어간다면?
>> 중복 데이터가 B-Tree에 삽입될 때, 알고리즘의 특정 구현에 따라 달라진다.

1.** 중복된 값을 가진 키를 삽입할 때, 비중복 키와 마찬가지로 키가 삽입되어야 하는 적절한 리프 노드를 검색
2. 리프 노드에 동일한 값을 가진 키가 이미 포함되어 있으면 B-Tree는 기존 키를 **새 키로 덮어쓰거나 기존 노드에 연결된 별도의 노드에 새 키를 삽입**

(기존 키를 덮어쓸지 아니면 별도의 노드에 새 키를 삽입할지에 대한 선택은 B-Tree의 구현과 애플리케이션의 특정 요구 사항에 따라 달라진다. 따라서, 경우에 따라 중복 키를 허용하는 것이 바람직할 수 있지만 다른 경우에는 고유 키를 적용해야 할 수도 있다.)

</aside>

<aside>
📢 질문 1. `B-Tree는 어디에 쓰이는지??`
**데이터베이스와 파일 시스템에서 데이터를 효율적으로 검색하고 검색하기 위한 인덱싱 데이터 구조로 일반적으로 사용된다. 
= 이것이 가능한 이유가 B-Tree는 Self-Balanced Tree이기 때문

1. 데이터베이스에서 인덱스에 사용**
   Oracle, MySQL, Postgre를 포함한 거의 모든 관계형 데이터베이스에서 사용

**2. 파일 시스템에서 파일 메타데이터(파일 이름, 크기 및 디스크의 위치 등)를 저장하고 관리하는데 사용**
Mac OS X에서 사용되는 HFS+ 파일 시스템과 윈도우에서 사용되는 NTFS 파일 시스템과 같은 곳에서 사용

**정리**
일반적으로 데이터의 빠르고 효율적인 검색 및 검색이 필요한 애플리케이션, 특히 **대량의 데이터를 처리하는** 애플리케이션에서 사용

</aside>

### 인덱스

- 인덱스란 **추가적인 쓰기 작업**과 **저장 공간을 활용**하여 **데이터베이스 테이블의 검색 속도를 향상**시키기 위한 자료구조
    - **추가적인 쓰기 작업? 저장 공간을 활용?**
    - >> 특정 컬럼에 인덱스를 생성하면, 해당 컬럼의 데이터들을 정렬하여 별도의 메모리 공간에 데이터의 물리적 주소와 함께 저장

      ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/2c555410-8e3b-42f8-adb3-1b5a91935f12/Untitled.png)

    - ex) 책의 “찾아보기”
        - 찾아보기 = 인덱스
        - 찾아보기를 통해 알아낼 수 있는 페이지 번호 = 데이터 파일에 저장된 레코드의 주소
        - 책 내용 = 데이터 파일

        <aside>
        💡 **Q. 만약 특정 키워드를 찾고 싶은데 찾아보기가 없다면??**
        - 책의 첫 페이지부터 시작해서, 원하는 키워드가 나올 때까지 모든 페이지를 일일이 찾아야 한다. (full scan)
        - 책의 분량이 적거나 찾고자 하는 내용이 책의 전반부에 위치한다면, 검색에 소요되는 시간이 비교적 짧을 수 있지만, 책의 분량이 많거나 찾고자 하는 내용이 책의 후반부에 위치한다면 검색에 오랜 시간이 걸리게 된다.

        </aside>

      ![Real MySQL 8.0 찾아보기](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3337347a-1b58-47e4-9e79-fc7182ff6375/Untitled.png)

      Real MySQL 8.0 찾아보기


    - INSERT, UPDATE, DELETE의 성능을 희생하고, SELECT의 성능을 향상
        - 주의할 점이 INSERT, UPDATE, DELETE의 동작이 느린 것이지 해당 데이터를 먼저 조회하는 과정은 빠르게 조회된다.
            - INSERT : 새로운 데이터에 대한 인덱스가 추가
            - DELETE : 삭제하는 데이터의 인덱스를 제거
            - UPDATE : 기존의 인덱스를 제거하고, 갱신된 데이터에 대해 인덱스를 추가
- Index는 테이블 내 1개의 컬럼, 혹은 여러 개의 컬럼을 지정하여 생성할 수 있다.

- 인덱스 생성

  [MySQL :: MySQL 8.0 Reference Manual :: 13.1.15 CREATE INDEX Statement](https://dev.mysql.com/doc/refman/8.0/en/create-index.html)

    ```sql
    CREATE [UNIQUE | FULLTEXT | SPATIAL] INDEX index_name
        [index_type]
        ON tbl_name (key_part,...)
        [index_option]
        [algorithm_option | lock_option] ...
    
    key_part: {col_name [(length)] | (expr)} [ASC | DESC]
    
    index_option: {
        KEY_BLOCK_SIZE [=] value
      | index_type
      | WITH PARSER parser_name
      | COMMENT 'string'
      | {VISIBLE | INVISIBLE}
      | ENGINE_ATTRIBUTE [=] 'string'
      | SECONDARY_ENGINE_ATTRIBUTE [=] 'string'
    }
    
    index_type:
        USING {BTREE | HASH}
    
    algorithm_option:
        ALGORITHM [=] {DEFAULT | INPLACE | COPY}
    
    lock_option:
        LOCK [=] {DEFAULT | NONE | SHARED | EXCLUSIVE}
    ```

    ```sql
    // 형식
    CREATE INDEX {INDEX_NAME} ON {TABLE}({COLUMN});
    
    // 예시
    CREATE INDEX USER_COMPANY_INDEX ON USER(COMPANY_ID);
    ```


<aside>
💡 **Q. 인덱스 컬럼으로 설정한 컬럼은 무조건 인덱스를 사용할까??**
X. 데이터베이스 옵티마이저가 판단하여 특정 쿼리에 인덱스를 사용 여부를 결정한다.

``

</aside>

```sql
-- 인덱스 생성
mysql> CREATE INDEX EMPOYEES_INDEX ON employees(last_name);
Query OK, 0 rows affected (1.32 sec)
Records: 0  Duplicates: 0  Warnings: 0

-- 인덱스 조회
mysql> SHOW INDEX FROM employees;
+-----------+------------+----------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
| Table     | Non_unique | Key_name       | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment | Visible | Expression |
+-----------+------------+----------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
| employees |          0 | PRIMARY        |            1 | emp_no      | A         |      299290 |     NULL |   NULL |      | BTREE      |         |               | YES     | NULL       |
| employees |          1 | EMPOYEES_INDEX |            1 | last_name   | A         |        1602 |     NULL |   NULL |      | BTREE      |         |               | YES     | NULL       |
+-----------+------------+----------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
2 rows in set (0.01 sec)

-- 테이블에 저장된 데이터 조회
mysql> SELECT * FROM employees LIMIT 0, 10;
+--------+------------+------------+-----------+--------+------------+
| emp_no | birth_date | first_name | last_name | gender | hire_date  |
+--------+------------+------------+-----------+--------+------------+
|  10001 | 1953-09-02 | Georgi     | Facello   | M      | 1986-06-26 |
|  10002 | 1964-06-02 | Bezalel    | Simmel    | F      | 1985-11-21 |
|  10003 | 1959-12-03 | Parto      | Bamford   | M      | 1986-08-28 |
|  10004 | 1954-05-01 | Chirstian  | Koblick   | M      | 1986-12-01 |
|  10005 | 1955-01-21 | Kyoichi    | Maliniak  | M      | 1989-09-12 |
|  10006 | 1953-04-20 | Anneke     | Preusig   | F      | 1989-06-02 |
|  10007 | 1957-05-23 | Tzvetan    | Zielinski | F      | 1989-02-10 |
|  10008 | 1958-02-19 | Saniya     | Kalloufi  | M      | 1994-09-15 |
|  10009 | 1952-04-19 | Sumant     | Peac      | F      | 1985-02-18 |
|  10010 | 1963-06-01 | Duangkaew  | Piveteau  | F      | 1989-08-24 |
+--------+------------+------------+-----------+--------+------------+
10 rows in set (0.00 sec)

```

<aside>
💡 `인덱스`는 데이터베이스 테이블의 **검색 속도를 향상시키기 위한 자료구조**
이것이 가능한 이유는 **데이터들이 항상 정렬이 되어 있기 때문**이다.

**인덱스의 데이터 저장 방식(알고리즘)**은 일반적으로 `B-Tree 알고리즘`을 사용한다.

> **MySQL 5.5 이상(8 포함)부터 기본 스토리지 엔진이 `InnoDB`로 설정되어 있음*
**InnoDB(MySQL)에서 `페이지`는 디스크에 데이터를 저장하는 기본 단위로 **B-Tree의 하나의 노드**를 의미함.*
- 검색 키워드: 클러스터링 인덱스, 논-클러스터링 인덱스(세컨더리 인덱스)
>
>
> ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4a3f7619-6e54-4dd8-86d6-38169e3ef253/Untitled.png)
>

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e4761609-1d63-4574-bcbf-537bb846e27f/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/af60f4c3-cdb5-4dd2-bc60-c2101c4c6c70/Untitled.png)

![이미지 출처: [https://velog.io/@evelyn82ny/B-Tree-index-feat-difference-from-B-plus-Tree](https://velog.io/@evelyn82ny/B-Tree-index-feat-difference-from-B-plus-Tree)](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/2c4a34bc-016f-4ac8-b5c7-4c640d22a8c2/Untitled.png)

이미지 출처: [https://velog.io/@evelyn82ny/B-Tree-index-feat-difference-from-B-plus-Tree](https://velog.io/@evelyn82ny/B-Tree-index-feat-difference-from-B-plus-Tree)

- MyISAM(마이아이삼)은 물리적인 주소를 가지고 있음
- InnoDB는 프라이머리 키를 주소처럼 사용하기 때문에 논리적인 주소를 가지고 있음
    - 이러한 이유로 MyISAM과 달리 데이터 파일에 바로 찾아가지 못하기 때문에 프라이머리 키 값으로 프라이머리 키 인덱스에 한 번 더 검색한 후, 리프 노드에 저장되어 있는 레코드를 읽는다.
    - 즉, InnoDB 스토리지 엔진에서는 데이터 레코드를 읽기 위해 프라이머리 키 인덱스 테이블(B-Tree 구조)에서 한번 더 검색해야 한다.
</aside>

<aside>
📢 질문 2. `인덱스는 왜 B-Tree를 도입했는지??`
사실 B-Tree로 설명했지만, 더 정확히는 `B+Tree`로 구현되어 있다.

</aside>

### B+Tree

- **B-Tree**

![이미지 출처: [https://rebro.kr/167](https://rebro.kr/167)](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/2e58ba7a-45ab-44e6-8a44-5f683ac82e76/Untitled.png)

이미지 출처: [https://rebro.kr/167](https://rebro.kr/167)

- 기존의 B-Tree는 어느 한 데이터의 검색은 효율적이지만, 모든 데이터를 한 번 순회하는 데에는 트리의 모든 노드를 방문해야 하므로 비효율적이다.
    - 이 말을 예시를 들어본다면, 데이터베이스에 범위 검색할 때 특정 노드를 기준으로

- **B+Tree**

B-Tree의 단점을 개선시킨 자료구조

![이미지 출처: [https://rebro.kr/167](https://rebro.kr/167)](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1c056b8a-9d15-420d-9e54-cf674f0f0568/Untitled.png)

이미지 출처: [https://rebro.kr/167](https://rebro.kr/167)

- B-Tree는 모든 노드들이 데이터를 가지고 있는 반면, **B+tree는 리프 노드만이 데이터를 저장하고 있다.** 따라서, **리프 노드를 제외한 나머지 노드들은 키와 포인터만 포함되어 실제 데이터를 저장하는 것에 비해 훨씬 적은 메모리를 차지하게 된다.**
- 전체


- 인덱스 대상 컬럼 기준
    - 일반적으로 카디널리티(Cardinality)가 높은 컬럼을 우선적으로 인덱싱하는 것이 검색 성능에 유리
    - 그 이유는, 카디널리티가 높을 수록 인덱스를 통해 데이터를 더 많이 필터링 할 수 있기 때문

        <aside>
        💡 **카디널리티(Cardinality)**: 특정 데이터 집합의 고유한 값의 개수
        - 성별(남, 여): 2가지의 값만 존재하는 성별 컬럼은 **중복도가 높으며, 카디널리티가 낮음
        -** 주민번호(고유한 값): 개인마다 유니크(Unique)한 값을 가지므로 **중복도가 낮으며, 카디널리티가 높음**

        </aside>


- 찾아볼만한 키워드
    - 인덱스 스캔 종류
    - 클러스터링 인덱스, 논-클러스터링 인덱스(세컨더리 인덱스)
    - 데이터 저장 방식(Hash Table, B-Tree, B+Tree, …)
    - Index 대상 컬럼 선정
    - 복합 인덱스

**참고**

- [링크](https://velog.io/@main_door/이진-트리와-이진-탐색-트리)
- [링크](https://velog.io/@evelyn82ny/B-Tree-index-feat-difference-from-B-plus-Tree)
- [링크](https://rebro.kr/169)
- [링크](https://tecoble.techcourse.co.kr/post/2021-09-18-db-index/)