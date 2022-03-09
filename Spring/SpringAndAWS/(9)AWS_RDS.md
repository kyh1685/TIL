_스프링부트와 AWS로 혼자 구현하는 웹서비스 책을 정리한 내용입니다._

<span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>RDS는 AWS에서 지원하는 클라우드 기반 관계형 데이터베이스</span>로 하드웨어 프로비저닝, 데이터베이스 설정, 패치 및 백업과 같은 운영 작업을 자동화하여 지원하는 서비스이다. 

조정 가능한 용량을 지원하여 예상치 못한 양의 데이터가 쌓여도 비용만 추가로 내면 정상적으로 서비스가 가능하다는 장점이 있다.

## RDS 인스턴스 생성
RDS 대시보드로 이동해서 데이터베이스 생성 클릭

<img src="https://images.velog.io/images/3hee_11/post/055c528c-10ae-45a9-ac51-1a8852e6b9ed/image.png" width="80%">

엔진 옵션은 MariaDB를 선택한다.

>**🤔 MariaDB 선택 이유는?**
>
<span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>가격</span> 
RDS의 가격은 라이센스 비용 영향을 받는다. 상용 데이터베이스인 오라클, MSSQL이 오픈 소스인 MySQL, MariaDB, PostgreSQL보다 동일한 사양 대비 가격이 더 높다.
>
<span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>Amazon Aurora 교체 용이성</span>
- Amazon Aurora는 AWS에서 MySQL과 PostgreSQL을 클라우드 기반에 맞게 재구성한 데이터베이스이다(성능이 더 뛰어남) 
- AWS에서 직접 엔지니어링 하고 있기 때문에 계속 발전하고 있다.
👉 클라우드 서비스에 가장 적합한 데이터베이스
>
그렇기 때문에 호환 대상이 아닌 오라클, MSSQL을 선택할 필요 없음
_시작 단계에서 Aurora를 안쓰는 이유는 프리티어 대상이 아니고 비용이 비싸기 때문_
>
📌 MariaDB는 MySQL(단순 쿼리 처리 성능↑) 기반으로 만들어져 사용법이 유사하나 MySQL보다 향상된 성능과 활성화된 커뮤니티, 다양한 기능과 스토리 엔진 등의 장점이 있다.

<img src="https://images.velog.io/images/3hee_11/post/d4d57938-5cf5-4931-9c04-4c8123496488/image.png">

프리티어 선택하기

<img src="https://images.velog.io/images/3hee_11/post/838aca4f-8ee5-415f-9086-372a2dcc4b2c/image.png">

DB 인스턴스 이름과 사용자 정보를 등록한다. **등록한 사용자 정보로 실제 데이터베이스에 접근해야하니 메모해놓자**

<img src="https://images.velog.io/images/3hee_11/post/8553c690-282c-4b2f-adb2-b75b0c7e4954/image.png" width="70%">

DB 인스턴스 클래스, 가용성 및 내구성은 기본 설정으로 놔둔다.

<img src="https://images.velog.io/images/3hee_11/post/a265f0b6-14a6-47b0-96cf-f1e7ecd0f21f/image.png" width="80%">

<img src="https://images.velog.io/images/3hee_11/post/ff36fbd0-a2fd-4c3b-aa4a-3816767b9254/image.png">

<img src="https://images.velog.io/images/3hee_11/post/1920bfa8-59bd-428e-8073-2da9447525b0/image.png" width="90%"> 

네트워크에서는 퍼블릭 액세스를 예로 변경한다. 이후 보안 그룹에서 지정된 IP만 접근하도록 막을 예정이다.

<img src="https://images.velog.io/images/3hee_11/post/89db2127-c5e0-4954-940f-18f3e979c0f3/image.png" width="90%"> 

데이터베이스 옵션은 이름을 제외한 나머지를 아래와 동일하게 설정하고 완료 버튼을 클릭한다.  _파라미터 그룹은 이후 변경할 예정_

