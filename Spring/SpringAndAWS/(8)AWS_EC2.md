_스프링부트와 AWS로 혼자 구현하는 웹서비스 책을 정리한 내용입니다._

## 클라우드란?
인터넷(클라우드)를 통해 서버, 스토리지(파일 저장소), 데이터베이스, 네트워크, 소프트웨어, 모니터링 등의 <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>컴퓨팅 서비스를 제공하는 것</span>

### 🤔 단순히 물리 장비만 대여하는 걸까?
AWS의 EC2는 서버 장비를 대여하는 것이지만, 실제로는 그 안의 로그 관리, 모니터링, 하드웨어 교체, 네트워크 관리 등을 기본으로 지원한다.

**즉, 개발자가 직접 해야 할 일을 AWS가 전부 지원한다.**

## 클라우드의 형태

### Infrastructure as a Service(IaaS)
- 기존 물리 장비를 미들웨어와 함께 묶어둔 추상화 서비스
- 가상머신, 스토리지, 네트워크, 운영체제 등의 IT 인프라를 대여해 주는 서비스
예) AWS의 EC2, S3 등

### Platform as a Service(PaaS)
- IaaS에서 한번 더 추상화한 서비스
- 한번 더 추상화 했기 때문에 많은 기능이 자동화됨
예) AWS의 Beanstalk(빈스톡), Heroku(헤로쿠) 등

### Software as a Service(SaaS)
- 소프트웨어 서비스를 말함
예) 구글 드라이브, 드랍박스, 와탭 등

## AWS 회원 가입
> 🚨 Master 혹은 Visa 카드 필요!

