## Lombok의 동작과정 
Lombok은 간단하게 **컴파일 시점**에서 바이트코드를 변환하여 원하는 부분을 주입해주는 방식으로 동작합니다.

### 동작과정

Lombok이 처리되는 과정은 아래와 같습니다.

1. 자바 컴파일러는 소스파일을 파싱하여 AST트리를 만듭니다.
2. Lombok은 AnnotationProcessor에 따라 AST트리를 동적으로 수정하고 새 노드를 추가하고 마지막으로 바이트 코드를 분석 및 생성합니다.
3. 최종적으로 자바 컴파일러는 Lombok Annotation Processor에 의해 수정된 AST를 기반으로 Byte Code를 생성합니다.

여기서 Annotation Processor란 무엇인지 알아봅시다.

### Annotation Processor

Lombok의 의존성을 추가할때 annotationProcessor이란 키워드를 사용하거나 IDE설정에서 annotation Processor관련설정을 했던 기억이 있습니다.

Annotation Processor란 자바 컴파일러 플러그인의 일종으로, 어노테이션에 대한 코드베이스를 검사, 수정, 생성하는 훅입니다. 컴파일 에러나 컴파일 경고를 만들어내거나, 소스코드(.java)와 바이트코드(.class)를 내보내기도 합니다.

즉, Annotation을 사용하려면 Annotation Processor가 필요합니다.

Annotation Processor의 동작 구조는
1. 어노테이션 프로세서를 사용한다는 것을 자바 컴파일러가 알고 있는 상태에서 컴파일을 수행한다.
2. 어노테이션 프로세서들이 각자의 역할에 맞게 구현되어 있는 상태에서 실행되지 않은 어노테이션 프로세서를 실행한다.
3. 어노테이션 프로세서 내부에서 어노테이션에 대한 처리를 한다.
4. 자바 컴파일러가 모든 어노테이션 프로세서가 실행되었는지 검사하고, 모든 어노테이션 프로세서가 실행되지 않았다면 반복합니다.


### Lombok 사용시 반환되는 바이트 코드
위에서 언급했듯 Lombok은 컴파일 시점에 Annotation Processor를 통하여 AST트리를 동적으로 수정하고 새 노드를 추가하여 마지막으로 바이트 코드를 분석 및 생성합니다.

생성된 바이트코드를 확인하기위해 Lombok을 사용해서 코드를 작성해봅시다.

```java
@AllArgsConstructor
@Getter
public class User{
	Long id;
    String name;
}
```

위 코드를 intelliJ에서 기본적으로 제공되는 디컴파일러를 이용해 직접 바이트코드를 확인하면 아래와 같이 class파일이 생성됩니다.

```java
//
// Source code recreated from a .class file by IntelliJ IDEA
// (powerd by FernFlower decompiler)
//
public class User{
	Long id;
    String name;
    
    public User(final Long id, final String name){
    	this.id = id;
        this.name = name;
    }
    
    public Long getId() { return this.id; }
    public String getName() { return thid.name; }
}
```

### 마무리

Lombok의 동작원리를 어느정도 살펴보았으며 확신을 가지고 Lombok을 사용할 수 있게 되었습니다.