<img src="https://images.velog.io/images/3hee_11/post/baa8e3f1-276d-489a-ab65-a4fa08d9c203/image.png" width="90%"> 

<img src="https://images.velog.io/images/3hee_11/post/f90de897-f607-4f2c-bffc-351199287652/image.png" width="90%"> 

## RDS 운영환경에 맞는 파라미터 설정
왼쪽 카테고리에서 파라미터 그룹 탭 선택하고 파라미터 그룹 생성 클릭

<img src="https://images.velog.io/images/3hee_11/post/e9254473-cafc-477e-a7ee-b8887da1981e/image.png" width="30%"> 

<img src="https://images.velog.io/images/3hee_11/post/a7cad76b-2c24-4628-b9aa-c929aa8e348f/image.png"> 

파라미터 그룹 패밀리는 **이전에 생성한 MariaDB와 같은 버전**으로 맞춘다.

<img src="https://images.velog.io/images/3hee_11/post/80444235-b621-4439-b73d-a8dc84e2fba1/image.png"> 

생성이 완료되면 파라미터 그룹 목록에서 생선된 그룹을 클릭하고 편집을 누른다.

<img src="https://images.velog.io/images/3hee_11/post/240ea957-140e-4e6d-922d-f6b532ab5ac8/image.png">

<img src="https://images.velog.io/images/3hee_11/post/f6fc2322-2340-401d-8b58-0cbfc71c9735/image.png">

### 타임존

편집 모드가 되면 time_zone을 검색해서 아래처럼 서울로 변경한다.

<img src="https://images.velog.io/images/3hee_11/post/0208fb22-669b-4df5-afdb-41d67eb5085b/image.png">
 

### Character Set
utf8과 utf8mb4의 차이는 이모지 저장 가능 여부이다. utf8mb4는 이모지 저장이 가능해서 많이 사용한다.

#### utf8mb4로 변경
- character_set_client
- character_set_connection
- character_set_database
- character_set_filesystem
- character_set_results
- character_set_server

#### utf8mb4_general_ci로 변경
- collation_connection
- collation_server

### Max Connection
RDS의 Max Connection은 인스턴스 사양에 따라 자동으로 정해진다. 현재 프리티어 사양으로는 약 60개의 커넥션만 가능해서 좀 더 넉넉한 값으로 지정한다.

<img src="https://images.velog.io/images/3hee_11/post/1ec9dba1-2ca0-4f42-aebd-bea668460772/image.png">

설정이 다 되었다면 오른쪽 위의 변경 사항 저장을 클릭해 최종 저장한다.

---

왼쪽 카테고리에 데이터베이스를 선택하고 생성해놓은 데이터베이스에 들어가서 수정을 누른다.

<img src="https://images.velog.io/images/3hee_11/post/729a4f59-ae5e-4fe5-90e5-9a9bbcde9f2d/image.png">

데이터베이스 옵션에서 기본으로 되어있는 DB 파라미터 그룹을 방금 생성한** 신규 파라미터 그룹으로  변경**한다.

<img src="https://images.velog.io/images/3hee_11/post/f1162f0a-deb7-49e4-b8e6-6b346b09c315/image.png">

수정 사항 요약에서 **반영 시점을 즉시 적용**으로 하고 **재부팅**한다. '예약된 다음 유지 관리 기간에 적용'을 선택하면 지금 하지 않고 새벽 시간대에 진행한다.

<img src="https://images.velog.io/images/3hee_11/post/4a96faf1-0a36-4e69-b5ad-8a5f65249b33/image.png">

## 내 PC에서 RDS 접속
로컬 PC에서 RDS로 접근하기 위해 <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>RDS의 보안 그룹에 내 PC의 IP를 추가</span>해야한다.

RDS의 세부 정보 페이지에서 보안 그룹을 클릭한다.

<img src="https://images.velog.io/images/3hee_11/post/eebe8648-4906-44e9-a04e-aaef4421f216/image.png">

