# HTTP 메서드 활용

_김영한 강사님의 모든 개발자를 위한 HTTP 웹 기본 지식 강의를 정리한 내용입니다._

## 클라이언트에서 서버로 데이터 전송

### 데이터 전달 방식

#### 쿼리 파라미터를 통한 데이터 전송
- GET
- 주로 조회, 검색, 정렬

#### 메시지 바디를 통한 데이터 전송
- POST, PUT, PATCH
- 회원 가입, 상품 주문, 리소스 등록, 리소스 변경

### 대표적인 4가지 상황

#### 정적 데이터 조회
- 이미지, 정적 텍스트 문서
- 쿼리 파라미터 미사용, GET /static/star.jpg

#### 동적 데이터 조회 
- 주로 검색, 게시판 목록에서 정렬 필터(검색어)
- 쿼리 파라미터 사용, GET /search?q=hello&hl=ko

#### HTML Form을 통한 데이터 전송
- 회원 가입, 상품 주문 등
- **Content-Type: application/x-www-form-urlencoded**
![](https://images.velog.io/images/3hee_11/post/07da760d-44d6-4ef0-9b7a-e0ad114e98c4/image.png)
- **Content-Type: multipart/form-data** : 바이너리 데이터 전송시 사용
![](https://images.velog.io/images/3hee_11/post/c58baa02-7dc0-4433-a0ee-d4eb6667a8c4/image.png)

🚨 **GET의 쿼리 파라미터로도 가능하지만 GET은 조회에만 사용! 리소스 변경에 사용하면 안됨!**

#### HTTP API를 통한 데이터 전송
- 회원 가입, 상품 주문 등
- 서버 to 서버, 앱 클라이언트, 웹 클라이언트(Form 대신 자바스크립트를 통한 통신에 사용, Ajax)
- POST, PUT, PATCH : 메시지 바디를 통해 데이터 전송
- GET : 조회, 쿼리 파라미터로 데이터 전달
- Content-Type: **application/json을 주로 사용**

## HTTP API 설계 예시

### HTTP API - 컬렉션
<img src="https://images.velog.io/images/3hee_11/post/00007505-a9a5-4230-be24-c120428fab7f/image.png" width="70%">

- **POST 기반 등록**
예) 회원 관리 API 제공
- **신규 자원 등록 특징 : 클라이언트는 리소스의 URI를 모르고 서버가 새로 등록된 리소스 URI를 생성해준다.**
- **컬렉션**
  - 서버가 관리하는 리소스 디렉토리(/members)
  - 서버가 리소스의 URI를 생성하고 관리

### HTTP API - 스토어
<img src="https://images.velog.io/images/3hee_11/post/d4a5565a-7644-4853-a95c-d514fe8d2239/image.png" width="60%">

- **PUT 기반 등록**
예) 정적 컨텐츠 관리, 원격 파일 관리
- **신규 자원 등록 특징 : 클라이언트가 리소스 URI를 알고 있고 직접 지정한다.**
예) 파일은 저장할 때 파일명을 알고 있어야 함 / 파일 등록 /files/{filename}
- **스토어(Store)**
  - 클라이언트가 리소스의 URI를 알고 관리하는 리소스 저장소(/files)


### HTML FORM 사용
<img src="https://images.velog.io/images/3hee_11/post/0640f14f-1bfb-4d67-b930-926c52a4c912/image.png" width="70%">

- GET, POST만 지원한다.
- **컨트롤 URI** 
  - GET, POST만 지원하므로 제약이 있음
  👉 제약을 해결하기 위해 동사로 된 리소스 경로 사용
- POST의 /new, /edit, /delete가 컨트롤 URI
- HTTP 메서드로 해결하기 애매한 경우 사용(HTTP API 포함)
