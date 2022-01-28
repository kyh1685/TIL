# NIO 기반 입출력 및 네트워킹
## TCP 블로킹 채널
**TCP(Transmission Control Protocol, 전송 제어 프로토콜)** : 연결 지향적 프로토콜
**블로킹** : 스레드가 대기 상태가 되는 것


### 서버소켓 채널과 소켓 채널의 용도

#### ServerSocketChannel
- 클라이언트 SocketChannel의 연결 요청을 수락
- 통신용 SocketChannel을 생성  

#### SocketChannel
  - 연결된 클라이언트, 서버와 통신
![](https://images.velog.io/images/3hee_11/post/d3f5818a-d25c-487a-bf14-f55f18f76041/image.png)

🤔 **IO와 차이점** : 버퍼를 이용하고 블로킹, 넌블로킹 방식을 모두 지원 

### 서버소켓 채널 생성과 연결 수락
```java
// ServerSocketChannel 객체 얻기 → 정적메서드 open() 사용
ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();

// 블로킹으로 설정하기(기본 설정이지만 명시적으로 설정하여 논블로킹과 구분)
ServerSocketChannel.configureBlocking(true);

// 포트에 바인딩시키기(포트 정보를 가진 InetSocketAddress 객체 매개값)
ServerSocketChannel.bind(new InetSocketAddress(5001));

// accep() 메서드로 클라이언트 연결 수락(클라이언트가 연결 요청하기 전까지 블로킹)
SocketChannel socketChannel = SeverSocketChannel.accept();

// 연결된 클라이언트의 IP와 포트 정보 얻기
// getHostName() : 클라이언트 IP 리턴
// getPort() : 클라이언트 포트 번호 리턴
// toString() : "IP : 포트번호" 형태의 문자열 리턴
InetSocketAdress socketAddress = (InetSocketAdress)socketChannel
				.getRemoteAddress();

// 포트 언바인딩
serverSocketChannel.close();
```

### 소켓 채널 생성과 연결 요청
```java
// SocketChannel 객체 얻기 → 정적메서드 open() 사용
SocketChannel socketChannel = SocketChannel.open();

// 블로킹으로 설정하기(기본 설정이지만 명시적으로 설정하여 논블로킹과 구분)
SocketChannel.configureBlocking(true);

// 서버 연결 요청(서버 IP와 포트 정보를 가진 InetSocketAddress 객체 매개값)
// connect() 메서드는 연결이 완료될 때까지 블로킹, 완료되면 리턴
SocketChannel.connect(new InetSocketAddress("localhost",5001));

// 연결 끊기
socketChannel.close();
```

### 소켓 채널 데이터 통신

#### write() 메소드를 이용해 문자열 보내기
```java
// 정적 메서드 forName()으로 Charset 인스턴스 생성
// Charset : 인스턴스의 캐릭터셋과 유니코드 사이의 변환을 처리하는 클래스
Charset charset = Charset.forName("UTF-8");

// encode() 메서드를 통해 유니코드로 변환하고 ByteBuffer에 담음
ByteBuffer byteBuffer = charset.encode("Hello Server");

socketChannel.write(byteBuffer);
```

#### read() 메소드를 이용해 문자열 받기
```java
// 100개의 byte를 저장하는 ByteBuffer 생성
ByteBuffer byteBuffer = ByteBuffer.allocate();

// socketChannel에서 읽은 byte들을 buteBuffer에 저장하고 읽은 바이트 수 리턴
// 상대방이 SocketChannel의 close()를 호출하면 -1, 비정상적으로 종료되면 예외 발생
int byteCount = socketChannel.read(byteBuffer);

// 데이터 읽기 위해 위치 속성값 변경
byteBuffer.flip();

// 정적 메서드 forName()으로 Charset 인스턴스 생성
Charset charset = Charset.forName("UTF-8");

// buteBuffer안에 바이트들을 UTF-8로 변환하고 문자열로 다시 변환
String message = charset.decode(byteBuffer).toString();
```

### 스레드 병렬 처리
TCP 블로킹 방식은 데이터 입출력 완료 전까지 read(), write() 메소드가 블로킹 됨
👉 **클라이언트 연결 하나에 작업 스레드 하나를 할당해서 병렬 처리**

🚨 병렬 처리를 할 경우, 여러 클라이언트가 동시에 연결되면 서버에 스레드가 여러개 생성되어 서버 성능이 저하됨
👉 **스레드풀 사용**

## TCP 넌블로킹 채널

### 넌블로킹 방식의 특징
- connet(), accept(), read(), write() 메소드에 블로킹이 없다.
  - accept() :  클라이언트의 연결 요청이 없으면 null 리턴
  - read() : 클라이언트가 데이터를 보내지 않으면 0 리턴
  
🚨 블로킹되지 않고 바로 리턴되기 때문에 반복문 형식으로 accept()를 사용하면 계속 코드가 실행되고 CPU가 과도하게 소비된다.
👉 **이벤트 리스너 역할을 하는 셀렉터(Selector) 사용**

### 셀렉터(Selector)
- 클라이언트의 연결 요청, 데이터가 올 경우 채널이 selector에 통보한다.
- selector는 통보한 채널들을 선택해 작업 스레드가 accept() 혹은 read() 메소드를 실행해 작업을 처리하도록 한다.
- 멀티 채널의 작업을 싱글 스레드에서 처리할 수 있도록 해주는 **멀티플렉서 역할**을 한다.

### 셀렉터 동작원리
![](https://images.velog.io/images/3hee_11/post/b22f370e-eb44-43bb-83a0-c5053a014b42/image.png)
- 채널은 자신의 작업 유형을 키로 생성하고 셀렉터의 관심 키셋에 키를 등록한다.
- 클라이언트가 처리를 요청하면 셀렉터는 관심키셋에서 작업 처리 준비가 된 키를 선택해서 선택된 키셋에 별도로 저장한다.
- 작업 스레드는 선택된 키셋에서 키를 하나씩 꺼내고 연관된 채널 작업 처리한다.

### 셀렉터 생성과 등록
```java
// Selector 생성
try{
    Selector selector = Selector.open();
}catch(IOException e){}

// 넌블로킹 설정
// Selector에는 SelectableChannel 하위 채널만 등록 가능
SocketChannel socketChannel = SocketChannel.open(); // 예시 
SocketChannel.configureBlocking(false);

// Selector에 등록
// 첫번째 매개값은 Selector, 두번째 매개값은 채널의 작업 
SelectionKey selectionKey = socketChannel.register(selector
						 , SelectionKey.OP_READ);
```
**두번째 매개값으로 사용 가능한 작업 유형별 SelectionKey의 상수**

<img src="https://images.velog.io/images/3hee_11/post/62b6541e-4a57-42e4-b5d8-1178924f866e/image.png">

🚨 **동일한 SocketChannel로 두가지 이상의 작업 유형을 등록할 수 없다.** 

- register가 리턴한 SelectionKey는 작업 유형 변경, 첨부 객체 저장, 채널 등록 취소 등을 할 때 사용된다.
- 채널이 Selector를 등록하면 keyFor() 메서드로 SelectionKey를 얻을 수 있다.

### 선택된 키셋
- select()는 관심키셋에 저장된 SelectionKey로부터 작업 처리 준비가 되었다는 통보가 올 때까지 블로킹한다.
- 리턴값은 통보를 해온 SelectionKey의 수이다.
![](https://images.velog.io/images/3hee_11/post/cfc90838-8740-4fed-ad5c-9e2813fc08b3/image.png)

#### select()가 리턴하는 경우
- 최소한 하나의 채널이 작업 처리 준비가 되었다는 통보를 할 때
- Selector의 wakeup() 메소드를 호출할 때
- select()를 호출한 스레드가 인터럽트될 때

#### 채널의 작업 유형이 변경되는 경우
```java
// selectionKey 작업 유형 변경
selectionKey.interstOps(SelectionKey.OP_WRITE);

// 블로킹되어 있던 select() 리턴
selector.wakeup();

// select() 다시 실행
int keyCount = selector.select();

// 값이 1 이상일 경우 selectedKeys() 작업 처리 준비된 Set<SelectionKey>를 얻음
if(keyCount > 0){
	Set<SelectionKey> selectedKey = selector.selectedKeys();
}
```

### 채널 작업 처리
작업스레드는 작업을 처리하기 위해 SelectionKey가 어떤 작업 유형인지 알아내야 함

<img src="https://images.velog.io/images/3hee_11/post/bea239b6-a919-4483-b8c5-572a62b3bee8/image.png">

```java
// 채널 객체를 얻으려면 SelectionKey의 channel() 메소드를 사용
ServerSocketChannel serverSocketChannel = (ServerSocketChannel) selectionKey.channel();

// 채널 객체 이외의 객체 첨부하기
Client client = new Client(socketChannel);
SelectionKey selectionKey = socketChannel.register(selector, SelectionKey.OP_READ);
selectionKey.attach(client);

// 첨부된 객체 얻기
if(selectionKey.isReadable()){
	Client client = (Client)selectionKey.attachment();
}
```

## TCP 비동기 채널

### 비동기 채널의 특징
- connect(), accept(), read(), write()를 호출하면 즉시 리턴한다.
- 실질적 입출력 작업 처리는 스레드 풀의 스레드가 담당하며 스레드가 작업 처리 완료하면 콜백 메소드 호출
![](https://images.velog.io/images/3hee_11/post/7ac49cb8-ae13-48ff-928f-42372b29b378/image.png)

#### read() 메소드 예시
![](https://images.velog.io/images/3hee_11/post/ad8c6d40-c532-4f05-9356-da3b44db9de2/image.png)
- 애플리케이션에서 read() 메소드를 호출하면 즉시 리턴
- 내부적으로 스레드풀의 작업 스레드가 read() 메소드 실질적으로 실행
- 작업 스레드가 read() 메소드를 모두 실행하고 나면 completed() 콜백
- completed() 메소드 실행하는 스레드는 스레드풀의 작업 스레드

### 비동기 채널 그룹
같은 스레드풀을 공유하는 비동기 채널들의 묶음
![](https://images.velog.io/images/3hee_11/post/8acebc23-4e16-4259-8d84-59efc73e55db/image.png)

#### 비동기 채널 그룹 생성
- 비동기 채널 생성할 때 채널 그룹 지정하지 않으면 기본 비동기 채널 그룹
- 기본 비동기 채널 그룹은 내부적으로 생성되는 스레드풀 이용

#### 비동기 채널 그룹 종료
- shutdown()
  - 비동기 채널 그룹을 종료하겠다는 의사만 전달
  - 즉시 비동기 채널 그룹을 종료하지 않음
  - 비동기 채널 그룹에 포함된 모든 비동기 채널이 닫히면 종료
  - 새로운 비동기 채널을 포함시키려고 하면 ShutdownChannelGroupException이 발생
- shutdownNow()
  - 강제적으로 비동기 채널 그룹에 포함된 모든 비동기 채널 닫고 비동
기 채널 그룹을 종료
  - 완료 콜백 실행하고 있는 스레드는 종료되거나 인터럽트 X
  
## UDP 채널
- NIO에서의 UDP 채널은 DatagramChannel이다.
- 동기(블로킹)과 넌블로킹 방식 모두 사용 가능

### 발신자 만들기
```java
// DatagramChannel 생성을 위해 open() 호출
// open()은 ProtocolFamilydml 인터페이스 타입의 매개값을 가짐
// 구현 객체로 StandardProtocolFamily 열거 상수 사용
// IPv4와 IPv6을 구분하는 역할
DatagramChannel datagramChannel = DatagramChannel.open(StandardProtocolFamily.INET);

// send() 메소드를 이용해 데이터 보내기
// 첫번째 매개값은 보낼 데이터, 두번째는 수신자 정보를 가진 SocketAddress(추상 클래스)
// 하위 클래스인 InetSocketAddress 생성해서 대입
int byteeCount = datagramChannel.send(byteBuffer, new InetSocketAddress("localhost", 5001));

// 닫기
datagramChannel.close();
```

### 수신자 만들기
```java
// DatagramChannel 생성을 위해 open() 호출
// bind()를 호출해서 포트와 바인딩
DatagramChannel datagramChannel = DatagramChannel.open(StandardProtocolFamily.INET);
datagramChannel.bind(new InetSocketAddress(5001));

// receive() 메소드를 이용해 데이터 받기
// 데이터 받기 전까지는 블로킹 받으면 리턴
SocketAddress socketAddress = datagramChannel.receive(ByteBuffer dst);

// 작업 스레드 종료
// 작업 스레드의 interrup() 호출로 예외 발생
// datagramChannel에 close()을 호출시켜 예외 발생
// 예외 처리코드에서 작업 스레드를 종료 시킴
datagramChannel.close();
```
