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
- 코드 악취(code smell) 제게 - [Refactoring: Improving the Design of Existing Code (2nd Edition)](https://www.amazon.com/Refactoring-Improving-Existing-Addison-Wesley-Signature/dp/0134757599?dib=eyJ2IjoiMSJ9.nihzxitn_PQoI4VScx0DzhsxxaWKPHLgfmlh2kMzelsQFDA_QJjetXN4P-H_4ZKIounCLpcTEuxkai8DiDexiQ.cYuuQQCqipeBEujTC1r9ykT9aEiHIBdXro9qvLrCjOk&dib_tag=se&keywords=Refactoring+to+Patterns&qid=1704936399&sr=8-8)
- 패턴 적용 - [Refactoring to Patterns](https://www.amazon.com/Refactoring-Patterns-Joshua-Kerievsky), joshua Kerievsky