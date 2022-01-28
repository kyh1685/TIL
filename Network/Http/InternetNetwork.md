# 인터넷 네트워크

_김영한 강사님의 모든 개발자를 위한 HTTP 웹 기본 지식 강의를 정리한 내용입니다._

<img src="https://images.velog.io/images/3hee_11/post/97c5772b-1ca3-413f-84a7-c07d8b1687f6/image.png" width="85%">

## IP(인터넷 프로토콜)
- 지정한 IP 주소(IP Address)에 데이터 전달
- 패킷(Packet)이라는 통신 단위로 전달
- IP 패킷 정보 : 출발지 IP, 목적지 IP 등

### 🚨 IP 프로토콜의 한계
- **비연결성** : 패킷을 받을 대상이 없거나 서비스 불능이어도 패킷 전송
- **비신뢰성** : 패킷이 사라지거나 순서대로 전달되지 않을 때 대처 방안 없음
- **프로그램 구분** : 같은 IP를 사용하는 서버에서 통신하는 애플리케이션이 두 개 이상일 때 구분할 수 없음

## TCP

#### 프로토콜 계층
<img src="https://images.velog.io/images/3hee_11/post/a1c43bf0-901d-4aa6-9e0a-2480abac1b91/image.png" width="85%">

#### TCP/IP 패킷 정보
<img src="https://images.velog.io/images/3hee_11/post/a4441b08-8bee-4eeb-82d4-0aac3fddd0c1/image.png" width="80%">

- 전송 제어 프로토콜(Transmission Control Protocol)
- **연결 지향** - TCP 3 way handshake(가상 연결)
👉 연결을 먼저하고 데이터를 전송한다.
<img src="https://images.velog.io/images/3hee_11/post/c8e15e3e-d180-4c13-ba90-e03d75b1d17d/image.png" width="80%">
 - **데이터 전달 보증** : 서버에 데이터를 전송하면 응답해준다.
 👉 데이터가 전달되지 않으면 알 수 있음
- **순서 보장** : 순서가 잘못되었을 경우 서버에서 잘못된 부분부터 다시 보내라고 알려준다.

📌 **신뢰할 수 있는 프로토콜로 대부분 TCP 사용**

## UDP
- 사용자 데이터그램 프로토콜(User Datagram Protocol)
- TCP와 같은 계층에 있는 프로토콜로 **기능이 거의 없다**(흰 도화지 비유)
- 연결 지향, 데이터 전달 보증, 순서 보장 모두 X
- handshake나 데이터 전송 시 응답 등을 안하기 때문에 **빠르다는 장점**이 있다.

📌 PORT, 체크섬을 제외하면 IP와 거의 같다. 
📌 애플리케이션에서 추가적으로 작업해서 빠르고 필요한 기능만을 포함할 수 있다.

## PORT
🚨 **IP만 가지고 통신할 때 문제점**
컴퓨터에 애플리케이션이 여러 개 돌아가고 있다면 여러 서버와 통신해야 한다. 서버에서 응답이 왔을 때, 같은 컴퓨터이기 때문에 IP 주소가 모두 같은데 여러 개의 애플리케이션 중 어디로 전달해야 하는지 알 수 없다.

📌 PORT는 **같은 IP일 때 프로세스를 구분하는 역할**을 한다. 
⭐ 쉽게 **IP는 아파트, PORT는 몇동, 몇호**로 이해하자

<img src="https://images.velog.io/images/3hee_11/post/58f6b614-37fb-40c0-95a4-4b30c540f9cd/image.png" width="70%">

## DNS

🚨 **IP 주소의 문제점**
1. 기억하기 어렵다. 
2. IP가 변경될 수도  있다.

👉 **DNS(Domain Name System, 도메인 네임 시스템)** 사용하기
- 도메인명을 등록해서 사용, 전화번호부 같은 역할
- 도메인 명을 IP 주소로 변환

📌 naver.com을 치면 DNS 서버에서 IP로 변환해서 응답해주고 그 주소로 접속
