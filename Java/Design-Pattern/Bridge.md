## 브릿지(Bridge) 패턴

### 브릿지 패턴이란?

브릿지 패턴이란 구현부에서 추상층을 분리하여 각자 독립적으로 변형이 가능하고 확장이 가능하도록 하는 패턴입니다.  
쉽게말해서 추상적인 것과 구체적인 것을 분리하여 연결하는 패턴입니다.

브릿지 패턴 클래스의 다이어그램은 아래와 같습니다.

![](http://abc.acb)

+ **Abstraction**
    + 기능 계층의 최상위 클래스로 추상 인터페이스를 정의합니다.
    + 구현 부분에 해당하는 클래스 인스턴스를 가지고 해당 인스턴스를 통해 구현부분의 메서드를 호출합니다.

+ **RefinedAbstraction**
  + Abstraction에 의해 정의된 인터페이스를 확장합니다.
  + 기능 계층에서 새로운 부분을 확장한 클래스입니다.
+ **Implemetor**
  + 구현 크래스를 위한 인터페이스를 정의합니다.
  + Abstraction의 기능을 구현하기 위한 인터페이스를 정의합니다.
+ **ConcreteImplementor**
  + Implementor 인터페이스를 구현하여 실제 기능을 구현한다.


브릿지 패턴의 예제를 만들어봅시다.
게임의 챔피언이 보여지는 스킨과 스킬셋을 다루는 예제입니다.

### Before
```java
public interface Champion{
    void move();
    void skillQ();
    void skillW();
    void skillE();
    void skillR();
}
```
```java
public class KdaAri implements Champion{
  @Override
  public void move(){
    System.out.println("KDA 아리 move");
  }
    
  @Override
  public void skillQ(){
    System.out.println("KDA 아리 Q");
  }
  
  @Override
  public void skillW(){
    System.out.println("KDA 아리 W");
  }
  
  @Override
  public void skillE(){
    System.out.println("KDA 아리 E");
  }
  
  @Override
  public void skillR(){
    System.out.println("KDA 아리 R");
  }
}




public class PoolPartyAri implements Champion{
  @Override
  public void move(){
    System.out.println("수영장 파티 아리 move");
  }

  @Override
  public void skillQ(){
    System.out.println("수영장 파티 아리 Q");
  }

  @Override
  public void skillW(){
    System.out.println("수영장 파티 아리 W");
  }

  @Override
  public void skillE(){
    System.out.println("수영장 파티 아리 E");
  }

  @Override
  public void skillR(){
    System.out.println("수영장 파티 아리 R");
  }
}
```
```java
public class App{
  public static void main(String[] args) {
    Champion kdaAri = new KdaAri();
    kdaAri.move();
    kdaAri.skillQ();
  }
}
```

위와 같이 구현되어있다고 치면 스킨이 추가되면 해당 스킨이 추가되면 챔피언과의 조합별로 클래스가 추가되어야 합니다.
브릿지패턴을 사용하여 리팩토링해봅시다.

### After

```java
public interface Champion{
  void move();
  void skillQ();
  void skillW();
  void skillE();
  void skillR();
}

//Abstraction
public class DefaultChampion implements Champion{
  private Skin skin;
  private String name;

  public DefaultChampion(Skin skin, String name) {
    this.skin = skin;
    this.name = name;
  }

  @Override
  public void move() {
    System.out.printf("%s %s move\n", skin.getName(), this.name);
  }

  @Override
  public void skillQ() {
    System.out.printf("%s %s Q\n", skin.getName(), this.name);
  }

  @Override
  public void skillW() {
    System.out.printf("%s %s W\n", skin.getName(), this.name);
  }

  @Override
  public void skillE() {
    System.out.printf("%s %s E\n", skin.getName(), this.name);
  }

  @Override
  public void skillR() {
    System.out.printf("%s %s R\n", skin.getName(), this.name);
  }
}
```
```java
// RefinedAbstraction
public class Ari extends DefaultChampion{
    public Ari(Skin skin) {
        super(skin, "아리");
    }
}
```

```java
//Implementor
public interface Skin {
  String getName();
}

//ConcreteImplementor
public class Kda implements Skin{
    public String getName(){
        return "KDA";
    }
}

//ConcreteImplementor
public class PoolParty implements Skin{
  public String getName(){
    return "수영장 파티";
  }
}
```
```java
public class App{
  public static void main(String[] args) {
    Champion kdaAri = new Ari(new Kda());
    kdaAri.move();
    kdaAri.skillQ();
  }
}
```

위 와 같이 추상적인 챔피언과 스킨별로 다르게 구현되는 구현부를 분리하여 사용하여 추상적인 코드를 독립적으로 확장할 수 있습니다.

### 브릿지 패턴의 장단점
#### 장점
+ 추상적인 코드를 구체적인 코드 변경 없이도 독립적으로 확장할 수 있다.
+ 추상적인 코드와 구체적인 코드를 분리하여 사용할 수 있다.

#### 단점
+ 계층 구조가 늘어나 복잡도가 증가할 수 있다.
