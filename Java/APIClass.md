### 자바 API 도큐먼트
- API는 프로그램 개발에 자주 사용되는 클래스 및 인터페이스의 모음을 말한다. ex) String 클래스, System 클래스
- API 도큐먼트는 쉽게 API를 찾아 이용할 수 있도록 문서화한 것을 말한다.
- 💡 [API Document](https://docs.oracle.com/javase/8/docs/api/)

### java.lang과 java.util 패키지

#### java.lang 패키지
- java.lang 패키지는 자바 프로그램의 기본적인 클래스를 담고 있는 패키지이다. 
- java.lang 패키지에 있는 클래스와 인터페이스는 import없이 사용할 수 있다.
- **주요 클래스 **
![](https://images.velog.io/images/3hee_11/post/0d5f0792-5aba-41d4-92c8-4258b447b760/image.png)

### Object 클래스
![](https://images.velog.io/images/3hee_11/post/a14fc15f-5a69-4285-97ab-4b6fe3b89dbd/image.png)
- 클래스를 선언할 때 다른 클래스를 상속받지 않으면 자동으로 Object 클래스를 상속받게 된다.
- Object는 자바의 최상위 부모 클래스이며 모든 클래스는 Object 클래스의 자식,자손 클래스이다.

#### 객체 비교(equals())
- equals() 메소드는 비교연산자인 ==과 동일한 결과를 리턴한다.
- String 객체의 equals() 메소드는 기존 메소드를 재정의해서 String 객체의 번지를 비교하는 것이 아니라 문자열이 동일한지를 비교한다. 

#### 객체 해시코드(hashCode())
- 객체 해시코드란 객체를 식별할 하나의 정수값을 말한다.
- Object의 hashCod()는 객체의 메모리 번지를 이용해서 해시코드를 만들어 리턴하기 때문에 객체마다 다른 값을 가지고 있다.
- 논리적 동등비교 시 hashCode() 오버라이딩의 필요성
  - 컬렉션 프레임워크의 HashSet, HashMap, Hashtable과 같은 클래스는 두 객체가 동등한 객체인지 판단할 때 아래의 과정을 거친다.
![](https://images.velog.io/images/3hee_11/post/ceae027d-0356-4706-a2fe-2f9bae5389a4/image.png)
```java
public class Key{
	public int number;
    
    public Key(int number){
    	this.number = number;
    }
    
    @Override
    public boolean equals(Object obj){
    	if(obj instanceof Key){
        	Key compareKey = (key)obj;
            if(this.number == compareKey.number){
            	return true;
            }
        }
        return false;
    }
}

 public class KeyExample{
	public static void main(String[] args){
	    // Key 객체를 식별키로 사용해서 
      	    // String 값을 저장하는 HashMap 객체 생성
    	    HashMap<key,String> hashMap=new HashMap<key, String>();
            
            // 식별키 "new Key(1)"로 "홍길동" 저장
            // new로 Key 생성자에 매개값을 1로 주고 새로운 객체를 생성함
            hashMap(new Key(1), "홍길동");
            
            // 식별키 "new Key(1)"로 "홍길동" 읽기
            String value = hashMap.get(new Key(1));
            System.out.println(value);
            
            // hsashCode()가 같지 않기 때문에 결과는 null
	}
}

// hashCode()를 재정의해서 같은 해시코드값 리턴하기
public class Key{
	...
    
    @Override
    public int hashCode(){
    	return number;
    }
}
```

#### 객체 문자 정보(toString())
- Object 클래스의 toString()은 객체를 문자열로 표현한 값을 리턴한다.
- 기본적으로 Object 클래스의 toString()은 "클래스명@16진수해시코드"로 구성된 문자 정보를 리턴한다.
- System.out.println(클래스)를 리턴하면 자동으로 클래스.toString()을 호출해 리턴값을 얻은 후 출력한다.

#### 객체 복제(clone())
- 객체 복제는 원본 객체의 필드값과 동일한 값을 가지는 새로운 객체를 생성한다. 
- 객체를 복제하는 이유는 원본 객체를 안전하게 보호하기 위해서이다.
- Object의 clone()은 자신과 동일한 필드값을 가진 얕은 복제된 객체를 리턴한다.
- clone()으로 객체를 복제하려면 원본 객체는 반드시 Cloneable 인터페이스를 구현해야 한다. 
- **얕은 복제(thin clone)**
    - 단순히 필드값을 복사해서 객체를 복제하는 것
    - 기본 타입일 경우 값 복사, 참조 타입일 경우 객체의 번지 복사
    ![](https://images.velog.io/images/3hee_11/post/3ea4d894-4a66-45e4-bfb8-ef92bfe6e149/image.png)
- **깊은 복제(deep clone)**
    - 깊은 복제란 참조하고 있는 객체도 복제하는 것을 말한다.
    - 깊은 복제를 하려면 Object의 clone()을 재정의해서 참조 객체를 복제하는 코드를 직접 작성해야 한다.
    ![](https://images.velog.io/images/3hee_11/post/49d00a88-c8fd-4b96-be93-96f01b1dd736/image.png)
```java
public class Member implements Cloneable{
	public String name;
    public int age;
    public int[] scores;
    public Car car;
    
    public Member(String name, int age, int[] scores, Car car){
    	this.name = name;
        this.age = age;
        this.scores = scores
        this.car = car;
    }
    
    @Override
    protected Object clone() throws CloneNotSupportedException{
    	// 얕은 복사를 해서 name, age 복사
        Member cloned = (Member)super.clone();
        // scores 깊은 복제
        cloned.scores = Arrays.copyOf(this.scores
        				, this.scores.length);
       // car 깊은 복제
       cloned.car = new Car(this.car.model);
       // 깊은 복제된 Member 객체 리턴
       return cloned;
    }
    
    public Member getMember(){
    	Member cloned = null;
        try{
		cloned = (Member) clone();
	}catch(CloneNotSupportedException e){
        	e.printStackTrace();
        }
        return cloned;
    }
}
```

#### 객체 소멸자(finalize())
- GC는 참조하지 않는 배열이나 객체를 소멸시키기 전에 finalize()를 실행시킨다.
- Object의 finalize()는 기본적으로 실행 내용이 없기 때문에 객체 소멸전 사용했던 자원을 닫고 싶거나 중요한 데이터를 저장하고 싶다면 finalize()를 재정의할 수 있다.![](https://images.velog.io/images/3hee_11/post/3d1c608a-2a10-4659-8d54-bd4485a6f218/image.png)
- GC는 메모리의 상태를 확인하고 일부 객체를 무작위로 소멸시키고 구동 시점이 일정하지 않기 때문에 되도록 사용하지 않는 것이 좋다.  
### Objects 클래스
- Objects 클래스는 객체 비교, 해시코드 생성, null 여부, 객체 문자열 리턴 등의 연산을 수행하는 정**적 메소드로 구성된 Object의 유틸리티 클래스**이다.
![](https://images.velog.io/images/3hee_11/post/1f4b2b51-67d0-4f22-bc60-176605d26d20/image.png)

#### 객체 비교(compare(T a, T b, Comparator< T >c))
- compare(T a, T b, Comparator< T >c)는 두 객체를 비교자(Comparator)로 비교해서 int 값을 리턴한다.
- Comparator< T >는 제네릭 인터페이스 타입으로 두 객체를 비교하는 compare(T a, T b)가 정의되어 있다.
- a가 b보다 작으면 음수, 같으면 0, 크면 양수를 리턴하도록 구현 클래스를 만들어야 한다.

#### 동등 비교(equals()와 deepEquals())
- Object.equals(Object a, Object b)는 두 객체의 동등을 비교하는데 다음과 같은 결과를 리턴한다.
- 만약 **a와 b가 모두 null이라면 true**를 리턴한다.
![](https://images.velog.io/images/3hee_11/post/3ea748fe-6a68-4b7c-bb6a-04b5a9452a22/image.png)
- deepEquals(Object a, Object b) 역시 두 객체의 동등을 비교하는데 a와 b가 서로 다른 배열일 경우 항목 값까지 모두 같다면 true를 리던한다.
- Arrays.deepEquals(Object[] a, Object[] b)와 동일하다.
![](https://images.velog.io/images/3hee_11/post/010366ae-41d2-41e5-9c88-d4a61bb382d4/image.png)

#### 해시코드 생성(hash(), hashCod())
- Object.hash(Object ...values)는 매개값으로 주어진 값들을 이용해 해시코드를 생성하는 역할을 하는데, 주어진 매개값들로 배열을 생성하고 Arrays.hashCode(Object[])를 호출해서 해시코드를 얻고 이 값을 리턴한다. 
- Object.hashCode(Object o)는 매개값으로 주어진 객체의 해시코드를 리턴하기 때문에 o.hashCode()와 리턴값이 동일하다.
- 매개값이 null이면 0을 리턴한다.

#### 널 여부 조사(isNull(), nonNull(), requireNonNull())
- Object.isNull(Object obj)는 매개값이 null인 경우 true를 리턴한다.
- nonNull(Object obj)은 매개값이 not null인 경우 true를 리턴한다.
- requireNonNull()은 다음 세가지로 오버로딩 되어있다.
![](https://images.velog.io/images/3hee_11/post/04c30937-a941-4a12-806b-e216b92439cf/image.png)

#### 객체 문자 정보(toString())
- Object.toString()은 객체의 문자 정보를 리턴한다.
- 첫번째 매개값이 not null이면 toString()으로 얻은 값을 리턴하고 null이면 "null" 또는 두번째 매개값인 nullDefault를 리턴한다.

### System 클래스
- System 클래스를 이용하면 운영체제의 일부 기능을 이용할 수 있다. 
ex) 프로그램 종료, 키보드로 입력, 메모리 정리 등
- System 클래스는 정적 필드와 정적 메소드로 구성되어 있다.

#### 프로그램 종료(exit)
- exit()은 현재 실행하고 있는 프로세스를 강제 종료시킨다.
- exit()은 int형 매개값을 가지고 있는데 이 값은 종료 상태값이다( 0이면 정상 종료, 0 이외의 값은 비정상 종료 )
- 특정한 값이 입력될 경우에만 종료하고 싶다면 보안 관리자( checkExit() )를 직접 설정한다.

#### 쓰레기 수집기 실행(gc())
- 자바는 메모리를 JVM이 자동으로 관리한다.
- JVM은 메모리가 부족하거나 CPU가 한가할 때 GC를 실행시켜 사용하지 않는 객체를 자동 제거한다.
- gc() 메소드는 GC의 실행을 요청하는 메소드이다.

#### 현재 시각 읽기( CurrentTimeMillis(), nanoTime() )
- CurrentTimeMillis(), nanoTime()는 컴퓨터의 시계로부터 현재 시간을 읽어 밀리세컨드 단위와 나노세컨드 단위의 long 값을 리턴한다.

#### 시스템 프로퍼티 읽기(getProperty())
- 시스템 프로퍼티는 JVM이 시작할 때 자동 설정되는 시스템의 속성값을 말한다.
![](https://images.velog.io/images/3hee_11/post/f1fec75c-ae85-4fda-acbd-4d557485c933/image.png)
- 시스템의 프로퍼티를 읽으려면 System.getProperty()를 사용한다.
![](https://images.velog.io/images/3hee_11/post/a1f9af14-422b-402a-8a90-c6f0ae12bd04/image.png)

#### 환경변수 읽기(getenv())
- **운영체제에서 이름과 값으로 관리되는 문자열 정보**이다.
- 자바 프로그램에서 환경 변수의 값이 필요할 경우 System.getenv()를 사용한다. 매개값으로 환경 변수의 이름을 주면 값을 리턴한다.
![](https://images.velog.io/images/3hee_11/post/02df9931-6e22-4fab-8523-31811b0cc821/image.png)

### Class 클래스
- 자바는 클래스와 인터페이스의 메타 데이터를 java.lang 패키지에 소속된 Class 클래스로 관리한다.
    - 메타 데이터란 클래스의 이름, 생성자 정보, 필드 정보 등

#### Class 객체 얻기(getClass(), forName())
- 프로그램에서 Class 객체를 얻기 위해서는 Object 클래스가 가지고 있는 **getClass()**를 이용하면 된다.
- getClass()는 해당 **클래스로 객체를 생성했을 때만 사용**할 수 있다.
- forName()은 객체를 생성하기 전에 직접 Class 객체를 얻을 수 있다. 
- forName()은 **클래스 전체 이름(패키지 포함)을 매개값으로 받고 Class 객체를 리턴**한다.

#### 리플렉션(getDeclaredConstructors(), getDeclaredFIelds(), getDeclaredMethods())
- Class 객체를 이용하면 클래스의 생성자, 필드, 메소드 정보를 알아낼 수 있고 이것은 리플렉션이라고 한다.
- 각각 배열을 리턴하며 상속된 멤버는 가져오지 않는다.
- 상속된 멤버도 얻고 싶다면 getFields(), getMethods()를 이용해야한다.
- getFields(), getMethods()는 public 멤버만 가져온다.

#### 동적 객체 생성(newInstance())
- ** Class 객체를 이용하면 new 연산자를 사용하지 않아도 동적으로 객체를 생성** 할 수 있다. 
- 이 방법은 코드 작성 시에 클래스 이름을 결정할 수 없고 런타임 시에 이름이 결정되는 경우 유용하다.
- Class.forName()으로 Class 객체를 얻은 다음 newInstance()를 호출하면 Object 타입의 객체를 얻을 수 있다.
- newInstance()는 기본 생성자를 호출해서 객체를 생성하기 떄문에 **반드시 클래스에 기본 생성자가 존재**해야 한다.
![](https://images.velog.io/images/3hee_11/post/774231f3-bdc5-41c0-bd44-4e889c4f7d09/image.png)![](https://images.velog.io/images/3hee_11/post/8179a377-a588-4491-a0f7-69f1a289ac76/image.png)

### String 클래스

#### String 생성자
- String 클래스의 생성자를 이용해 직접 String 객체를 생성할 수 있다.
    - byte[]를 문자열로 변환
    ![](https://images.velog.io/images/3hee_11/post/628cd22b-c8b8-4153-b289-604a94dadd67/image.png)
    - 키보드로부터 읽은 byte[]을 문자열로 변환
    ![](https://images.velog.io/images/3hee_11/post/ae927fe2-feb1-437b-b7ed-58b3176b4b9e/image.png)
    
#### String 메소드![](https://images.velog.io/images/3hee_11/post/480680f0-a1c8-4ece-bb7b-4ccbfc4f4c8f/image.png)

### String Tokenizer 클래스
- 문자열을 구분자 기준으로 분리하기 위해 split()이나 StringTokenizer 클래스를 이용할 수 있다.
- split()은 정규 표현식으로 구분하고 StringTokenizer은 문자로 구분한다.

#### String Tokenizer 클래스
- String Tokenizer 객체를 생성할 때 첫번째 매개값으로 문자열, 두번째 매개값으로 구분자를 준다.
![](https://images.velog.io/images/3hee_11/post/bc7b35d4-909c-4719-9b88-de7c9599814c/image.png)
- 객체가 생성되면 부분 문자열을 분리할 수 있는데 아래 메소드들을 이용해서 전체 토큰, 남은 토큰 여부를 확인한 다음 토큰을 읽는다.
![](https://images.velog.io/images/3hee_11/post/a97669ef-f728-4dad-acbc-e4fe46a27142/image.png)

### StringBuffer, StringBuilder 클래스
- String은 내부의 문자열을 수정할 수 없다.
- replace(), String 객체의 + 연산자는 문자열 수정이 아니라 새로운 문자열을 생성해 리턴한다.
-> 변수는 새로 생성된 객체를 참조
![](https://images.velog.io/images/3hee_11/post/b2e2a5d6-de34-4525-9b7b-8f888621f809/image.png)
- 문자열을 결합하는 + 연산자 사용 
-> String 객체가 늘어나 프로그램 성능이 느려짐
- **버퍼(데이터를 임시로 저장하는 메모리)에 문자열을 저장해 두고 그 안에서 추가, 수정, 삭제 작업** 
- **StringBuffer** : 멀티 스레드 환경에서 사용할 수 있도록 동기화가 적용 ->스레드에 안전
- **StringBuilder** : 단일 스레드 환경에서만 사용
![](https://images.velog.io/images/3hee_11/post/775ab0e4-f9b8-455a-b6db-5185bd8cc93c/image.png)

### 정규 표현식과 Pattern 클래스
- 문자열이 정해져 있는 형식으로 구성되어 있는지 검증할 때 사용
 Ex) 이메일, 전화번호, 비밀번호 등

#### 정규 표현식 작성 방법
- 문자 또는 숫자 기호와 반복 기호가 결합된 문자열
![](https://images.velog.io/images/3hee_11/post/7b44fb2f-9597-4449-99b8-c35bf7c784ce/image.png)

#### Pattern 클래스
- matches()로 정규 표현식 검증
![](https://images.velog.io/images/3hee_11/post/c94ca872-0894-4d1c-b32c-c184594ced74/image.png)

### Arrays 클래스	
- Arrays 클래스는 **배열 조작 기능**을 가지고 있다.
- 배열 조작 기능 :  **복사, 항목 정렬, 항목 검사**
- Array 클래스의 **모든 메소드는 정적**이므로 바로 사용이 가능하다.
![](https://images.velog.io/images/3hee_11/post/354c6d2d-77f9-43cc-83de-1d4296951637/image.png)

#### 배열 복사
- **Arrays.copyOf(원본 배열, 복사할 길이)**
  - 0번 ~ (복사할 길이 - 1)까지 항목을 복사한다.
  - 복사할 길이는 원본의 길이보다 커도 되며 타겟의 길이가 된다.
- **copyOfRange(원본 배열, 시작 인덱스, 끝 인덱스)**
  - 원본 배열의 시작부터 끝까지 복사한다. 
  - 시작 인덱스는 포함되지만 끝 인덱스는 포함되지 않는다.
- **System.arraycopy()**
  - 단순히 배열을 복사하는 것이 목적일 때 사용한다.
![](https://images.velog.io/images/3hee_11/post/87634bd8-cb3c-43f7-810a-1d712945d562/image.png)

#### 배열 항목 비교
- **Arrays.equals(배열, 배열)**는 1차 항목의 값만 비교한다.
- **Arrays.deepEquals(배열, 배열)**는 중첩된 배열의 항목까지 비교한다.

#### 배열 항목 정렬
- **Arrays.sort()**
  - 기본 타입, String 배열을 **오름차순으로 자동 정렬**해준다.
  - 사용자 정의 클래스 타입은 **Comparable 인터페이스를 구현**하고 있어야 정렬이 된다.

#### 배열 항목 검색
- 특정한 값이 위치한 인덱스를 얻는 것을 배열 검색이라고 한다.
- Arrays.sort()로 항목을 오름차순으로 정렬한 후 **Arrays.binarySearch(배열, 찾는 값)**로 항목을 찾는다.

### Wrapper(포장) 클래스
- 포장(Wrapper)는 기본 타입의 값을 내부에 갖는 객체를 말한다.
- **포장하고 있는 기본 타입 값은 외부에서 변경할 수 없다.**
  ![](https://images.velog.io/images/3hee_11/post/86c6083b-d884-42c5-a95f-6f799ce467bd/image.png)
  
#### 박싱(Boxing)과 언박싱(UnBoxing)
- Boxing 👉 기본 타입의 값을 포장 객체로 만드는 과정
- UnBoxing 👉 포장 객체에서 기본 타입의 값을 얻는 과정

**박싱(Boxing) 하는 법**
- 생성자 이용
![](https://images.velog.io/images/3hee_11/post/80724fd7-5988-4148-93bd-6d64590933ea/image.png)
- valueOf() 이용
![](https://images.velog.io/images/3hee_11/post/1d2b5c56-7609-42fb-b2b8-02cff29b2f32/image.png)

**언박싱(Unboxing) 하는 법**
- 기본 타입명 + Value() 이용
![](https://images.velog.io/images/3hee_11/post/902f1bee-dc27-4298-b508-bfbb60d4b959/image.png)

#### 자동 박싱과 언박싱
- 자동 박싱은 포장 클래스 타입에 기본값이 대입될 경우 발생한다.
```java
Integer obj = 100; // 자동 박싱
```
- 자동 언박싱은 기본 타입에 포장 객체가 대입될 경우 발생한다.
```java
Integer obj = new Integer(200);
int value1 = obj; // 자동 언박싱
int value2 = obj + 100; // 자동 언박싱
```

#### 문자열을 기본 타입 값으로 변환
- parse + 기본 타입 👉 정적 메소드
![](https://images.velog.io/images/3hee_11/post/4177ec6d-1226-45ff-b1e1-c35b07451d1d/image.png)

#### 포장 값 비교
- 포장 객체는 내부의 값 비교시 ==와 != 연산자를 사용할 수 없다.
👉 언박싱 혹은 equals() 메소드 사용

### Math, Random 클래스

#### Math 클래스
- 수학 계산에 사용할 수 있는 정적 메소드를 제공한다.
![](https://images.velog.io/images/3hee_11/post/e5241581-a47b-41d3-91bb-d7210e176966/image.png)

#### Random 클래스
- boolean, int, long, float, double 난수를 얻을 수 있다.
- Random 클래스는 종자값을 설정할 수 있다.
👉 종자값이 같으면 같은 난수

**Random 클래스로부터 Random 객체 생성**
![](https://images.velog.io/images/3hee_11/post/f7cc9a3b-550b-4140-8013-d5fc067eac6b/image.png)

**Random 클래스 제공 메소드**
![](https://images.velog.io/images/3hee_11/post/aba788b0-61dd-4f8e-9223-fdeb51da11f4/image.png)

### Date, Calendar 클래스

#### Date 클래스
- 날짜를 표현하는 클래스로 객체 간에 날짜 정보를 주고 받을 때 주로 사용한다.
```java
public class DateExample{
	public static void main(String[] args){
    	Date now = new Date();
        String strNow1 = now.toString();
        
        SimpleDateFormat sdg = new SimpleDateFormat
        		      ("yyyy년 MM월 dd일 mm분 ss초");
        String strNow2 = sdf.format(now);
    }
}
```

#### Calendar 클래스
- 달력을 표현한 클래스이며 추상 클래스이므로 new 연산자로 인스턴스를 생성할 수 없다.
```java
Calendar now = Calendar.getInstance();
```
![](https://images.velog.io/images/3hee_11/post/002d1639-bf16-4356-9489-d2bd5af4bf33/image.png)

- 다른 시간대의 Calendar 객체 얻기
![](https://images.velog.io/images/3hee_11/post/9fb84fdb-395b-410d-9d3b-974fc46ec14d/image.png)

### Format 클래스
- 숫자와 날짜를 원하는 형식의 문자열로 변환한다. 

#### 숫자 형식 클래스(DecimalFormat)
- DecimalFormat 생성자를 매개값으로 지정해서 객체를 생성하고 format 메소드를 호출해 패턴을 적용한다.
![](https://images.velog.io/images/3hee_11/post/561df346-f4fa-4ef9-ad34-17171e71365c/image.png)

#### 날짜 형식 클래스(SimpleDateFormat)
![](https://images.velog.io/images/3hee_11/post/34babde0-d9e0-43c4-9a97-307dbda23cf6/image.png)

#### 문자열 형식 클래스(MessageFormat)
![](https://images.velog.io/images/3hee_11/post/bb710575-671b-4c62-9514-62a77bbcad2e/image.png)

### java.time 패키지
- java8부터 날짜와 시간을 나타내는 여러 API가 추가됐다.
![](https://images.velog.io/images/3hee_11/post/c1031e33-3b1a-4b5b-a35f-8acbbe3b40be/image.png)

#### 날짜와 시간 객체 생성
![](https://images.velog.io/images/3hee_11/post/07086a3c-fa97-4801-ab9f-2a729faa5a2c/image.png)

