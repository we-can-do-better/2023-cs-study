##### Stream

> Stream : Java 8 부터 추가된 기능으로, 컬렉션 요소들을 함수형으로 처리할 수 있는 기능을 제공한다. Stream 은 여러 요소들을 람다식이나 메서드 참조등의 함수형 인터페이스를 이용해 한줄로 처리 할수 있도록 함으로써 코드의 가독성과 유지보수성이 증가하고 병렬 처리에도 유리한 특징이 있다.

```람다식 : 함수를 하나의 식으로 표현한 것 ```
+ 함수를 람다식으로 표현하면 메소드의 이름이 필요 없기 때문에 람다식은 '익명함수'의 한 종류이다.
+ 익명 함수란 함수의 이름이 없는 함수로, 모두 ```일급 객체``` 이다.</br>
  ```일급 객체(First-class object) : 밑의 조건을 만족하는 객체  ```
  1. 변수나 데이터 구조안에 담을 수 있어야 한다.
  2. 인자(parameter)로 전달할 수 있어야 한다.
  3. 반환값으로 사용할 수 있어야 한다.

```
    // 기존
    public String hello(){
        return "Hello!";
    }
    
    // 람다
    () -> "Hello!"; 
 ```

+ 람다식의 특징
  + 람다식 내에서 사용되는 지역변수는 final 이 붙지 않아도 상수로 간주된다.
  + 람다식으로 선언된 변수명은 다른 변수명과 중복될 수 없다.
+ 람다식의 장점
  + 코드를 간결하게 만들 수 있다.
  + 개발자의 의도가 명확히 드러나 가독성이 높아진다.
  + 함수를 만드는 과정없이 한번에 처리 할 수 있어 생산성이 높아진다.
  + 병렬 프로그래밍이 용이하다.
+ 람다식의 단점
  + 람다식을 사용하면서 만든 무명함수는 재사용이 불가능하다.
  + 디버깅이 어렵다.
  + 람다식을 남발하면 비슷한 함수가 중복 생성되어 코드가 지저분해질 수 있다.

##### Stream API 의 특징
1. 내부 반복(Internal iteration) : Stream API 는 요소를 처리하기 위해 반복문을 사용하는 것이 아니라 내부적으로 처리한다. 이를 내부반복이라 하며, 이렇게 하면 병렬 처리가 더 쉬워지며 코드의 가독성도 향상된다.
2. 지연 평가(Lazy evaluation) : Stream API 에서 처리작업은 요청할 때까지 실제로 수행되지 않는다. 이러한 방식으로 작업을 수행하면 필요한 데이터만 처리하므로 메모리 사용이 최소화 된다.
3. 파이프라인(Pipeline) : Stream API 는 작업을 파이프라인으로 구성하여 처리한다. 각 작업은 이전 작업에서 생성된 스트림을 입력으로 사용하고, 새로운 스트림을 출력으로 생성한다. 이를 통해 데이터 처리과정을 쉽게 이해할 수 있다.
4. 병렬 처리(Parallel processing) : Stream API 는 병렬 처리를 지원한다. 처리속도를 높일 수 있다.
5. 스트림 연산(Stream operations) : Stream API 는 많은 종류의 연산을 제공한다. 제공되는 연산들을 사용하면 데이터 처리 작업을 쉽게 수행할 수 있다.
6. 원본의 데이터를 변경하지 않는다.
7. Stream 은 일회용이다.
> 이러한 특징들을 통해, 코드의 가독성, 유지보수성, 처리 속도 등을 향상 시켜준다.

##### 1. 내부반복</br>
```
    List<String> names = Arrays.asList("Tom","John","Jane","Peter","David");
    
    // forEach 메소드를 사용하여 모든 이름을 출력한다.
    names.stream().forEach(name -> System.out.print(name));
    
    // for문 사용시
    for(String name: names){
        System.out.print(name); 
    }
```
> Stream을 이용하지 않는다면 개발자는 별도로 반복문을 작성해야하며, 병렬 처리를 위해서는 개발자가 별도로 스레드를 생성하거나 동기화를 처리해야 하므로 코드의 복잡도가 증가 할 수 있다.
</br>
##### 2. 지연 평가 </br>
```
    List<Integer> numbers = Arrays.asList(1,2,3,4,5,6,7,8,9,10);
    
    // filter를 사용하여 짝수만 걸러내기
    Stream<Integer> stream = numbers.stream().filter(n->n%2 == 0);
    
    // map을 사용하여 각 숫자를 제곱
    stream = stream.map(n-> n*n);
    
    // reduce를 사용하여 모든 숫자의 합을 구한다. 
    int sum = stream.reduce(0, Integer::sum);
    System.out.print(sum);
```

