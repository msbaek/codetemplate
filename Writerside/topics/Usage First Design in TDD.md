# Usage First Design in TDD, By Example

- [Test Driven Development: By Example: Beck, Kent: 8601400403228: Amazon.com: Books](https://t.ly/j_95E)
  에서 `By Example`의 의미
    - TDD는 그 자체가 예를 통해서 진행된다는 뜻(TDD에서 테스트는 유용하고 문제 해결에 본질적인 시스템 사용의 예로 구성됨)
- [Usage-First Design in TDD - Part I](https://www.youtube.com/watch?v=4xNPMbV4J4w), [Incremental TDD: Part 2, Usage-First | Guided Learning Hour](https://www.youtube.com/watch?v=5BftptSNrAg)

## Specification vs Example

### Specification / Requirement

![spec.png](spec.png)

![image_2.png](example.png)

- 장바구니가 비어 있으면 청구서를 사용할 수 없습니다.
- 총 가격은 단가 * 수량의 합계입니다.
- 한 바구니에 허용되는 최대 품목 수는 100개입니다.
- 총 금액이 $200 이상이면 10% 할인을 제공합니다.
- 청구서에 장바구니의 모든 품목이 나열됩니다.
- 총 금액이 $100 초과 시 5% 할인 제공

### Example

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
public void Total_Over_100_Gives_Five_Percent_Discount() {
    var basket = new ShoppingBasket);
    var itemA = new BasketItem("A", new decimal(10));
    var itemB = new BasketItem("B", new decimal(25));
    var item = new BasketItem("C", new decimal(9.99));
    basket.add(itemA, 5);
    basket.add(itemB, 2);
    basket.add(itemC, 6);
    Assert.That(basket.GetQuantity("C"), Is.EqualTo(6));
    Assert.That(basket.CalculateTotal(), Is.EqualTo(new decimal(151.94)).Within(0.01));
}
```

#### 설계 특성

![design-characteristics.png](design-characteristics.png)