브라우저 다른 창을 열어서 **EC2에 사용된 보안 그룹의 그룹 ID를 복사**한다.

<img src="https://images.velog.io/images/3hee_11/post/4288cfbe-ee35-4084-88ae-24656ac59a50/image.png">

복사된 보안 그룹 ID와 내 IP를 **RDS 보안 그룹의 인바운드로 추가**한다.
인바운드 규칙 유형에서는 MYSQL/Aurora를 선택하면 자동으로 3306 포트가 선택된다.

>- 보안 그룹 첫 번째 줄 : 현재 내 PC의 IP 등록
- 보안 그룹 두 번째 줄 : EC2의 보안 그룹 추가
>
👉 이렇게 하면 **EC2와 RDS 간 접근이 가능**해짐
>
EC2의 경우 이후에 여러 대가 될 수 있는데 매번 IP를 등록할 수는 없으니 보안 그룹 간에 연동을 함


<img src="https://images.velog.io/images/3hee_11/post/0a72a837-7a3e-4157-b8b5-4dcf12b35af5/image.png">

### Database 플러그인 설치
RDS 정보 페이지에서 엔드포인트를 복사한 다음 인텔리제이에서 Database Navigator을 설치한다.

<img src="https://images.velog.io/images/3hee_11/post/136d397c-f620-4bd2-8c32-eb0a1e97a846/image.png">

설치가 다 되었다면 인텔리제이를 재시작한 뒤 Database Browser을 실행한다.

<img src="https://images.velog.io/images/3hee_11/post/1324c1d2-248e-4653-be6c-ef3e95ed297b/image.png">

왼쪽 사이드바에 DB Browser이 뜨는데 아래처럼 클릭해서 MySQL 접속 정보를 연다.

<img src="https://images.velog.io/images/3hee_11/post/af55afea-20d8-42a3-a8fc-7f265d242fc6/image.png">

이전에 생성한 RDS의 정보를 등록하고(**Host는 방금 전 복사한 엔드 포인트**) Test Connection을 클릭해 연결 테스트를 해본다.

<img src="https://images.velog.io/images/3hee_11/post/3158d5db-335e-4d9e-a412-69ab8ad846d3/image.png">

**🚨 오류 발생** 
Ultimate 버전같은 경우에는 database 탭에서 MariaDB를 연동해주면 되지만 나는 Community 버전을 사용하기 때문에 그런게 없다..

<img src="https://images.velog.io/images/3hee_11/post/52bb6914-54df-4ac5-b7f7-1079a8c440b4/image.png">

#### ~ MariaDB 드라이버 설치하고 DB 연결하기 ~
검색해보니 MariaDB와 MySQL의 드라이버 관련 문제인 것 같아서 MariaDB의 드라이버를 설치하고 DB 연결하는 부분을 커스텀으로 설정했다.

