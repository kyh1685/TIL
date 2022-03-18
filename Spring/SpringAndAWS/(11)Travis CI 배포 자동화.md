_스프링부트와 AWS로 혼자 구현하는 웹서비스 책을 정리한 내용입니다._

**🚨 이전까지 진행한 배포 방식의 문제점**
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>수동 실행되는 Test</span>
  - 개발자가 작업을 진행할 때마다 내가 짠 코드가 다른 개발자의 코드에 영향을 끼치진 않는지 확인하기 위해 수동으로 전체 테스트를 해야한다.
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>수동 Build</span>
  - 다른 사람이 작성한 브랜치와 내가 작성한 브랜치가 합쳐졌을 때(merge) 이상이 없는지 Build를 해야 알 수 있다.
  - 매번 개발자가 직접 실행해봐야한다.
  
## CI&CD 소개
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>CI(Continuous Integration)</span> : 코드 버전 관리를 하는 VCS 시스템에 PUSH가 되면 자동으로 테스트와 빌드가 수행되어 안정적인 배포 파일을 만드는 과정
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>CD(Continous Deployment)</span> : CI 빌드 결과를 자동으로 운영 서버에 무중단 배포까지 진행하는 과정

## Travis CI 연동
Travis CI는 깃허브에서 제공하는 무료 CI 서비스이다. 
_젠킨스와 같은 CI 도구도 있지만 젠킨스는 설치형이기 때문에 이를 위한 EC2 인스턴스가 하나 더 필요하다._

### Travis CI 웹 서비스 설정
https://travis-ci.com/ 에서 깃허브 계정으로 로그인한 뒤 왼쪽 위에 계정명에서 Settings를 클릭한다.

<img src="https://images.velog.io/images/3hee_11/post/40b6e6f7-febd-427a-9bd1-ffbb1101b996/image.png">

Repositories 탭 아래에 있는 active를 클릭한다.

<img src="https://images.velog.io/images/3hee_11/post/e8d08d7e-4f65-4cd6-8c61-2b63b549ebb4/image.png">

Approve&install Tavis CI에서 사용할 repository를 선택한다.

<img src="https://images.velog.io/images/3hee_11/post/a173938c-f1ab-488d-b06b-2f460f0941bd/image.png">

다시 Travis에 Repositories를 보면 Repository가 등록된 걸 볼 수 있다.

<img src="https://images.velog.io/images/3hee_11/post/490cfe59-7397-46e2-9a87-76cf543cd7ad/image.png">

>**🚨 주의**
Travis에서 plan을 선택하지 않으면 앞으로 진행되지 않으니 플랜을 등록하자

### 프로젝트 설정
build.gradle과 같은 위치에 .travis.yml을 생성해 아래처럼 코드를 추가하고 master 브랜치에 커밋과 푸시를 한다.

```yml
language: java
jdk:
  - openjdk11

branches:
  only:
    - master

# Travis CI 서버의 Home
cache:
  directories:
    - '$HOME/.m2/repository'
    - '$HOME/.gradle'

script: "./gradlew clean build"

# CI 실행 완료 시 메일로 알람
notifications:
  email:
    recipients:
      - 내 메일 주소
```

>- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>branches</span> 
  - Travis CI를 어느 브랜치가 푸시될 때 수행할지 지정
  - 현재 옵션은 오직 master 브랜치에 push될 때만 수행
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>cache</span> 
  - 그레이들을 통해 의존성을 받게 되면 이를 해당 디렉토리에 캐시하여 같은 의존성은 다음 배포 때부터 받지 않도록 설정
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>script</span> 
  - master 브랜치에 푸시되었을 때 수행하는 명령
  - 여기서는 프로젝트 내부에 둔 gradlew을 통해 clean&build를 수행
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>notifications</span> 
  - Travis CI 실행 완료 시 자동으로 알림이 가도록 설정
  
Travis CI 저장소를 확인하니 권한 오류가 났다. 
_홈페이지 확인 외에도 내가 설정해놓은 이메일로 꼬박꼬박 에러가 발생했다고 온다_

<img src="https://images.velog.io/images/3hee_11/post/f72d3a4c-6b05-4e8f-92c5-d2d488f76f5d/image.png">

.tavis.yml에 아래와 같이 권한을 주는 코드를 추가했더니 성공했다.

