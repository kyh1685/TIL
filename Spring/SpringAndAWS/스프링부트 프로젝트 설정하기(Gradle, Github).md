_스프링부트와 AWS로 혼자 구현하는 웹서비스 책을 정리한 내용입니다._

>✅ 이 프로젝트는 [start.spring.io](start.spring.io)를 사용하지 않고 하나씩 직접 설정해본다.

## 프로젝트 설정

### build.gradle
>**💡 설명**
- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>ext</span> : build.gradle에서 사용하는 전역 변수 설정
- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>io.spring.dependency-management</span> : 스프링부트의 의존성을 관리해주는 플러그인
- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>repositories</span> : 각종 의존성(라이브러리)들을 어떤 원격 저장소에 받을지 지정
- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>mavenCentral</span> : 기본적으로 많이 사용하지만, 본인이 만든 라이브러리를 업로드하려면 많은 설정이 필요해서 점점 공유가 안된다는 단점이 있음
- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>jcenter</span> : 라이브러리 업로드가 간단하며 jcenter에 업로드하면 mavenCentral에도 업로드되도록 자동화가 가능함
- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>dependencies</span> : 프로젝트 개발에 필요한 의존성을 선언

```java
buildscript {
    ext {
        springBootVersion = '2.1.7.RELEASE'
    }
    repositories {
        mavenCentral()
        jcenter()
    }
    // 특정 버전을 명시하지 않기! 그래야 springBootVersion의 버전을 적용!
    dependencies {
        classpath("org.springframework.boot:spring-boot-gradle-plugin:
        ${springBootVersion}")
    }
}

apply plugin: 'java'
apply plugin: 'eclipse'
apply plugin: 'org.springframework.boot'
apply plugin: 'io.spring.dependency-management'

group 'com.jojoldu.book'
version '1.0-SNAPSHOT'
sourceCompatibility = 1.8

repositories {
    mavenCentral()
}

dependencies {
	implementation('org.springframework.boot:spring-boot-starter-web')
    testImplementation('org.springframework.boot:spring-boot-starter-test')
}

test {
    useJUnitPlatform()
}
```
#### 🚨 주의
책에서의 Gradle 버전은 4.10.2이라 <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>dependencies</span>에서 <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>compile과 testCompile</span>를 사용했지만 현재  버전은 7.1이므로 <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>implementation와 testImplementation</span>로 수정함

> 💡 Gradle 버전은 gradle→wrapper→gradle-wrapper.properties에서 확인

## Github
- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>Ctrl + Shift + A</span>을 눌러 Action 검색창을 열고 share project on github를 검색한다.
- 해당 Action을 선택하면 깃허브 로그인 화면이 나온다.
- 로그인을 하면 깃허브에 생성할 저장소 정보를 입력하는 화면이 나온다.
- share 버튼을 누르면 깃허브 저장소와 동기화를 진행한다. 
- 프로젝트의 첫 번째 커밋을 위한 창이 뜨면 .idea 디렉토리의 체크를 해제한다.
_인텔리제이에서 프로젝트 실행시 자동으로 생성하는 파일로 불필요함_
![](https://images.velog.io/images/3hee_11/post/7f091b39-478f-43d8-96f9-2a2a3d09fd21/image.png)
- 커밋메세지 작성후 add 버튼을 누르면 커밋과 깃허브 푸시가 진행된다.

### .ignore 플러그인
인텔리제이에는 .gitignore파일에 대한 기본적인 지원이 없으나 대신 플러그인에서 지원한다.

#### 기능
- 파일 위치 자동완성
- 이그노어 처리 여부 확인
- 다양한 이그농 파일 지원

#### 설정
- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>setting</span> → <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>plugins</span> → <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>marketplace</span>
- .ignore 검색후 install
- 🚨 인텔리제이 재시작해야 설치한 플러그인이 적용된다 🚨

### .gitignore
앞으로 커밋할 때도 .idea 폴더를 쭉 제외시켜보자
- 프로젝트에서 마우스 우클릭하기
- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>new</span> → <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>.ignore file</span> → <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>.gitignore file (Git)</span>
- 미리 만들어둔 이그노어 템플릿이 없기 때문에 아무것도 선택하지않고 Generate
![](https://images.velog.io/images/3hee_11/post/7f33dfa2-2904-4fad-b421-e2e774a15ecb/image.png)
- 생성된 이그노어 파일에 <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>.gralde</span>과 <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>.idea</span> 작성하기
![](https://images.velog.io/images/3hee_11/post/4af38028-fad7-47be-b955-3f82f2322846/image.png)
- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>Ctrl + K</span> 혹은 <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>메뉴 → Git → Commit</span>에서 .gitignore 파일을 선택하고 메세지를 작성한 다음 Commit 클릭
- <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>Ctrl + Shift + K</span> 혹은 <span style='padding: 0px 7px; background-color: #e2e2e2; border-radius: 4px'>메뉴 → Git → Push</span>에서 클릭
![](https://images.velog.io/images/3hee_11/post/a004a52a-2779-4672-8a2b-7b9cebac1ddf/image.png)
