# HTTP 헤더 

_김영한 강사님의 모든 개발자를 위한 HTTP 웹 기본 지식 강의를 정리한 내용입니다._

## 일반 헤더

### HTTP 헤더
> header-field = field-name ":" OWS field-value OWS (OWS:띄어쓰기 허용)

![](https://images.velog.io/images/3hee_11/post/4004e6a0-99fb-4844-b4bb-1b396f48bbe7/image.png)

#### HTTP 헤더 용도
- HTTP 전송에 필요한 모든 부가정보
예) 메시지 바디의 내용, 메시지 바디의 크기, 압축, 인증, 요청 클라이언트, 서버 정보, 캐시 관리 정보 등
- 필요시 임의의 헤더 추가 가능

### HTTP 바디
![](https://images.velog.io/images/3hee_11/post/293dce58-6ea5-4689-87ad-be666c1b2987/image.png)
- 메시지 본문(페이로드, payload)를 통해 표현 데이터 전달
- 표현은 요청이나 응답에서 전달할 실제 데이터
- 표현 헤더는 표현 데이터를 해석할 수 있는 정보 제공
  - 데이터 유형(html, json), 데이터 길이, 압축 정보 등

### 표현 
- **Content-Type**: 표현 데이터의 형식
  - 미디어 타입, 문자 인코딩
  예) text/html; charset=utf-8, application/json
- **Content-Encoding**: 표현 데이터의 압축 방식
  - 표현 데이터를 압축하기 위해 사용
  - 데이터를 전달하는 곳에서 압축 후 인코딩 헤더 추가
  - 데이터를 읽는 쪽에서 인코딩 헤더의 정보로 압축 해제
  예) gzip, deflate, identity
- **Content-Language**: 표현 데이터의 자연 언어
예) ko, en, en-US
- **Content-Length**: 표현 데이터의 길이
  - 바이트 단위
  - Transfer-Encoding(전송 코딩)을 사용하면 Content-Length를 사용하면 안됨
  
### 협상(콘텐츠 네고시에이션) 
클라이언트가 선호하는 표현 요청(요청시에만 사용)
- Accept: 클라이언트가 선호하는 미디어 타입 전달
- Accept-Charset: 클라이언트가 선호하는 문자 인코딩
- Accept-Encoding: 클라이언트가 선호하는 압축 인코딩
- Accept-Language: 클라이언트가 선호하는 자연 언어

#### 협상과 우선순위
- Quality Values(q) 값 사용
- 0~1, 클수록 높은 우선순위(생략시 1)
예) Accept-Language: ko-KR,ko;q=0.9,en-US;q=0.8,en;q=0.7
- 구체적인 것이 우선순위
예) Accept: text/*, text/plain, text/plain;format=flowed, */
- 구체적인 것을 기준으로 미디어 타입을 맞춘다.
예) Accept: text/*;q=0.3, text/html;q=0.7, text/html;level=1, text/html;level=2;q=0.4, */*;q=0.5
![](https://images.velog.io/images/3hee_11/post/af1c96f8-935c-4c26-b3de-f5bc4f1f5d7c/image.png)

### 전송 방식

- **단순 전송** : Content-Length
- **압축 전송** : Content-Encoding
- **분할 전송** : Transfer-Encoding 
- **범위 전송** : Range, Content-Range

### 일반 정보
- **From** : 유저 에이전트의 이메일 정보
  - 요청에서 사용
  - 잘 사용되지 않으며 주로 검색 엔진 같은 곳에서 사용
- **Referer** : 이전 웹 페이지 주소
  - 요청에서 사용
  - 현재 요청된 페이지의 이전 웹 페이지 주소
  - A -> B로 이동하는 경우 B를 요청할 때 Referer: A 를 포함해서 요청
  - Referer를 사용해서 유입 경로 분석 가능 
- **User-Agent** : 유저 에이전트 애플리케이션 정보
  - 요청에서 사용
  - 클리이언트의 애플리케이션 정보(웹 브라우저 정보, 등등)
  - 어떤 종류의 브라우저에서 장애가 발생하는지 파악 가능 
- **Server** : 요청을 처리하는 오리진 서버의 소프트웨어 정보
  - 응답에서 사용
  예) Server: Apache/2.2.22 (Debian)
- **Date** : 메시지가 생성된 날짜
  - 응답에서 사용
  
### 특별한 정보

- **Host** : 요청한 호스트 정보(도메인), **필수!**
  - 요청에서 사용
  - 하나의 IP 주소에 여러 도메인이 적용되어 있을 때 필요
- **Location** : 페이지 리다이렉션
  - 웹 브라우저는 3xx 응답의 결과에 Location 헤더가 있으면 해당 위치로 자동 이동(리다이렉트)
- **Allow** : 허용 가능한 HTTP 메서드
  - 405 (Method Not Allowed) 에서 응답에 포함해야함
  - Allow: GET, HEAD, PUT