<img src="https://images.velog.io/images/3hee_11/post/b6851d1f-2001-424f-b10c-87665bf06607/image.png">

## Travis CI와 AWS S3 연동
<span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>S3란 AWS에서 제공하는 일종의 파일 서버이다.</span> 이미지 파일을 비롯한 정적 파일들을 관리하거나 배포 파일들을 관리하는 등의 기능을 지원한다.

_보통 이미지 업로드를 구현한다면 S3를 이용하여 구현하는 경우가 많다._

<img src="https://images.velog.io/images/3hee_11/post/f5cc066a-07db-4ee3-adb5-71c8eaa5c42c/image.png" width="80%">

여기서 S3는 Travis CI가 빌드한 Jar 결과물을 받아서 CodeDeploy가 가져가 배포하기 전까지 보관하는 용도이다(CodeDeploy는 저장 기능 X)

>**📌 참고**
CodeDeploy는 깃허브 코드를 가져오는 기능이 있어 빌드, 배포 모두 가능하다. 하지만 빌드없이 배포만 필요할 때 대응이 어렵다.
>
빌드와 배포가 분리되어 있다면 전에 만들어진 Jar를 재사용하면 되지만 CodeDeploy가 모든 것을 하게 되면 항상 빌드를 하게 되니 확장성이 떨어진다.
>
**👉 빌드와 배포는 분리하자**

### AWS Key 발급
일반적으로 AWS 서비스에 외부 서비스가 접근할 수 없으므로 <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>접근 권한을 가진 Key를 생성해서 사용</span>해야 한다.

<span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>AWS IAM(Identity and Access Management)</span>가 인증과 관련된 기능을 제공한다. IAM은 AWS에서 제공하는** 접근 방식과 권한을 관리**한다.

**💡 IAM을 통해 Travis CI가 S3와 CodeDeploy에 접근할 수 있도록 해보자. **

IAM 페이지 왼쪽 사이드바에서 사용자 → 사용자 추가를 클릭한다.

<img src="https://images.velog.io/images/3hee_11/post/aee12255-94e5-4b20-abca-d049ba74747d/image.png">

생성할 사용자 이름과 액세스 유형(프로그래밍 방식 액세스)을 선택한다.

<img src="https://images.velog.io/images/3hee_11/post/2ab56f8f-f7fe-437c-add2-821cef466b15/image.png">

권한 설정 방식은 기존 정책 직접 연결을 선택하고 아래처럼 체크한다.

<img src="https://images.velog.io/images/3hee_11/post/8b3a5959-699e-4a55-905b-989eb4c01fc8/image.png">
<img src="https://images.velog.io/images/3hee_11/post/d96fd233-b737-474a-8770-821049ba60e4/image.png">
<img src="https://images.velog.io/images/3hee_11/post/cec44724-be33-4fc9-b1ea-4ff73bc891bd/image.png">
<img src="https://images.velog.io/images/3hee_11/post/91163772-5e1a-4bfd-ae77-3c18b29db3f9/image.png">

마지막으로 생성한 권한 설정 항목을 확인하고 사용자 만들기를 누른다.

<img src="https://images.velog.io/images/3hee_11/post/e9b6c5d6-d56d-402d-bdc5-3cfe0e4d1149/image.png">

완료되면 다음과 같이 액세스 키와 비밀 액세스 키가 생성된다.

<img src="https://images.velog.io/images/3hee_11/post/889212c0-1b52-44cc-9253-bf72b2f658a1/image.png">

### Travis CI에 키 등록
Travis CI의 설정 화면에서 Settings를 클릭한다.

<img src="https://images.velog.io/images/3hee_11/post/7af06d36-096b-4c45-a301-3e1907c1fbdb/image.png">

아래에 있는 Environment Variables 항목에 AWS_ACCESS_KEY와 AWS_SECRET_KEY를 변수로 해서 IAM 사용자에서 발급받은 값들을 등록한다.

<img src="https://images.velog.io/images/3hee_11/post/dd31d910-71e6-40e7-8bb3-7608b0319252/image.png">

여기에 등록된 값들은 .travis.yml에서 $AWS_ACCESS_KEY, $AWS_SECRET_KEY로 사용할 수 있다.

### S3 버킷 생성
S3로 이동해 버킷을 생성한다.

