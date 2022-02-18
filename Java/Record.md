## 레코드(Record)

### 레코드(Record)란?
레코드란 자바14 버전에 preview로 등장하여 16버전부터 정식 스펙으로 포함된 자료구조입니다.
파이썬의 Dictionary(dicts)처럼 특정 데이터와 관련있는 필드를 묶어놓은 자료구조이고
C#의 Record와 동일합니다.

레코드는 불변(immutable)데이터 객체를 쉽게 생성할 수 있도록 하는 새로운 유형의 클래스입니다.

### 기존 불변 데이터 객채

```java
public class User {
    private final Long id;
    private final String email;

    public User(Long id, String email) {
        this.id = id;
        this.email = email;
    }

    public Long getId() {
        return id;
    }

    public String getEmail() {
        return email;
    }
}
```

기존의 불변 데이터 객체는 필드를 final로 정의하고 생성자로부터 값을 할당받은 다음 setter가없이 getter만 생성하는 등..
여러 방식으로 존재합니다. 이러한 객체를 Record로 표현하자면 어떻게될까요?

### Record를 이용한 불변 데이터 객체
```java
public record User(Long id, String email){
}
```

위 와 같이 레코드 클래스를 이용하면 훨씬 간결하게 동일한 불변 데이터 객체를 생성할 수 있습니다.
record는 이름(User), 헤더(Long id, String email), 바디({})로 이루어져있고 컴파일러는 헤더를 통해 내부 필드를 추론합니다.

레코드는 생성자를 작성하지 않아도 되고, toString, equals, hashCode메소드에 대한 구현을 자동으로 제공합니다.
단, getter역할을 하는 메소드도 존재하는데 getId(), getEmail() 이 아닌 id(), email()과 같이 필드명으로 생성됩니다. 

값을 주입받을때 필요한 로직은 아래와 같이 구현할 수 있습니다.
```java
public record User(Long id, String email){
    public User{
        if(email.isBlank()){
            throw new IllegalArgumentException("이메일은 필수입니다.");
        }
    }
}
```

### Record 클래스의 사용법
```java
public class App{
    public static void main(String[] args) {
        User user = new User(1L, "namookk@test.com");
        System.out.println(user.id()); // 1
        System.out.println(user.email()); // namookk@test.com
        System.out.println(user.toString()); 
        
        User user1 = new User(1L, "namookk@test.com");
        User user2 = new User(2L, "namookk2@test.com");
        System.out.println(user.equals(user1)); // true
        System.out.println(user.equals(user2)); // false
    }
}
```

### 제한사항
+ 암묵적으로 final클래스라 상속이 불가하고 abstract를 선언 불가 
+ 다른 클래스를 상속받을 수 없지만 인터페이스는 구현 가능
+ static 멤버 생성가능
+ native 메소드 생성 불가
+ 제네릭 가능
+ annotation가능


### 마무리
아직 실무에서는 8이나 11버전을 많이 사용하지만 이후 버전에서 새로나운 기술들을 미리 공부해서 대비를 해놔야겠습니다.