**[MariaDB 사이트](https://downloads.mariadb.com/Connectors/java/connector-java-2.7.3/)**에서 mariadb-java-client-2.7.3.jar을 다운받는다.

<img src="https://images.velog.io/images/3hee_11/post/ff76cbe0-47b9-4003-a3ef-eca09cd826bc/image.png" width="70%">

새로 추가해서 Custom을 선택한다.

<img src="https://images.velog.io/images/3hee_11/post/e76c4638-ed87-4e00-882c-3fa72735b3f3/image.png">

URL에 <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>jdbc:mariadb://RDS의 엔드 포인트:3306</span>과 User, Password를 입력하고 Driver library의 위치를 방금 다운로드 받은 위치로 설정한다.

<img src="https://images.velog.io/images/3hee_11/post/a8c5c4b8-3776-4be4-88af-be81208ab51c/image.png" width="80%">

Test Connection 성공!

<img src="https://images.velog.io/images/3hee_11/post/7141ea4a-96b6-4c98-9801-c9f6e7c96b45/image.png">

위쪽에 있는 Open SQL Console을 클릭하고 New SQL Console을 선택해서 SQL을 실행할 콘솔창을 연다.

<img src="https://images.velog.io/images/3hee_11/post/88a846d0-ecee-469a-a98a-81c2a91628d6/image.png">

생성된 콘솔창에 <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>use AWS RDS 웹 콘솔에서 지정한 데이터베이스명;</span> SQL을 실행해보자. **쿼리가 수행될 database를 선택하는 쿼리**이다.

만약 RDS 생성시 지정한 database 명을 잊었다면 아래처럼 Schema 항목에서 볼 수 있다.

<img src="https://images.velog.io/images/3hee_11/post/7461581e-4895-4851-955e-479af74e7cd7/image.png">

쿼리문을 드래그한 뒤 Execute Statement 버튼을 누른다. SQL statement executed successfully 메시지가 떴다면 쿼리가 정상적으로 수행된 것이다

<img src="https://images.velog.io/images/3hee_11/post/824b19e5-9ef6-4855-8726-05fbc1b2813d/image.png">

데이터베이스가 선택된 상태에서 <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>show variables like 'c%'</span>로 현재 character_set, collation 설정을 확인한다.

utf8mb4가 적용되지 않고 latin1로 되어 있는 필드는 직접 변경한다.

<img src="https://images.velog.io/images/3hee_11/post/6bbcb61f-797e-44c4-b8dd-f69083547106/image.png">

>ALTER DATABASE 데이터베이스명
CHARACTER SET = 'utf8mb4'
COLLATE = 'utf8mb4_general_ci'

위에 방법처럼 해도 안됐었는데 RDS의 파라미터 그룹으로 다시 가서 character_set_server도 utf8mb4로 설정해주고 리부트하니 적용이 잘 됐다!

<span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>select @@time_zone, now();</span>로 타임존도 확인하기

<img src="https://images.velog.io/images/3hee_11/post/9c7a452e-266b-4f2d-bd69-36f4673af917/image.png">

마지막으로 한글명이 잘 들어가는지 테이블 생성과 insert 쿼리를 실행해본다.

🙋‍♀️ 테이블 생성은 인코딩 설정 변경 전에  생성하면 안됨
👉 만들어질 때의 설정값을 그대로 유지하고 있어 강제로 변경해줘야 함

>CREATE TABLE test (
    id bigint(20) NOT NULL AUTO_INCREMENT,
    content varchar(255) DEFAULT NULL,
    PRIMARY KEY (id)   
) ENGINE=InnoDB;
>
insert into test(content) values ('테스트');
>
select * from test;

<img src="https://images.velog.io/images/3hee_11/post/10b288a9-624c-4faf-8d39-05e4cdaccd41/image.png">

## EC2에서 RDS에서 접근 확인
putty로 EC2의 ssh에 접속하고 <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>MySQL 접근 테스트를 위해 MySQL CLI를 설치한다.</span>

<img src="https://images.velog.io/images/3hee_11/post/700de2e2-aaa6-46e6-9011-385438ed2130/image.png">

설치가 완료되면 로컬에서 접근하듯이 계정, 비밀번호, 호스트 주소를 사용해 RDS에 접속한다.

>mysql -u 계정 -p -h Host주소

<img src="https://images.velog.io/images/3hee_11/post/ff77f723-cf7d-4e82-9d3d-3f3391ead6a4/image.png">

패스워드 입력하라는 메세지가 나오면 패스워드를 입력한다( _패스워드는 쳐도 눈에 안보이니 그냥 치면 됨_ )

EC2에서 RDS로 접속된 것을 아래처럼 확인할 수 있다.

<img src="https://images.velog.io/images/3hee_11/post/38d71f1f-5190-44eb-9f0e-9dac37da8e43/image.png">

실제로 생성한 RDS가 맞는지  <span style='padding: 2px; background-color: #e2e2e2; border-radius: 4px'>show databases;</span> 쿼리를 실행해 확인한다. 

<img src="https://images.velog.io/images/3hee_11/post/0e8de652-1f57-4d21-b32f-7c127d27fa86/image.png">
