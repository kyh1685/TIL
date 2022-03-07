_스프링부트와 AWS로 혼자 구현하는 웹서비스 책을 정리한 내용입니다._

## 템플릿 엔진
<span style='padding: 0px 7px; background-color: #fff5b1; border-radius: 4px'>지정된 양식과 데이터가 합쳐져 HTML 문서를 출력하는 소프트웨어</span>
예) JSP, Freemarker, 리액트, 뷰 등

### 🤔 서버 템플릿 엔진 VS 클라이언트 템플릿 엔진
- JSP를 비롯한 서버 템플릿 엔진은 <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>서버에서 구동</span>된다.
- 서버 템플릿 엔진을 이용한 화면 생성은 <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>서버에서 Java 코드로 문자열을 만든 뒤 이 문자열을 HTML로 변환하여 브라우저로 전달</span>한다.
- Vue.js나 Reat.js를 이용한 SPA는 <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>브라우저에서 화면을 생성</span>한다.
- 서버에서는 Json 혹은 Xml형식의 데이터만 전달하고 클라이언트에서 조립한다.

### 머스테치
<span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>수많은 언어를 지원하는 가장 심플한 템플릿 엔진</span>

#### 장점
- 문법이 다른 템플릿 엔진보다 심플함
- 로직 코드를 사용할 수 없어 View와 서버의 역할이 명확하게 분리됨
- 하나의 문법으로 클라이언트/서버 템플릿을 모두 사용 가능

#### 플러그인 설치
<span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>Setting → Plugin → Marketplace</span>에 mustache 검색
![](https://images.velog.io/images/3hee_11/post/d9b6aa74-052e-4baa-bc14-0f45b82c8cd1/image.png)

#### 의존성 등록
```java
// build.gradle
implementation('org.springframework.boot:spring-boot-starter-mustache')
```

> **머스테치의 기본 파일 위치는 <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>src/main/resources/templates</span>이다.**

- templates 안에 index.mustache 생성 URL을 매핑할 IndexController 생성
- IndexControllerTest로 "/" 호출 시 index.mustache에 포함된 코드가 있는지 확인

## 등록

**프론트엔드 라이브러리 사용 방법**
- 외부 CDN 사용 👉 실무에서 사용하지 않음
- 직접 라이브러리 받아서 사용

**레이아웃 방식**
공통 영역을 별도의 파일로 분리하여 필요한 곳에 가져다 쓰는 방식
- src/main/resources/templates 디렉토리에 layout 디렉토리 생성
- footer.mustache, header.mustache 파일을 생성

**<span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>{{<layout/header}}</span>** : {{> }}는 현재 머스테치 파일을 기준으로 다른 파일을 가져온다.

>**📌 css와 js의 위치가 다른 이유는 페이지 로딩속도를 높이기 위함**
>
- HTML은 위부터 코드가 실행되기 때문에 head가 다 실행되고 body가 실행됨
- head가 다 불러지지 않으면 백지가 노출되는데, js의 용량이 클수록 body 부분의 실행이 늦어짐
👉 js를 body 하단에 두어 화면이 다 그려진 뒤 호출하는 것이 좋음
- 반면 css는 화면을 그리는 역할이므로 head에서 부르는게 좋음
  
- index.mustache에 글 등록 버튼 추가
- indexController에 버튼 클릭시 호출되는 메소드 추가
- 게시글을 작성하는 post-save.mustache 파일 생성
- src/main/resources/static/js/app 디렉토리 생성
- index.js파일 생성
- index.js를 footer.mustache에 추가
  
>**📌 index.js에서 var main = {}을 선언한 이유는 유효범위를 만들기 위함**
>
- 브라우저의 스코프는 공용 공간으로 쓰이기 때문에 나중에 로딩된 js가 먼저 로딩된 function을 덮어쓰게 된다.
- 여러 사람이 참여하는 프로젝트는 중복된 함수 이름이 자주 발생하기 때문에 index.js만의 유효범위를 만들어 사용
👉 이렇게 하면 index.js 객체 안에서만 함수가 유효하기 때문에 중복이 방지됨
>  
**📌 스프링부트는 기본적으로 src/main/resources/static에 위치한 js, css, 이미지 등 정적 파일들은 URL에서 /로 설정**

## 조회
- index.mustache UI 변경
- Controller, Service, Repository 코드 추가
- PostsListResponseDto 클래스 생성
  
## 수정
- templates에 수정 화면인 posts-update.mustache 파일 생성
- 수정 버튼 클릭시 호출한 update 기능 index.js에 추가
- index.mustache에서 제목 클릭시 수정화면으로 가도록 수정 
- indexController에 수정 화면으로 이동하는 메소드 추가
  
## 삭제
- posts-update.mustache에 삭제 버튼 추가
- index.jsdp 삭제 이벤트 추가
- service, PostApiController에 코드 추가 
