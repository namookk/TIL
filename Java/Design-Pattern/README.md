## 디자인 패턴(Design Pattern)

먼저 객체지향 소프트웨어를 잘 설계하려면 어떻게 해야할까? 객체지향 언어를 사용하여 개발하고 있다면 항상 고민하는 문제입니다.  
자바에대해 공부하다 보면 객체지향의 원칙(SOLID)을 잘 지킨 설계를 하기 위해 끊임없이 노력합니다.  
또, 우리가 개발하다보면 자신이 전에 사용했던 코드들을 다시 보며 개발하는 경우가 종종 있습니다.

디자인패턴도 과거의 소프트웨어 개발 과정에서 발견한 객체지향의 설계 노하우를 전달하여 특정 부분에서 많이 발생하는 문제를 해결할 수 있는 해결책을 뜻합니다.  

디자인 패턴에 대해 공부하기 전에 사용하는 목적을 알아보자면 아래와 같습니다.
```
1. 우리 주변에서 자주 반복해서 발생하는 문제와 그 문제를 해결하는 핵심을 기술하여 동일한 일을 두번 다시 하지 않고 해결핧 수 있도록 한다.
2. 구체적인 설명없이 구조화된 패턴에 대한 사전 지식으로 개발자 간의 커뮤니케이션이 원할해질수 있다.
3. 이미 검증된 구조를 사용하므로 설계 과정의 속도를 높일 수 있다.
```

디자인패턴의 교과서로 불리는 **GoF의 디자인패턴**에서는 객체지향적 디자인 패턴의 카테고리를 `생성 패턴(Creational Pattern)`, `구조 패턴(Structural Pattern)`, `행동 패턴(Behavioral Pattern)` 3가지로 구분하고 있습니다.



### 23가지 패턴 
|생성 패턴|구조 패턴|행동 패턴|
|---|---|---|
|Singleton|Adapter|Chain of Responsibility|
|Factory Method|Bridge|Command|
|Abstract Factory|Composite|Interpreter|
|Builder|Decorator|Iterator|
|Prototype|Facade|Mediator|
| |Flyweight|Memento|
| |Proxy|Observer|
| | |State|
| | |Strategy|
| | |Template Method|
| | |Visitor|

1. **생성 패턴(Creational Patter)**  
객체 생성에 관련된 패턴으로 객체의 생성과 조합을 캡슐화해 특정 객체가 생성되거나 변경되어도 프로그램의 구조에 영향을 크게 받지 않도록 유연성을 제공  
<br/>
2. **구조 패턴(Structural Pattern)**  
클래스나 객체를 조합해 더 큰 구조를 만드는 패턴  
<br/>

3. **행동 패턴(Behavioral Pattern)**  
객체나 클래스 사이의 알고리즘이나 책임 분배에 관련된 패턴으로 한 객체가 혼자 수행할 수 없는 작업을 여러개의 객체로 어떻게 분배하는지, 그러면서 객체 사이의 결합도를 최소화하는 것에 중점을 둔다.




