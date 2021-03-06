### 프로그래밍 언어란?
* 사람이 컴퓨터에 명령을 내릴 때 필요한 언어이다.
* 고급 언어와 저급 언어로 구분된다.
   * 고급 언어 : 프로그래밍 언어 중에 사람이 이해하기 쉬운 언어이다. 컴퓨터가 바로 이해할 수 없기 때문에 컴파일을 통해 컴퓨터가 이해할 수 있는 기계어로 변환하여 컴퓨터가 사용한다.
   * 저급 언어 : 기계어와 가까운 언어를 말하며 대표적으로 어셈블리어가 있다.
* 고급 언어(Java, C, C++)로 작성된 내용을 소스라고 부르며 이 소스는 컴파일러라는 소프트웨어에 의해 기계어로 변환된 후 컴퓨터에서 실행된다.

### 자바란?
 * 자바의 특징
   * 이식성이 높은 언어이다.
      * 이식성이란 서로 다른 실행 환경을 가진 시스템 간에 프로그램을 옮겨 실행할 수 있는 것을 말한다. 
      * 자바로 개발된 프로그램은 소스 파일을 수정하지 않아도 <b>자바 실행 환경(JRE : Java Runtime Environment)이 설치되어 있는 모든 운영체제에서 실행이 가능하기 때문에</b> 이식성이 높다.
      
   * 객체지향 언어이다.
      * 프로그램을 개발하는 기법으로 부품에 해당하는 객체를 먼저 만들고 이것들을 조립해서 프로그램을 완성하는 기법을 객체 지향 프로그래밍(OOP : Object Oriented Programming)이라고 한다. 이때 사용되는 언어를 객체 지향 언어라고 한다.
      * 객체 지향 언어가 가져야 할 캡슐화, 상속, 다형성을 지원하고 있다.
         * 캡슐화 : 객체의 변수, 메서드를 외부 객체가 접근하지 못하게 감싸는 것이다. 
         * 상속 : 부모클래스의 변수와 메서드를 자식클래스가 물려받는 것이다.
         * 다형성 : 하나의 객체가 여러 타입을 가질 수 있는 것이다. 
         
   * 함수적 프로그래밍을 위해 람다식을 지원한다.(자바8 이상)
   
   * 메모리를 자동으로 관리한다.
       * 개발자가 메모리에 직접 접근할 수 없도록 되어있으며 메모리를 자바가 직접 관리한다. 객체 생성 시 자동으로 메모리 영역을 찾아서 할당하고 사용이 끝나면 Garbage Collector를 실행시켜 사용하지 않는 객체를 제거시켜준다.
   
   * 다양한 애플리케이션을 개발할 수 있다.
      * Java SE(Standard Edition) - 기본 에디션 : <b>자바 가상 기계(JVM : Java Vertual Machine)를 비롯해서 자바 프로그램 개발에 필수적인 도구와 라이브러리, API를 정의한다.</b> 프로그램을 개발하기 위해서는 반드시 Java SE(Standard Edition)의 구현체인 자바 개발 키트 JDK(Java Development Kit)를 설치해야 한다.
      * Java EE(Enterprise Edition) -서버용 애플리케이션 개발 에디션 : 분산 환경(네트워크, 인터넷)에서 서버용 애플리케이션을 개발하기 위한 도구 및 라이브러리 API를 정의한다. 서버용 애플리케이션으로는 Servlet/JSP를 이용한 웹 애플리케이션, 분산 처리 컴포넌트인 EJB(Enterprise Java Bean) 그리고 XML 웹 서비스 등이 있다.
   
   * 스레드 생성 및 제어와 관련된 라이브러리 API를 제공하고 있기 때문에 운영체제에 상관없이 멀티 스레드(Multi-Thread)를 쉽게 구현할 수 있다.
   
   * 동적 로딩(Dynamic Loading)을 지원한다.
      * 애플리케이션이 실행될 때 모든 객체가 생성되지 않고 객체가 필요한 시점에 클래스를 동적 로딩해서 생성한다.
      * 수정을 하더라도 해당 클래스만 수정하면 되므로 전체 애플리케이션을 다시 컴파일할 필요가 없다. -> 유지보수가 빠름
   
   * 오픈소스 라이브러리가 풍부하다.   

* 자바 가상 기계(JVM : Java Vertual Machine)
    * 자바는 완전한 기계어가 아니기 때문에 이것을 <b>해석하고 실행할 수 있는 가상의 운영체제가 필요한데 이것이 JVM이다.</b>
    * 자바 프로그램 실행 과정
        * 소스 파일을 컴파일러로 컴파일 -> 확장자가 .class인 바이트 코드 파일 생성
        * 바이트 코드 파일이 JVM 구동 명령어에 의해 JVM에서 해석되고 해당 운영체제에 맞게 기계어로 번역된다.
        
### 자바 개발 환경 구축
* 자바 개발 도구(JDK) 설치
    * Java SE의 구현체는 JDK(JRE + 개발에 필요한 도구)과 JRE(JVM, 라이브러리 API)가 있다.
    * JDK 설치 방법
        * [오라클](https://www.oracle.com/java/technologies/downloads/)에서 Java8이상을 운영체제에 맞게 설치한다.
        * 내 컴퓨터 > 속성 > 고급 시스템 설정 > 환경변수로 들어가준다.
        * 시스템 변수에서 새로 만들기를 누르고 시스템 변수 편집 화면이 나오면 변수 이름에 JAVA_HOME, 변수 값에 JDK가 설치된 경로를 넣는다.
        * 시스템 변수에 있는 Path 편집 화면에서 변수 값 앞에 %JAVA_HOME%\bin;을 추가한다.
        * 환경변수가 잘 적용됐는지 확인하기 위해 cmd에서 javac -version을 입력하고 버전이 출력되는지 확인한다.

* API 도큐먼트
    * JDK에 포함된 API들이 방대하기 때문에 쉽게 찾을 수 있도록 [API DOCUMENT](https://docs.oracle.com/javase/8/docs/)를 제공한다.
    
### 자바 프로그램 개발 순서
* 소스 작성에서부터 실행까지
    * .java 소스 파일 작성 
    * 컴파일러(javac.exe)로 바이트 코드 파일(.class) 생성
    * JVM 구동 명령어(java.exe)로 실행
* 프로그램 소스 분석
    * 클래스 : 필드 또는 메소드를 포함하는 블록
    * 메소드 : 어떤 일을 처리하는 실행문들을 모아 놓은 블록
    
### 이클립스 설치 
* 이클립스는 프로그램을 개발하기 위한 통합 개발 환경(IDE : Integrated Development Environment)을 제공한다.