**[ AWS 공식 사이트](https://aws.amazon.com/ko/free/?trk=fa2d6ba3-df80-4d24-a453-bf30ad163af9&sc_channel=ps&sc_campaign=acquisition&sc_medium=ACQ-P|PS-GO|Brand|Desktop|SU|Core-Main|Core|KR|KR|Text&ef_id=Cj0KCQiA95aRBhCsARIsAC2xvfxyaEn1loP74R6CpXcJij-qmLykNPFYPP3FzCwtgN-tx0tkqnE7IB0aAkAQEALw_wcB:G:s&s_kwcid=AL!4422!3!563761819834!e!!g!!aws&ef_id=Cj0KCQiA95aRBhCsARIsAC2xvfxyaEn1loP74R6CpXcJij-qmLykNPFYPP3FzCwtgN-tx0tkqnE7IB0aAkAQEALw_wcB:G:s&s_kwcid=AL!4422!3!563761819834!e!!g!!aws&all-free-tier.sort-by=item.additionalFields.SortRank&all-free-tier.sort-order=asc&awsf.Free%20Tier%20Types=*all&awsf.Free%20Tier%20Categories=*all)**에 접속해서 무료 계정 만들기를 선택한다.

<img src="https://images.velog.io/images/3hee_11/post/2e48b51e-54d8-4518-ac0c-01c2b3d6202d/image.png"  width="80%">

이메일 주소, 암호를 설정하여 AWS 계정을 생성한 다음 **개인을 선택**해 회원 가입한다.

<img src="https://images.velog.io/images/3hee_11/post/63d36eba-3481-4793-ae17-4fed6cfc2079/image.png"  width="60%">

**Master 혹은 Visa 카드**로 결제 정보를 입력하고 자격 증명 확인을 한다.

<img src="https://images.velog.io/images/3hee_11/post/ba66379b-bf8a-4c52-98e8-745230acc73f/image.png"  width="60%">

마지막으로 **지원 플랜(기본 지원)**을 선택한다.

<img src="https://images.velog.io/images/3hee_11/post/07a51f3e-f7fa-4d48-b1a8-293754a944c7/image.png"  width="90%">

## EC2 인스턴스 생성
EC2(Elastic Compute Cloud)는 <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>AWS에서 제공하는 성능, 용량 등을 유동적으로 사용할 수 있는 서버</span>이다.

> **🚨 주의**
AWS 프리티어 플랜은 EC2를 사용할 때 아래와 같은 제한이 있다.
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>t2.micro 사양만 가능</span>
  - vCPU(가상 CPU) 1 Core, 메모리 1GB 사양
  - 보통 vCPU는 물리 CPU 사양의 절반 정도의 성능을 가짐  
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>월 750시간의 제한(초과하면 비용 부과)</span>
  - 24 * 31일 = 744시간이므로 **⭐1대⭐의 t2.micro 사용 시 24시간 사용 가능**
  
EC2를 만들기 전에 **내 리전을 확인**하고 서울로 되어있지 않다면 **서울로 변경**한다.

>🙋‍♀️ **리전이란?** AWS 서비스가 구동될 지역을 말한다. AWS는 도시별로 클라우드 센터를 지어 해당 센터에서 구축된 가상머신들을 사용할 수 있다.

![](https://images.velog.io/images/3hee_11/post/626916fb-4028-4ad9-8bb4-88d57c1b805c/image.png)

EC2 서비스를 선택하고 대시보드 중앙에 있는 인스턴스 시작 버튼을 누른다.

>🙋‍ **인스턴스란?** EC2 서비스에서 생성된 가상머신이다.

<img src="https://images.velog.io/images/3hee_11/post/50016a58-6c6a-41c0-9780-fb2e1f8a9298/image.png"  width="80%">

### 단계 1. AMI(Amazon Machine Image) 선택
AMI는 <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>EC2 인스턴스를 시작하는데 필요한 정보를 이미지로 만들어 둔 것</span>이다.
_인스턴스라는 가상머신에 운영체제 등을 설치할 수 있게 구워 넣은 이미지라고 생각!_

예를 들어 아마존 리눅스 1 AMI를 사용한다면 Amazon Linux 1 OS가 인스턴스에 설치되어 개발자가 사용할 수 있음을 의미한다.

여기서는 최신 버전의 **Amazon Linux 2 AMI 선택**

>💡 **아마존 리눅스 AMI를 사용하는 이유 **
- 아마존이 개발하고 있기 때문에 지원받기 쉬움
- AWS의 각종 서비스와 상성이 좋음
- Amazon 독자적인 개발 리포지토리를 사용하고 있어 yum이 빠름
>
**yum(Yellodog Update Modified)이란?** 레드햇 계열의 리눅스 배포판에서 사용하는 패키지 설치 관리 도구( + rpm 명령어가 해결하지 못했던 패키지 의존성 문제를 해결 )

![](https://images.velog.io/images/3hee_11/post/afdfb3c1-cd97-4648-bb80-1dde206caf75/image.png)

### 단계 2. 인스턴스 유형 선택
인스턴스 유형에서는 <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>프리티어로 표기된 t2.micro를 선택</span>한다.

**🚨 이외 다른 유형 선택 시 비용이 청구됨**

![](https://images.velog.io/images/3hee_11/post/e72dcac8-140d-431c-8bf7-58a0b5d0cce8/image.png)

>**📌 유형 설명**
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>t2는 요금 타입, micro는 사양</span>
- t2 외에 t3도 있으며 이들을 **T시리즈**라고 함
- T시리즈는 다양한 사양을 사용할 수 있어 **범용 시리즈**로 불린다
_다른 시리즈는 nano, micro 등의 저사양이 존재하지 않음_
- 다른 서비스와 달리 **크레딧이 존재**(CPU를 사용할 수 있는 포인트 개념)
- 인스턴스 크기에 따라 정해진 비율로 **CPU 크레딧을 계속 받으며**, 사용하지 않을 때는 **크레딧을 축적**하고 사용할 때는 **크레딧을 사용**
- 정해진 사양보다 높은 트래픽이 오면 크레딧을 더 적극적으로 사용하면서 처리하지만, 크레딧이 **모두 사용되면 더이상 EC2를 사용할 수 없음**
👉 그래서 트래픽이 높은 서비스들은 T시리즈를 사용하지 않음

### 단계 3. 인스턴스 세부 정보 구성
_기업에서 사용할 경우 VPC나 서브넷 등을 세세하게 설정하지만 여기서는 혼자 1대의 서버만 사용하므로 별다른 설정을 하지 않는다._

### 단계 4. 스토리지 추가
스토리지는 흔히 하드디스크라고 부르는 **서버의 디스크(SSD 포함)**를 말하며 <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>서버의 용량을 얼마나 정할지 선택</span>하는 단계이다.

설정의 기본값은 8GB로 **프리티어는 30GB까지 가능**하다. 그 이상은 비용이 청구되니 최대치인 30GB로 변경한다.

![](https://images.velog.io/images/3hee_11/post/c8f80653-133a-44f7-9d26-b1bcfc20d102/image.png)

### 단계 5. 태그 추가
태그에는 <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>웹 콘솔에서 표기될 태그인 Name 태그를 등록</span>한다. 태그는 해당 인스턴스를 표현하는 여러 이름으로 사용될 수 있다. 

여러 인스턴스가 있을 경우 태그별로 구분하면 검색이나 그룹 짓기가 편하므로 본인의 서비스의 인스턴스를 나타낼 수 있는 값으로 등록한다.

![](https://images.velog.io/images/3hee_11/post/63e1d355-cc8b-4fc3-a59d-090655be677b/image.png)

### ⭐ 단계 6. 보안 그룹 구성
<span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>보안 그룹은 방화벽</span>을 말한다. '서버로 80 포트 외에는 허용하지 않는다'는 역할을 하는 방화벽이 AWS에서는 보안 그룹으로 사용된다.

새 보안 그룹 생성을 선택하고 보안 그룹 이름을 **유의미한 이름으로 변경**한다.

![](https://images.velog.io/images/3hee_11/post/65cd6e87-3a7b-4df7-b2cf-a489e0e79104/image.png)

유형이 <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>**SSH이면서 포트 범위가 22인 경우는 AWS EC2에 터미널로 접속할 때**</span>이다.

#### 🚨 주의!!
<span style='padding: 2px; background-color: #fff5b1; border-radius: 4px'>pem 키가 없으면 접속이 안되니 전체 오픈(0.0.0.0/0, ::/0)하는 경우가 있는데 이렇게 되면 이후 파일 공유 디렉토리나 깃허브 등에 실수로 pem 키가 노출되는 순간 서버가 털릴 수 있다ㅎ</span>

**⭐ pem 키 관리와 지정된 IP에서만 SSH 접속이 가능하도록 구성하는 것이 안전 ⭐**

그래서 본인 집의 IP를 기본적으로 추가하고(내 IP 선택 시 현재 접속한 장소의 IP가 자동 지정됨) 카페와 같이 집 외에 <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>다른 장소에서 접속할 때 해당 장소의 IP를 다시 SSH 규칙에 추가하는게 안전</span>하다.

### 단계 7. 인스턴스 시작 검토
<span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>인스턴스로 접근하기 위해서는 pem 키가 필요하다.</span> 그래서 인스턴스 마지막 단계는 할당한 pem 키를 선택하는 것이다.

<span style='padding: 2px; background-color: #fff5b1; border-radius: 4px'>인스턴스는 지정된 pem 키와 매칭되는 공개키를 가지고 있어, 해당 pem 키 외에는 접근을 허용하지 않는다.</span>

**🚨 일종의 마스터키이기 문에 절대 유출되면 안됨 🚨**

pem 키는 이후 EC2 서버로 접속할 때 필수 파일이니 <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>잘 관리할 수 있는 디렉토리로 저장</span>한다. _기존에 생성된 pem 키가 있다면 선택하고 업다면 신규로 생성_

<img src="https://images.velog.io/images/3hee_11/post/73262994-f394-4e58-90e0-e07ffead297b/image.png" width="80%">

pem 키를 다운받고 아래와 같이 인스턴스 생성 시작 페이지로 이동한 다음 인스턴스 id를 클릭하여 EC2 목록으로 이동한다.

![](https://images.velog.io/images/3hee_11/post/ef4d9f01-6e0c-47be-824c-dfbd9097e7ca/image.png)

인스턴스가 생성 중인 것을 확인할 수 있으며 Name 태그로 인해 Name이 노출되는 것도 볼 수 있다.

![](https://images.velog.io/images/3hee_11/post/37f40d2d-a72b-4383-97c2-fcb77ebdf30c/image.png)

인스턴스를 생성하면 새 IP가 할당되는데 같은 인스턴스를 중지하고 다시 시작할 때도 새 IP가 할당된다.
👉 재시작할 때마다 매번 IP가 변경되므로 **고정 IP가 필요**

### EIP 할당
<span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>AWS의 고정 IP를 Elastic IP(EIP, 탄력적 IP)라고 한다.</span>

EC2 인스턴스 페이지의 왼쪽 카테고리에서 탄력적 IP를 선택하고 주소가 없으므로 탄력적 IP 주소 할당 버튼을 클릭하고 나온 팝업에서 할당 버튼을 누른다.

![](https://images.velog.io/images/3hee_11/post/f687643a-58b3-4733-827d-4d92e1dc4763/image.png)

![](https://images.velog.io/images/3hee_11/post/ad34d58c-b83c-4750-b2d7-d620cedcd0b0/image.png)

생성된 탄력적 IP를 선택하고 오른쪽 상단의 작업을 눌러 주소 연결 메뉴를 선택한다.

![](https://images.velog.io/images/3hee_11/post/aa6db5b7-a852-4f4e-a2c2-a513f81a39ed/image.png)

주소 연결을 위해 생성한 EC2 이름과 IP를 선택하고 연결 버튼을 클릭한다.

![](https://images.velog.io/images/3hee_11/post/7b5e330d-4108-4c4e-8c7f-6df6d3494811/image.png)

연결이 완료되면 왼쪽 카테고리에 있는 인스턴스 탭을 클릭해 인스턴스 목록으로 이동하고 인스턴스를 선택해 퍼블릭, 탄력적 IP가 연결됐는지 확인한다.

#### 🚨 주의
탄력적 IP는 생성하고 EC2 서버에 연결하지 않으면 비용이 발생한다. 만약 더는 사용할 인스턴스가 없다면 탄력적 IP를 삭제해야한다.

## EC2 서버 접속(Window)
>📌 **접속이 오래 걸리거나 권한이 없다는 메세지가 나오는 경우**
- HostName 값이 탄력적 IP로 되어있는지 확인
- EC2 인스턴스가 running 상태인지 확인
- EC2 인스턴스의 보안그룹 → 인바운드 규칙에서 현재 본인의 IP가 등록되어 있는지 확인

윈도우는 Mac과 같이 ssh 접속하기엔 불편한 점이 많아 별도의 클라이언트(**[putty](https://www.putty.org/)**)를 설치한다.

![](https://images.velog.io/images/3hee_11/post/a15fe975-9c1f-42d5-8895-74cfde0123c8/image.png)

![](https://images.velog.io/images/3hee_11/post/24cda929-869e-43b8-b79d-705434fc14ed/image.png)

설치가 완료되면 **puttygen.exe**를 실행한다. 

### puttygen

<span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>putty는 pem 키로 사용할 수 없기 때문에 pem 키를 ppk 파일로 변환해야 하는데 puttygen이 이 과정을 수행한다.</span>

puttygen 상단의 Conversions → Import Key를 선택해 **내려받은 pem 키를 선택**한다.

![](https://images.velog.io/images/3hee_11/post/52d92cf1-8350-4181-8fec-c6c9b43c9d72/image.png)

선택하면 자동으로 변환이 진행되고 하단의 Save private key 버튼을 클릭하여 **ppk 파일을 생성**한다.

경고메세지에 예를 클릭하고 ppk 파일이 저장될 위치와 ppk의 이름을 등록한다.

### putty
ppk 파일이 잘 생성되었으면 putty.exe를 실행하여 아래와 같이 항목을 등록한다.

<img src="https://images.velog.io/images/3hee_11/post/522c8a05-e853-49ca-939a-ec5f2b7d23b9/image.png" width="80%">

>- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>Host Name</span>
  - username@public_IP 등록
  - 이전에 생성한 Amazon Linux는 ec2-user가 username이므로 ec2-user@탄력적 IP 주소를 등록하면 됨
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>Port</span> : ssh 접속 포트인 22를 등록
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>Connection type</span> : SSH 선택

왼쪽에 있는 Connection → SSH → Auth를 클릭한 다음 Browse를 클릭해 이전에 생성한 ppk 파일을 불러온다.

<img src="https://images.velog.io/images/3hee_11/post/8d2930fd-b4b7-4b25-a489-3c84354ed55c/image.png" width="80%">

왼쪽에 Session 메뉴로 이동하여 Saved Sessions에 현재 설정들을 저장할 이름을 등록하고 Save 클릭

<img src="https://images.velog.io/images/3hee_11/post/0a9bcc30-5978-4948-912d-2942a71e9d21/image.png" width="80%">

저장한 뒤 open을 누르면 SSH 접속 알림이 나오는데 여기서 accept를 눌러주면 아래와 같이 SSH 접속이 성공한 것을 확인할 수 있다.

![](https://images.velog.io/images/3hee_11/post/93949e53-0d31-4091-8f37-837021899b67/image.png)

## 아마존 리눅스 서버 생성 시 필수 설정
이 설정들은 모두 자바 기반의 웹 어플리케이션(톰캣과 스프링부트)가 작동해야 하는 서버들에선 필수로 해야하는 설정이다.

### java11 설치
<span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>yum list java*jdk-devel</span>로 설치 가능한 자바 패키지 버전을 확인한다.

<img src="https://images.velog.io/images/3hee_11/post/8d4e6440-cc1e-4cdd-8727-db739833c6ec/image.png">

11버전이 없어서 aws corretto를 다운로드하고 jdk11을 설치하려고 했으나 아래와 같은 문구가 뜨면서 설치되지 않았다.

<img src="https://images.velog.io/images/3hee_11/post/b54eefcb-a0a2-4c7b-9702-f91cf934949b/image.png">

<img src="https://images.velog.io/images/3hee_11/post/c4f79350-e0b5-45e2-b9f3-04fd6481ff17/image.png">

<span style='padding: 2px; background-color: #fff5b1; border-radius: 4px'>구글링을 해본 결과 Amazon Linux 2에서 지원하는 Extras 라이브러리에서 소프트웨어 패키지를 활성화하고 간단하게 설치가 가능하다는 사실을 알았다</span> (**[링크](https://aws.amazon.com/ko/premiumsupport/knowledge-center/ec2-install-extras-library-software/)**)

#### Extras 라이브러리로 java11 설치하기

<span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>which amazon-linux-extras</span>로 amazon-linux-extras 패키지가 설치돼 있는지 확인한다.

설치되어 있다면 <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>amazon-linux-extras</span>로 활성화 가능한 토픽들을 찾는다
👉 available 상태인 java11 버전 발견

<img src="https://images.velog.io/images/3hee_11/post/fdcf88e9-4613-4260-b821-b417f79967ad/image.png">

<span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>sudo amazon-linux-extras enable java-11-openjdk</span>로 java11을 활성화시키면 아래처럼 enabled가 뜨고 설치할 수 있다고 알려준다.

<img src="https://images.velog.io/images/3hee_11/post/badbd5b6-8b2a-46d6-9d1a-101abbf6d1b8/image.png">

<img src="https://images.velog.io/images/3hee_11/post/0f08b37b-66f0-445d-a0c8-5c3658795c83/image.png">

<span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>sudo yum install java-11-openjdk</span>로 설치해주고  y를 입력한다.

<img src="https://images.velog.io/images/3hee_11/post/d1312cd5-3c7f-4a25-9fc3-a09018906efc/image.png">

설치가 완료되면 버전을 확인한다.

<img src="https://images.velog.io/images/3hee_11/post/d5f378f0-478a-4d46-be87-288a02a1ddcf/image.png">

### 타임존 변경
기본 서버의 시간은 미국 시간대이다. 한국 시간대로 변경해야 이후에 사용하는 시간이 모두 한국 시간으로 등록되고 사용된다.

<span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>sudo rm /etc/localtime</span> : 기존 localtime 삭제

<span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>sudo ln -s /usr/share/zoneinfo/Asia/Seoul /etc/localtime</span> : 한국 시간대로 변경

<span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>date</span> : 타임존 확인

### 호스트네임 변경
현재 접속한 서버의 별명을 등록한다. 실무에서는 여러 대의 서버가 작동되는데 IP만으로 서버의 역할이 무엇인지 알 수 없기 때문에 필수로 호스트 네임을 등록한다.

#### 퍼블릭 DNS 이름 없이 시스템 호스트 이름을 변경(Amazon Linux 2)

<span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>sudo hostnamectl set-hostname 원하는 이름.localdomain</span>로 호스트 이름을 설정한다.

<span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>sudo vim /etc/hosts</span>로 hosts 파일을 열어서 o로 입력 모드로 전환한 다음 맨 아래에 <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>127.0.0.1 등록한HOSTNAME</span> 을 입력하고 esc + :wq + enter로 저장한다.

<img src="https://images.velog.io/images/3hee_11/post/7b72a848-7bf2-4e56-a093-55d9ab811c3c/image.png">

<span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>sudo reboot</span>로 재부팅한 다음 <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>hostname</span>로 잘 설정됐는지 확인한다.

<img src="https://images.velog.io/images/3hee_11/post/9521bff3-4a40-42a2-9670-616f750f4590/image.png">

<span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>curl 등록한 호스트 이름</span>을 쳐서 아래처럼 나온다면 성공~

<img src="https://images.velog.io/images/3hee_11/post/8d2ef6ad-708a-4e6b-b34b-b8b0eb06fd33/image.png">

**[hostname이 /etc/hosts에 등록되지 않으면 발생할 수 있는 장애](https://techblog.woowahan.com/2517/)**

**[AWS 홈페이지에서 호스트 이름 변경 더 자세히 보기](https://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/set-hostname.html)**

**[VIM 편집기 기본 사용법 보기](https://jhnyang.tistory.com/54)**

### 출처

⭐ **[아마존 클라우드 Amazon Linux 2 OS 인스턴스 만들기, SSH로 접속하기](https://ansan-survivor.tistory.com/1070)**

**[리눅스 yum이란? yum 명령어 사용법](https://dololak.tistory.com/331)**

**[AWS 프리티어 적용 여부 & 남은 기간 확인](https://bin-e.tistory.com/32)**

**[EC2(Amazon Linux 2)에 Java 11 설치하기](https://gre-eny.tistory.com/m/343)**

---
AWS Documentation 최고👏👏👏
