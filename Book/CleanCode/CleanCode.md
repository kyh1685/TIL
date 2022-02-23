## 목차

- [추천사](#추천사)
- [1장 깨끗한 코드](#1장-깨끗한-코드)
- [2장 의미있는 이름](#2장-의미있는-이름)
- [3장 함수](#3장-함수)
- [4장 주석](#4장-주석)
- [5장 형식 맞추기](#5장-형식-맞추기)
- [6장 객체와 자료구조](#6장-객체와-자료구조)
- [7장 오류 처리](#7장-오류-처리)
- [9장 단위 테스트](#9장-단위-테스트)
- [10장 클래스](#10장-클래스)

## 추천사

#### 😃 책에서 기억하고 싶은 내용을 써보세요
- 사소한 곳에서 발휘하는 정직은 사소하지 않다.
- 세세함에 몰두하는 태도는 탁월함을 추구하는 모든 노력에서 공통으로 발견된다.
- 진정으로 책임있는 개발자라면 제품 생명주기까지 고려해야 한다.
- 읽기 좋은 코드는 돌아가는 코드만큼이나 중요하다.

#### 🤔 오늘 읽은 소감은? 떠오르는 생각을 가볍게 적어보세요
전에 프로젝트를 진행했을 때, 설계의 중요성을 깨닫지 못해서 세부적인 것에 신경을 못 썼던 것이 생각났다. 설계에 신경을 못 썼더니 유지보수가 쉽지 않았고 프로젝트가 아예 마무리 된 후에는 다시 들여다 볼 엄두가 안났다. 이 책을 읽으니 그 때 생각이 나면서 설계의 중요성을 더욱 실감하게 된다. 다음 프로젝트를 진행하게 된다면 이 책을 떠올리며 설계 단계가 가장 중요하다는 점을 상기시켜야겠다.

#### 🔎 궁금한 내용이 있거나, 잘 이해되지 않는 내용이 있다면 적어보세요 
X

#### 📝 소감 요약
- 소프트웨어 개발에서 아키텍쳐는 중요하다.
- 리팩터링은 완료라는 개념의 일부분이어야 한다.
- 무언가를 익히는 데에는 이론도 중요하지만 실전도 중요하다. 이론만 안다고 해서 그것을 완벽하게 구현할 수는 없다.

## 1장 깨끗한 코드

#### 😃 책에서 기억하고 싶은 내용을 써보세요
- 제대로 명시한 요구사항은 코드만큼 정형적이며 테스트 케이스로 사용해도 좋다.
- 궁극적으로 코드는 요구사항을 표현하는 언어라는 사실을 명심한다.
- 좋은 코드를 사수하는 일은 프로그래머의 책임이다.
- 빨리 가는 유일한 방법은 언제나 코드를 최대한 깨끗하게 유지하는 습관이다.
- 깨끗한 코드와 나쁜 코드를 구분할 줄 안다고 깨끗한 코드를 작성할 줄 안다는 뜻은 아니다.
- 코드 감감이 있는 프로그래머는 나쁜 모듈을 보면 좋은 모듈로 개선할 방안을 떠올린다.
- 논리가 간단해야 버그가 숨어들지 못한다. 
- 의존성을 최대한 줄여야 유지보수가 쉬워진다. 
- 깨끗한 코드는 한 가지를 제대로 한다.
- 나쁜 코드는 나쁜 코드를 유혹한다
- 깨끗한 코드는 세세한 사항까지 꼼꼼하게 처리하는 코드다.
- 테스트 케이스가 없는 코드는 깨끗한 코드가 아니다.
- 언어를 단순하게 보이도록 만드는 열쇠는 프로그래머다.

#### 🤔 오늘 읽은 소감은? 떠오르는 생각을 가볍게 적어보세요
몇 번의 프로젝트를 경험하고 몇 가지 깨달은 사실들이 있다. 첫 번째는 저번 TIL에 작성한 설계의 중요성이고 두 번째는 깨끗한 코드의 중요성이다. 두 번째 사실을 알게 된 것은 내가 학원에서 프로젝트를 진행했을 때이다.

그 당시 나는 이 책의 존재조차 몰랐고 깨끗하게 코드를 작성해야 한다는 사실도 몰랐으나 같은 팀원 K씨의 코드를 보고 그 사실을 단번에 깨달을 수 있었다. K씨는 코드를 작성할 때 자신만 알아볼 수 있는 변수명을 사용했고 코드에 들여 쓰기를 전혀 하지 않았다. 각자 파트를 진행하느라 바빴기 때문에 나는 그것에 신경을 쓰지 못했다. 그 후에 그 부분에서 오류가 발생했을 때 코드를 열심히 뜯어봤으나 도저히 어떤 코드인지 이해하기가 힘들었다.

이건 K씨를 원망하기 위해 꺼낸 이야기가 아니다. 내가 그 경험으로 인해 깨끗한 코드가 얼마나 중요한지 깨닫게 되었다는 것을 말하고 싶어서 꺼낸 이야기다. 이 경험으로 인해 나는 그래디 부치의 "깨끗한 코드는 단순하고 직접적이며, 잘 쓴 문장처럼 읽힌다."는 말에 백퍼센트 동의하게 되었다. 

나도 내 코드를 보며 반성을 많이 한다. 종종 알고리즘을 풀고 다른 사람의 풀이를 둘러본다. 똑같은 문제를 푸는데도 어떤 사람은 단순하고 이해하기 쉽게 푸는 것을 보며 나는 아직 한참 멀었구나라는 생각을 한다.

이 책을 완독했을 때에는 내게도 좋은 코드와 나쁜 코드를 구별하는 능력이 생겼으면 좋겠다. 나쁜 코드를 좋은 코드로 변경하는 데에는 시간이 걸리겠지만 그래도 조금씩 발전해서 어제보다는 더 나은 개발자가 되고 싶다. 

#### 🔎 궁금한 내용이 있거나, 잘 이해되지 않는 내용이 있다면 적어보세요 
X

#### 📝 소감 요약
- 요구사항이 제대로 명시되어 있어도 언젠가는 정밀한 표현 즉, 코드가 필요하다.
- 나쁜 코드는 나쁜 코드를 불러온다. 그렇기 때문에 개발자는 어떤 상황에도  최대한 좋은 코드를 사수해야 한다. 
- 깨끗한 코드란 단순하고 직관적인 코드이며 한 가지를 제대로 한다.
- 코드를 짤 때는 자신이 저자라는 사실과 노력을 판단할 독자가 있다는 사실을 기억해라
- 잘 짠 코드가 전부는 아니다. 시간이 지나도 언제나 깨끗하게 유지해야 한다.
- 이 책을 읽는다고 뛰어난 프로그래머가 되진 않는다. 단지 뛰어난 프로그래머가 생각하는 방식과 그들이 사용하는 기술을 소개할 뿐이다.\

## 2장 의미있는 이름

#### 😃 책에서 기억하고 싶은 내용을 써보세요
- 의도가 분명하게 이름을 지어라.
- 변수에 따로 주석이 필요하다면 의도를 분명히 드러내지 못했다는 말이다.
- 유사한 개념은 유사한 표기법을 사용한다.
- 읽는 사람이 차이를 알도록 이름을 지어라.
- 이름 길이는 범위 크기에 비례해야 한다.
- 클래스 이름과 객체 이름은 명사나 명사구가 적합하다.
- Manager, Processor, Data, Info 등과 같은 단어는 피한다.
- 메서드 이름은 동사나 동사구가 적합하다.
- 한 개념에 한 단어를 사용하라.
  - 똑같은 메서드를 클래스마다 get, fetch 등으로 제각각 부르면 혼란스러움
- 한 단어를 두 가지 목적으로 사용하지 마라.
  - 여러 클래스에 add()가 있을 때 매개변수, 리턴값이 의미적으로 같다면 괜찮다.
  - 지금까지 구현한 add()가 두 개의 값을 더하는 기능을 한다면 새로 추가된 add()가 집합에 값을 추가하는 기능을 할 때 이 메서드는 add()라는 이름은 적합하지 않다.
- 의미있는 맥락을 추가하라.
  - 주소를 뜻하는 변수들이 있다면 그냥 state보다 addrState가 맥락이 더 분명하다.
  - Address 클래스를 생성하면 변수가 더 큰 개념에 속한다는 사실이 분명해진다.
- 불필요한 맥락을 없애라.

#### 🤔 오늘 읽은 소감은? 떠오르는 생각을 가볍게 적어보세요
개발을 처음 배웠을 때 변수명을 잘 짓는 게 중요하다는 말을 들었다. 그 이후에 변수명을 다른 이름들과 명확하게 구분 짓기 위해 변수명을 길게 지었다. 하지만 변수명의 길이가 길어지니 이번에는 가독성이 떨어지는 것 같았다. 그래서 어떻게 해야하나 고민했었는데 책에서 긴 이름이 짧은 이름보다 좋다고 하니 다행이라는 생각이 든다. 

이렇게 책에서 변수명을 잘 지으라고 말만 하는 게 아니라, 어떻게 잘 지어야 하는지 방법을 직접 제시해줘서 좋았다. 더 많은 예시를 보기 위해 다른 사람의 코드에서 변수명을 주의 깊게 살펴봐야겠다.

#### 🔎 궁금한 내용이 있거나, 잘 이해되지 않는 내용이 있다면 적어보세요 
- 정적 팩토리 메소드란 ? 객체 생성의 역할을 하는 클래스 메서드!
- 생성자를 중복정의할 때 왜 정적 팩토리 메서드를 사용해야 하는가?
  - 메서드 이름에 객체의 생성의 목적을 담아 낼 수 있다.
  
참고) [정적 팩토리 메서드는 왜 사용할까?](https://velog.io/@ljinsk3/%EC%A0%95%EC%A0%81-%ED%8C%A9%ED%86%A0%EB%A6%AC-%EB%A9%94%EC%84%9C%EB%93%9C%EB%8A%94-%EC%99%9C-%EC%82%AC%EC%9A%A9%ED%95%A0%EA%B9%8C)

#### 📝 소감 요약
- 변수명을 지정할 때 변수가 어떤 기능을 하는지 명확하게 작성해야 한다.
- 많이 쓰이는 의미있는 단어는 지양하는게 좋다.
- 읽는 사람이 차이점을 알 수 있도록 의미있게 구분지어야 한다.
- 같은 개념이라면 같은 단어를 사용한다.
- 한 단어를 여러 개의 목적으로 사용하면 안된다.
- 큰 개념이 같다면 접두사나 클래스로 맥락을 추가하자.

## 3장 함수

#### 😃 책에서 기억하고 싶은 내용을 써보세요
- 함수를 만드는 첫째 규칙은 '작게'이다.
- if문, else문, while문 등에 들어가는 블록은 한 줄이어야 한다.
  - 대개 거기서 함수를 호출한다.그러면 바깥을 감싸는 함수가 작아질 뿐 아니라, 블록 안에서 호출하는 함수 이름을 적절히 짓는다면, 코드를 이해하기도 쉬워진다.
- 함수는 한 가지를 해야 한다.
- 코드는 위에서 아래로 이야기처럼 읽혀야 좋다.
- 서술적인 이름을 사용하라
- 함수에서 이상적인 인수 개수는 0개(무항)다.
- 인스턴스 변수로 선언하는 대신 함수 인수로 넘기는 방법도 있었다. 하지만 그랬다면 코드를 읽는 사람이 StringBuffer를 발견할 때마다 의미를 해석해야 한다.
- 함수에 인수 1개를 넘기는 이유로 가장 흔한 경우는 두 가지다. 하나는 인수에 질문을 던지는 경우다. 다른 하나는 인수를 뭔가로 변환해 결과를 반환하는 경우다.
- 이벤트 함수는 입력 인수만 있다. 프로그램은 함수 호출을 이벤트로 해석해 입력 인수로 시스템 상태를 바꾼다. 이벤트 함수는 이벤트라는 사실이 코드에 명확히 드러나야 한다. 그러므로 이름과 문맥을 주의해서 선택한다.
- 입력 인수를 변환하는 함수라면 변환 결과는 반환값으로 돌려준다.
- 인수가 2-3개 필요하다면 일부를 독자적인 클래스 변수로 선언할 가능성을 짚어 본다.
- 함수의 의도나 인수의 순서와 의도를 제대로 표현하려면 좋은 함수 이름이 필수다. 단항 함수는 함수와 인수가 동사/명사 쌍을 이뤄야 한다.
- 부수효과를 일으키지마라.
- 일반적으로 출력 인수는 피해야 한다. 함수에서 상태를 변경해야 한다면 함수가 속한 객체 상태를 변경하는 방식을 택한다.
- 함수는 뭔가 수행하거나 무언가에 답하거나 둘 중 하나만 해야 한다.
- try/catch 블록은 별도 함수로 뽑아내는 편이 좋다.
- 함수는 한 가지 작업만 해야 한다. 오류 처리도 한 가지 작업에 속한다.

#### 🤔 오늘 읽은 소감은? 떠오르는 생각을 가볍게 적어보세요
그동안은 코드를 함수로 만드는 일이 가독성을 높이는 일이라고 생각하지 못했다. 앞으로는 코드를 짤 때 최대한 추상화 수준을 높혀서 이해하기 쉽도록 짜야겠다.

#### 🔎 궁금한 내용이 있거나, 잘 이해되지 않는 내용이 있다면 적어보세요 
**추상화 수준이란?** 어느 정도까지 **디테일한 부분을 숨겼는지**에 대한 정도
예) 추상화의 수준이 높다 → 디테일을 많이 숨김
, 추상화의 수준이 낮다 → 디테일을 많이 드러남

**추상화의 목적** : 목적을 명확히 하여 복잡한 것은 숨기고 핵심만 드러내는 것

📌 **추상화 수준이 낮은 코드를 묶어서 추상화 수준을 높게 표현하면 가독성이 좋아짐**

출력 인수란? [Output Parameter에 대한 고찰](https://velog.io/@sansam202/Output-Parameter%EC%97%90-%EB%8C%80%ED%95%9C-%EA%B3%A0%EC%B0%B0)

**참고)** 
[클린코드 작성법](https://velog.io/@jiwon709/%ED%81%B4%EB%A6%B0%EC%BD%94%EB%93%9C-%EC%9E%91%EC%84%B1%EB%B2%95-2)
[함수의 추상화 수준이란?](https://sosimhan-dev.tistory.com/4)

#### 📝 소감 요약
- 함수는 작게 만들어야 한다.
- 함수는 한 가지 일만 해야한다.
- 서술적인 이름을 사용하면 좋다.
- 함수의 인수는 적을수록 좋다.
- 오류 코드 대신 예외 코드를 사용하면 오류 처리 코드가 원래 코드에서 분리되어 코드가 깔끔해진다.
- 알고리즘을 반복하지 않아야 한다.

## 4장 주석

#### 😃 책에서 기억하고 싶은 내용을 써보세요
- 주석은 오래될수록 코드에서 멀어진다.
  - 프로그래머들이 주석을 유지하고 보수하기란 현실적으로 불가능하니까.
- 부정확한 주석은 아예 없는 주석보다 훨씬 더 나쁘다.
- 코드만이 자기가 하는 일을 진실되게 말한다.
- 인수나 반환값이 표준 라이브러리나 변경하지 못하는 코드에 속한다면 의미를 명료하게 밝히는 주석이 유용하다.
- 주석을 달아야 한다면 근처에 있는 코드만 기술하라. 코드 일부에 주석을 달면서 시스템의 전반적인 정보를 기술하지 마라.
- 주석과 주석이 설명하는 코드는 둘 사이 관계가 명백해야 한다.
- 짧고 한 가지만 수행하며 이름을 잘 붙인 함수가 주석으로 헤더를 추가한 함수보다 훨씬 좋다.

#### 🤔 오늘 읽은 소감은? 떠오르는 생각을 가볍게 적어보세요
나쁜 주석에 적혀있는 주석들을 한 번씩 다 달아본 적이 있어서 반성하게 되었다. 특히 같은 이야기를 중복하거나 있으나 마나 한 주석들을 많이 달았던 것 같다. 이 책을 읽기 전까지는 주석을 적는 게 코드를 이해하기 훨씬 쉽다고 생각했다. 그래서 '좋은 주석은 주석을 달지 않을 방법을 찾아낸 주석'이라는 말에 놀랐지만 사례를 보고 나니 충분히 이해할 수 있었다. 

#### 🔎 궁금한 내용이 있거나, 잘 이해되지 않는 내용이 있다면 적어보세요 
X

#### 📝 소감 요약
- 주석은 나쁜 코드를 보완하지 못한다.
- 코드로 의도를 표현해야 한다.
- 좋은 주석은 주석을 달지 않을 방법을 찾아낸 주석이다.
- 함수나 변수로 표현할 수 있다면 주석을 달지 마라
- 나쁜 주석 : 있으나 마나 한 주석, 위치를 표시하는 주석의 남용, 닫는 괄호에 다는 주석, 저자를 표시한 주석, HTML 주석, 전역 정보, 너무 많은 정보, 모호한 관계
- 최대한 주석을 줄이고 함수명이나 변수로 표현해야겠다. 만약 주석이 꼭 필요한 경우에는 간단하고 핵심만 명료하게 적도록 노력해야겠다.

## 5장 형식 맞추기

#### 😃 책에서 기억하고 싶은 내용을 써보세요
- 적절한 행 길이를 유지하라
- 신문 기사처럼 작성해라
  - 이름은 간단하면서도 설명이 가능하게 짓는다.
  - 소스 파일 첫 부분은 고차원 개념과 알고리즘을 설명한다.
  - 아래로 내려갈수록 의도를 세세하게 묘사한다.
  - 마지막에는 가장 저차원 함수와 세부 내역이 나온다
- 서로 밀접한 개념은 세로로 가까이 둬야 한다.
  - 변수는 사용하는 위치에 최대한 가까이 선언한다.
    - 지역 변수는 각 함수 맨 처음에 선언한다.
    - 인스턴스 변수는 클래스 맨 처음에 선언한다.
  - 한 함수가 다른 함수를 호출한다면 두 함수는 세로로 가까이 배치한다.
    - 호출하는 함수를 호출되는 함수보다 먼저 배치한다.
  - 개념적 유사성, 개념적인 친화도가 높을수록 코드를 가까이 배치한다.
    - 한 함수가 다른 함수를 호출해 생기는 직접적인 종속성
    - 변수와 그 변수를 사용하는 함수
    - 비슷한 동작을 수행하는 함수

#### 🤔 오늘 읽은 소감은? 떠오르는 생각을 가볍게 적어보세요
알고리즘을 풀고 나서 다른 사람의 코드를 볼 때, 가끔 if문과 for문이 한줄짜리인 경우 들여쓰기를 하지않고 선언부와 한 줄로 처리한 경우들을 봤다. 확실히 괄호를 사용하는게 구분이 쉬울 것 같았지만 안에 들어가는 코드가 간단하니 괜찮다고 생각했고 코드가 몇줄이라도 짧아지는게 좋아보였다. 그래서 이 문장을 읽고 의아했으나 슬랙에서도 저자와 비슷한 의견인 것을 보고 간단한 코드여도 들여쓰기를 하는게 가독성을 더 높이는 방법이란걸 수긍했다.  

책을 벌써 100p가 넘도록 읽었다. 거의 10p씩 야금야금 읽느라 언제 다 읽을 수 있을까 했는데 의외로 금방 읽고 있다. 노개북에 참여하니 조금이라도 꾸준히 읽을 수 있고 인상깊은 부분이나 감상평을 남길 수 있어서 너무 좋다. 슬랙을 통해 다른 사람의 의견을 볼 수 있다는 점도 너무 매력적이다. 다음에도 다른 책으로 꼭 참여하고 싶다. 

#### 🔎 궁금한 내용이 있거나, 잘 이해되지 않는 내용이 있다면 적어보세요 
X

#### 📝 소감 요약
- 시간이 지나 코드가 바뀌어도 처음 잡아놓은 구현 스타일과 가독성 수준은 유지보수 용이성과 확장성에 계속 영향을 미친다.
- 좋은 소프트웨어 시스템은 읽기 쉬운 문서로 이뤄진다는 사실을 기억해야 한다.
- 스타일은 일관적이고 매끄러워야 한다.

## 6장 객체와 자료구조

#### 😃 책에서 기억하고 싶은 내용을 써보세요
- 추상 인터페이스를 제공해 사용자가 구현을 모른 채 자료의 핵심을 조작할 수 있어야 진정한 의미의 클래스다.
- 디미터 법칙은 모듈이 자신이 조작하는 객체의 속사정을 몰라야 한다는 법칙이다.
- 객체라면 뭔가를 하라고 말해야지 속을 드러내라고 말하면 안된다.
```java
// 좋은 예 : 객체에 임시 파일을 생성하라고 시킴
BufferedOutputStream bos = ctxt.createScratchFileStream(classFileName);
```
- 자료 구조체의 전형적인 형태는 공개 변수만 있고 함수가 없는 클래스다. 이런 자료 구조체를 때로는 자료 전달 객체(Data Transfer Object, DTO)라고 한다.
- 불행히도 활성 레코드에 비즈니스 규칙 메서드를 추가해 이런 자료 구조를 객체로 취급하는 개발자가 흔하다.
- 활성 레코드는 자료 구조로 취급한다. 비즈니스 규칙을 담으면서 내부 자료를 숨기는 객체는 따로 생성한다.

#### 🤔 오늘 읽은 소감은? 떠오르는 생각을 가볍게 적어보세요
처음에 읽고 이해가 잘 안가서 한번 더 읽었다. 챕터 내내 객체와 자료 구조 얘기를 하기 때문에 개념을 정확히 짚고 넘어가는게 중요한데 기존에 알던 개념과 판이하게 달라서 어려웠다. 그래서 아래와 같이 정리하고 다시 보니 저자가 말하고자 하는 바를 잘 이해할 수 있었다.

#### 🔎 궁금한 내용이 있거나, 잘 이해되지 않는 내용이 있다면 적어보세요 
- **객체** : 추상화로 자료를 숨기고 자료를 다루는 함수만 공개
- **자료구조** : 자료를 숨기지 않고 공개, 별다른 함수는 제공하지 않음
- **절차적인 코드**
  - 기존 자료구조를 변경하지 않고 함수 추가 쉬움
  - 새로운 자료구조 추가 어려움 모든 함수를 고쳐야함
  👉 **함수 추가 편함, 자료 구조 추가 불편**
- **객체 지향 코드**  
   - 기존 함수를 변경하지 않고 새 클래스 추가 쉬움
   - 새 함수 추가가 어려움 모든 클래스 고쳐야함
   👉 **함수 추가 불편, 클래스 추가 편함**

📌 **결론** : 서로 상호 보완적인 특징이 있다.

- **활성 레코드** 
  - DTO의 특수한 형태
  - 공개 변수가 있거나 비공개 변수에 조회, 설정 함수가 있는 자료 구조
  - 대개 탐색 함수도 제공 ex) save, find
  - 데이터베이스 테이블이나 다른 소스에서 자료를 직접 변환한 결과

#### 📝 소감 요약
- 구현을 감추려면 추상화가 필요하다.
- 때로는 객체 대신 단순한 자료 구조와 절차적인 코드가 가장 적합할 때도 있다.
- 디미터 법칙을 준수하는 것이 좋다.

## 7장 오류 처리

#### 😃 책에서 기억하고 싶은 내용을 써보세요
- 오류 처리는 프로그램에 반드시 필요한 요소이다.
- 깨끗한 코드와 오류 처리는 확실히 연관성이 있다. 
  - 여기저기 오류 코드가 흩어진다면 실제 코드가 하는 일을 파악하기 어렵다!
- try-catch-finally를 이용해 예외 처리를 하면 무슨 일이 생겨도 호출자가 기대하는 상태를 정의하기 쉬워진다.
- 미확인 예외를 사용하라. 확인된 예외는 OCP를 위반한다.
- 외부 API를 감싸면 외부 라이브러리와 프로그램 사이 의존성이 크게 준다. 
- 또 감싸기 클래스에서 외부 API를 호출하는 대신 테스트 코드를 넣어주면 프로그램을 테스트하기도 쉬워진다. 
- 마지막 장점으로 감싸기 기법을 사용하면 특정 업체가 API를 설계한 방식에 발목 잡히지 않는다.
#### 🤔 오늘 읽은 소감은? 떠오르는 생각을 가볍게 적어보세요
감싸기 기법을 처음 알았는데 사용하면 코드가 깔끔해져서 가독성이 매우 좋을 것 같다. 나중에 예외 처리를 나열하는 코드가 생기면 꼭 적용시켜봐야겠다! 

#### 🔎 궁금한 내용이 있거나, 잘 이해되지 않는 내용이 있다면 적어보세요 
- 확인된 예외(checked exception)
  - 잘못된 코드가 아닌 잘못된 상황에서 발생
  - 정확한 코드로 구현해도 외부 환경(파일이 없는 상황 등)에 따라 발생 가능
  - 예외처리를 하지 않으면 컴파일 에러 발생
  👉 컴파일 시 확인해서 확인된 예외
  - RuntimeException 이외의 예외들
- 미확인된 예외(unchecked exception) 
  - 런타임 시 잘못 구현된 코드로 인해 발생하는 예외
  - 컴파일 에러가 나지 않지만 예외처리가 없을 경우 프로그램 강제 종료
  👉 컴파일 시 확인하지 않기 때문에 미확인 예외
  - RuntimeException에 포함된 예외들
### 
**참고)** [예외(Exception) 처리하기_기본개념과 예외처리 클래스](https://codevang.tistory.com/140)

#### 📝 소감 요약
- 오류 코드보다 예외를 사용하는 것이 좋다.
- 예외를 던질 때는 발생 원인과 위치를 찾기 쉽도록 오류 메시지에 정보도 담는다. 
- 미확인 예외를 사용하자. 일반적인 애플리케이션은 의존성이라는 비용이 이익보다 크다.

## 9장 단위 테스트

#### 😃 책에서 기억하고 싶은 내용을 써보세요
- TDD 법칙 세가지
  - 실패하는 단위 테스트를 작성할 때까지 실제 코드를 작성하지 않는다.
  - 컴파일은 실패하지 않으면서 실행이 실패하는 정도로만 단위 테스트를 작성한다.
  - 현재 실패하는 테스트를 통과할 정도로만 실제 코드를 작성한다.
- 테스트 코드가 복잡할수록 실제 코드를 짜는 시간보다 테스트 케이스를 추가하는 시간이 더 걸린다.
- 테스트는 유연성, 유지보수성, 재사용성을 제공한다. 테스트 케이스가 있으면 변경이 쉬워지기 때문이다.
- 테스트 코드를 짤 때, 함수마다 assert문을 하나씩만 사용한다면 결론이 하나라서 코드를 이해하기 쉽다. 
- 테스트 함수마다 한 개념만 테스트하라
- 깨끗한 테스트는 다섯 가지 규칙을 따른다.
  - **빠르게**(Fast)
  - **독립적으로**(Independent)
  - **반복 가능하게**(Repeatable)
  - **자가검증하는**(Self-Validating)
  - **적시에**(Timely)

#### 🤔 오늘 읽은 소감은? 떠오르는 생각을 가볍게 적어보세요
테스트 함수마다 한 개념, 하나의 assert문을 사용하는 것이 테스트 코드를 이해하기 훨씬 쉬울 것 같아서 빨리 활용해 보고 싶어졌다. 또 깨끗한 테스트의 다섯 가지 규칙을 통해 앞으로 테스트 코드를 작성할 때 다섯 가지 규칙을 유의하며 작성해야겠다는 생각이 들었다.   

#### 🔎 궁금한 내용이 있거나, 잘 이해되지 않는 내용이 있다면 적어보세요 
X

#### 📝 소감 요약
- TDD 법칙 세가지를 준수하자
- 깨끗한 테스트 코드를 유지하자
- 테스트 코드는 실제 코드 못지않게 중요하다.
- 테스트 코드는 지속적으로 깨끗하게 관리하자
- 테스트 코드의 표현력을 높이고 간결하게 정리하자
- 테스트 함수마다 하나의 개념, 하나의 assert문을 사용하자

## 10장 클래스

#### 😃 책에서 기억하고 싶은 내용을 써보세요
- 캡슐화를 풀어주는 결정은 언제나 최후의 수단이다.
- 클래스는 작아야 한다.
- 클래스 이름은 해당 클래스 책임을 기술해야 한다.
- 간결한 이름(클래스명)이 떠오르지 않는다면 클래스 크기가 너무 커서 그렇다.
- 클래스 이름이 모호하다면 클래스 책임이 너무 많아서다.
- 예를 들어, 클래스에 Processor, Manager, Super등과 같은 모호한 단어가 있다면 클래스에다 여러 책임을 떠안겼다는 증거다.
- 큰 클래스 몇 개가 아니라 작은 클래스 여럿으로 이뤄진 시스템이 더 바람직하다.
- 작은 클래스는 각자 맡은 책임이 하나며 변경할 이유가 하나며 다른 작은 클래스와 협력해 시스템에 필요한 동작을 수행한다.
- 클래스는 인스턴스 변수 수가 작아야 한다.
- 각 클래스 메서드는 클래스 인스턴스 변수를 하나 이상 사용해야 한다.
- 일반적으로 메서드가 변수를 더 많이 사용할수록 메서드와 클래스는 응집도가 더 높다.
- **응집도가 높다는 말은 클래스에 속한 메서드와 변수가 서로 의존하며 논리적인 단위로 묶인다는 의미다.**
- '함수를 작게, 매개 변수 목록은 짧게'라는 전략을 따르다 보면 몇몇 메서드만이 사용하는 인스턴스 변수가 많아지고 그걸 **새로운 클래스로 쪼개**면 된다.
- 큰 함수에서 작은 함수를 빼내려 할 때, 작은 함수가 큰 함수에 정의된 변수를 사용하다면 해당 **변수를 클래스 인스턴스 변수로 만들어라.** 새 함수는 인수가 필요없어지지만 클래스는 응집력을 잃는다. 이런 경우 몇몇 함수만 사용하는 인스턴스 변수가 많아지므로 위에 적힌 것처럼 **새로운 클래스로 쪼개**면 된다.
- 깨끗한 시스템은 클래스를 체계적으로 정리해 변경에 수반하는 위험을 낮춘다.
- 이상적인 시스템이라면 새 기능을 추가할 때 시스템을 확장할 뿐 기존 코드를 변경하지 않는다.
- 시스템의 결합도를 낮추면 유연성과 재사용성도 더욱 높아진다.
- 결합도가 낮다는 것은 각 시스템 요소가 다른 요소로부터 그리고 변경으로부터 잘 격리되었다는 의미다.
  
#### 🤔 오늘 읽은 소감은? 떠오르는 생각을 가볍게 적어보세요
규모가 큰 프로젝트를 진행해보진 못했지만, 내가 참여했던 프로젝트에서도 클래스가 여러 개라 단번에 이해하기 어렵다고 생각했었다. 하지만 책을 읽으니 저자의 말대로 하나의 책임만 지는 클래스가 장기적으로는 이해하기가 더 수월할 것 같았다. 그래서 다음에 프로젝트를 진행한다면 설계 과정에서 컴포넌트 다이어그램처럼 클래스에 간략한 설명과 서로 간의 관계를 정리하고 시작하면 좋을 것 같다.

#### 🔎 궁금한 내용이 있거나, 잘 이해되지 않는 내용이 있다면 적어보세요 
X

#### 📝 소감 요약
- 클래스는 크기가 작을수록 좋다.
- 클래스는 하나의 책임만 져야한다(SRP).
- 작은 클래스가 많으면 프로그램 전체를 이해하기 어렵다고 생각할 수 있지만 어느정도 규모가 있는 시스템은 논리가 많고 복잡하기 때문에 체계적인 정리가 필수다.
- 응집도를 유지하면 작은 클래스 여럿이 나온다.
- 이상적인 시스템이라면 새 기능을 추가할 때 기존 코드를 변경하지 않는다.
- 시스템의 결합도를 낮추면 유연성과 재사용성도 높아진다.