- **Retry-After** : 유저 에이전트가 다음 요청을 하기까지 기다려야 하는 시간
  - 503 (Service Unavailable): 서비스가 언제까지 불능인지 알려줄 수 있음
  
### 인증 
- **Authorization** : 클라이언트 인증 정보를 서버에 전달
- **WWW-Authenticate** : 리소스 접근시 필요한 인증 방법 정의
  - 401 Unauthorized 응답과 함께 사용
  - WWW-Authenticate: Newauth realm="apps", type=1, 
 title="Login to \"apps\"", Basic realm="simple"

### 쿠키

🚨 **HTTP는 [무상태(Stateless) 프로토콜](https://velog.io/@3hee_11/HTTP-HTTP-%EA%B8%B0%EB%B3%B8)이다.** 클라이언트와 서버가 요청과 응답을 주고 받으면 연결이 끊어진다.
👉 클라이언트가 다시 요청하면 서버는 이전 요청을 기억하지 못한다.

#### 쿠키란?
- 사용자 로그인 세션 관리, 광고 정보 트래킹에 사용된다.
- 쿠키 정보는 항상 서버에 전송되기 때문에 네트워크 트래픽을 더 유발한다.
👉 최소한의 정보만 사용해야 함(세션 id, 인증 토큰)
🔎 서버 전송말고 웹 브라우저 내부에 저장하고 싶다면 웹 스토리지 참고 

💀 **주의** : 보안에 민감한 데이터는 저장하면 안됨(주민번호, 신용카드 번호 등)

#### 쿠키 사용
- **Set-Cookie** : 서버에서 클라이언트로 쿠키 전달
예) set-cookie: sessionId=abcde1234; expires=Sat, 26-Dec-2020 00:00:00 GMT; path=/; domain=.google.com; Secure
- **Cookie** : 클라이언트가 서버에서 받은 쿠키를 저장하고, HTTP 요청시 서버로 전달

**🍪 쿠키를 사용하면?**
클라이언트가 요청으로 데이터를 줬을 때 **서버는 쿠키 저장소에 데이터를 저장**한다. 다음에 **클라이언트가 요청할 때 쿠키 저장소를 조회**해서 조회된 쿠키를 포함하여 요청한다.

#### 생명 주기 
- **expires** : 만료일이 되면 쿠키 삭제
예) Set-Cookie: expires=Sat, 26-Dec-2020 04:39:21 GMT
- **max-age** : 0이나 음수를 지정하면 쿠키 삭제
예) Set-Cookie: max-age=3600 (3600초)
- 세션 쿠키: 만료 날짜를 생략하면 브라우저 종료시 까지만 유지
- 영속 쿠키: 만료 날짜를 입력하면 해당 날짜까지 유지

#### 도메인
- **명시** : 명시한 문서 기준 도메인 + 서브 도메인 포함
  - domain=example.org를 지정해서 쿠키 생성
  👉 example.org, dev.example.org도 쿠키 접근
- **생략** : 현재 문서 기준 도메인만 적용
  - example.org 에서 쿠키를 생성하고 domain 지정을 생략
  👉 example.org 에서만 쿠키 접근
  
#### 경로
- 적은 경로를 포함한 하위 경로 페이지만 쿠키 접근
- 일반적으로 path=/ 루트로 지정

#### 보안
- **Secure**
  - Secure를 적용하면 https인 경우에만 전송한다.
- **HttpOnly**
  - XSS 공격 방지
  - 자바스크립트에서 접근 불가(document.cookie)
  - HTTP 전송에만 사용
- **SameSite**
  - XSRF 공격 방지
  - 요청 도메인과 쿠키에 설정된 도메인이 같은 경우만 쿠키 전송

## 캐시와 조건부 요청

### 캐시 기본 동작

#### 🚨 캐시 없을 때
- 데이터에 변경이 없어도 매번 네트워크를 통해서 다운로드 해야 함
👉 인터넷 네트워크는 매우 느리고 비쌈
👉 브라우저 로딩 속도 느림

#### 💡 캐시 있을 때
- 캐시 가능 시간동안 네트워크를 사용하지 않아도 됨
👉 비싼 네트워크 사용량을 줄일 수 있음
👉 브라우저 로딩 속도가 매우 빠름

### 캐시 동작 과정
- 클라이언트가 이미지(예시)를 요청했을 때, 서버는 응답으로 이미지와 함께 캐시 유효 시간을 응답으로 줌
- 클라이언트는 응답 결과를 브라우저 캐시에 저장
- 똑같은 이미지를 재요청할 때, 클라이언트는 우선 브라우저 캐시의 캐시 유효 시간을 보고 유효하면 캐시에서 조회함
- 만약 유효 시간이 초과됐다면 서버를 통해 데이터를 다시 조회하고 캐시를 갱신

### 검증 헤더와 조건부 요청

#### 🤔 캐시 만료후에도 서버에서 데이터를 변경하지 않았다면?
- 데이터를 전송하는 대신에 저장해 두었던 캐시를 재사용 할 수 있음
- 대신 **클라이언트와 서버의 데이터가 같은지 확인** 해야함