##### 3. 파이프라인 </br>
```
    List<String> words = Arrays.asList("coke","sprite","pepsi","redbull","fanta");
    
    List<String> result = words.stream()
            .filter(s-> s.length() >= 5)    // 길이가 5이상인 문자열만 필터링
            .map(String::toUpperCase)       // 문자열 대문자로 변환
            .sorted()                       // 오름차 순으로 정렬
            .collect(Collectors.toList());  // 결과를 리스트로 수집
    
    System.out.println(result);             // [SPRITE,PEPSI,REDBULL,FANTA] 
```
> 이렇게 파이프라인을 이용하여 요소를 처리하면 코드를 간결하게 유지하며, 가독성도 높이는 효과를 볼 수 있다.

```
    List<String> words = Arrays.asList("coke","sprite","pepsi","redbull","fanta");
    
    List<String> filterWords = new ArrayList<>();
    
    for(String word: words){
        if(word.length() >= 5)
            filterWords.add(word);
    }
    
    List<String> upperCaseWords = new ArrayList<>();
    for(String filterWord : filterWords){
        upperCaseWords.add(filterWord.toUpperCase());
    }
   
    Collections.sort(upperCaseWords);
    
    System.out.println(upperCaseWords);
```
##### 4. 병렬처리 </br>
```
    List<Integer> numbers = Arrays.asList(1,2,3,4,5,6,7,8,9,10);
    
    // parallelStream을 사용하여 모든 숫자를 제곱한다.
    int sum = numbers.parallelStream().map(n -> n*n).reduce(0, Integer::sum);
    System.out.println(sum);
```
##### 5. 스트림 연산 </br>
##### 6. 원본의 데이터를 변경하지 않는다.</br>
```
    String[] arr = {"Java", "C++", "Python", "JavaScript", "Scala"};
    Stream<String> stream = Arrays.stream(arr);
    
    List<String> list = stream.sorted().collect(Collectors.toList());
    System.out.println(list);       // [C++, Java, JavaScript, Python, Scala]
```
> Stream API 는 원본의 데이터를 조회하여 원본의 데이터가 아닌 별도의 요소들로 Stream 을 생성한다. 그렇기 때문에 원본의 데이터로부터 읽기만 할뿐, 정렬이나 필터링등의 작업은 별도의 Stream 요소들에서 처리된다.
</br>
##### 7. Stream 은 일회용이다.
```
   stream.forEach(System.out::println);      //  Java, C++, Python, JavaScript, Scala
   
    List<String> list = stream.sorted().collect(Collectors.toList());
    // Exception in thread "main" java.lang.IllegalStateException: stream has already been operated upon or closed
```
> Stream API 는 일회용이기 때문에 한번 사용이 끝나면 재사용이 불가능하다. Stream 이 또 필요한 경우에는 Stream 을 다시 생성해 주어야 한다. 닫힌 Stream 을 다시 사용하게 된다면 IllegalStateException 이 발생한다.

##### Stream API 의 3가지 단계
1. 생성하기
  + Stream 객체를 생성하는 단계
2. 가공하기
  + 원본의 데이터를 별도의 데이터로 가공하기 위한 중간 연산
  + 연산 결과를 Stream 으로 다시 반환하기 때문에 연속해서 중간 연산을 이어갈 수 있다.
3. 결과 만들기
  + 가공된 데이터로부터 원하는 결과를 만들기 위한 최종 연산
  + Stream 의 요소들을 소모하면서 연산이 수행되기 때문에 한번만 처리 가능하다.

#### 생성하기
+ Stream of Array
  + ``` Stream<String> stream = Stream.of("Java", "C++", "Python", "JavaScript", "Scala"); ```
    </br>
+ Stream of ArrayList
  + ```
     ArrayList<String> arr = new ArrayList<>(Arrays.asList("Java", "C++", "Python", "JavaScript", "Scala"))
     Stream<String> streamOfArrayList = arr.stream(); 
    ```
+ Stream of Collection
  + ```
      Collection<String> collection = Arrays.asList("Java", "C++", "Python", "JavaScript", "Scala");
      Stream<String> streamOfCollection = collection.stream();
    ```
+ Empty Stream
  + ```
      Stream<String> empty = Stream.empty();
    ```
+ Stream.builder
  + ```
      Stream<String> builder = Stream.<String>builder().add("Java").add("C++").add("Python").build();
    ```
+ Stream.generate
  + ```
      Stream<String> generate = Stream.generate(() -> "element").limit(10); 
    ``` 
+ Stream.iterate
  + ``` 
      Stream<Integer> iterate = Stream.iterate(40, n-> n+2).limit(20);
    ```
