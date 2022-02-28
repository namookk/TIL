## 빌더(Builder) 패턴

빌더패턴이란 동일한 프로세스를 거쳐 다양한 구성의 인스턴스를 만드는 방법입니다.

![](https://images.velog.io/images/papakang22/post/ec305643-0db6-4f4e-a34e-3cd54afdd43a/builderGof.png)

생성해야하는 클래스에 대한 속성값이 많을때 타입, 순서 등에 대한 관리가 어려워져 실수가 발생할 확률이 높아지고 
경우에 따라 필요없는 파라미터들에 대해서는 null값을 넘겨줘야하는 번거로움이 있는데 
이러한 문제들을 해결하기 위해 별도의 Builder클래스를 생성해서 메소드를 통해 chain형태로 값을 입력받은 후 생성하는 패터입니다.

lombok에서  @Builder 어노테이션이 빌더패턴을 구현해주는 어노테이션입니다.

어떻게 구성되는지 확인해봅시다.

### 구현전 코드
```java
@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
public class Product {
    private Long id;
    private String name;
    private String category;
    private Long price;
    private String description;
    private Long reviewCount;
    private LocalDate createAt;

    public Product(Long id, String name, String category, Long price){
        this(id, name, category, price, null, 0L, LocalDate.now());
    }
}
```
먼저 제품객체를 생성해줍니다. 코드길이를 위해 lombok을 사용하였습니다.

```java
public class Client{
    public static void main(String[] args) {
        
        //setter를 통한 값 주입
        Product product = new Product();
        product.setId(1L);
        product.setName("제품명");
        product.setCategory("카테고리");
        product.setPrice(10_000L);
        product.setReviewCount(0L);
        product.setLocalDate(LocalDate.now());
       
        //생성자를 통한 생성
        Product product2 = new Product(1L, "제품명", "카테고리", 10_000L);
    }
}
```

위와 같이 구현전 소스를 보면 setter를 통해 값을 주입하거나 생성자를 만들어 주입하는 방법이있습니다.
하지만 description같은 필수값이 아닌 속성이 많을수록 조합해야하는 경우의수가 많아집니다. 

이러한 문제를 해결하기위해 빌더패턴을 적용해봅시다.

### 빌더패턴 적용한 코드

```java
@Getter
public class Product {
    private Long id;
    private String name;
    private String category;
    private Long price;
    private String description;
    private Long reviewCount;
    private LocalDate createAt;
    
    //1. Builder클래스를 Static Nested Class로 생성
    public static class Builder{
        private Long id;
        private String name;
        private String category;
        private Long price;
        private String description;
        private Long reviewCount;
        private LocalDate createAt;

        public Builder(){
            this.reviewCount = 0L;
            this.createAt = LocalDate.now();
        }
        public Builder id(Long id){
            this.id = id;
            return this;
        }
        public Builder name(String name){
            this.name = name;
            return this;
        }
        public Builder category(String category){
            this.category = category;
            return this;
        }
        public Builder price(Long price){
            this.price = price;
            return this;
        }
        public Builder description(String description){
            this.description = description;
            return this;
        }
        public Builder reviewCount(Long reviewCount){
            this.reviewCount = reviewCount;
            return this;
        }
        public Builder createAt(LocalDate createAt){
            this.createAt = createAt;
            return this;
        }
        public Product build(){
            return new Product(this);
        }
    }
    
    //2.정적메소드로 Builder클래스를 리턴
    public static Builder builder(){
        return new Builder();
    }
    
    //3. private 생성자로 Builder를 통한 생성
    private Product(Builder builder){
        this.id = builder.id;
        this.name = builder.name;
        this.category = builder.category;
        this.price = builder.price;
        this.description = builder.description;
        this.reviewCount = builder.reviewCount;
        this.createAt = builder.createAt;
    }
}
```

빌더 패턴으로 구현한 코드입니다. 
먼저 Builder클래스를 Static Nested Class로 생성한 후 생성자를 통해 default값을 정의할수도 있고, 필수값을 생성자를 통해 받은다음 Optional한 필드만 메소드로 정의하여 사용할 수 있습니다.
그리고 빌더 클래스 안에서 각각의 속성마다 메소드로 제공하며, 중요한것은 메소드의 리턴값이 빌더객체 자신이어야 합니다.(Chaining형태로 사용)
사용해야할 값을 다 채웠다면 build()메소드를 통해 생성된 결과물을 제공합니다. 이때 사용하는 Product객체의 생성자는 private으로 정의합니다.

그럼 빌더패턴을 사용한 클라이언트 코드를 봐보도록 합시다.

```java
public class Client{
    public static void main(String[] args) {
        Product product = Product.builder()
                .id(1L)
                .name("제품명")
                .category("카테고리")
                .price(10_000L)
                .build();
    }
}
```

위와 같은형태로 생성할 수 있으며 Optional한 필드는 해당 메소드를 사용하지 않으면 됩니다.
Builder객체는 lombok의 @Builder 어노테이션으로 만들 수도 있지만 어떻게 생성되는지 알고 사용했으면 좋겠습니다.

### Builder패턴의 장단점

#### 장점
+ 복잡한 객체도 만드는 순서를 정의할 수 있고 동일한 프로세스로 각기 다른 인스턴스를 생성 가능하다.

#### 단점
+ 빌더 객체륾 만들어야 하는 단점이 있고, 구조가 조금 복잡해진다.

### 마무리

실무에서 lombok을 통해 빌더를 자주 사용해봤지만 실제 어떻게 구성되는지는 모르고 사용하는 경우가 많습니다.
물론 저도 동작과정을 자세히 모르고 사용했었지만 이렇게 공부하면서 어떻게 구성되는지 또 롬복의 Builder어노테이션을 안쓰고 구성하면서 추가적인 validation이나 로직을 추가할 수 있게되었습니다.
