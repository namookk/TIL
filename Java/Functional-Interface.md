## 함수형 인터페이스 (Functional Interface) 

### 함수형 인터페이스란?

함수형 인터페이스는 1개의 추상 메소드를 갖는 인터페이스를 의미합니다.    
그래서 Single Abstract Method (SAM)이라고 불리기도 합니다.  

```java
public interface Example {
  void execute();
}
```

위 와 같이 추상메소드가 1개만 존재하는 인터페이스 형태입니다.
```java
public interface Example {
  void execute();
  static void print() {
    System.out.println("print");
  }
  default void defaultExecute(){
    System.out.println("default");  
  }
}
```
Java8 버전 부터는 인터페이스에 static, default메소드를 사용할 수 있습니다. (9이상은 private메소드도 가능)
위 인터페이스도 추상 메서드는 execute하나이므로 함수형 인터페이스라고 할 수 있습니다.

### @FunctionalInterface 어노테이션 활용
```java
@FunctionalInterface
public interface Example{
  void execute();
}
```
함수형 인터페이스를 생성할 때 java.lang에서 지원하는 @FunctionalInterface 어노테이션을 사용하여 안전성을 보장할 수 있습니다.
( 함수형 인터페이스가 아닐 경우 컴파일 에러를 발생 )

그렇담 이 함수형 인터페이스를 어떻게 사용할까요?

### 함수형 인터페이스 사용법
```java
Example example = new Example() {
  @Override
  public void execute() {
    System.out.println("execute 실행");  
  }
};

//execute 실행
example.execute();
```
위 와 같이 익명 내부클래스를 활용하여 사용할 수 있고,
```java
Example example = () -> System.out.println("execute 실행");

//execute 실행
example.execute();
```
위 와 같이 Java8에서 등장한 람다식을 사용하여 간편하게 작성할 수 있습니다.

### Java의 기본 함수형 인터페이스

람다식을 사용할 때 마다 함수형 인터페이스를 매번 정의하기에는 불편하므로 자바에서 라이브러리로 제공하는 인터페이스가 있습니다.

+ Runnable
+ Supplier
+ Consumer
+ Function<T, R>
+ Predicate

#### 1. Runnable

```java
@FunctionalInterface
public interface Runnable {
  void run();
}
```
`Runnable` 인터페이스는 아무런 인자를 받지 않고 또 리턴도 하지 않습니다.
람다식으로는 () -> void로 표현합니다.

#### 2. Supplier 
```java
@FunctionalInterface
public interface Supplier<T> {
  T get();
}
```
`Supplier` 인터페이스는 아무런 인자를 받지 않고 T타입의 객체를 리턴합니다.
람다식으로는 () -> T 로 표현합니다.

#### 3. Consumer
```java
@FunctionalInterface
public interface Consumer<T> {
  void accept(T t);
}
```
`Consumer` 인터페이스는 인자 하나를 받고 아무것도 리턴하지 않습니다.
람다식으로는 T -> void 로 표현합니다.

#### 4. Function
```java
@FunctionalInterface
public interface Function<T, R> {
  R apply(T t);
}
```
Function은 T타입 인자를 받아서 R타입을 리턴합니다.
람다식으로는 T -> R로 표현합니다. 여기서 T와 R은 같은 타입일 수도 있습니다.

#### 5. Predicate
```java
@FunctionalInterface
public interface Predicate<T> {
  boolean test(T t);
}
```
`Predicate` 인터페이스는 인자 하나를 받아서 boolean타입을 리턴합니다.
람다식으로는 T -> boolean으로 표현합니다.

### 파생된 인터페이스

#### 두 개의 인자를 받는 Bi 인터페이스

특정 인자를 받는 Predicate, Consumer, Function 등은 두 개 이상의 타입을 받을 수 있는 인터페이스가 존재합니다.

| 인터페이스       | 람다식               | 메소드                    |
|-------------|-------------------|------------------------|
| BiPredicate | (T, U) -> boolean | boolean test(T t, U u) |
| BiConsumer  | (T, U) -> void    | void accept(T t, U u)  |
| BiFunction  | (T, U) -> R       | R apply(T t, U u)      |


#### Primitive Type의 인터페이스
Stream에서도 Stream<T>도 있고 또 Primitive타입의 IntStream, LongStream 등이 있는 것 처럼 함수형 인터페이스에도 존재합니다.

이 부분에 대해서는 다음을 참고하였으니 자세한 부분은 아래 링크를 참고해주세요.
+ [기본형 타입 처리 함수형 인터페이스](https://junhyunny.blogspot.com/2019/03/blog-post_8.html)


### 마무리

Comparator와 같은 만들어져 있는 인터페이스에서 람다식을 사용해봤지 직접 만들어서 사용해본적은 없는것 같아 정리하였습니다.
리팩토링시 메소드의 매개변수로 함수형 인터페이스를 받아 람다식을 더욱 더 적극적으로 사용하도록 노력해야될 것 같습니다.


