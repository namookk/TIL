### JVM(Java Virtual Machine)이란?

자바의 가상머신으로 자바 바이트 코드를 실행할 수 있는 주체입니다.
이러한 JVM덕분에 자바는 CPU나 운영체제에 독립적으로 동작이 가능합니다.
JVM은 크게 Class Loader, Excute engine, Runtime Data Area, GC로 나뉩니다.

![](https://images.velog.io/images/papakang22/post/34537416-32b5-4c29-b06e-ed09280e86ab/JVM%EA%B5%AC%EC%A1%B0.png)

### Java의 실행과정

1. 개발자가 Java코드를 작성하여 *.java 소스파일을 생성한다.
2. Java Compiler(javac)가 *.java파일을 *.class(바이트코드) 파일로 변환한다.
3. 생성된 class파일을 엮어서 Class Loader가 JVM메모리에 적재합니다.
4. JVM에 있는 class파일을 Execution Engine의 interpreter와 JIT Compiler를 통해 해석된다.
5. 해석된 바이트 코드는 Runtime Data Area에 배치되어 실질적인 수행이 이루어진다.

### Class Loader

Class Loader는 동작과정에서 설명한 듯이 class파일을 엮어 메모리에 적재하는 역할을 합니다.

### Execution Engine

메모리에 적재된 class파일들을 기계어로 변경해 명령어 단위로 하나씩 실행시킵니다.
Interpreter와 JIT Compiler에 의해 동작합니다.

**1. Interpreter**
컴파일러 처럼 고레벨언어를 기계어로 해석해주는 번역 프로그램입니다. 컴파일러는 전체 소스코드를 보고 명령어를 수집하고 재구성하는 반면 Interpreter는 소스코드의 각 행을 연속적으로 분석하며 실행합니다. 때문에 일반적으로 각 행마다 실행하는 Interpreter보다는 컴파일러가 빠릅니다.

**2. JIT Compiler**
인터프리터는 각 행마다 변환을 해주기 때문에 속도 측면에서 느려 개선하기위해 도입한 것이 JIT Compiler입니다. JIT Compiler는 바이트 코드 전체를 읽어 한꺼번에 변환을 합니다.

모두 인터프리터 방식으로 하기엔 속도가 너무 느리고 그렇다고 프로그램 수행 초기에 모두 컴파일을 하게 되면 초기속도가 너무 느리게 되어 Execution Engine에서는 초기에는 Interpreter방식을 사용하고 해당 코드를 충분히 많이 사용할 경우  JIT Compiler에 의해 컴파일을 수행하게 됩니다. 초기에 Interpreter방식으로 바이트 코드를 변환하면서 그 코드를 캐싱하여, 같은 함수가 여러번 불릴 때 매번 코드가 생성되는 것을 방지합니다.

### Runtime Data Area

Runtime Data Area는 OS로부터 할당받는 메모리영역으로 WAS의 성능에 문제가 발생했을때, 대부분 이 영역들이 원인이 됩니다.(Memory Leak or GC)

Runtime Data Area는 5가지로 구분됩니다.

PC Register, JVM stack, Native Method stack, Heap, Method Area

좌측 3개 영역은 Thread별로 생성되고 우측 2개의 영역은 모든 Thread가 공유하는 영역입니다.

**1. Method Area**

Class Loader에 의해 적재된 클래스에 대한 인스턴스 변수, 메소드 코드, 정적변수 등 메타데이터 정보가 저장되는 영역입니다.

**2. Heap**

사용자가 관리하는 인스턴스가 생성되는 공간으로 객체를 동적으로 생성하면 인스턴스가 Heap영역의 메모리에 할당되어 사용됩니다. 프로그램은 시작될 때 미리 Heap영역을 많이 할당해 놓으며 인스턴스와 인스턴스 변수가 저장됩니다. 참조변수일 경우 Heap에 인스턴스가 저장되는 것이 아니라 포인터가 됩니다(Stack영역을 참조)

Heap영역은 Garbage Collection(GC)의 대상이 되는 영역인데 자세한건 아래에서 살펴보겠습니다.

**3. JVM Stacks**

Thread 제어를 위해 사용되는 메모리 영역으로 Thread가 생성될 때 마다 하나씩 생성되며 Method의 정보( 매개변수, 지역변수, 임시변수 그리고 호출한 주소) 등을 저장합니다.

단일 Thread당 Method가 호출될 때 메모리를 차지하게 됩니다. Method가 호출되면 Method와 Method정보는 Stack에 쌓이게 되며 Method 호출이 종료될때 Stack에서 제거됩니다.

멀티 Thread 프로그램의 경우 각 Thread가 자신의 Stack을 가지고는 있지만 Heap영역은 공유하기 때문에, 프로그래밍시 Thread-safe하지 않는 이슈에 주의하며 프로그래밍을 해야합니다.

**4. Native Method Stacks**

자바 이외의 이기종 언어에서 제공되는 Method의 정보가 저장되는 공간, JNI를 통해 표준에 가까운 방식으로 구현이 가능합니다.

**5. PC Register**

PC Registers도 Thread가 생성될 때 마다 생기는 영역으로 Thread가 어떠한 명령을 실행하게 될지에 대한 부분을 기록합니다.


### GC(Garbage Collection)

GC에 대해 알아보기 전에 알아야할 용어가 있다.

**Stop the world**
Stop the world란 GC를 실행하기 위해 JVM이 어플리케이션 실행을 멈추는 것이다. Stop the world가 발생하면 GC를 실행하는 쓰레드를 제외한 나머지 쓰레드는 모두 작업을 멈춘다. GC작업을 완료한 이후에야 중단했던 작업을 다시 시작한다. 어떤 GC 알고리즘을 사용하더라도 Stop the world는 발생한다. GC튜닝이란 이 Stop the world의 시간을 줄이는 것이다.

**Mark and Sweep**
* Mark : 사용되는 메모리와 사용되지 않는 메모리를 식별하는 작업
* Sweep : Mark단계에서 사용되지 않음으로 식별된 메모리를 해체하는 작업




프로그램을 개발하다 보면 유효하지 않은 메모리인 가비지(Garbage)가 발생하게 됩니다. C언어에서는 직접 메모리를 해제해주어야 하지만 Java에서는 개발자가 메모리를 직접 해제해주는 일이 없다. 그 이유는 바로 JVM의 GC가 불필요한 메모리를 알아서 정리해 주기 때문이다. 이때 정리해주는 영역은 Heap영역의 메모리이다.

먼저 Heap영역에는 크게 세가지 영역으로 나뉘어져 있습니다.

    1. Young 영역 : 생성된지 얼마 안된 객체들이 저장되는 장소이고 Eden영역과 두개의 Survivor영역으로 나뉘어져 있습니다. 여기서 GC가 일어나는걸 Minor GC라고 합니다.
    2. Old 영역 : 이곳은 오랫동안 사용중인 객체들이 저장되는 장소입니다. 여기서 GC가 일어나는걸 Major GC(Full GC)라고 하며 Minor GC보다 시간이 많이 걸립니다.
    3. Perm 영역 : 이곳은 클래스, 메타데이터가 저장되는 곳 입니다.

**1. Minor GC**

Young영역에서 일어나며 작동방식은 Eden영역이 가득차게되면 참조되지 않는 객체들은 비우고 남은 객체들을 서바이버 영역으로 옮기는데 이때 한쪽의 서바이버 영역은 비워져 있어야 합니다. 그리고 서바이버 영역으로 옮길 때 마다 기존에 저장되어있는 객체들의 age값은 늘어나며 이 과정을 aging이라고 하며 기준 age값을 넘어가면 Old영역으로 올라갑니다.

**2. Major GC(Full GC)**
Old영역의 메모리가 가득차게되면 발생하는 GC로 Young영역은 일반적으로 Old영역에 비해 크기가 작기 때문에 0.5초에서 1초사이에 끝나지만 Old영역은 Young영역보다 크며 Young영역을 참조할 수도 있습니다. 그렇기 때문에 Major GC는 시간이 오래걸리며, 10배 이상의 시간을 사용합니다.

### Garbage Collector의 종류

**1. Serial GC (-XX:+UseSerialGC)**
주로 적은 메모리와 CPU 코어 개수가 적을 때 적합한 방식이다. 별도로 지정하지 않는 경우 기본 GC이다.
Minor GC뿐만 아니라 Major GC인 경우도 Stop the wolrd하는 싱글쓰레드 방식

**2. Parallel GC (-XX:+UseParellelGC)**
Parallel GC는 Serial GC와 기본적인 알고리즘은 같지만 Serial GC는 처리하는 스레드가 하나이지만 Parallel GC는 처리하는 쓰레드가 여러개이다. 그렇기 때문에 Serial GC보다 빠르게 처리를 할 수 있다.
Parallel GC는 메모리가 충분하고 코어의 개수가 많을 때 유리하다.

**3. Parallel Old GC (-XX:+UseParallelOldGC)**

Parallel GC와 비교하여 Old영역의 알고리즘만 다르게 적용된다.

또 CMS GC, G1 GC도 있다.

이로써 GC에 대해 조금 알아보았다.
GC에 대하여 자세한 설명은 Naver D2블로그에서 참조하였고 이후 GC튜닝과 GC모니터링에 대한 내용도 블로그에 게시되어있으니 확인해보면 좋을 것 같다.

참고자료
- NaverD2 Blog (https://d2.naver.com/helloworld/1329)





