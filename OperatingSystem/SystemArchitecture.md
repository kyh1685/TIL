_KOCW 반효경 교수님의 운영체제 강의를 정리한 내용입니다._

## 시스템 구조
![](https://images.velog.io/images/3hee_11/post/2ea4db54-5a83-45d2-885b-dd5ea68f9c15/image.png)

- **CPU** : CPU는 I/O장치에 직접 접근하지 않고 device controller한테 일을 시킨다.
  - **interrupt line** : I/O장치에서 접근 instruction이 온다.
  예) 키보드 입력, 디스크 입력 등의 요청
  - **mode bit** : 운영체제인지 사용자 프로그램인지 구분한다.
  
  - **registers** : CPU 내부의 저장공간으로 메모리보다 빠르다.
- **메모리** : CPU의 작업공간으로 클럭마다 메모리에서 instruction을 읽어서 실행한다.

- **하드디스크** : 보조기억장치로 불리지만 I/O장치로도 볼 수 있다. 
👉 메모리가 디스크에서 데이터를 읽어오기 때문
- **device controller** : 각 I/O장치를 전담하는 작은 CPU같은 것이다.
  예) 디스크에서 헤드의 움직임을 어떻게 할지, 어떤 데이터를 읽을지 통제
- **local buffer** : device controller의 작업 공간
- **timer** : 특정 프로그램이 CPU를 독점하는 것을 막는다.
  - OS에서 사용자 프로그램으로 CPU를 넘길 때, timer 시간을 지정한다.
  - 지정한 시간이 끝나면 timer가 CPU한테 인터럽트를 건다.
  - CPU는 하던 일을 멈추고, 사용자 프로그램에서 OS로 제어권을 넘긴다.
  - CPU는 instruction을 실행하고 interrupt line을 체크하기 때문에 타이머가 인터럽트를 걸었다면 innterrupt line을 보고 알 수 있다.
- **DMA Controller** : CPU에 인터럽트가 많이 걸리면 비효율적이기 때문에 DMA Controller가 I/O 작업이 끝난 데이터를 메모리에 올려주고 CPU에게 알린다.

>**📌 모든 I/O처리는 OS를 통해서만 가능**
- 프로그램A가 디스크에서 데이터를 읽어와야 한다면 CPU 제어권을 OS에게 넘기고 device controller한테 일을 시켜 데이터를 읽는다.
- 데이터를 읽었다면 device controller가 인터럽트를 건다.

## Mode bit
- 사용자 프로그램의 잘못된 수행으로 다른 프로그램 및 운영체제에 피해가지 않도록 하기 위함(보호, 보안 목적)
- Mode bit을 통해 두 가지 모드 지원
  - **1 사용자 모드** : 사용자 프로그램이 CPU 제어권을 가지고 수행
  - **0 모니터 모드** : OS가 CPU 제어권을 가지고 코드 수행
- 모니터 모드 실행 후 사용자 모드로 변경해서 사용자 프로그램이 제한을 가지고 수행하도록 처리

## Timer
- CPU를 특정 프로그램이 독점하는 것을 방지
- 지정한 시간이 지나면(타이머 값이 0이 되면) OS에 제어권이 넘어가도록 인터럽트를 발생시킨다.
- 매 클럭마다 1씩 감소한다.
- Timer는 Time sharing 구현을 위해 널리 사용된다.

## Device Controller
- I/O 장치를 관리하는 일종의 작은 CPU이다.
  - 제어 정보를 위해 control register, status register을 가짐
  - local buffer 가짐
- I/O 처리가 끝났을 경우 interrupt로 CPU에 알린다.

>- device driver(장치구동기) : OS 코드 중 각 장치 처리 루틴 → 소프트웨어
 - device controller(장치제어기) : 각 장치를 통제하는 일종의 작은 CPU → 하드웨어
 
## I/O(입출력)의 수행
- 모든 입출력 명령은 특권 명령(모니터 모드에서만 수행)
👉 OS를 통해서만 I/O 처리 가능

#### 🤔 사용자 프로그램은 어떻게 I/O 할까?
- 사용자 프로그램은 OS에게 I/O 요청(시스템콜)
- mode bit이 1로 바뀌고 CPU의 제어권이 OS에게 넘어간다.
- OS는 올바른 I/O 요청인지 확인 후 수행한다.

## 인터럽트 (Interrupt)

### Interrupt(하드웨어 인터럽트)
- 하드웨어가 발생시킨 인터럽트
- 보통의 인터럽트는 하드웨어 인터럽트를 의미한다.

### Trap(소프트웨어 인터럽트)
- Exception : 프로그램이 오류가 날 경우
- System call : 프로그램이 커널 함수를 호출한 경우

>**💡 인터럽트 관련된 용어**
- 인터럽트 벡터 : 해당 인터럽트의 처리 루틴 주소를 가지고 있다.
- 인터럽트 처리 루틴 : 해당 인터럽트의 처리하는 커널 함수

![](https://images.velog.io/images/3hee_11/post/d2895930-5739-46e4-8d09-9b5cae9b264d/image.png)

- I/O를 하기 위해서 인터럽트 두종류가 걸린다.
- 사용자 프로그램이 OS한테 시스템 콜(소프트웨어 인터럽트)
- OS가 일을 시키고 시킨 일이 끝나면 하드웨어 인터럽트로 알림

## 시스템콜
- 사용자 프로그램이 OS한테 요청하는 것이다.
- 사용자 프로그램이 운영체제 코드를 직접 실행할 수 없기 때문에 Interrupt line에 서면, CPU 제어권이 OS로 넘어가면서 OS 코드를 수행.
