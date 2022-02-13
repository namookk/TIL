## 싱글톤(Singleton) 패턴

### 정의
싱글톤 패턴의 정의는 **객체의 인스턴스를 오직 1개만 생성하고 생성된 객체를 어디서든지 참조할 수 있도록 하는 패턴**이다.  

### 장점
1. 최초 한번의 new 연산자를 통해서 고정된 메모리 영역을 사용하기 때문에 메모리 낭비를 방지할 수 있다.
2. 다른 클래스간의 데이터 공유가 쉽다.

### 단점
1. private 생성자를 갖고 있어 상속이 불가능하다.
2. 매번 인스턴스의 상태를 초기화시켜주어야 하기 때문에 테스트하기 불편하다.

### 기본 생성방법
```java
public class SingleTon {

    private static SingleTon singleton_instance = null;

    //1.
    private SingleTon() {}

    public static SingleTon getInstance() {
        //2.
        if(singleton_instance == null){
            singleton_instance = new SingleTon();
        }
        //3.
        return singleton_instance;
    }
}
```
1. 생성자를 private하게 만들어서 외부에서 객체생성을 막고 getInstance메소드를 사용해서 객체를 가져오게 한다.
2. getInstance를 호출할때 instance변수가 null일 경우 new를 통하여 객체를 생성한다.
3. instance변수를 반환한다.

하지만 위 방법으로는 멀티 쓰레드 환경에서 안전하지 않다. 왜냐하면 멀티쓰레드에서 if문을 동일하게 동작할 수 있어 new SingleTon()이 여러번 호출 될 수 있습니다.

### Thread-Safe하게 생성하는 방법

1. synchronized키워드 사용

```java
public class SingleTon {

    private static SingleTon singleton_instance = null;

    private SingleTon() {}

    public static synchronized SingleTon getInstance() {
        if(singleton_instance == null){
            singleton_instance = new SingleTon();
        }
        return singleton_instance;
    }
}
```
synchronized키워드를 사용하여 Thread-safe하게 구현할 수 있지만 해당 메소드를 호출할 때마다 동기화처리를 해야하므로 성능 부하가 발생합니다.
그리고 instance변수에 이미 객체가 생성되어 있음에도 불구하고 메소드를 호출할 때 synchronized키워드로 인해 락이 걸릴 수 있어 Double Checked Locking Pattern을 사용하는게 바람직하다.

2. synchronized 코드 블럭 사용

```java
public class SingleTon {

    private static volatile SingleTon singleton_instance = null;

    private SingleTon() {}

    public static SingleTon getInstance() {
        if(singleton_instance == null){
            synchronized (SingleTon.class) {
                if(singleton_instance == null) {
                    singleton_instance = new SingleTon();
                }
            }
        }
        return singleton_instance;
    }
}
```
instance변수가 비어있을 경우(if문 안에서)에만 synchronized블럭을 사용해 double checked locking을 사용해 효율적으로 처리 가능하다.
단, instance변수에 volatile키워드를 사용해야 한다.

* **volatie**: CPU Cache에 저장하지 않고 Main 메모리에 저장시키는 키워드 java 1.5이상에서 사용 가능


4. inner static class 사용

```java
public class SingleTon {
    private SingleTon() {}

    //static inner class사용
    private static class SingleTonHolder{
        private static final SingleTon SINGLE_TON_INSTANCE = new SingleTon();
    }

    public static SingleTon getInstance() {
        return SingleTonHolder.SINGLE_TON_INSTANCE;
    }
}
```

위 방법을 많이 사용하는데 그 이유는 아래와 같다.
+ synchronized block을 사용하는 코드와 비교했을때 구현이 간단하다.
+ Lazy Loading이 가능하다. 클래스가 로드될 때 인스턴스가 생성되므로 getInstance()를 호출하기 전에는 클래스도 로드되지 않고 인스턴스도 생성되지 않는다.
+ Thread-safe하다.


### 결론

싱글톤 패턴이 무엇인지 검색하고 알아본 결과 싱글톤 패턴은 안티패턴이라고 불릴 만큼 단독으로 사용한다면 객체지향원칙(SOLID)에 위반되는 사례가 많고, 스프링 프레임워크를 사용할 시 싱글톤 패턴의 문제점들을 보완하면서 장점의 혜택을 누릴 수 있으니 잘 살펴보고 적용해야 될 듯 하다.

