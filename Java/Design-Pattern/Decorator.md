## 데코레이터(Decorator) 패턴

### 데코레이터 패턴이란?

기존 코드를 변경하지 않고 부가 기능을 추가하는 패턴으로 상속이 아닌 위임을 사용해서 
보다 유연하게(런타임에) 부가기능을 추가하는 것도 가능하다.

즉, 기본 기능에 추가할 수 있는 기능의 종류가 많은 경우에 각 추가 기능을 Decorator클래스로 정의 한 후 필요한 Decorator 객체를 조합하여 추가 기능의 조합을 설계하는 방식이다.

데코레이터 패턴의 구조는 아래와 같습니다.

![](https://images.velog.io/images/papakang22/post/f0d5ec49-6007-4e66-bf05-3b1fb43d1910/99C6E44E5BD86CE428.png)

1. Component
    + 기본 기능과 추가기능의 공통 기능을 정의하는 인터페이스
    + 클라이언트는 Component를 통해 실제 객체를 사용함
2. ConcreteComponent
    + 기본 기능을 구현하는 클래스로 Component를 구현한다.
3. Decorator
   + 많은 수가 존재하는 구체적인 Decorator의 공통 기능을 제공합니다.
   + Component를 구현하는 클래스입니다.
4. ConcreteDecoratorA, B ...
    + Decorator를 상속받은 하위 클래스로 기본 기능에 추가되는 개별적인 기능을 뜻합니다.

만약 댓글을 저장하는 서비스에서 댓글에 대한 필터링이 필요한 상황이라면
댓글을 저장하는 기본 기능과 필터링하는 부가기능들을 조합해서 사용해야합니다.

데코레이터를 사용하지 않고 코드를 작성해봅시다.

### Before
```java
public class CommentService {
    public void addComment(String comment){
        System.out.println(comment);
    }
}
```
```java
//특정 단어를 검색해 스팸처리
public class SpamFilteringCommentService extends CommentService{

    @Override
    public void addComment(String comment){
        boolean isSpam = isSpam(comment);
        if(!isSpam){
            super.addComment(comment);
        }
    }

    private boolean isSpam(String comment) {
        return comment.contains("http");
    }
}
```
```java
//...을 공백으로 바꿔주는 trim필터링
public class TrimmingCommentService extends CommentService{

    @Override
    public void addComment(String comment){
        super.addComment(trim(comment));
    }

    private String trim(String comment) {
        return comment.replace("...", "");
    }

}
```
```java
public class Client {

    private CommentService commentService;

    public Client(CommentService commentService) {
        this.commentService = commentService;
    }

    private void writeComment(String comment){
        commentService.addComment(comment);
    }

    public static void main(String[] args) {
        Client client = new Client(new TrimmingCommentService());
        client.writeComment("오징어게임");
        client.writeComment("보는게 하는거 보다 재밌을 수가 없지...");
    }
}
```

위와 같이 설계되었다고 한다면 스팸처리하는 필터, trim처리하는 필터를 동시에 적용하고 싶다면
SpamAndTrimFilterService와 같은 클래스를 하나 더 만들어 사용해야됩니다.
필터링 해야되는 개수가 2개가 아니라 더 늘어난다면 조합의 개수는 훨씬 더 늘어날것이고 유지보수하기 힘들어지는데
이러한 문제를 데코레이터 패턴을 통해 해결할 수 있습니다.

### After
```java
//Component에 해당하는 인터페이스
public interface CommentService {
    void addComment(String comment);
}
```
```java
//ConcreteComponent에 해당하는 클래스
public class DefaultCommentService implements CommentService{

    @Override
    public void addComment(String comment) {
        System.out.println(comment);
    }
}
```
```java
//Decorator에 해당하는 클래스
public class CommentDecorator implements CommentService{

    private CommentService target;

    public CommentDecorator(CommentService target) {
        this.target = target;
    }

    @Override
    public void addComment(String comment) {
        target.addComment(comment);
    }
}
```
```java
//ConcreteDecoratorA 에 해당하는 클래스 
public class SpamFilteringCommentDecorator extends CommentDecorator{

    public SpamFilteringCommentDecorator(CommentService target) {
        super(target);
    }

    @Override
    public void addComment(String comment){
        if(!isSpam(comment)) {
            super.addComment(comment);
        }
    }
    private boolean isSpam(String comment) {
        return comment.contains("http");
    }
}
```
```java
//ConcreteDecoratorB에 해당하는 클래스
public class TrimmingDecorator extends CommentDecorator{
    public TrimmingDecorator(CommentService target) {
        super(target);
    }

    @Override
    public void addComment(String comment){
        super.addComment(trim(comment));
    }
    private String trim(String comment) {
        return comment.replace("...", "");
    }
}
```
```java
public class Client {
    private static boolean enabledSpamFilter = false;
    private static boolean enableTrimming = false;
    
    private CommentService commentService;

    public Client(CommentService commentService) {
        this.commentService = commentService;
    }

    public void writeComment(String comment){
        commentService.addComment(comment);
    }

    public static void main(String[] args) {
        CommentService commentService = new DefaultCommentService();

        if(enabledSpamFilter){
            commentService = new SpamFilteringCommentDecorator(commentService);
        }
        if(enableTrimming){
            commentService = new TrimmingDecorator(commentService);
        }

        Client client = new Client(commentService);
        client.writeComment("오징어 게임");
        client.writeComment("보는게 하는거 보다 재밌을 수가 없지...");
        client.writeComment("http://www.naver.com");
    }
}
```

위 와 같이 구성할 수 있습니다. 클라이언트 코드를 보면 필터링 여부값을 판단해 ConcreteDecorator의 target을 하나씩 쌓고있습니다.
따라서 필터링객체가 추가되어도 여러 조합에 대한 객체를 만들 필요가 없어집니다.

즉, 어떤 필터를 추가하느냐에 관계없이 Client는 동일한 CommentService클래스만을 통해 일관성 있는 방식으로 댓글을 필터링하여 저장할 수 있습니다.

### Decorator패턴의 장단점
#### 장점
+ 새로운 클래스를 만들지 않고 기존 기능을 조합할 수 있다.
+ 컴파일 타임이 아닌 런타임에 동적으로 기능을 변경할 수 있다.

#### 단점
+ 데코레이터를 조합하는 코드가 복잡할 수 있다.

