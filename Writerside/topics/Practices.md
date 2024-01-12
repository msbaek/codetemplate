# Practices

## 기능 구현 절차

- 2024-01-09 GMS 대체 입출고

![inventory-transfer.png](inventory-transfer.png)

### 개발 절차

1. Use Case Diagram

- Actor와 주요 Use Case 표현

2. Use Case Description

- 각 Use Case별로
    - 액터가 "XXX"를 요청
    - 시스템은
        - 검증
        - 필요한 변경
        - 결과 반환
    - 의 반복으로 표현(테이블 형식도 가능)

3. 클래스 다이어그램
    - 현재의 요구사항을 충족할 수준으로
4. 객체 다이어그램
    - 엑셀 등을 활용해서 내가 생각하는대로 데이터가 있다면 원하는 요구사항이 충족되는지 확인
5. 구현(TDD)
   5.1 절차적/명령형으로 컨트롤러에 모든 기능을 구현 with Test First
    - 여기까지는 설계 역량(refactoring)이 없어도 할 수 있는 영역임
    - 빵구조만 유지하고, 테스트만 존재한다면 지속적으로 개선 할 수 있음
      5.2 Refactoring to 가독성/유지보수성(Composed Method, SLAP)
    - 코드 품질의 목표는 가독성과 유지보수성임

### 이슈

- 처음부터 모든 기능을 수용할 수 있는 테이블 구조 설계
    - 하나의 테이블로 다양한 경우를 다루기 위해
    - nullable이 많음
    - 객체도 nullable이 많으면 코드가 복잡해지듯
    - table도 nullable이 많으면 query, code가 복잡해짐
    - 상속관계를 매핑하는 3가지 방법을 생각해 보라 Nullable의 문제에 대해
- 제일 첫번째 기능만 충족하는 테이블 설계
    - 가벼운 조건으로 빠르게 구현 가능
    - 후에 필요하면 변경
    - 처음부터 마지막까지 고려하면 처음부터 느릴 수 밖에 없음