### 람다식
람다식은 **익명 함수를 생성하기 위한 식으**로 객체 지향 언어보다 함수 지향 언어에 가깝다.
형태는 매개 변수를 가진 코드 블록이지만 **런타임 시에는 익명 구현 객체를 생성**한다.
> 람다식 → 매개 변수를 가진 코드 블록 → 익명 구현 객체

#### 자바에 람다식을 도입한 이유
1. 자바코드가 매우 간결해진다.
2. 컬렉션의 요소를 필터링하거나 매핑해서 결과를 쉽게 얻을 수 있다.

### 람다식 기본 문법
<img src="https://images.velog.io/images/3hee_11/post/333bd61f-c281-4bff-b7a1-5edd91b85eaf/image.png">

- 타입은 런타임 시 대입되는 값에 따라 인식되기 때문에 언급하지 않는다.
- 매개 변수가 하나거나 실행문이 하나라면 각각 괄호와 중괄호 생략이 가능하다.
- 매개 변수가 없다면 빈괄호를 반드시 사용한다.
- 결과값을 리턴해야 한다면 중괄호 안에 return문을 사용할 수 있다.
- 중괄호에 return문만 있을 경우 return과 중괄호를 생략한다.

### 타겟 타입과 함수적 인터페이스
람다식이 대입될 인터페이스를 람다식의 타겟 타입이라고 한다.

#### 함수적 인터페이스(@FunctionalInterface)
- **하나의 추상 메소드가 선언된 인터페이스를 함수적 인터페이스**라고 하며 함수적 인터페이스만 람다식의 타겟 타입이 될 수 있다.
- 인터페이스에 @FunctionalInterface 어노테이션을 붙이면 두개 이상의 추상 메소드가 선언되지 않도록 컴파일러가 체크해준다 → 선택 사항

#### 매개 변수와 리턴값이 없는 람다식
- method() 호출은 람다식의 중괄호를 실행시킨다.
<img src="https://images.velog.io/images/3hee_11/post/3f32e936-cd7b-4ad9-938f-3e3120b25262/image.png">

#### 매개 변수가 있고 리턴값이 없는 람다식
<img src="https://images.velog.io/images/3hee_11/post/8b526e53-c3e5-49f4-82ca-d1f43a0b3296/image.png">

#### 매개 변수와 리턴값이 있는 람다식
<img src="https://images.velog.io/images/3hee_11/post/757d322b-aa87-4f12-8a75-bee581c5b3e1/image.png">
<img src="https://images.velog.io/images/3hee_11/post/271011c8-b7c9-4056-aeac-678a65bad68f/image.png">

### 클래스 멤버와 로컬 변수 사용
람다식의 실행 블록에는 클래스의 멤버 및 로컬 변수를 사용할 수 있다.

#### 클래스 멤버 사용
🚨 람다식의 this 키워드는 **람다식을 실행한 객체의 참조**이다.

<img src="https://images.velog.io/images/3hee_11/post/87ef080d-ef0d-4dc1-9aa0-1d2b8f1f7650/image.png">

#### 로컬 변수 사용
람다식에서 사용하는 외부 로컬 변수는 final 특성을 가져야 한다.

### 표준 API의 함수적 인터페이스
- java.util.function 패키지로 제공한다.
- 매개타입으로 사용되어 람다식을 매개값으로 대입할 수 있도록 위함이다.
- 한 개의 추상 메소드를 가지는 인터페이스들은 모두 람다식 사용이 가능하다.
- 함수적 인터페이스는 인터페이스에 선언된 추상 메소드의 매개값과 리턴 유무 따라 구분된다.

#### Consumer
**매개값은 있고 리턴값이 없는** 추상메소드를 가진다.

<img src="https://images.velog.io/images/3hee_11/post/5c1797b6-4f1f-4aac-bc32-1102948a224a/image.png">

#### Supplier
**매개 변수가 없고 리턴값이 있는** 추상메소드를 가진다.

<img src="https://images.velog.io/images/3hee_11/post/d86de527-f454-4bda-a1c2-07bf3247a6ad/image.png">

#### Function 
**매개값과 리턴값이 있는** 추상메소드를 가진다.
**매개값을 리턴값으로 매핑(타입 변환)**한다.

