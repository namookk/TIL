## 컴포짓(Composite) 패턴

### 컴포짓 패턴이란?

컴포짓 패턴은 구조패턴 중 하나로 클라이언트 입장에서는 '전체'나 '부분'이나 모두 동일한 컴포넌트로 인식할 수 있는 계층구조를 만든다.
의도는 트리 구조로 작성하여, 부분-전체 관계(Part-Whole Hierarchy)를 표현하는 것입니다.

컴포짓 패턴의 구조는
1. Component Interface  

    + Leaf클래스와 Composite클래스에 공통 인터페이스를 정의

2. Leaf Class  
    + 구체적인 부분 클래스
    + Composite 객체의 부품으로 설정
3.  Composite Class
    + 전체 클래스
    + 복수 개의 Component를 갖도록 정의

가방에 들어있는 아이템의 가격을 출력하는 예제를 들어봅시다.
컴포짓 패턴을 적용하면 가방객체는 Composite Class가 되고, 아이템객체는 Leaf Class가 됩니다.

컴포짓 패턴 적용 전 코드를 살펴봅시다.

### Before

```java
public class Item {
    private String name;
    private int price;

    public Item(String name, int price) {
        this.name = name;
        this.price = price;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getPrice() {
        return price;
    }

    public void setPrice(int price) {
        this.price = price;
    }
}
```
```java
public class Bag {
    List<Item> items;

    public Bag() {
        items = new ArrayList<>();
    }

    public List<Item> getItems(){
        return this.items;
    }
    
    public void add(Item item){
        items.add(item);
    }
}
```
```java
public class Client {
    public static void main(String[] args) {
        Item doranBlade = new Item("도란검", 450);
        Item healPotion = new Item("체력 물약", 50);

        Bag bag = new Bag();
        bag.add(doranBlade);
        bag.add(healPotion);

        Client client = new Client();
        client.printPrice(doranBlade);
        client.printPrice(bag);
    }

    private void printPrice(Bag bag) {
        System.out.println(bag.getItems().stream()
                        .mapToInt(Item::getPrice)
                        .sum());
    }

    private void printPrice(Item item) {
        System.out.println(item.getPrice());
    }
}
```

위와같이 Bag안에 여러 아이템이 존재하는 트리구조가 만들어집니다.
하지만 printPrice가 단일객체와 복합 객체가 다른 방법으로 처리되는 형태가 만들어집니다.
컴포짓 패턴을 적용해봅시다.

### After

```java
public interface Component {
    int getPrice();
}
```
```java
public class Item implements Component{
    private String name;
    private int price;

    public Item(String name, int price) {
        this.name = name;
        this.price = price;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public int getPrice() {
        return price;
    }

    public void setPrice(int price) {
        this.price = price;
    }
}
```

```java
public class Bag implements Component {
    List<Component> components;

    public Bag() {
        components = new ArrayList<>();
    }

    public List<Component> getComponents() {
        return this.components;
    }

    public void add(Component component) {
        components.add(component);
    }

    @Override
    public int getPrice() {
        return this.components.stream()
                .mapToInt(Component::getPrice)
                .sum();
    }
}
```
```java
public class Client {
    public static void main(String[] args) {
        Item doranBlade = new Item("도란검", 450);
        Item healPotion = new Item("체력 물약", 50);

        Bag bag = new Bag();
        bag.add(doranBlade);
        bag.add(healPotion);

        Client client = new Client();
        client.printPrice(doranBlade);
        client.printPrice(bag);
    }

    private void printPrice(Component component) {
        System.out.println(component.getPrice());
    }
}
```

먼저 가격을 반한하는 getPrice를 가진 컴포넌트 인터페이스를 생성한 후
복합객체인 Bag, 단일 객체인 Item에서 구현해줌으로써 클라이언트단에서는 동일한 방법으로
복합객체와 단일객체의 가격을 표현할 수 있습니다. 

### 컴포짓 패턴의 장단점

#### 장점
+ 복잡한 트리 구조를 편리하게 사용할 수 있다.
+ 다형성과 재귀를 활용할 수 있다.
+ 클라이언트 코드를 변경하지 않고 새로운 엘리먼트 타입을 추가할 수 있다.

#### 단점
+ 트리를 만들어야 하기 때문에 (공통된 인터페이스를 정해야함) 지나치게 일반화 해야 하는 경우가 발생할 수 있다.

