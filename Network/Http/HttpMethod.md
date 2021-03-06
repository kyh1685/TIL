# HTTP 메서드

_김영한 강사님의 모든 개발자를 위한 HTTP 웹 기본 지식 강의를 정리한 내용입니다._

## API URI 설계
- 리소스를 식별하고 URI에 매핑 
👉 리소스는 회원 등록에서 회원 자체를 뜻함
- URI 계층 구조 활용(계층 구조상 상위를 컬렉션으로 보고 복수단어 사용 권장)
예) 회원 등록, /members/~
- 리소스와 행위를 분리
👉 리소스 : 회원, 행위 : 조회, 등록, 수정, 삭제

## HTTP 메서드의 종류

### GET
- 리소스를 조회한다.
- 서버에 전달해야 할 데이터는 쿼리 파라미터를 통해 전달한다.
- 메세지 바디를 통해 데이터를 전달할 수 있으나 지원하는 곳이 많지 않다.

### POST
- 요청한 데이터를 처리한다.
- 메세지 바디를 통해 서버로 요청 데이터를 전달한다.
👉 서버는 메시지 바디를 통해 들어온 데이터를 처리하는 **모든 기능을 수행**
- 주로 신규 리소스 등록, 프로세스 처리에 사용한다.

### PUT
- 리소스를 대체한다(없으면 생성)
- **클라이언트가 리소스 위치를 알고 URI를 지정**한다.
예) PUT /members/100 HTTP/1.1

🚨 **주의!**
리소스를 완전히 대체하기 때문에 만약 기존 /members/100이 username: young, age:20으로 되어 있을 때 PUT으로 보낸게 age:50이라면 username이 사라지고 age:50만 남게된다. **부분 변경 불가, 파일 덮어쓰기와 비슷함!**

### PATCH
- 리소스 부분 변경이 가능하다. 

### DELETE
- 리소스를 제거한다.

## HTTP 메서드의 속성
![](https://images.velog.io/images/3hee_11/post/37bcdfdf-d593-4cbb-bfca-1bf198099586/image.png)

### 안전(Safe Methods)
- 호출해도 **리소스를 변경하지 않으면** 안전하다고 한다.

### 멱등(Idempotent Methods)
- **몇 번을 호출해도 결과가 같을 경우**를 말한다.
- 멱등 메서드로는 GET, PUT, DELETE가 있다.
- POST는 데이터를 처리하기 때문에 중복되면 안된다.
- 활용 : 서버가 정상 응답을 못주었을 때 클라이언트가 같은 요청을 다시 해도 되는가? 판단 근거
- 멱등은 외부 요인으로 중간에 리소스가 변경되는 것 까지는 고려하지는 않는다.

### 캐시가능(Cacheable Methods)
- ** 응답 결과 리소스를 캐시해서 사용해도 되는지**의 여부이다.
- GET, HEAD, POST, PATCH가 캐시가능이지만 **GET, HEAD만 캐시로 사용**한다.
👉 POST, PATCH는 본문 내용까지 캐시 키로 고려해야 하는데 구현이 쉽지 않음