<img src="https://images.velog.io/images/3hee_11/post/6fe05e1e-c575-4a45-8c49-53252a2ed9f2/image.png">

#### Operator
**매개값과 리턴값이 있는** 추상메소드를 가진다.
**매개값을 이용해 연산 후 동일한 타입으로 리턴**한다.

<img src="https://images.velog.io/images/3hee_11/post/33e83a64-4296-4c84-bf63-b80ad0783416/image.png">

#### Predicate
**매개값과 boolean 리턴값이 있는** 추상메소드를 가진다.
**매개값을 조사해 true 혹은 false를 리턴**한다.

<img src="https://images.velog.io/images/3hee_11/post/802e9313-bbe4-46fd-889e-de6e36d4e54e/image.png">

#### andThen()과 compose() 디폴트 메소드
- 함수적 인터페이스는 하나 이상의 디폴트 및 정적 메소드를 가지고 있다.
- 두 개의 함수적 인터페이스를 순차적으로 연결해 실행한다.
- 첫 번째 리턴값을 두 번째 매개값으로 제공해 최종 결과값 리턴한다.
- andThen()과 compose()의 차이점은 어떤 함수적 인터페이스부터 처리하느냐이다.

<img src="https://images.velog.io/images/3hee_11/post/2701585d-184a-4f63-9d88-98ffd9069fd5/image.png" width="70%">

<img src="https://images.velog.io/images/3hee_11/post/f5848f8c-6d19-459a-8df2-83f033c484a8/image.png"  width="70%">

**Consumer의 순차적 연결**
Consumer 종류의 함수적 인터페이스는 결과를 리턴하지 않기 때문에 andThen()은 함수적 인터페이스의 호출 순서만 정한다.

**Operator, Function 순차적 연결**
먼저 실행한 함수적 인터페이스의 결과를 다음 함수적 인터페이스의 매개값으로 넘겨주고 최종 값을 리턴한다.

#### and(), or(), negate() 디폴트 메소드와 isEqual() 정적 메소드
Predicate 종류의 함수적 인터페이스는 and(), or(), negate() 디폴트 메소드를 가진다.
이 메소드들은 각각 논리 연산자인 &&,||,!과 대응된다.

<img src="https://images.velog.io/images/3hee_11/post/4d80388e-c195-4d0c-9af9-e4d4e53e8942/image.png">

Predicate< T > 함수적 인터페이스는 isEqual() 정적 메소드도 제공한다. 

<img src="https://images.velog.io/images/3hee_11/post/d05158b2-5eaf-4e9e-8f19-00a7016c7ba0/image.png">

#### minBy(), maxBy() 정적 메소드
매개값으로 제공되는 Comparator를 이용해 최대 T와 최소 T를 얻는 BinaryOperator< T>를 리턴한다.

<img src="https://images.velog.io/images/3hee_11/post/0e958772-d526-40d7-878b-18d7814a5cf7/image.png">

### 메소드 참조
메소드를 참조해서 **매개 변수의 정보 및 리턴 타입을 알아내** 람다식에 **불필요한 매개 변수를 제거**하는 것이 목적이다.

🚨 람다식은 종종 기존 **메소드를 단순히 호출**만 하는 경우가 많다.
```java
(left, right) -> Math.max(left, right);
```
👉 **메소드 참조 이용**
```java
Math :: max;
```

#### 정적 메소드와 인스턴스 메소드 참조
```java
// 정적 메소드 참조
클래스 :: 메소드


// 인스턴스 메소드 참조
참조 변수 :: 메소드
```

#### 매개 변수의 메소드 참조
- 외부 클래스 멤버인 메소드 외에 람다식에서 제공되는 a 매개 변수의 메소드를 호출하여 b 매개변수를 매개값으로 사용하는 경우
```java
// 메소드 참조 전
(a, b) -> { a.instanceMethod(b); }


// 메소드 참조 표현
클래스 :: instanceMethod
```

#### 생성자 참조
```java
// 메소드 참조 전
(a, b) -> { return new 클래스(a,b); }


// 생성자 참조 표현
클래스 :: new
```
🚩 생성자가 오버로딩되어 여러개인 경우 컴파일러는 함수적 인터페이스의 추상 메소드와 동일한 매개 변수 타입, 개수를 가지고 있는 생성자를 찾아 실행한다.
