## 어댑터(Adapter) 패턴

실생활에서 어댑터는 무엇인지 생각해봅시다.    
110v를 220v로 변경해주는 변환기를 예로 들 수 있습니다.

프로그래밍에서의 어댑터 패턴은 기존 코드를 클라이언트가 사용하는 인터페이스의 구현체로 바꿔주는 패턴입니다.
더 나아가 인터페이스가 바뀌더라도 그 변경 내역은 어댑터에 캡슐화되기 때문에 클라이언트는 바뀔 필요가 없어집니다.

Spring Security에서 사용되는 예제로 살펴봅시다.

코드길이를 짧게하기위해 lombok을 사용하였습니다.

### Adaptee에 해당하는 Account클래스

```java
@Getter
@Setter
@AllArgsConstructor
public class Account{
    private String name;
    
    private String password;
    
    private String email;
}

public class AccountService{
    public Account findByAccountByUsername(String username){
        return new Account(username, username, username);
    }
    
    public void createNewAccount(Account account){
        //회원가입 로직
    }
    
    public void updateAccount(Account account){
        //회원정보 수정 로직
    }
}
```

### target 인터페이스인 UserDetails, UserDetailsService

Spring Security의 UserDetails, UserDetailsService를 예로 들어봅시다.

```java
public interface UserDetails {
    String getUsername();

    String getPassword();
}

public interface UserDetailsService {
    UserDetails loadByUsername(String username);
}
```

또, UserDetails, UserDetailsService를 이용한 로그인 로직을 예로들어봅시다.

```java
public class LoginHandler {
    private final UserDetailsService userDetailsService;

    public LoginHandler(UserDetailsService userDetailsService) {
        this.userDetailsService = userDetailsService;
    }

    public String login(String username, String password) {
        UserDetails userDetails = userDetailsService.loadByUsername(username);
        if(userDetails.getPassword().equals(password)){
            return userDetails.getUsername();
        }else{
            throw new IllegalArgumentException();
        }
    }
}
```

이렇게 짜여진 코드가 있다면 로그인 로직은 Spring Security의 UserDetails, UserDetailsService를 사용하여 로그인처리를 하고 어플리케이션에서 사용하는 회원객체는 Account가 됩니다.
Security의 로그인기능과 실제 어플리케이션의 회원객체에는 간극이 존재하며 이 간극을 해결하기위해 Adapter를 생성해야합니다. (110v -> 220v)

### Adapter클래스 구현
UserDetails -> Account
```java
public class AccountUserDetails implements UserDetails{
    private Account account;

    public AccountUserDetails(Account account) {
        this.account = account;
    }

    @Override
    public String getUsername() {
        return account.getName();
    }

    @Override
    public String getPassword() {
        return account.getPassword();
    }
}
```

UserDetailsService -> AccountService

```java
public class AccountUserDetailsService implements UserDetailsService {

    private final AccountService accountService;

    public AccountUserDetailsService(AccountService accountService) {
        this.accountService = accountService;
    }

    @Override
    public UserDetails loadUser(String username) {
        Account account = accountService.findAccountByUsername(username);
        return new AccountUserDetails(account);
    }
}
```

위와같은 2개의 Adapter 객체를 생성한 후 Adapter객체를 사용하여 로그인하는 Client 로직을 살펴보자면

```java
public class App {
    public static void main(String[] args) {
        AccountService accountService = new AccountService();
        UserDetailsService userDetailsService = new AccountUserDetailsService(accountService);
        LoginHandler handler = new LoginHandler(userDetailsService);
        String login = handler.login("namookk", "1234");
        System.out.println(login);
    }
}
```

예시에서 보듯 인터페이스를 따르지않는 기존 코드를 사용할 수 있게 해주는 패턴이 바로 어댑터패턴입니다.

그럼 어댑터패턴의 장단점을 살펴봅시다.

#### 장점

+ 기존코드를 변경하지 않고 원하는 인터페이스 구현체를 만들어 재사용할 수 있다.
+ 기존 코드가 하던 일과 특정 인터페이스 구현체로 변환하는 작업을 각기 다른 클래스로 분리하여 관리할 수 있다.

#### 단점
+ 새 클래스가 생겨 복잡도가 증가할 수 있다. 경우에 따라서는 기존코드가 해당 인터페이스를 구현하도록 수정하는것이 좋은 선택이 될 수도 있다.


### 마무리

기존 프로젝트에서 회원 부분을 맡아 개발한적이 있어 Security로 예를 들어보았는데 단점에서 설명하듯 Account가 UserDetails를 구현하도록 또, AccountService가 UserDetailsService를 구현하도록 설계하는 방법도 있으니 상황에 맞게 사용해야 할 것 같습니다.