+ Stream of Primitives
  + ``` 
      IntStream intStream = IntStream.range(1,3);   // [1,2]
      LongStream longStream = LongStream.rangeClosed(1,3);   // [1,2,3]
    ```
  + ```
      Random random = new Random();
      DoubleStream doubleStream = random.doubles(3); 
    ```
+ Stream of String
  + ```
      Stream<String> stringStream = Pattern.complie(",").splitAsStream("A,B,C"); // [A, B, C]
    ``` 
+ Stream of File
  + ```
      Path filepath = Paths.get("C:\\file.txt");
      Stream<String> streamofStrings = Files.lines(filepath);
    ```
+ Stream connect
  + ```
      Stream<String> stream1 = Stream.of("A","B","C");
      Stream<String> stream2 = Stream.of("D","E","F");
      Stream<String> concat = Stream.concat(stream1,stream2);         // [A,B,C,D,E,F] 
    ``` 
+ Stream Parallel
  + ```
      String[] arr = {"Java", "C++", "Python", "JavaScript", "Scala"};
      Stream<String> stream = Arrays.stream(arr);
      stream.parallel().forEach(System.out::println);            // Python Scala JavaScript C++ Java
    ```

#### 가공하기
1. Filtering
  + filter
    + ```
        Stream<T> filter(Predicate<? superT> predicate);
        
        List<String> list = Arrays.asList("A","B","C","D");
        Stream<String> stream = list.stream()
                                    .filter(s-> s.equals("A"));  // [A]
       ```
  + limit
    + ```
        Stream<T> limit(long maxSize);
        
        Stream<String> stream = Stream.generate(()-> "element").limit(2);   // [element,element]  
       ```
  + skip
    + ```
        Stream<T> skip(long n);
       
        List<Integer> list = Arrays.asList(3,7,3,1,2,6,1,9,0);
        Stream<Integer> stream = list.stream()
                                     .sorted(Comparator.reverseOrder())
                                     .skip(4);                          // [3,2,1,1,0]
       ```
  + distinct
    + ```
        Stream<T> distinct();
       
        List<String> list = Arrays.asList("A","A","B","B","C","D"); 
        Stream<String> stream = list.stream()
                                    .distinct();                       // [A,B,C,D]
       ```
2. Mapping
  + map
    + ```
        <R> Stream<R> map(Function<? super T, ? extends R> mapper);
        
        List<String> list = Arrays.asList("A","B","C","D");
        Stream<String> stream = list.stream()
                                    .map(String::toLowerCase);      // [a,b,c,d] 
       ```
  + mapToInt, mapToLong, mapToDouble
    + ```
         IntStream mapToInt(ToIntFunction<? super T> mapper);
         LongStream mapToLong(ToLongFunction<? super T> mapper);
         DoubleStream mapToDouble(ToDoubleFunction<? super T> mapper);
       
         List<String> list = Arrays.asList("1","2","3","4");
         IntStream stream = list.stream()
                                .mapToInt(s-> Integer.parseInt(s));  // [1,2,3,4]
       ```
  + flatMap, flatMapToInt, flatMapToLong, flatMapToDouble
    + ```
         <R> Stream<R> flatMap(Function<? super T, ? extends Stream<? extends R>> mapper);
         
         List<List<String>> list = Arrays.asList(Arrays.asList("a"), Arrays.asList("b")); // [[a],[b]]
         List<String> flatMap = list.stream()
                                    .flatMap(Collection::stream)
                                    .collect(Collectors.toList());        // [a,b]
       ```
3. Sorting
  + sorted
    + ```
         Stream<T> sorted(Comparator<? super T> comparator);
       
         List<Integer> list = Arrays.asList(3,2,1,6,8,9,2,6,7);
         Stream<Integer> stream = list.stream()
                                      .sorted();               // [1,2,2,3,6,6,7,8,9]
       ```
4. Iterating
  + peek
    + ```
         Stream<T> peek(Consumer<? super T> action);
         
         List<Integer> list = Arrays.asList(5,7,3,1,2,6,1,9,0);
         Stream<Integer> stream = list.stream()
                                      .peek(System.out::println)
                                      .sorted(Comparator.reverseOrder());
       ```

