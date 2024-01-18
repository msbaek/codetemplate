# TDD Terms

## 삼각측량(Triangulation)

- [github 메모](https://github.com/msbaek/memo/blob/master/Triangulation.md)
- 삼각 측량은 건축에서 시작되었겠지만
    - TDD에서 테스트를 추가할 때
    - 코드 리뷰를 할 때
- 도 적용된다고 생각함
- 하나의 테스트만 추가하면 null, 상수 등을 반환해서 속일 수 있다
    - 하지만 이 방법으로 성공시킬 수 없도록 하나의 테스트만 더 추가해도 테스트가 깨지게 할 수 있다
    - 이를 통해 코드가 사소한 일부터 처리하고 본질적인 일을 처리하도록 점진적으로 구현해 갈 수 있다
    - 테스트는 Degenerate한 순서로 추가하고, 제품 코드는 TPP를 준수해서
    - 결과적을 Guard Clause를 준수하는 코드가 작성됨
- 코드 리뷰도 마찬가지다
    - 혼자서 작성하면 아무리 잘 작성해서 "토끼 굴"에 빠질 수 있다
    - 하지만 한사람이라도 더 보면 그 코드는 다른 사람이 볼 때 이해하기 쉬운 코드가 될 가능성이 높다
    - "한사람 이해가 가능한 코드" 보다 "적어도 작성자 외 1인 이상이 이해한 코드"가 다른 사람들도 이해하기 쉬울 가능성이 높다

## 테스트는 오류가 없을 증명하지 못함

- Dijkstra는 다음과 같은 말을 했다

> “Program testing can be used to show the presence of bugs, but never to show their absence.”

> "Tests can only prove a program wrong; they can never prove a program right"

- 수학은 참임을 증명 가능하지만, 과학은 참임을 증명하지 못함
- 과학은 반증가능성(falsifiable)의 원칙을 따름
- 즉, 실험 또는 관찰을 통해 반증될 수 있어야 함
- 틀리는 경우를 찾을 때까지는 참이라고 할 수 있음

## 리팩터링의 목적

- 중복 제거 - Kent Beck
- 코드 악취(code smell)
  제게 - [Refactoring: Improving the Design of Existing Code (2nd Edition)](https://www.amazon.com/Refactoring-Improving-Existing-Addison-Wesley-Signature/dp/0134757599?dib=eyJ2IjoiMSJ9.nihzxitn_PQoI4VScx0DzhsxxaWKPHLgfmlh2kMzelsQFDA_QJjetXN4P-H_4ZKIounCLpcTEuxkai8DiDexiQ.cYuuQQCqipeBEujTC1r9ykT9aEiHIBdXro9qvLrCjOk&dib_tag=se&keywords=Refactoring+to+Patterns&qid=1704936399&sr=8-8)
- 패턴 적용 - [Refactoring to Patterns](https://www.amazon.com/Refactoring-Patterns-Joshua-Kerievsky), joshua Kerievsky

## Test Doubles

- [Mocks Aren't Stubs](https://martinfowler.com/articles/mocksArentStubs.html)
- [xUnit Test Patterns: Refactoring Test Code: Meszaros, Gerard](https://www.amazon.com/xUnit-Test-Patterns-Refactoring-Code/dp/0131495054?dib=eyJ2IjoiMSJ9.zynxx3squhN4Qj0XXwCQeY-TcnsuhtWO1T0WJOi2_1aoBBUcGKASU_kH104K4U4E-c6LdRAUkRMihaFKFPjiJw.jJlVTYSgMv3grHWu6ORsilmpSjTT82QT-Y2jq9J8JqY&dib_tag=se&keywords=xUnit+Patterns&qid=1705468127&sr=8-1)
- 테스트 목적으로 실제 객체 대신 사용되는 모든 종류의 가상 객체에 대한 일반 용어로 Test Double을 사용
- Meszaros는 5가지 종류의 Test Double을 정의하였습니다.
    1. Dummy
        - 객체는 전달되지만 실제로 사용되지는 않습니다. 일반적으로 매개변수 목록을 채우는 데만 사용됩니다.
    2. Fake
        - 실제로 동작하는 구현을 가지고 있지만 일반적으로 프로덕션에는 적합하지 않은 방식을 취함
        - ex. 메모리 기반 데이터베이스
    3. Stub
        - 테스트 중에 만들어진 호출에 미리 준비된 답변을 제공하고 일반적으로 테스트를 위해 프로그래밍된 것 외에는 전혀 응답하지 않음
    4. Spy
        - stub의 일종으로 어떻게 호출되었는지에 따라 일부 정보를 기록함
        - mockito에서 spy는 실제 객체의 일부 행위를 재정의할 수 있는 mock임
    5. Mock은 호출될 것으로 예상되는 명세를 형성하는 기대값으로 미리 프로그래밍 된 객체입니다.
        - Mock만 행동 검증을 수행
- Stub은 상태 검증, Mock은 상호작용 검증
    - Stub
      ```Java
      class OrderStateTester {
  
        public void testOrderSendsMailIfUnfilled() {
          Order order = new Order(TALISKER, 51);
          MailServiceStub mailer = new MailServiceStub();
          order.setMailer(mailer);
          order.fill(warehouse);
          assertEquals(1, mailer.numberSent());
        }
      }
      ```
        - Stub에서 상태 확인을 사용하려면 확인을 돕기 위해 Stub에서 몇 가지 추가 메서드를 만들어야 함
        - 결과적으로 Stub은 MailService를 구현하지만 추가적인 테스트 메서드를 구현해야함
    - Mock
      ```Java
      class OrderInteractionTester {
  
        public void testOrderSendsMailIfUnfilled() {
          Order order = new Order(TALISKER, 51);
          Mock warehouse = mock(Warehouse.class);
          Mock mailer = mock(MailService.class);
          order.setMailer((MailService) mailer.proxy());
  
          mailer.expects(once())
              .method("send");
          warehouse.expects(once())
              .method("hasInventory")
              .withAnyArguments()
              .will(returnValue(false));
  
          order.fill((Warehouse) warehouse.proxy());
        }
      }
      ```
- Traditional vs Mockist
    - Traditional
        - MailService만 mocking
        - Sociable Test
        - 좋은 mock library가 많더라도 stub을 직접 만드는게 유용하다고 생각함
    - Mockist
        - Warehouse, MailService 모두 mocking
        - Solitary Test
- 선택하기
    - 맥락(Context)에 따라
        - Order와 Warehouse는 쉬운 협력: 고민의 여지가 없음
        - Order와 MailService는 어색환 협력 관계: Classicist의 경우 고민이 됨. 각 상황에 가장 쉬운 방법을 사용
    - 어색한 관계가 아니라도 상태로 검증이 어려운 경우
        - Cache hit, miss
- 선택을 위해 고려할 요소들
    - Driving TDD
        - Mockist
            - NDD 옹호
            - Outside in
            - Collaborator의 기대치를 고려해서 SUT의 아웃바운드 인터페이스를 효과적으로 설계
            - 첫번째 테스트를 실행하고 나면 모의 객체에 대한 기대치가 다음 단계에 대한 명세와 테스트의 시작점을 제공
        - Classicist
            - middle out
            - 구현할 기능을 선택하고 이 기능이 작동하기 위해 도메인에서 필요한 것을 결정
    - Fixture Setup
        - mock을 이용하면 복잡한 fixture setup이 수월해 짐
        - classicist는 fixture 재사용 가능케하는 경향. Object Mothers
    - 테스트 격리
        - 버그 발생 시 전통적인 방법이 깨지는 테스트가 훨씬 많음
        - 모의 테스트
            - SUT에 버그가 있는 경우만 깨짐
            - 주 문제로 다룸
            - 디버깅이 수월
        - 전통적인 방법
            - 협력객체를 사용하는 모든 테스트가 깨짐
            - 문제의 원인으로 삼지 않음
        - SUT Cluster가 6개 이하의 객체로 제한하는 것이 좋음
        - 전통적인 xunit 테스트는 단순 단위 테스트가 아니라 미니 통합 테스트임
    - 구현과 테스트 결합
        - 전통적인 테스트는 최종 상태에만 관심. 해당 상태가 어떻게 파생되었는지 중요치 않음
        - 모의 테스트는 메소드 구현과 더 연관이 있음. 협력 객체에 호출의 성경이 변경되면 일반적으로 테스트가 깨짐. 리팩터링에 방해가 됨
    - 설계 스타일
        - 모의객체
            - 값을 반환하는 것 보다 값을 수집하는 메소드 선호하는 경향
            - `train wreck` 피하는 것에 더 많이 얘기함
                - 메소드 체인이 악취인 것 만큼 middleman도 악취임
            - `tell don't ask`를 촉진한다고 함. 하지만 전통주의자들은 다른 방법이 있다고 함
            - 상태 테스트의 문제점 중 하나는 검증만을 위한 쿼리 메소드 생성 가능성. 하지만 전통주의자들인 사소한 것이라 함
            - [Role Interface](https://martinfowler.com/bliki/RoleInterface.html)를 장려한다고 함
            - Test First가 설계에 도움이 된다는 측면
- 그럼 어떤 방식을 취하나 ?