# 네트워크 구성

## 네트워크 엣지(Network edge)
네트워크의 가장 가장자리이며 많은 end system(host : 클라이언트, 서버)들이 존재한다. 
👉 즉, end system는 스마트폰이나 PC 혹은 회사의 서버를 뜻한다.
![](https://images.velog.io/images/3hee_11/post/c9d5c063-13ab-4468-b961-1ada399fc8c4/image.png)

### TCP(Transmission Control Protocol) 서비스
- 인터넷의 연결 지향 서비스
- 안정적인 순차 바이트 스트림 데이터 전송
  - 데이터를 전송한 그대로 받을 수 있게 신뢰성을 보장
  - 패킷이 유실되면 재전송
- 흐름 제어(flow control)
  - 더 빠른 송신자가 느린 수신자를 제압하지 못하도록 데이터 전송 속도를 관리
  👉 보낼 때 받는 곳에서 받아들일 수 있는 속도로 전송
- 혼잡 제어(congestion control)
  - 데이터량을 조절하여 네트워크가 혼잡해지지 않게 조절
  👉 네트워크가 혼잡하면 송신자가 느리게 전송
- 데이터 전송을 위해 미리 연결을 준비
  - 요청/응답
  - 커뮤니케이션 하는 두 개의 호스트들의 상태를 설정
  
### UDP(User Datagram Protocol) 서비스
- TCP와 달리 UDP는 신뢰성이 낮음
- 신뢰성을 보장하지 않기 때문에 자원 소모가 적음
  
>🤔 **프로토콜이란?** 컴퓨터나 원거리 통신 장비 사이에서 데이터를 주고 받는 규칙

## 네트워크 코어(Network core)
- 전체 네트워크 시스템의 중앙에 위치하여 데이터를 전송하는 핵심적인 역할
- 네트워크 코어의 구조는 'Mesh of interconnected routers' 즉, 수많은 라우터들이 그물처럼 얽혀있는 구조
![](https://images.velog.io/images/3hee_11/post/f4d95073-da8b-4098-b5d3-8cf70c312cda/image.png)

### 🤔 데이터가 어떻게 이 그물을 통해 전송될까?

#### 패킷 교환(Packet Switching) 
- 데이터를 <span style='padding: 2px; background-color: #fff5b1; border-radius: 4px'>패킷 단위로 쪼개서 전송</span>하는 방식 
- 패킷은 다음 링크로 전송하기 전에 저장을 한 뒤 전달하는 <span style='padding: 2px; background-color: #fff5b1; border-radius: 4px'>store and forward</span> 방식 사용
- 라우팅 알고리즘을 이용해 경로를 설정하고, 중간의 라우터들을 거쳐 최종 목적지에 도달
- 패킷은 다음 라우터로 이동하기 위해 <span style='padding: 2px; background-color: #fff5b1; border-radius: 4px'>큐에서 대기(queueing)</span>
- 만약 수용할 수 있는 <span style='padding: 2px; background-color: #fff5b1; border-radius: 4px'>큐의 범위를 초과</span>하게 되면 <span style='padding: 2px; background-color: #fff5b1; border-radius: 4px'>손실(loss)</span>이 발생
- 인터넷은 패킷 교환 사용!

#### 회선 교환(Circuit Switching)
- <span style='padding: 2px; background-color: #fff5b1; border-radius: 4px'>하나의 회선을 할당</span>받아 데이터를 주고받는 방식
- 출발지부터 목적지까지 도착하는데 사용되는 <span style='padding: 2px; background-color: #fff5b1; border-radius: 4px'>회선 전체 독점</span>
- 주로 전화같은 실시간 통신에 사용되며 속도와 성능이 일정

>**📌 패킷 교환 VS 회선 교환**
- 라우터에서는 1 Mbps의 속도로 데이터를 전송한다.
- 유저는 활성화되어 있을 때, 100kb/s만큼의 자원을 이용한다.
- 각 유저는 전체 시간의 10%만큼 활성화되어 있다.
>
>**회선 교환**은 1 Mbps / 100 kbps = 10명의 유저만이 사용할 수 있다. 미리 회선을 예약해놓고, 예약된 회선은 반드시 그 사람만 이용할 수 있다. 따라서 사용하지 않을 때에도 다른 유저는 그 회선을 이용할 수 없다.
>
> **패킷 교환**은 많은 유저가 사용할 수 있다. 하지만 많은 유저가 동시에 네트워크를 이용하면, 라우터의 데이터 전송량보다 유저의 데이터 전송 요구량이 더 많아져서 문제가 생긴다.

### 패킷 전송에서의 시간 지연
![](https://images.velog.io/images/3hee_11/post/b928adfc-e885-40aa-aa02-3c52b8762720/image.png)

#### nodal processing delay
라우터에서 데이터를 처음 받으면 제대로 전달되었는지, 전송받은 비트에 에러가 없는지 확인한다. 이 과정에서 발생한 지연을 <span style='padding: 2px; background-color: #fff5b1; border-radius: 4px'>Processing delay</span>라 한다.

#### queueing delay
- 라우터는 데이터를 전송받으면 큐에 저장한다.
  - 저장하는 이유는 데이터 전송 속도보다 수신 속도가 느리면 데이터를 다 보내기 전에 유실되는 데이터가 생기기 때문
- 패킷 데이터를 큐에 순서대로 넣어서 다른 패킷이 모두 전송될 때까지 대기하는 데 걸리는 지연 시간을 <span style='padding: 2px; background-color: #fff5b1; border-radius: 4px'>queueing delay</span>라 한다.

#### Transmission delay
첫 비트부터 마지막 비트까지를 링크(outgoing edge)에 올리는 데에 걸리는 시간을 말한다.

#### Propagation delay
링크를 따라 다음 라우터까지 전달되는 데에 걸리는 시간을 말한다.

### 참고
**[네트워크 구성 (Network core, Network edge, Access network)](https://ddongwon.tistory.com/69)**

**[패킷의 지연과 손실 (Packet delay, loss, throughput)](https://ddongwon.tistory.com/70)**

**[네트워크 전달방식 비교 (Packet switching, Circuit switching)](https://swalloow.tistory.com/55)**

⭐ **[1-1. 컴퓨터 네트워크 기본 1](https://dev-nicitis.tistory.com/22?category=1015442)**
