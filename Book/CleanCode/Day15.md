## Day15
🔖 오늘 읽은 범위 : 7장 오류 처리(138 ~ 142p) 
  
---

#### 😃 책에서 기억하고 싶은 내용을 써보세요
- 클래스를 만들거나 객체를 조작해 특수 사례를 처리하는 방식을 특수 사례 패턴이라고 부른다.
  - 클라이언트 코드가 예외적인 상황을 처리할 필요가 없어진다.
- 메서드에서 null 객체를 반환하고픈 유혹이 든다면 그 대신 예외를 던지거나 특수 사례 객체를 반환한다.

#### 🤔 오늘 읽은 소감은? 떠오르는 생각을 가볍게 적어보세요
140p에서 메서드의 반환값이 없어도 null을 반환하지 않게끔 빈 리스트를 반환해서 불필요한 확인 절차를 줄이는 코드가 인상깊었다. 메서드를 호출하고 null을 체크하는 경우가 잦았는데 앞으로는 호출하려는 메서드 내부에서 처리해야겠다.

#### 🔎 궁금한 내용이 있거나, 잘 이해되지 않는 내용이 있다면 적어보세요 
X

#### 📝 소감 3줄 요약
- 메서드에서 null을 반환하거나, 메서드로 null을 전달하지 않는 것이 좋다.
- 깨끗한 코드는 읽기도 좋아야 하지만 안정성도 높아야 한다.
- 오류 처리를 프로그램 논리와 분리하면 독릭적인 추론이 가능해지며 코드 유지보수성도 높아진다.