<img src="https://images.velog.io/images/3hee_11/post/fa65db02-b24e-4bde-97b7-294ccfac4379/image.png">

버킷명은 이 버킷에 배포할 Zip파일이 모여있는 장소임을 의미하도록 짓는다.

<img src="https://images.velog.io/images/3hee_11/post/c4bc5a9c-0179-4de4-b07c-01b8f8ea34e2/image.png" width="65%">

버킷 버전은 기본 설정인 비활성화로 놔둔다.

<img src="https://images.velog.io/images/3hee_11/post/93c0cdd4-4b31-4551-b8da-fd7096047b3e/image.png">

**퍼블릭 액세스는 모두 차단**한다. 실제 서비스에서 Jar 파일이 퍼블릭일 경우 누구나 내려 받을 수 있어 **코드나 설정값, 주요 키값들이 탈취될 수 있다.**

<img src="https://images.velog.io/images/3hee_11/post/30935db7-a71d-4f1a-a622-543385149408/image.png">

### .travis.yml 추가
Travis CI에서 빌드하여 만든 Jar파일을 S3에 올릴 수 있도록 .travis.yml를 수정하고 깃허브로 푸시한다.

<img src="https://images.velog.io/images/3hee_11/post/2d6dc55a-ca5c-4e29-833a-ca4aab8486f4/image.png">

>- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>before_deploy</span>
  - deploy가 실행되기 전에 수행
  - ** CodeDeploy는 Jar 파일은 인식하지 못하므로** Jar + 기타 설정 파일들을 모아 압축
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>zip -r freelec-springboot2-webservice</span>
  - 현재 위치의 모든 파일을 freelec-springboot2-webservice 이름으로 압축
  - 명령어의 마지막 위치는 내 프로젝트 이름이어야 함
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>mkdir -p deploy</span>
  - deploy라는 디렉토리를 Travis CI가 실행 중인 위치에서 생성
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>mv freelec-springboot2-webservice.zip deploy/freelec-springboot2-webservice.zip</span>
  - freelec-springboot2-webservice.zip을 deploy/freelec-springboot2-webservice.zip로 이동시킴
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>deploy</span>
  - S3로 파일 업로드 혹은 CodeDeploy로 배포 등 외부 서비스와 연동될 행위들을 선언
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>local_dir: deploy</span>
  - 앞에 생성한 deploy 디렉토리 지정
  - 해당 위치의 파일들만 S3로 전송
  
S3 버킷을 가보면 업로드가 성공한 걸 볼 수 있다.
  
<img src="https://images.velog.io/images/3hee_11/post/1e77a802-cf7b-483c-a11f-c8350c38c327/image.png">

## Travis CI와 AWS S3, CodeDeploy 연동
CodeDeploy를 이용하기 전 배포 대상인 EC2가 CodeDeploy를 연동받을 수 있게 IAM 역할을 생성한다.

### EC2에 IAM 역할 추가
IAM → 역할 탭을 클릭해서 이동한 다음 역할 만들기를 클릭한다.

>**🤔 IAM의 역할과 사용자의 차이는 뭘까?**
- 역할 : <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>AWS 서비스에만</span> 할당할 수 있는 권한
예) EC2, CodeDeploy, SQS 등
- 사용자 :  <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>AWS 서비스 외에</span> 사용할 수 있는 권한
예) 로컬 PC, IDC, 서버 등

서비스 선택에서 AWS 서비스, EC2를 차례대로 선택한다.

<img src="https://images.velog.io/images/3hee_11/post/3f38b4d0-9f7b-4ef5-9266-eefbd4a1c4d1/image.png">

정책에서는 EC2RoleForA를 검색해 AmazonEC2RoleforAWSCodeDeploy를 선택한다.

<img src="https://images.velog.io/images/3hee_11/post/5732d52e-52a4-4cf2-937e-00984014fea2/image.png">

역할의 이름과 태그를 등록한다.

<img src="https://images.velog.io/images/3hee_11/post/99ff6e3f-e7fc-4c64-aaf5-4d016343a9ad/image.png" width="70%">
<img src="https://images.velog.io/images/3hee_11/post/2d48d668-4648-4998-9092-d20ca45d870e/image.png">

EC2 인스턴스 목록으로 이동한 다음 내가 만든 인스턴스에 마우스 우클릭을 해서 보안 → IAM 역할 수정을 누른다.

