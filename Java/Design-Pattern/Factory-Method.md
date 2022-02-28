## 팩토리 메서드(Factory Method) 패턴

### 팩토리 메서드 패턴이란?

팩토리 메서드 패턴은 객체 생성 처리를 서브 클래스로 분리하여 처리하도록 캡슐화 하는 패턴입니다.
즉, 구체적으로 어떤 인스턴스를 만들지는 서브클래스가 정해주는 패턴이라고 생각하면 됩니다.

![](https://images.velog.io/images/papakang22/post/433eb44c-6b9b-423f-91ea-49fc684636e9/9999D8385BE5725009.png)


다양한 구현체(Product)가 있고, 그 중에서 특정한 구현체를 만들 수 있는 다양한 팩토리(Creator)를 제공할 수 있다.

### 팩토리 메서드 구현 전 코드
```java
public class Client {
    public static void main(String[] args) {
        Client client = new Client();
        CardService cardService = new CardService();
        String shinhanCardResult = cardService.order("shinhan","1234-1234-1234-1234", 10_000);
        String wooriCardResult = cardService.order("woori", "1234-1234-1234-1234", 20_000);
    }
}

public class CardService {
    public String order(String cardType, String cardNumber, Long price){
        //validate
        if(cardType == null || cardType.isBlank()) {
            throw new IllegalArgumentException("카드종류를 선택해주세요.");
        }
        if(cardNumber == null || cardNumber.isBlank()) {
            throw new IllegalArgumentException("카드번호를 입력해주세요.");
        }
        if(price == null || price <= 0) {
            throw new IllegalArgumentException("가격을 제대로 입력해주세요.");
        }

        checkCard(cardType, cardNumber);
        
        Card card = new Card();
        if(cardType.equalsIgnoreCase("shinhan")){
            card.setType("신한");
        }else if(cardType.equalsIgnoreCase("woori")){
            card.setType("우리");
        }
        card.setCardNumber(cardNumber);

        return payment(card, price);
    }

    private void checkCard(String cardType, String cardNumber) {
        System.out.println(cardType + "카드 카드번호 : "+cardNumber+" 확인 중");
    }

    private String payment(Card card, Long price) {
        System.out.println(card.getType() + "카드로 "+price+"원 결제");
        return "주문 성공";
    }
}

public class Card{
    private String type;
    private String cardNumber;
    
    public String getType() { return this.type; }
    public void setType(String type) { this.type = type; }
    public String getCardNumber() { return this.cardNumber; }
    public void setCardNumber(String cardNumber) { this.cardNumber = cardNumber; }
}
```

팩토리 메서드로 구현하기 전 코드를 보시면 CardService에서 매개변수 카드타입에 따른 작업이 if문으로 추가되어있습니다.
이렇게 설계되어있으면 결제수단 카드가 확장할때마다 if문을 추가해줘야됩니다. 확장시 기존코드를 변경하니 SOLID원칙중 개방-폐쇄 법칙을 위반하게 됩니다.
팩토리 메서드패턴을 적용한 코드를 확인해봅시다.

#### 구현 후 코드

먼저 기존의 CardService클래스를 인터페이스로 변경 후 구현체에서는 createCard메소드만 구현하도록 설계할 수 있습니다.
이후 CardFactory를 생성하여 각각의 맞는 type별 구현체를 리턴해 주도록 하면 됩니다.

`java8 이후에서는 default 메소드를 생성 가능하며, java9이후에는 private 메소드도 구현할 수 있어 추상클래스 대신 인터페이스를 사용할 수 있습니다.`

```java


public interface CardService {

    default String order(String cardNumber, Long price) {
        validate(cardNumber, price);
        Card card = createCard();
        checkCard(card);
        return payment(card, price);;
    }

    private void validate(String cardNumber, Long price) {
        //validate
        if (cardNumber == null || cardNumber.isBlank()) {
            throw new IllegalArgumentException("카드번호를 입력해주세요.");
        }
        if (price == null || price <= 0) {
            throw new IllegalArgumentException("가격을 제대로 입력해주세요.");
        }
    }

    Card createCard(cardNumber);

    private void checkCard(Card card) {
        System.out.println(card.getType() + "카드 카드번호 : " + card.getCardNumber() + " 확인 중");
    }

    private String payment(Card card, Long price) {
        System.out.println(card.getType() + "카드로 " + price + "원 결제");
        return "주문 성공";
    }
}
```

CardService 구현체를 생성해 줍니다.
```java
public class ShinhanCardService implements CardService {
    
    @Override
    public Card createCard(String cardNumber) {
        return new ShinhanCard(cardNumber);
    }
}

public class WooriCardService implements CardService {

    @Override
    public Card createCard(String cardNumber) {
        return new WooriCard(cardNumber);
    }
}
```
각각에 해당하는 카드클래스를 상속을 이용해 구현해줍니다.
```java
public class ShinhanCard extends Card{
    public ShinhanCard(String cardNumber){
        this.cardType = "신한";
        this.cardNumber = cardNumber;
    }
}

public class WooriCard extends Card{
    public WooriCard(String cardNumber){
        this.cardType = "우리";
        this.cardNumber = cardNumber;
    }
}
```
```java
public class CardFactory{
    public static CardService of(String cardType){
        switch (cardType) {
            case "shinhan":
                return new ShinhanCardService();
                break;
            case "woori":
                return new WooriCardService();
                break;
            default:
                throw new IllegalStateException("결제가능한 카드타입이 아닙니다.");
                break;
        }
    }
}
```

변경된 코드를 사용하는 클라이언트 코드는 어떻게 작성해야할까요?
```java
public class Client{
    public static void main(String[] args) {
        CardService shinhanCardService = CardFactory.of("shinhan");
        shinhanCardService.order("1234-1234-1234-1234", 10_000L);
        
        CardService wooriCardService = CardFactory.of("woori");
        wooriCardService.order("1234-1234-1234-1234", 20_000L));
    }
}
```

팩토리 메서드를 사용한다면 결제가능한 카드가 추가되더라도 기존의 객체들은 변경하지 않고
팩토리 메서드 부분만 수정하여 새로운 카드를 확장할 수 있습니다.

#### 장점
+ 객체지향설계원칙(SOLID) 중 OCP(개방-폐쇄 원칙)을 적용하여 기존 코드를 변경하지 않고 새로운 인스턴스를 다른 방법으로 얼마든지 확장 가능합니다. 그 이유는 팩토리와 제품군간의 관계를 느슨하게 가져갔기 때문입니다.

#### 단점
+ Product가 추가될수록 새로운 클래스를 계속해서 생성해야 합니다.

#### 마무리

간단하게 팩토리 메서드 패턴을 공부해봤는데 예제에서는 카드에 따라 변경해야하는 메소드가 하나뿐이여서 그렇지
실무에서 카드별로 다른 작업을 하게된다면 if문이 어마하게 늘어날 것 입니다. 이러한 부분을 효과적으로 개선할 수 있으며 유지보수시 용이하게 되는 결과를 낳습니다.


