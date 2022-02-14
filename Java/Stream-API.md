
먼저 Java 는 객체지향 언어이므로 기본적으로 함수형 프로그래밍이 불가능했었습니다.   
하지만 Java8부터 Stream API, lambda, 함수형 인터페이스 등을 지원하면서 Java에서도 함수형 프로그래밍을 할 수 있게 되었습니다. 그 중 Stream API에 대해 알아보려고 합니다.

## Stream이란?

함수형 인터페이스를 적용하여 컬렉션, 배열과 같은 저장요소를 반복적으로 순환하며 가공,처리를 해주는 기능입니다. 이러한 Stream API를 사용한다면 불필요한 for문과 그 안에서 이루어지는 if문 등의 분기처리를 쓰지않고 깔끔하고 직관적인 코드로 변형할 수 있습니다.

### Stream API의 특징

+ Stream은 Immutable하여 원본 데이터를 변경하지 않습니다.
+ lazy연산을 통해서 효율적인 처리가 가능합니다.
+ 일회성으로 재사용이 불가능합니다.
+ 내부 반복 작업을 통해 반복문을 메소드의 내부에 숨길 수 있습니다.

### Stream API의 연산 종류

1. **생성하기**

+ Stream객체를 생성하는 단계
+ Stream의 특성상 재사용이 불가능하므로, 닫히면 다시 생성해주어야 한다.

2. **가공하기(중개연산)**

+ 원본의 데이터를 별도의 데이터로 가공하기 위한 중간 연산
+ 연산 결과를 Stream으로 다시 반환하기 때문에 연속해서 중간 연산을 이어갈 수 있다.

3. **결과 만들기(최종연산)**

+ 가공된 데이터로부터 원하는 결과를 만들기 위한 최종 연산
+ Stream의 요소들을 소모하면서 연산이 수행되기 때문에 한번만 처리 가능하다.


### Stream API 연산 예시

1. **생성하기**

먼저 생성하기에 대한 예시로 Collection은 내부에 stream() 메소드를 통하여 stream을 생성 할 수 있습니다. 배열은 Arrays.stream()을 사용하여 만들 수 있고 Stream.of() 정적 팩토리 메소드를 통해서도 생성 할 수 있다.

또 기타 생성하는 코드는 아래 코드에서 확인 가능합니다.

```java
//Collection -> Stream
List<Integer> list = new ArrayList<>();
list.stream(); // Stream<Integer>

//Array -> Stream
int[] array = new int[2];
Arrays.stream(array); // IntStream
Stream.of(1, 2, 3, 4, 5);s // Stream<Integer>

//특정 범위의 정수를 요소로 갖는 Stream
IntStream.range(1, 5); // 1,2,3,4
IntStream.rangeClosed(1, 5) // 1,2,3,4,5 

//난수를 요소로 갖는 Stream
new Random().ints(); //무한 스트림
new Random().ints(5); // 크기가 5인 난수 스트림 (유한)
```


여기서 Stream<Integer>과 IntStream의 차이를 알아두어야 합니다.

IntStream은 기본형 Stream으로 LongStream DoubleStream이 있고 오토 박싱/언박싱의 비효율이 제거되어있고 숫자와 관련된 유용한 메소드를 Stream<T>보다 많이 제공합니다.


2. **가공하기(중개연산)**

가공하는 중개연산에는 filter, map, sorted, distinct, mapToInt, mapToDouble 등이 있습니다.

```java
  // filter
  // list = [-2, -1, 0, 1, 2]
  list.stream()
  		.filter(item -> item > 0); // [1, 2]
  
  //map
  //list = ["a", "b", "c", "d"]
  list.stream()
  	   .map(item -> item.toUpperCase()); // ["A", "B", "C", "D"]
  
  //sorted
  //list = [3, 5, 1, 2, 4]
  list.stream()
  	   .sorted(); // [1, 2, 3, 4, 5]
  list.stream()
  	   .sorted(Comparator.reverseOrder());// [5, 4, 3, 2, 1]
  
  //distinct
  //list = [1, 1, 2, 2, 3]
  list.stream()
  	   .distinct(); // [1, 2, 3]
  
  //mapToObj
  IntStream.range(1,4)
  			.mapToObj(item -> "a" + item); // ["a1", "a2", "a3"]
  
  //mapToInt
  //list = [1, 2, 3];
  list.stream().mapToInt(i -> i) // IntStream
```

외에도 다른 가공연산들도 찾아보면 좋을것같습니다.


3. **결과 만들기(최종연산)**

두번째 중간연산에서는 Stream을 반환하여 체이닝되어 연산을 계속 이어나갈수 있지만 최종연산에서는 반환되는 값이 Stream이 아닙니다.

최댓값/최솟값/총합/평균/갯수 등 과 같은 연산과 연산된 Stream을 컬렉션이나 다른 종류의 결과로 수집할 수 있으며 join을 수행하여 String으로 반환할 수 있습니다.

```java
  //min, max는 Stream이 비어있을 경우를 값을 특정할 수 없어 Optional로 반환됩니다.
  OptionalInt max = IntStream.of(1, 2, 3).max(); // 3
  OptionalInt min = IntStream.of(1, 2, 3).min(); // 1
  OptionalDouble avarge = IntStream.of(1, 2, 3).average(); // 2.0
  
  //Stream이 비어있는 경우 0을 리턴한다.
  long count = IntStream.of(1, 2, 3).count(); // 3
  long sum = IntStream.of(1, 2, 3).sum(); // 6
  
  //collect(stream to list)
  //Product(id, name)
  List<String> nameList = productList.stream()
  									.map(Product::getName)
  									.collect(Collectors.toList());
  
  //collect(stream to map)
  Map<Long, String> map = productList.stream()
  									.collect(Collectors.toMap(
  												item1 -> item1.getId(),
  												item2 -> item2.getName())
  									);
  Map<Long, String> map = productList.stream()
  									.collect(Collectors.toMap(
  												Product::getId,
  												Product::getName
  									);
  
  //collect(stream to set)
  Set<Integer> set = IntStream.of(1, 2, 3, 4, 5)
  							.collect(Collectors.toSet());
  
  
  //joining
  //productList = 
  String nameString = Stream.of("hello","java","world")
  						.collect(Collectors.joining()); //hellojavaworld
  String nameString = Stream.of("hello", "java", "world")
  						.collect(Collectors.joining(",")); //hello,java,world
  String nameString = Stream.of("hello", "java", "world")
  						.collect(Collectors.joining(",","<",">"));//<hello,java,world>
  
  
  //forEach
  Stream.of("hello","java","world")
  		.forEach(System.out::println);
  
  //hello
  //java
  //world
```  


### 마무리

Stream API에 대해 간단하게 알아보았는데 위에서 다룬 메소드들은 극히 일부고 여러 메소드들도 사용해보면서 공부하면 좋을 것 같습니다.
  
  

 
  
  