<img src="https://images.velog.io/images/3hee_11/post/0211d6f4-1788-4643-a78a-86855bd8a4a0/image.png">

방금 생성한 역할을 선택한 다음 EC2 인스턴스를 **재부팅**한다.

<img src="https://images.velog.io/images/3hee_11/post/c58d98e2-838c-4530-96c3-4d1d4e6d60b9/image.png">

### CodeDeploy 에이전트 설치
EC2에 접속해 아래 명령어를 입력한다.

<img src="https://images.velog.io/images/3hee_11/post/45f9a2a2-ff11-4a2b-95d3-7b28fadffccd/image.png">

내려받기에 성공했다면 아래와 같은 메세지가 뜬다.

<img src="https://images.velog.io/images/3hee_11/post/0c518c94-25bd-4404-9b60-fb91edc522e1/image.png">

install 파일에 실행 권한을 주고 설치를 진행하면 오류가 난다.

<img src="https://images.velog.io/images/3hee_11/post/114b4db3-9caf-4ac0-be0f-5e79514b9f49/image.png">

루비가 설치 안된 상태라 뜨는 오류이므로 루비를 설치하고 다시 진행한다.

<img src="https://images.velog.io/images/3hee_11/post/62fdac97-4891-4c91-ae58-8c0a7f094ccf/image.png">

설치가 완료되면 Agent가 정상적으로 실행되고 있는지 확인한다. 
_아래처럼 출력되면 정상이다._

<img src="https://images.velog.io/images/3hee_11/post/d8408210-7503-468e-9a33-2eb5606f9661/image.png">

### CodeDeploy를 위한 권한 생성
CodeDeploy에서 EC2에 접근하려면 권한이 필요하다. AWS 서비스이니 IAM 역할을 생성한다.

서비스는 AWS 서비스, CodeDeploy를 선택한다.

<img src="https://images.velog.io/images/3hee_11/post/c7fd6b2d-ac33-4d0f-b042-8725c29497a5/image.png">

CodeDeploy는 권한이 하나뿐이라 선택없이 넘어간다.

<img src="https://images.velog.io/images/3hee_11/post/b4e7ed19-6722-4741-8f3d-933062733a43/image.png">

역할 이름과 태그를 등록한다.

<img src="https://images.velog.io/images/3hee_11/post/35a8d0a2-e3dc-4f13-8768-8d9558a5cd78/image.png" width="65%">
<img src="https://images.velog.io/images/3hee_11/post/95e16c42-a1fb-4121-b0cc-24374ee11264/image.png" width="70%">

### CodeDeploy 생성
>**📌 AWS 배포 관련 서비스**
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>Code Commit</span>
  - 깃허브와 같은 코드 저장소의 역할
  - 프라이빗 기능을 지원한다는 강점이 있지만 현재 깃허브에서도 무료로 지원
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>Code Build</span>
  - Travis CI와 마찬가지로 빌드용 서비스
  - 멀티 모듈을 배포하는 경우 사용해 볼만 하지만 규모있는 서비스에서는 대부분 젠킨스/팀시티 등을 이용
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>CodeDeploy</span>
  - AWS의 배포 서비스
  - 오토 스케일링 그룹 배포,블루 그린 배포,롤링 배포, EC2 단독 배포 등을 지원
  
CodeDeploy 서비스로 이동해 애플리케이션 생성 버튼을 클릭한다.

<img src="https://images.velog.io/images/3hee_11/post/912f0f09-80ee-41a5-b094-03084718d78b/image.png">

CodeDeploy의 이름과 컴퓨팅 플랫폼을 선택한다.

<img src="https://images.velog.io/images/3hee_11/post/de7ce6d4-c966-4d15-8bdd-f5e4bc6173f1/image.png">

생성이 완료되면 배포 그룹 생성 클릭

<img src="https://images.velog.io/images/3hee_11/post/198fe4f7-4901-4dd4-814d-fa887d1d5bf1/image.png">

배포 그룹 이름과 서비스 역할을 등록한다. 서비스 역할은 방금 생성한 CodeDeploy용 IAM 역할을 선택하면 된다.

<img src="https://images.velog.io/images/3hee_11/post/e7e5aa33-d356-4a10-a92b-4fe38c4ef8a4/image.png">

