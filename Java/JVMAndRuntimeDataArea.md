_참조 타입 글에서 JVM과 Runtime Data Area를 간략하게 정리했었는데 좋은 글을 발견해서 더 자세히 정리해보려고 한다!_

### JVM 구조
![](https://images.velog.io/images/3hee_11/post/dd460a93-72ce-4abc-a544-75116b64afbf/JVM%EA%B5%AC%EC%A1%B0.png)

- java.exe로 JVM이 실행되면 JVM은 운영체제로부터 메모리 영역(Runtime Data Area)을 할당받는다.
- 자바에서 소스(.java)를 작성하면 자바 컴파일러(Javac.exe)가 바이트 코드(.class)로 컴파일해준다.
- 생성된 클래스 파일들을 **Class Loader을 통해 Runtime Data Area로 로딩**한다.
- Class Loader를 통해 로딩된 클래스 파일들이 **Execution Engine에 의해 기계어로 변경되고 명령어 단위로 실행**된다.
    - Interpreter : 명령어를 하나씩 실행하는 대화형식 컴파일
    - JIT(Just-In-Time) Compiler : 정해진 시간 내에 모든 바이트 코드를 컴파일
- 해석된 바이트 코드는 Runtime Data Area의 각 영역에 배치된다.

### Runtime Data Area 구조
![](https://images.velog.io/images/3hee_11/post/895b9b9e-b626-49b5-9ee2-e63935183ad4/%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C.png)

#### Method Area(메소드 영역)
- **필드 정보**(변수의 이름, 데이터 타입, 접근 제어자 등), **메소드 정보**(이름, 리턴 타입, 파라미터, 접근 제어자), **타입 정보**(Interface 혹은 class), **상수풀**(Constant Pool : 문자 상수, 타입, 필드, 객체 참조 저장) 등이 생성되는 영역
- 메소드 영역은 JVM이 시작할 때 생성되고 모든 스레드가 공유한다.

#### Heap Area(힙 영역)
- new 키워드로 생성된 객체와 배열이 생성되는 영역이다.
- 메소드 영역에 로드된 클래스만 생성이 가능하다.
- 참조되지 않는 메모리는 Garbage Collector가 제거한다. 

#### Stack Area(스택 영역)
- 지역 변수, 파라미터, 리턴 값, 연산에 사용되는 임시 값 등이 생성되는 영역이다.
- 스택 영역은 각 스레드마다 하나씩 존재하며 스레드가 시작될 때 할당된다.
- 스택은 메소드를 호출할 때마다 프레임을 추가(push)하고 메소드가 종료되면 프레임을 제거(pop)하는 동작을 수행한다.
- 프레임 내부에는 로컬 변수 스택이 있는데 변수가 추가되거나 제거된다. 변수가 이 영역에 생성되는 시점은 초기화가 될 때, 즉 최초로 변수에 값이 저장될 때이다. 변수는 선언된 블록안에서만 스택에 존재하고 블록을 벗어나면 스택에서 제거된다.

> **프로세스** : 운영체제에서 실행중인 하나의 프로그램
**스레드** : 하나의 프로세스 내부에서 독립적으로 실행되는 하나의 작업 단위
**프레임** : 스택에 들어가는 것으로 메소드가 호출될 때마다 만들어지며, 메소드 상태 정보를 저장한다.

#### PC Register(PC 레지스터)
- 스레드가 생성될 때마다 생성되는 영역으로 Program Counter 즉, 현재 스레드가 실행되는 부분의 주소와 명령을 저장하고 있는 영역이다. (CPU의 레지스터와 다름)

#### Native Method Stack
- 자바 외 언어로 작성된 네이티브 코드를 위한 메모리 영역이다.

### Heap과 GC(Garbage Collector)
#### heap의 구조
- Eden 
    - 새로 생성된 객체가 위치한다.
    - Eden 영역에 객체가 가득 차면 GC가 일어난다.
    - 살아남은 객체들은 Survivor1로 이동한다.
- Survivor
    - Survivor1 영역을 제외한 다른 영역의 객체를 제거한다.
    - Eden과 Survivor1 영역이 가득차게 되면 Eden과 Survivor1 영역에 생성된 객체 중에 참조가 없는 객체를 확인한다.
    - 참조되고 있는 객체만 Survivor2 영역에 복사하고 다른 영역의 객체들을 제거한다.
	- 오래 살아남은 객체들은 Old영역으로 이동시킨다.( **Minor GC** : New 영역에서 일어나는 GC )
- Old
    - Old 영역에 있는 객체들의 참조를 확인하며 참조되지 않은 객체들을 모아 한 번에 제거한다.( **Major GC(Full GC)** : Old 영역에서 일어나는 GC )
    - Minor GC보다 시간이 훨씬 많이 걸리고 실행중에 GC를 제외한 모든 쓰레드가 중지한다.

JDK8부터는 permanent 영역이 사라지고 일부가 meta space 영역(Native stack 영역에 포함)으로 변경됨

---
**참고**

[**기본기를 쌓는 정아마추어 코딩블로그**](https://jeong-pro.tistory.com/148)

[**자바와 JVM 메모리 구조**](https://velog.io/@agugu95/%EC%9E%90%EB%B0%94%EC%99%80-JVM-%EA%B7%B8%EB%A6%AC%EA%B3%A0-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EA%B5%AC%EC%A1%B0)
