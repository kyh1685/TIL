> 스트림이란 단일 방향으로 연속적으로 흘러가는 것이다.

## 입력 스트림과 출력 스트림
![](https://images.velog.io/images/3hee_11/post/32293c50-ff96-44cb-a626-a6007641fb30/image.png)

### 스트림 클래스의 종류
- 바이트 기반 스트림 : 그림, 멀티미디어, 문자 등 모든 종류의 데이터를 받고 보냄
- 문자 기반 스트림 : **문자만 받고 보낼 수 있도록** 특화
![](https://images.velog.io/images/3hee_11/post/14f0404b-d61a-4476-ba7c-84ad602f0eae/image.png)

### InputStream
바이트 기반 입력 스트림의 최상위 클래스로 추상 클래스이다.
![](https://images.velog.io/images/3hee_11/post/5d78b346-ef26-419f-8a18-cfe835f22b3c/image.png)

#### InputSteam 클래스의 주요 메소드
- **read()** 
  - 입력 스트림으로부터 1바이트를 읽고 4바이트 int 타입으로 리턴
  👉 리턴된 4바이트 중 끝의 1 바이트에만 데이터가 있음
  - 더 이상 입력 스트림으로부터 바이트를 읽을 수 없다면 -1 리턴(오버로딩된 다른 read()도 동일)
- **read(byte[] b)**
  - 입력 스트림으로부터 주어진 매개값 배열의 길이만큼 바이트를 읽고 저장하며 읽은 바이트 수를 리턴 
  - 한 번 읽을 때 매개값으로 주어진 바이트 배열 길이만큼 읽기 때문에 루핑 횟수가 read()보다 적다.
  👉 많은 양의 바이트를 읽을 때 적합
- **read(byte[] b, int off, int len)**
  - 입력 스트림으로부터 len개의 바이트만큼 읽고, 매개값으로 주어진 바이트 배열 b[off]부터 len개까지 저장하며 읽은 바이트 수인 len개를 리턴
- **close()**
  - IntStream에서 사용했던 시스템 자원을 풀어준다.
  
### OutputStream
바이트 기반 출력 스트림의 최상위 클래스로 추상 클래스이다.
![](https://images.velog.io/images/3hee_11/post/06882fab-f197-4c4c-9347-4e67126942c5/image.png)