#### 캐시 시간 초과
- 클라이언트가 데이터를 요청하고 서버가 응답할 때, 캐시 유효 시간 외에 최종 수정 시간(Last-Modified)도 함께 보낸다.
- 클라이언트는 응답 결과를 브라우저에 캐시에 저장한다.
- 클라이언트가 똑같은 데이터를 다시 요청할 때, 만약 캐시 유효 시간이 초과되었다면 캐시에서 최종 수정 시간을 꺼내서 요청에 if-modified-since에 넣어 보낸다.
- 만약 서버가 가진 최종 수정일과 요청으로 보낸 수정일이 동일하다면 304 Not Modified로 바디없이 응답한다.
- 클라이언트는 서버가 보낸 헤더 정보로 캐시 정보를 갱신하고 브라우저 캐시의 데이터를 재사용한다.

#### 📌 정리
- 캐시 유효 시간이 초과해도 서버의 데이터가 갱신되지 않으면 304 Not Modified + 헤더 메타 정보만 응답(바디X)
- 클라이언트는 서버가 보낸 응답 헤더 정보로 캐시의 메타 정보를 갱신
- 클라이언트는 캐시에 저장되어 있는 데이터 재활용
- 결과적으로 네트워크 다운로드가 발생하지만 용량이 적은 헤더 정보만 다운로드

### 검증 헤더
- 캐시 데이터와 서버 데이터가 같은지 검증하는 데이터
예) Last-Modified , ETag

### 조건부 요청 헤더
- 검증 헤더로 조건에 따른 분기
예) If-Modified-Since: Last-Modified, If-None-Match: ETag 사용
- 조건이 만족하면 200 OK, 만족하지 않으면 304 Not Modified

#### 🚨 Last-Modified, If-Modified-Since 단점
- 1초 미만 단위로 캐시 조정이 불가능하며 날짜 기반의 로직임
- 데이터를 수정해서 날짜는 다르지만 수정 결과가 똑같은 경우 판별이 안됨
- 주석처럼 크게 영향이 없는 변경일 경우 캐시를 유지하고 싶을 때도 변경됨

#### 💡 ETag, If-None-Match
- **ETag(Entity Tag)** : 캐시용 데이터에 임의의 고유한 버전 이름을 달아둠
예) ETag: "v1.0", ETag: "a2jiodwjekjl3"
- 데이터가 변경되면 이 이름을 바꾸어서 변경함(Hash를 다시 생성)
예) ETag: "aaaaa" -> ETag: "bbbbb"
- ** ETag만 보내서 같으면 유지, 다르면 다시 받음**
👉 캐시 제어 로직을 서버에서 완전히 관리

### 캐시와 조건부 요청 헤더

#### Cache-Control: 캐시 제어
- Cache-Control : max-age
  - 캐시 유효 시간, 초 단위
- Cache-Control : no-cache
  - 데이터는 캐시해도 되지만, 항상 원(origin) 서버에 검증하고 사용
- Cache-Control : no-store
  - 데이터에 민감한 정보가 있으므로 저장하면 안됨
  👉 메모리에서 사용하고 최대한 빨리 삭제
  
#### Pragma: 캐시 제어(하위 호환)
- Pragma: no-cache
  - HTTP 1.0 하위 호환
  
#### Expires: 캐시 유효 기간(하위 호환)
- 캐시 만료일을 정확한 날짜로 지정(HTTP 1.0 부터 사용)
- 현재는 Cache-Control: max-age 권장
👉 Cache-Control: max-age와 함께 사용하면 Expires는 무시

### 프록시 캐시

#### Cache-Control
- Cache-Control: public 
  - 응답이 public 캐시에 저장되어도 됨
- Cache-Control: private 
  - 응답을 private 캐시에 저장해야 함(기본값)
- Cache-Control: s-maxage 
  - 프록시 캐시에만 적용되는 max-age
- Age: 60 (HTTP 헤더)
  - 오리진 서버에서 응답 후 프록시 캐시 내에 머문 시간(초)
  
### 캐시 무효화

#### Cache-Control
- Cache-Control: no-cache 
- Cache-Control: no-store
- Cache-Control: must-revalidate 
  - 캐시 만료후 최초 조회시 원 서버에 검증해야함
  - 원 서버 접근 실패시 반드시 오류가 발생해야함 - 504(Gateway Timeout)
  - must-revalidate는 캐시 유효 시간이라면 캐시를 사용함
- Pragma: no-cache 

**🤔 no-cache과 must-revalidate의 차이점**
- no-cache는 원서버에 접근할 수 없는 경우, 서버 설정에 따라서 캐시 데이터를 반환할 수 있음(Error or 200 OK)
- must-revalidate는 원 서버에 접근할 수 없는 경우, 항상 오류가 발생해야 함(504 Gateway Timeout)