배포 유형은 **현재 위치를 선택**한다. 만약 배포할 서비스가 2대 이상이면 블루/그린을 선택한다.

<img src="https://images.velog.io/images/3hee_11/post/3c5e684e-d47f-4ce7-aa4a-81987759a75e/image.png">

환경 구성에는 Amazon EC2 인스턴스를 체크한다.

<img src="https://images.velog.io/images/3hee_11/post/bdf03bdf-4e8f-48c5-8b72-5d13926277bf/image.png">

마지막으로 배포 구성을 아래처럼 선택하고 로드밸런싱은 체크를 해제한다.

<img src="https://images.velog.io/images/3hee_11/post/c3e70595-7d0b-4973-88ac-588afda6cc03/image.png">

>**🤔 배포 구성이란?**
한 번 배포할 때 몇 대의 서버에 배포할지를 결정한다. 2대 이상이면 1대씩 배포할지, 30,50%씩 나눠서 배포할지 등 여러 옵션을 선택하겠지만 현재는 1대 서버이니 전체 배포하는 옵션을 선택한다.
>
_CodeDeployDefault.AllAtOnce는 한번에 다 배포하는 걸 의미_

### Travis CI, S3, CodeDeploy 연동
EC2 서버에 S3에 넘겨줄 zip 파일을 저장할 디렉토리를 생성한다.

<span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>mkdir ~/app/step2 && mkdir ~/app/step2/zip</span>

Travis CI의 Build가 끝나면 S3에 zip파일이 전송되고, 이 zip 파일은 /home/ec2-user/app/step2/zip으로 복사되어 압축을 풀 예정이다.

Travis CI의 설정은 .travis.yml로 진행했고 CodeDeploy의 설정은 appspec.yml에서 한다.

.travis.yml와 같은 위치에 appspec.yml을 생성하고 코드를 추가한다.

<img src="https://images.velog.io/images/3hee_11/post/429e1a93-3807-4f1a-b1cb-dd9accaf8087/image.png">

>- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>version: 0.0</span>
  - CodeDeploy의 버전
  - 프로젝트 버전이 아니므로 0.0 외에 다른 버전을 사용하면 오류남
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>source</span>
  - CodeDeploy에서 전달해준 파일 중 destination으로 이동시킬 대상 지정
  - 루트 경로(/)를 지정하면 전체 파일을 말함
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>destination</span>
  - source에서 지정된 파일을 받을 위치
  - 이후 Jar를 실행하는 등은 destination에서 옮긴 파일들로 진행
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>overwrite</span>
  - 덮어쓰기 여부
  
.travis.yml에도 CodeDeploy 내용을 추가한하고 푸시한다.

<img src="https://images.velog.io/images/3hee_11/post/29b5f7ba-32bd-489d-b991-e467f851ec6a/image.png">

Travis CI가 끝나면 CodeDeploy 화면 아래에서 배포가 수행되는 것을 볼 수 있다.

<img src="https://images.velog.io/images/3hee_11/post/4d006296-8510-48e4-ad1c-1509620e4692/image.png" width="60%">

배포가 끝났다면 아래 명령어로 파일들이 도착했는지 확인한다.

<img src="https://images.velog.io/images/3hee_11/post/751d41ba-f3da-4eaf-8e9b-d5cf4f4c6e9e/image.png">

## 배포 자동화 구성
앞의 과정으로 Travis CI, S3, CodeDeploy 연동까지 마쳤다. 이제 이것을 기반으로 실제 Jar를 배포하여 실행까지 해보자

### deploy.sh 파일 추가 
scripts 디렉토리와 스크립트 생성

<img src="https://images.velog.io/images/3hee_11/post/cf21538e-c192-46f5-b1e3-e45d574418e2/image.png">

>- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>CURRENT_PID</span>
  - 현재 수행 중인 스프링 부트 애플리케이션의 프로세스 ID를 찾는다.
  - 프로젝트 이름으로만 검색하면 다른 프로그램이 있을 수 있어 jar 프로세스를 찾은 뒤 ID를 찾는다.
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>chmod +x $JAR_NAME</span>
  - Jar 파일은 실행 권한이 없는 상태이기 때문에 nohup으로 실행할 수 있게 실행 권한을 부여한다.
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>$JAR_NAME > $REPOSITORY/nohup.out 2>&1 &</span>
  - nohup 실행 시 CodeDeploy는 무한 대기하기 때문에 이를 위해 nohup.out 파일을 표준 입출력용으로 별도로 사용한다.
  - 이렇게 하지 않으면 nohup.out파일이 생기지 않고, CodeDeploy 로그에 표준 입출력이 출력된다.
  _nohup이 끝나기 전까지 CodeDeploy도 끝나지 않으니 꼭 이렇게 해야함_
  
