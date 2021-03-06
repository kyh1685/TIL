### 데이터 타입 분류 
- 참조 타입으로 선언된 변수는 heap 메모리에 있는 객체의 주소를 값으로 가지며 stack에 저장된다. ex) 배열, 열거, 클래스, 인터페이스
### 메모리 사용 영역
- java.exe로 JVM이 시작되면 JVM은 운영체제에서 할당받은 메모리 영역(Runtime Data Area)을 그림과 같이 세부 영역으로 구분해서 사용한다.
![](https://images.velog.io/images/3hee_11/post/1398872a-86a0-4c6d-957d-1edcd8e2f66c/JVM%20%EB%A9%94%EB%AA%A8%EB%A6%AC%20%EA%B5%AC%EC%A1%B0.png)
- 메소드 영역
   - 코드에서 사용되는 클래스(.class)들을 클래스 로더로 읽어 클래스별로 런타임 상수풀, 필드 데이터, 메소드 데이터, 메소드 코드, 생성자 코드 등을 분류해서 저장한다. 
   - 메소드 영역은 JVM이 시작할 때 생성되고 모든 스레드가 공유한다.
- 힙 영역
   - 객체와 배열이 생성되는 영역이며 **힙 영역에서 생성된 객체와 배열은 JVM 스택 영역의 변수나 다른 객체의 필드에서 참조**한다.
   - 참조하는 필드가 없다면 **GC(Garbage Collector)로 객체를 힙 영역에서 자동으로 제거**한다.
- JVM 스택 영역
   - 스택 영역은 각 스레드마다 하나씩 존재하며 스레드가 시작될 때 할당된다.
   - 스택은 메소드를 호출할 때마다 프레임을 추가(push)하고 메소드가 종료되면 프레임을 제거(pop)하는 동작을 수행한다. 
   - 프레임 내부에는 로컬 변수 스택이 있는데 변수가 추가되거나 제거된다. 변수가 이 영역에 생성되는 시점은 초기화가 될 때, 즉 최초로 변수에 값이 저장될 때이다. 변수는 선언된 블록안에서만 스택에 존재하고 블록을 벗어나면 스택에서 제거된다.
> 프로세스 : 운영체제에서 실행중인 **하나의 프로그램**
스레드 : 하나의 프로세스 내부에서 독립적으로 실행되는 **하나의 작업 단위**
프레임 : 스택에 들어가는 것으로 메소드가 호출될 때마다 만들어지며, **메소드 상태 정보를 저장**한다.

### 참조 변수의 ==, != 연산
- 참조 타입 간의 ==, !=는 동일한 객체를 참조하는지 아닌지를 알아볼 때 사용된다. 참조 타입 변수의 값은 힙 영역 객체의 주소이므로 결국 **주소값을 비교**하는 것이다. **동일한 주소값을 가지고 있다는 건 동일한 객체를 참조한다는 의미**이다.

### null과 NullPointException
- 참조 타입 변수는 힙 영역에 객체를 참조하지 않는다는 뜻으로 null값을 가질 수 있다. null값도 초기값으로 사용할 수 있기 때문에 null로 초기화된 참조 변수는 스택 영역에 생성된다.
- 참조 타입 변수를 사용하는 것은 곧 객체의 사용을 의미한다. 참조 타입 변수가 null값을 가지고 있을 경우 참조할 객체가 없으므로 NullPointException 예외가 뜬다. 

### String 타입
- 변수가 동일한 문자열 리터럴을 참조할 경우 둘은 동일한 String 객체를 참조하게 된다. 하지만 new 연산자를 사용해서 힙 영역에 새로운 String 객체를 만든다면 이 경우 서로 다른 String 객체를 참조하게 된다.
```java
//두 변수의 객체는 같다
String name1 = "김아무개";
String name2 = "김아무개";

//두 변수의 객체는 다르다
String name1 = new String("김아무개");
String name2 = new String("김아무개");
```
- 동일한 객체인지 상관없이 문자열만을 비교할 때는 equals() 메소드를 사용한다.

### 배열 타입
- 배열은 같은 타입의 데이터를 연속된 공간에 나열시키고 각 데이터에 인덱스를 부여해놓은 자료구조이다.
```java
int[] num1 = new int[]{1,2,3};
int[] num2 = new int[3];
```

#### 커맨드 라인 입력
- java 클래스로 프로그램을 실행하면 JVM은 길이가 0인 String 배열을 먼저 생성하고 main() 메소드를 호출할 때 매개값으로 전달한다.

#### 다차원 배열
- 값들이 행과 열로 구성된 배열을 2차원 배열이라고 한다.
```java
int[][] scores = new int[2][3];
int[][] scores = {{95, 80}, {92 96}};
```
#### 향상된 FOR문
```java
for(int score : scores){
	sum += score;
```
- 괄호안에는 배열에서 꺼낸 값을 저장할 변수 선언과 콜론(:) 그리고 배열을 작성한다. for문이 실행되면 배열에서 가져올 값이 존재하는지 판단하고 값이 존재하면 진행하고 없으면 for문을 종료한다.

### 열거 타입
- 한정된 값만을 갖는 데이터 타입이다. ex) 봄, 여름, 가을, 겨울
- 몇 개의 열거 상수 중에서 하나의 상수를 저장하는 데이터 타입이다.

#### 열거 타입 선언
- 열거 타입의 이름을 정하고 그 이름으로 소스 파일을 생성해야 한다.
- public enum은 열거 타입을 선언하기 위한 키워드이다.
- 열거 타입의 값인 열거 상수는 모두 대문자로 작성한다.
- 열거 상수가 여러 단어로 구성이 된다면 단어 사이를 언더바로 연결한다. 
```java
public enum Week{ MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY ...} 
```

#### 열거 타입 변수
```java
열거타입 변수 = 열거타입.열거상수
Week today = Week.SUNDAY;
```