#### 결과 만들기
1. Reduction
  + Stream은 reduce() 메서드를 이용해 결과를 만드는데 이 메서드는 3가지의 파라미터를 가지고 있다.
    + accumulator : 각 요소를 처리하는 계산 로직
    + identity    : 계산을 위한 초기 값으로 Stream이 비어서 계산할 내용이 없더라도 리턴한다.
    + combiner    : 병렬 stream 처리할 때 나눠서 계산한 결과를 하나로 합치는 로직
  + ```
       Optional<T> reduce(BinaryOperator<T> accumulator);
       T reduce(T identity, BinaryOperator<T> accumulator);
       <U> U reduce(U identity,
                 BiFunction<U, ? super T, U> accumulator,
                 BinaryOperator<U> combiner); 
     ``` 
  + ```
      OptionalInt reduce = IntStream.range(1,10)
                                    .reduce((a,b)-> {return a+b});   // 45
     
      Integer reduce = IntStream.range(1,10)
                                .reduce(10, Integer::sum);          // 55
      
      Integer reduce = Stream.of(1,2,3)
                             .parallel()
                             .reduce(10, Integer::sum, (a,b)-> {return a+b;});   //36
     ```  
2. Collecting
  + ```
       <R> R collect(Supplier<R> supplier,
                  BiConsumer<R, ? super T> accumulator,
                  BiConsumer<R, R> combiner);
       <R, A> R collect(Collector<? super T, A, R> collector);
     ```
  + ```
       List<String> list = Stream.of("a","b","c").filter(element-> element.contains("b"))
                                                 .collect(Collectors.toList());
     ```  
3. Calculating
  + ```
       Optional<T> min(Comparator<? super T> comparator);
       Optional<T> max(Comparator<? super T> comparator);
       long count();
     ```
4. Matching
  + ```
       boolean anyMatch(Predicate<? super T> predicate);
       boolean allMatch(Predicate<? super T> predicate);
       boolean noneMatch(Predicate<? super T> predicate); 
     ```
  + ```
      List<String> list = Arrays.asList("A","B","C","D");
      
      boolean anyMatch = list.stream()
                             .anyMatch(s->s.contains("A"));   // true
      boolean allMatch = list.stream()
                             .allMatch(s->s.contains("A"));   // false
      boolean noneMatch = list.stream()
                              .noneMatch(s->s.contains("A")); // false  
     ```
5. Iterating
  + forEach()
    + ```
         void forEach(Consumer<? super T> action);
       
         List<Integer> list = Arrays.asList(1,2,3);
         list.stream().forEach(System.out::println);    // 1 2 3 
        ```
  + forEachOrdered()
    + ```
        void forEachOrdered(Consumer<? super T> action);
        
        Instream.range(1,10).parallel().forEach(System.out::print);    // 852361479
        Instream.range(1,10).parallel().forEachOrdered(System.out::print); // 123456789
       ```

#### Stream API 의 실행순서
```
    Stream.of("a","b","c","d","e")
          .filter(s->{
            System.out.println("filter: " + s);
            return true;
          }) 
          .forEach(s-> System.out.println("forEach: " + s));
     // filter : a
     // forEach : a     
     // filter : b
     // forEach : b     
     // filter : c
     // forEach : c     
     // filter : d
     // forEach : d     
     // filter : e
     // forEach : e        
```
> 모든 데이터에 대한 filter 가 진행 되고 forEach가 실행되는 수평적 구조로 순회하는 것이 아닌 각각의 filter와 forEach가 먼저 수행하는 수직적 구조로 순회

왜? 수직적으로 순회할까
```
    Stream.of("a", "b", "c", "d", "e")
        .map(s -> {
            System.out.println("map: " + s);
            return s.toUpperCase();
        })
        .anyMatch(s -> {
            System.out.println("anyMatch: " + s);
            return s.startsWith("A");
        });
        
    // map      : a
    // anyMatch : A    
```

실행 순서를 고려해야하는 이유?
```
    Stream.of("a", "b", "c", "d", "e")
        .map(s -> {
            System.out.println("map: " + s);
            return s.toUpperCase();
        })
        .filter(s -> {
            System.out.println("filter: " + s);
            return s.startsWith("A");
        })
        .forEach(s -> System.out.println("forEach: " + s));
   // map       : a
   // filter    : A
   // forEach   : A
   // map       : b
   // filter    : B
   // map       : c
   // filter    : C
   // map       : d
   // filter    : D
   // map       : e
   // filter    : E      
```

```
    Stream.of("a", "b", "c", "d", "e")
        .filter(s -> {
            System.out.println("filter: " + s);
            return s.startsWith("a");
        })
        .map(s -> {
            System.out.println("map: " + s);
            return s.toUpperCase();
        })
        .forEach(s -> System.out.println("forEach: " + s));
        
    // filter   : a
    // map      : a
    // forEach  : A
    // filter   : b
    // filter   : c
    // filter   : d
    // filter   : e    
```

#### 장점
1. 가독성
2. 유지보수성
3. 성능향상
4. 메모리 사용량 감소

#### 단점
1. 병렬 처리의 부작용
2. 재사용성
3. 성능이슈