### .travis.yml 수정
현재는 프로젝트의 모든 파일을 zip 파일로 만드는데 실제로 필요한 파일들은 (Jar, appspec.yml, 배포를 위한 스크립트)이므로 .travis.yml 파일의 before_deploy를 수정한다.

```yml
before_deploy:
  - mkdir -p before-deploy # zip에 포함시킬 파일들을 담을 디렉토리 생성
  - cp scripts/*.sh before-deploy/ # scripts파일 추가
  - cp appspec.yml before-deploy/ # appsepc.yml 추가
  - cp build/libs/*.jar before-deploy/ # build/libs 목록 추가
  - cd before-deploy && zip -r before-deploy * # before-deploy로 이동 후 전체 압축
  - cd ../ && mkdir -p deploy # 상위 디렉토리 이동 후 deploy 디렉토리 생성
  - mv before-deploy/before-deploy.zip deploy/freelec-springboot2-webservice.zip # deploy로 zip파일 이동
```

>- Travis CI는 S3로 **특정 파일만 업로드가 안된다.** 
👉 디렉토리 단위로만 가능하기 때문에 before-deploy 디렉토리는 항상 생성
- before-deploy에는 zip 파일에 포함시킬 파일들을 저장
- zip -r 명령어를 통해 before-deploy 디렉토리 전체 파일을 압축

### appspec.yml 수정
아래 코드를 추가하고 푸시한다.
```yml
permissions:
  - object: /
    pattern: "**"
    owner: ec2-user
    group: ec2-user

hooks:
  ApplicationStart:
    - location: deploy.sh
      timeout: 60
      runas: ec2-user
```

 >- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>permissions</span>
  - CodeDeploy에서 EC2 서버로 넘겨준 파일들에게 ec2-user 권한을 줌
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>hooks</span>
  - CodeDeploy 배포 단계에서 실행할 명령어를 지정
  - ApplicationStart 단계에서 deploy.sh를 ec2-user 권한으로 실행하게 함
  - timeout: 60으로 스크립트 실행. 60초 이상 수행되면 실패. (무한정 기다릴 수 없으니 시간 제한을 둠)
  
Travis CI, CodeDeploy에서 확인하고 웹 브라우저에도 도메인을 입력해 확인한다.

### 실제 배포 과정 체험
build.gradle에서 프로젝트 버전을 변경한다.

>version '1.0.1-SNAPSHOT'

변경한 내용을 알 수 있게 src/main/resources/templates/index.mustache에 다음과 같이 Ver.2 텍스트를 추가한다.

<img src="https://images.velog.io/images/3hee_11/post/96c10c46-7bc4-4c38-88b6-3fe110ce6db2/image.png">

깃허브로 푸시하면 변경된 코드가 배포된 것을 확인할 수 있다.

## CodeDeploy 로그 확인
CodeDeploy에 관한 대부분의 내용은 /opt/codedeploy-agent/deployment-root에 있다. 해당 디렉토리로 이동한 다음 목록을 보자

<img src="https://images.velog.io/images/3hee_11/post/91ae6211-7c7f-4c05-aacc-12e5bf4e0e2c/image.png">

>- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>최상단의 영문과 대시(-)가 있는 디렉토리명은 CodeDeploy ID이다.</span>
   - 사용자마다 고유한 ID가 생성되어 각자 다른 ID가 발급된다.
  - 해당 디렉토리로 들어가 보면 배포한 단위별로 배포 파일들이 있다.
- <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>/opt/codedeploy-agent/deployment-root/deployment-logs/codedeploy-agent-deployments.log</span>
  - CodeDeploy 로그 파일이다.
  - CodeDeploy로 이루어지는 배포 내용 중 표준 입/출력 내용은 모두 여기에 있다( 작성한 echo 내용도 모두 표기 )
