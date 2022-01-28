# URI와 웹 브라우저 요청 흐름

_김영한 강사님의 모든 개발자를 위한 HTTP 웹 기본 지식 강의를 정리한 내용입니다._

## URI(Uniform Resource Identifier)
<img src="https://images.velog.io/images/3hee_11/post/e26cbbb6-1437-44f5-b221-cd8b6809551b/image.png" width="70%">

- URI는 로케이터(locator), 이름(name) 또는 둘 다 추가로 분류될 수 있다.

### URI 뜻
- **Uniform** : 리소스 식별하는 통일된 방식
- **Resource** : 자원, URI로 식별할 수 있는 모든 것(제한 없음)
- **Identifier** : 다른 항목과 구분하는데 필요한 정보

### URL, URN 뜻
- **URL - Locator**: 리소스가 있는 위치를 지정
- **URN - Name**: 리소스에 이름을 부여
- URN 이름만으로 실제 리소스를 찾을 수 있는 방법이 보편화 되지 않음

### URL 전체 문법
<img src="https://images.velog.io/images/3hee_11/post/ce6071c2-6a53-4925-a470-7f0359d508f3/image.png" width="70%">

- 프로토콜(https)
- 호스트명(www.google.com)
- 포트 번호(443)
- 패스(/search)
- 쿼리 파라미터(q=hello&hl=ko)

#### scheme
-  주로 프로토콜 사용
- 프로토콜 : 어떤 방법으로 자원에 접근할 것인지 약속 규칙	
예) http, https, ftp 등
- http는 80포트, https는 443포트 주로 사용, 포트 생략 가능

#### userinfo
- URL에 사용자정보를 포함해서 인증

#### host
- 호스트명
- 도메인명 또는 IP 주소를 직접 사용 가능

#### PORT
- 포트(PORT)
- 접속 포트
- 일반적으로 생략, 생략시 http는 80, https는 443

#### path
- 리소스 경로(path), 계층적 구조

#### query
- key = value 형태
- ?로 시작, &로 추가 가능 
예) ?keyA=valueA&keyB=valueB
- query parameter, query string 등으로 불림, 웹서버에 제공하는 파라미터, 문자 형태	

#### fragment
- html 내부 북마크 등에 사용
- 서버에 전송하는 정보 아님

## 웹 브라우저 요청 흐름
<img src="https://images.velog.io/images/3hee_11/post/20b3328a-50a4-49ba-869b-18a0ae95a711/image.png">

<img src="https://images.velog.io/images/3hee_11/post/092120d6-2424-4398-8e46-4d4ee2e9e8cb/image.png">
