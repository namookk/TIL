## 추상 팩토리(Abstract Factory) 패턴
### 추상팩토리 패턴이란?

추상팩토리 패턴은 서로 관련있는 여러 객체를 만들어주는 인터페이스를 제공하는 패턴입니다.
즉, 관련성 있는 여러 종류의 객체를 일관된 방식으로 생성하는 경우 사용됩니다.

추상팩토리 패턴을 사용하면 구체적으로 어떤 클래스의 인스턴스를 사용하는지 클라이언트에게 감출 수 있습니다.

추상팩토리 패턴의 다이어그램은 아래와 같습니다.

![](https://images.velog.io/images/papakang22/post/8d89a240-2f3e-40e9-994e-4ab72ebad7d7/abstract-factory-pattern.png)

+ AbstractFactory
  + 실제 팩토리 클래스의 공통 인터페이스
+ ConcreteFactory
  + 구체적인 팩토리 클래스로 AbstractFactory클래스의 추상 메서드를 오버라이드 함으로써 구체적인 제품을 생성함.
+ AbstractProduct
  + 제품의 공통 인터페이스
+ ConcreteProduct
  + 구체적인 팩토리 클래스에서 생성되는 구체적인 제품

배를 만드는 프로그램을 예시로 들어봅시다.

### before
```java
public class Ship {
  private Anchor anchor;
  private Wheel wheel;

  public Ship() {
  }

  public Anchor getAnchor() {
    return anchor;
  }

  public void setAnchor(Anchor anchor) {
    this.anchor = anchor;
  }

  public Wheel getWheel() {
    return wheel;
  }

  public void setWheel(Wheel wheel) {
    this.wheel = wheel;
  }
}

public class WhiteShip extends Ship{
    public WhiteShip(){}
}
```
```java
public interface ShipFactory{
    Ship createShip();
}

public class WhiteShipFactory implements ShipFactory{
    @Override 
    public Ship createShip(){
        Ship ship = new WhiteShip();
        ship.setAnchor(new WhiteAnchor());
        ship.setWheel(new WhiteWheel());
    }
}
```
```java
public class Client{
  public static void main(String[] args) {
    ShipFactory whiteShipFactory = new WhiteShipFactory();
    Ship whiteShip = whiteShipFactory.createShip();
  }
}
```

이전 소스가 위와 같이 되어있다면 추상화가 잘 되어있어서 문제가 없을 것 같지만 제품의 파츠를 변경한다면 createShip메소드를 변경해야할 것입니다.
추상팩토리 메서드 패턴을 사용해서 변경해봅시다.

### After
```java
public interface ShipPartsFactory {
  Anchor createAnchor();

  Wheel createWheel();
}
public class WhiteSheepPartsFactory implements ShipPartsFactory {

  @Override
  public Anchor createAnchor() {
    return new WhiteAnchor();
  }

  @Override
  public Wheel createWheel() {
    return new WhiteWheel();
  }
}
```
```java
public class WhiteShipFactory implements ShipFactory{
    private ShipPartsFactory shipPartsFactory;

    public WhiteShipFactory(ShipPartsFactory shipPartsFactory) {
        this.shipPartsFactory = shipPartsFactory;
    }

    @Override
    public Ship createShip() {
        Ship ship = new WhiteShip();
        ship.setAnchor(shipPartsFactory.createAnchor());
        ship.setWheel(shipPartsFactory.createWheel());

        return ship;
    }
}

public class Client {
  public static void main(String[] args) {
    ShipFactory whiteShipFactory = new WhiteShipFactory(new WhiteSheepPartsFactory());
    Ship whiteShip = whiteShipFactory.createShip();

    ShipFactory whiteShipProFactory = new WhiteShipFactory(new WhiteSheepProPartsFactory());
    Ship whiteShipProship = whiteShipProFactory.createShip();
  }
}
```

새로운 부품으로 만든 인스턴스가 추가되어도 의존성만 잘 주입한다면 createShip메서드를 수정없이 사용 가능합니다.

### 팩토리메서드 패턴과 추상팩토리 패턴의 공통점과 차이점

여기서 팩토리 메서드 패턴과 추상팩토리 패턴은 무슨차이가 있는지 의문이 생깁니다.
둘다 구체적인 객체 생성 과정을 추상화한 인터페이스를 제공하지만 차이점은 관점이 다릅니다.

팩토리 메서드 패턴은 팩토리를 구현하는 방법에 초점을 두고,  
추상 팩토리 패턴은 팩토리를 사용하는 방법에 초점을 둡니다.

또 목적이 조금 다른데  
팩토리 메서드 패턴은 구체적인 객체 생성 과정을 하위 또는 구체적인 클래스로 옮기는 것이 목적이고,  
추상 팩토리 패턴은 관련있는 여러 객체를 구체적인 클래스에 의존하지 않고 만들 수 있게 해주는것이 목적입니다.
