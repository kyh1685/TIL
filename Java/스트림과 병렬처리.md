## 스트림

**스트림은 컬렉션의 저장 요소를 하나씩 참조해 람다식으로 처리할 수 있도록 도와주는 반복자**이다(자바8부터 추가)

### 반복자 스트림
자바7 이전까지는 List< String > 컬렉션에서 요소를 순차적으로 처리하기 위해 Iterator 반복자를 사용했다.

```java
/* Iterator */
List<String> list = Arrays.asList("홍길동", "김자바");
Iterator<String> iterator = list.iterator();
while(iterator.hasNext()){
	String name = iterator.next();
}

/* Stream */
List<String> list = Arrays.asList("홍길동", "김자바");
Stream<String> stream = list.stream();
stream.forEach( name -> System.out.println(name));
```

### 스트림의 특징
- 람다식으로 요소 처리 코드를 제공
- 내부 반복자를 사용하므로 병렬 처리가 쉬움
![](https://images.velog.io/images/3hee_11/post/21d7c25a-a9da-43ca-aec7-d56f2084a87a/image.png)
- 중간 처리와 최종 처리 가능
  - **중간 처리에서는 매핑, 필터링, 정렬**을 수행하고 **최종 처리에서는 반복, 카운팅, 평균, 총합 등의 집계 처리**를 수행

## 스트림의 종류
자바8부터 java.util.stream 패키지에서 스트림 API들을 제공한다.
![](https://images.velog.io/images/3hee_11/post/c10b584b-ef38-44f6-8574-3084c6ae6b7d/image.png)

![](https://images.velog.io/images/3hee_11/post/0ac2ce9c-0fb6-49f5-9b55-01268567d100/image.png)

### 컬렉션으로부터 스트림 얻기
```java
List<Studunt> studentList = Arrays.asList(
	new Student("홍길동", 10), 
    	new Student("김자바", 20)
);

Stream<Student> stream = studentList.stream();
```

### 배열로부터 스트림 얻기
```java
String[] strArray = {"홍길동", "김자바"};
Stream<String> strStream = Arrays.stream(strArray);
```

### 숫자 범위로부터 스트림 얻기
```java
// rangeClosed() : 첫번째 인자값에서 두번째 인자값까지 순차적으로 IntStream 리턴
IntStream stream = IntStream.rangeClosed(1, 100);
```

### 파일로부터 스트림 얻기
```java
// 파일의 경로 정보를 가지고 있는 Path 객체 생성
Path path = Paths.get("src/sec02/stream_kind/lineddata.txt");

// Charset.defaultCharset()는 운영체제의 기본 문자셋
Stream<String> stream = Files.lines(path, Charset.defaultCharset());

// BufferedReader의 lines() 이용
File file = path.toFile();
FileReader fileReader = new FileReader(file);
BufferedReader br = new BufferedReader(fileReader);
stream = br.lines();
```

### 디렉토리로부터 스트림 얻기
```java
Path path = Paths.get("C:/JavaProgramming/source");
Stream<Path> stream = Files.list(path);
stream.forEach( p -> System.out.println(p.getFileName()));
```

## 스트림 파이프라인

**리덕션**
대량의 데이터를 가공해서 축소하는 것을 말한다.
예) 데이터의 합계, 평균, 최대값, 카운팅 등

🚨 컬렉션의 요소를 리덕션의 결과물로 바로 집계할 수 없는 경우
👉 리덕션이 집계하기 좋도록 필터링, 매핑, 정렬, 그룹핑 등 중간 처리 필요

### 중간 처리와 최종 처리
스트림은 데이터의 **중간 처리와 최종 처리를 파이프라인(여러 개의 스트림이 연결된 구조)로 해결**한다.

_최종 처리 시작 전까지 중간 처리는 지연된다._
![](https://images.velog.io/images/3hee_11/post/2cea1372-4e24-40ed-8235-2e770563c61b/image.png)

예) 회원 컬렉션에서 남자 평균 나이 집계하기
```java
double ageAvg = list.stream()  			// 오리지날 스트림
    .filter(m -> m.getSex() == Member.MALE)  	// 중간 처리 스트림
    .mapToInt(Member :: getAge)  		// 중간 처리 스트림
    .average()  				// 최종 처리
    .getAsDouble
```

## 중간 처리 메소드

### 필터링
요소들을 걸러내는 역할을 하며 distinct(), filter()는 모든 스트림이 가지고 있는 메소드이다.

<img src="https://images.velog.io/images/3hee_11/post/89f3d4c4-24f2-4d75-b508-ccd3b623691a/image.png">

### 매핑
스트림의 요소를 다른 요소로 대체하는 작업을 말한다.

- **flatMapXXX()** : 요소를 대체하는 복수 개의 요소들로 구성된 새로운 스트림 리턴
![](https://images.velog.io/images/3hee_11/post/7cc647f0-f495-4fb8-9ed7-b45808931b39/image.png)

![](https://images.velog.io/images/3hee_11/post/3609d32f-744f-4dae-a29f-76f8c0cb0ede/image.png)
```java
List<String> inputList1 = Arrays.asList("java8 lamda", "stream mapping");
inputList1.stream().flatMap(data -> Arrays.stream(data.split(" ")));

List<String> inputList2 = Arrays.asList("10, 20, 30", "40, 50, 60");
inputList2.stream()
    .flatMapToInt(data -> {
        String strArr = data.split(",");
        int intArr = new int[strArr,length];
        for(int i = 0; strArr > i; i++){
        	intArr[i] = Integer.parseInt(strArr[i].trim());
        }
        return Arrays.stream(intArr);
    })
```
- **mapXXX()** : 요소를 대체하는 요소들로 구성된 새로운 스트림을 리턴
![](https://images.velog.io/images/3hee_11/post/6d3900a3-b155-4ed1-9576-bf086b558578/image.png)
![](https://images.velog.io/images/3hee_11/post/f4c0160b-7ce9-4458-86ad-4cef26399372/image.png)
```java
List<Studunt> studentList = Arrays.asList(
	new Student("홍길동", 10), 
    	new Student("김자바", 20)
);

studentList.stream().mapToInt(Student :: getScore);
```
- **asDoubleStream(),asLongStream()** : IntStream의 int 요소 또는 LongStream의 Long 요소를 double 요소로 타입 변환해서 DoubleStream을 생성한다.
- **boxed()** : int, double 요소를 Integer, Double 요소로 박싱해서 Stream을 생성한다.

### 정렬
요소가 최종 처리되기 전에 중간 단계에서 요소를 정렬한다.
![](https://images.velog.io/images/3hee_11/post/66669efb-76e1-4705-89f5-540c925c5bc8/image.png)

🚨 객체 요소일 경우 클래스가 Comparable을 구현하지 않으면 sorted()를 호출했을 때 예외가 발생한다.

❔ **기본 비교 방법으로 정렬하고 싶을 때**
```java
sorted();
sorted( (a,b) -> a.compareTo(b) );
sorted(Comparator.naturalOrder());
```

❔ **기본 비교 방법과 정반대 방법으로 정렬하고 싶을 때**
```java
sorted( (a,b) -> b.compareTo(a) );
sorted( Comparator.reversOrder() );
```

❔ **객체 요속 Comparable을 구현하지 않았을 때**
```java
// Comparator를 매개값으로 갖는 sorted() 사용
// a가 작으면 음수, 같으면 0, a가 크면 양수 리턴
sorted( (a,b) -> {...})
```

### 루핑
요소 전체를 반복하는 것을 말하며 **peek()는 중간 처리 메소드**이고 **forEach()는 최종 처리 메소드**이다.

## 최종 처리 메소드

### 매칭
- **allMatch()** : 모든 요소들이 매개값으로 주어진 Predicate의 조건을 만족하는지 조사
- **anyMatch()** : 최소한 1개의 요소가 매개값으로 주어진 Predicate의 조건을 만족하는지 조사
- **noneMatch()** : 모든 요소들이  매개값으로 주어진 Predicate의 조건을 만족하지 않는지 조사

### 기본 집계
요소들을 처리해서 카운팅, 합계, 최대값 등과 같이 하나의 값으로 산출하는 것이다.

- **스트림이 제공하는 기본 집계**
![](https://images.velog.io/images/3hee_11/post/c1eb06a1-ef7f-47ad-a93e-4d031ea103db/image.png)
_OptionalXXX 타입에서 값을 얻으려면 get(), getAsXXX()를 호출한다._

- Optional 클래스
  - 집계 값을 저장하며 집계 값이 존재하지 않을 경우 디폴트 값을 설정할 수 있다. 
  - 집계 값을 처리하는 Consumer도 등록할 수 있다.
  ![](https://images.velog.io/images/3hee_11/post/90ad45d0-6b59-4583-b7e3-ffb66734d7f2/image.png)
 
  ```java
  // isPresent()
  OptionalDoble optional = list.stream().mapToInt(Integer :: intValue).average();
  
  if(optional.isPresent()){
  	~
  }else{
  	~
  }
  
  // orElse()
  double avg = list.stream().mapToInt(Integer :: intValue).average().orElse(0.0);
  
  // ifPresent() : 값이 있을 경우 람다식 실행
  list.stream().mapToInt(Integer :: intValue).average().ifPresent(a -> ~);
  ```
### 커스텀 집계
- reduce() : 다양한 집계 결과를 만드는 메소드
![](https://images.velog.io/images/3hee_11/post/c1b1ca3b-5a01-4a73-abbf-2f790c5137f9/image.png)

```java
// 요소가 없을 경우 NoSuchElementException 발생
int sum = studentList.stream()
	.map(Student :: getScore)
	.reduce((a,b) -> a + b)
	.get();
    
// 요소가 없어도 default 값 0 리턴    
int sum = studentList.stream()
	.map(Student :: getScore)
	.reduce(0, (a,b) -> a + b)
	.get();
```

### 수집(collect())
**collect()** : 요소를 필터링, 매핑한 후 컬렉션에 수집하는 최종 처리 메소드
```java
// 매개값인 Collector는 어떤 요소를 수집할 것인지 결정
// 타입 파라미터의 T는 요소, A는 누적기, R은 요소가 저장될 컬렉션
R(리턴타입) | collect(Collector<T,A,R> collector) | Stream(인터페이스)
```
**Collector의 구현 객체를 얻을 수 있는 Collectors의 정적 메소드**
![](https://images.velog.io/images/3hee_11/post/ce9c6d2d-76a9-43b2-979c-b1ef7f7c7a40/image.png)
#### 사용자 정의 컨테이너에 수집![](https://images.velog.io/images/3hee_11/post/6c79bf8e-9a6a-4fd3-ac5d-5f7ed30127ad/image.png)
- 첫 번째 Supplier는 요소들이 수집된 컨테이너 객체를 생성한다.
    - **순차 처리(싱글 스레드) 스트림** : 한번 실행, 하나의 컨테이너 객체 생성
    - **병렬 처리(멀티 스레드) 스트림** : 여러번 실행, 스레드 별로 여러 개 생성 👉 최종적으로 하나의 컨테이너 객체로 결합
- 두번 째 XXXConsumer는 컨테이너 객체(R)에 요소(T)를 수집한다.
  - 스트림에서 요소를 컨테이너에 수집할 때마다 실행
- 세번 째 XXXConsumer는 컨테이너 객체(R)를 결합한다(병렬 처리만 호출)
```java
// 기본
Stream<Student> totalStream = totalList.stream();
Stream<Student> maleStream = totalList.filter(s -> s.getSex() == Student.Sex.MALE);
Supplier<MaleStudent> supplier = ()->new MaleStudent();
BiConsumer<MaleStudent, Student> accumulator = (ms, s) -> ms.accumulate(s);
BiConsumer<MaleStudent, MaleStudent> combiner = (ms1, ms2) -> ms1.combine(ms2);
MaleStudent maleStudent = maleStream.collect(supplier, accumulator, combiner);

// 변수 생략
MaleStudent maleStudent = totalList.stream()
	.filter(s->s.getSex() == Student.Sex.MALE)
    .collect(
    	() -> new MaleStudent(),
        (r, t) -> r.accumulate(t),
        (r1, r2) -> r1.combine(r2)
    );
    
// 람다식 메소드 참조
MaleStudent maleStudent = totalList.stream()
	.filter(s -> s.getSex() == Student.Sex.MALE)
    .collect(MaleStudent :: new
    	   , MaleStudent :: accumulate
           , MaleStudent :: combine);
```
#### 요소를 그룹핑해서 수집
collect()는 컬렉션의 요소들을 그룹핑해서 **Map 객체를 생성**하는 기능도 제공
![](https://images.velog.io/images/3hee_11/post/bd315784-aeec-4ed0-9751-fa38dd06369e/image.png)

```java
Map<Student.Sex, List<Student>> mapBySex = totalList.stream()
	.collect(Collectors.groupingBy(student :: getSex));
```

#### 그룹핑 후 매핑 및 집계
그룹핑 후 매핑, 집계를 할 수 있도록 두 번째 매개값으로 Collector를 가질 수 있다.
![](https://images.velog.io/images/3hee_11/post/acf59818-61ea-4ab2-959a-9ea1ce0c0d49/image.png)
```java
Map<Student.Sex, Double> mapBySex = totalList.stream()
	.collect(
    	Collectors.groupingBy{
        	Student :: getSex,
            Collectors.averagingDouble(Student :: getScore)
        }
    );
```	

### 병렬 처리
- 병렬 처리 : 멀티 코어 CPU 환경에서 하나의 작업을 분할해서 각각의 코어가 병렬적으로 처리하는 것
- 목적 : **작업 처리 시간 감소**

#### 동시성(Concurrency)과 병렬성(Parallelism)
- 동시성 : 멀티 작업을 위해 멀티 스레드가 **번갈아가며 실행**하는 성질
- 병렬성 : 멀티 작업을 위해 **멀티 코어를 이용해 동시에 실행**하는 성질
![](https://images.velog.io/images/3hee_11/post/49a878e1-b542-48f4-8dcb-eb10fb99a5a9/image.png)

💡 **병렬성은 데이터 병렬성과 작업 병렬성로 구분**할 수 있다.
- **데이터 병렬성** : 전체 데이터를 쪼개어 서브 데이터로 만들고, 서브 데이터들을 병렬 처리해서 작업을 빠르게 끝내는 것
  - 자바8이 지원하는 병렬 스트림은 데이터 병렬성을 구현한 것
  - 멀티 코어의 수만큼 대용량 요소를 서브 요소로 나누고, 각각의 서브 요소들을 분리된 스레드에서 병렬처리 시킴 
- **작업 병렬성** : 서로 다른 작업을 병렬 처리하는 것
  - 예) 웹 서버, 각 브라우저에서 요청한 것을 개별 스레드에서 병렬로 처리

#### 포크조인(ForkJoin) 프레임워크
- 병렬 스트림을 이용하면 런타임 시에 포크조인 프레임워크 동작
- **포크 단계** : 전체 데이터를 서브 데이터로 분리한 후 서브 데이터를 멀티 코어에서 병렬로 처리
- **조인 단계** : 서브 결과를 결합해서 최종 결과를 낸다.
![](https://images.velog.io/images/3hee_11/post/93d7962e-4571-4a3e-a61c-84e9f51e57af/image.png)
- 포크조인 프레임워크는 스레드풀인 ForkJoinPool을 제공한다.
👉 각 코어에서 서브 요소를 처리하는 것은 개별 스레드가 해야 하므로 스레드 관리 필요

#### 병렬 스트림 생성![](https://images.velog.io/images/3hee_11/post/1c57bb63-f99f-4a0e-8671-ce734b62bf1b/image.png)
- parallelStream()은 컬렉션으로부터 병렬 스트림을 바로 리턴한다.
- parallel()는 순차 처리 스트림을 병렬 처리 스트림으로 변환해서 리턴한다.

#### 병렬 처리 성능
- 요소의 수와 요소당 처리 시간
  - 컬렉션에 요소의 수가 적고 각 요소의 처리 시간이 짧으면 순차 처리가 더 빠를 수 있다. 
  👉 병렬 처리는 스레드풀 생성, 스레드 생성이라는 추가적인 비용 발생
- 스트림 소스의 종류
  - ArrayList, 배열은 인덱스로 요소를 관리하기 때문에 쉽게 분리할 수 있어 병렬 처리 시간이 절약된다.
  - HashSet, TreeSet, LinkedList는 요소 분리가 쉽지 않아 상대적으로 느리다.
- 코어의 수
  - 싱글 코어일 경우 순차 처리가 빠르다. 병렬 스트림 사용 시 스레드의 수만 증가하고 동시성 작업으로 처리되어 좋지 않다.
