# Usage First Design in TDD, By Example

- [Test Driven Development: By Example: Beck, Kent: 8601400403228: Amazon.com: Books](https://t.ly/j_95E)
  에서 `By Example`의 의미
    - TDD는 그 자체가 예를 통해서 진행된다는 뜻(TDD에서 테스트는 유용하고 문제 해결에 본질적인 시스템 사용의 예로 구성됨)
- [Usage-First Design in TDD - Part I](https://www.youtube.com/watch?v=4xNPMbV4J4w), [Incremental TDD: Part 2, Usage-First | Guided Learning Hour](https://www.youtube.com/watch?v=5BftptSNrAg)
- [emilybache/ShoppingBasket-TDD-Kata](https://github.com/emilybache/ShoppingBasket-TDD-Kata)

## Specification vs Example

### Specification / Requirement

- 장바구니가 비어 있으면 청구서를 사용할 수 없습니다.
- 총 가격은 단가 * 수량의 합계입니다.
- 한 바구니에 허용되는 최대 품목 수는 100개입니다.
- 총 금액이 $200 이상이면 10% 할인을 제공합니다.
- 청구서에 장바구니의 모든 품목이 나열됩니다.
- 총 금액이 $100 초과 시 5% 할인 제공

### Example0

- 바구니 내용물:
    - 프린터 카트리지 3개 총 $150,
    - 펜 5개 총 $10,
    - 메모장 5개 총 $50,
    - 예상 할인 10%
- 바구니 내용물
    - 프린터 카트리지 1개 $50,
    - 펜 5개 $2
    - 메모장 3개 $10,
    - 예상 총 가격: $90
- 바구니에 프린터 카트리지와 일부 펜이 포함된 경우 청구서에는 프린터 카트리지, 펜과 같은 항목이 표시되어야 합니다.
- 바구니 내용물
    - 프린터 카트리지 1개 총 $50,
    - 펜 5개 총 $10,
    - 메모장 5개 총 $50,
    - 예상 할인 5%

### Spec. vs Example

- 개발을 할 때 Spec으로 시작
- Example은 Spec을 이해하는데 도움을 줌
- 또한 Example은 Test Case로 전환하기에 매우 좋다

## From Spec to Code

![spec-exam-tdd-cycle.png](spec-exam-tdd-cycle.png)

- 예제는 스펙을 이해하는 데 도움이 됩
    - TDD에서는 예제가 테스트 케이스로 전환됨
- TDD에서는 테스트 케이스가 설게(Design)를 유도(derive)함

## Shopping Basket Discount kata

### Spec

- 장바구니에 있는 아이템에는 단가와 수량이 있습니다.
- 이를 허용하는 코드를 작성합니다:
    1. 장바구니에 있는 특정 아이템의 수량 확인
    2. 적용 가능한 할인을 포함하여 전체 장바구니의 총 가격을 계산합니다.
- 일반적으로 총 가격은 다음과 같습니다.
    - 모든 품목의 단가 * 수량을 합한 값입니다.
- 대량으로 구매하면 할인을 받을 수 있습니다:
    1. 총 장바구니 금액이 $100 이상인 경우 5% 할인 적용
    2. 총 장바구니 금액이 $200 이상인 경우 10% 할인 적용

### Example

1. 상품 Apple  : $10, 수량 5
2. 품목 바나나 i : $25, 수량 2 개
3. 품목 감귤류 - : $9.99, 수량 6

- 이 장바구니는 5% 할인을 받을 수 있으며 총 가격은 $151.94입니다.

### Test Case

```java

@DisplayName("총 금액이 $100 초과 시 5% 할인 제공")
@Test
public void total_over_100_gives_five_percent_discount() {
    ShoppingBasket basket = new ShoppingBasket();
    BasketItem itemA = new BasketItem("A", BigDecimal.valueOf(10));
    BasketItem itemB = new BasketItem("B", BigDecimal.valueOf(25));
    BasketItem itemC = new BasketItem("C", BigDecimal.valueOf(9.99));
    basket.add(itemA, 5);
    basket.add(itemB, 2);
    basket.add(itemC, 6);
    assertThat(basket.getQuantity("C")).isEqualTo(6);
    assertThat(basket.calculateTotal().setScale(2, RoundingMode.HALF_UP)).isEqualTo(151.94);
}
```

#### 설계 특성

| 이 코드에 있는 설계 특성                                   |                                               | 이 코드에 업는 설계 특성                         |
|--------------------------------------------------|-----------------------------------------------|----------------------------------------|
| 아이템 이름은 고유해야 함                                   |                                               | ShoppingBasket의 서브 클래스를 쉽게 도입할 수 있어야 함 |
| 장바구니에 있는 아이템을 저장하는데 사용되는 데이터 구조는 노출되지 말아야 함      | JSON으로 아이템을 쉽게 생성할 수 있어야 함                    | 다른 장바구니 아이템 서브클래스를 쉽게 도입할 수 있어야 함      |
| 금액 계산은 Decimal 타입을 사용해야 함 - 소숫점 문제에서 더블 타입보다 안전한 | 장바구니에 영향을 미치지 않고 무게, 컬러 등의 속성을 쉽게 추가할 수 있어야 함 | 실수로 가격이 없는 아이텝을 생성할 수 있음               |

## Usage-First Design

> Design new classes and methods by how they will be used

- also known as "**Consume First**" or "**Example-guided Design**", "**Outside-In**"
    - Use the thing before you implement it
    - Focus on interface not implementation
    - Change the design when it's cheapest

## Incremental Design

> Start with a simple example

- example by example 방식으로 점진적으로 구현

### 초기 테스트 코드

```java

@Test
@Disabled("WIP")
public void totalOver100GivesFivePercentDiscount() {
    ShoppingBasket basket = new ShoppingBasket();
    BasketItem itemA = new BasketItem("A", BigDecimal.valueOf(10));
    BasketItem itemB = new BasketItem("B", BigDecimal.valueOf(25));
    BasketItem itemC = new BasketItem("C", BigDecimal.valueOf(9.99));
    basket.add(itemA, 5); // 메서드 이름과 파라미터 순서를 자바 관례에 맞게 조정
    basket.add(itemB, 2);
    basket.add(itemC, 6);
    assertEquals(6, basket.getQuantity("C"), "The quantity of item C should be 6");
    assertEquals(0, BigDecimal.valueOf(151.94).compareTo(basket.calculateTotal()), "Total should be within 0.01 of 151.94");
}
```

- 스터빙을 해서 컴파일은 되도록 할 수 있음
    - 모든 구현은 예외를 발생시키도록 구현
    - 동작하지 않으니 `@Disabled` 처리함
    - 이 정도 구현이면 설계를 평가하기에 충분함

### 테스트 시나리오 리스트 작성

- **example을 test case로 변환하는 것**과 **어떻게 설계를 끌어내는지** 주의하고 과정을 보라
- 일련의 example을 도출
    - 아래와 같이 테스트 클래스의 상단에 테스트 시나리오 목록을 작성
  ```java
  /**
   * Implement tests in this order:
   *
   * Empty basket - basket contains no items
   * one item "A" - basket contains 1 item "A"
   * two items "A" - basket contains 2 items "A"
   * two items, "A" and "B" - basket contains 1 item "A"
   * Empty basket - total price 0
   * "A" costs $10, basket contains one "A" - total $10
   * "D" costs $160, basket contains one "D" - total $152 (5% discount)
   * "E" costs $250, basket contains one "E" - total $225 (10% discount)
   * Then remove the "Ignore" marking on the test that's here and hopefully it will pass!
   */
  ```
- 예제를 분해하는 좋은 방법임(degenerate 부터)
- 이제부터 Example을 Test Case로 변환함. 제일 위(degenerate)부터

### 빈 장바구니

- 제일 쉬운 예제는 빈 장바구니임

```java

@Test
public void emptyBasketContainsNothing() {
    ShoppingBasket basket = new ShoppingBasket();
    assertThat(basket.getQuantity("C")).isEqualTo(0);
}
```

### 하나의 아이템

- `@Diabled` 처리한 테스트 케이스와 최대한 동일해지게 테스트 케이스를 작성(itemName, price, quantity 등)
    - `@Diabled` 처리한 테스트 케이스는 최초의 의도를 가지고 있으니 이와 같아지는게 좋음
- 이런 식이면 이전 테스트 케이스를 **복붙**해서 새로운 테스트 케이스를 추가하게 됨

```java

@Test
public void oneItemA() {
    BasketItem itemA = new BasketItem("A", BigDecimal.valueOf(10));
    basket.add(itemA, 5);
    assertThat(basket.getQuantity("A")).isEqualTo(5);
}
```

- 모든 테스트 시나리오 리스트를 구현한 후에는 `@Disabled` 제거하고 이 테스트가 성공하도록 함