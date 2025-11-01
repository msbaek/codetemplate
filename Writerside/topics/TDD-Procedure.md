# TDD 절차

테스트 주도 개발(Test-Driven Development, TDD)은 소프트웨어를 개발하는 방법론 중 하나로, **테스트를 먼저 작성하고 그 테스트를 통과하는 코드를 작성**하는 방식이다.

## TDD란 무엇인가

### TDD의 본질

TDD는 단순한 테스트 작성 기법이 아니라 **테스팅, 설계, 프로그래밍의 특성을 모두 가진 복합적인 접근법**이다.

**TDD의 진정한 가치**:
- 테스트 자체보다 **설계 과정에서 받는 즉각적인 피드백**
- **작은 단계로 작업하도록 강제하는 규율**
- **행위(behavior) 중심의 개발**

### 이중 부기(Double Entry Bookkeeping) 비유

TDD는 회계의 이중 부기와 같은 원리를 따른다.

**회계의 이중 부기**:
- 모든 거래를 두 번 기록 (자산, 부채와 자본)
- 양쪽의 합을 계산하여 대차대조표에서 차감
- 결과는 반드시 0이어야 함

**TDD의 이중 기록**:
- 모든 행위를 두 번 작성
  - 한 번은 테스트로
  - 다시 한 번은 프로덕션 코드로
- 두 기록은 반드시 일치해야 함

이 방식은 **500년간 검증된 회계 원칙**을 소프트웨어 개발에 적용한 것으로, 코드의 정확성을 보장하는 강력한 방법이다.

## TDD 기본 절차 (Canon TDD)

Kent Beck이 제시한 정통 TDD 절차는 다음 5단계로 구성된다.

### 1. 테스트 시나리오 목록 작성

> Write a list of the test scenarios you want to cover

**무엇을 하는가**:
- 행위 변경이 일어나는 모든 경우에 대한 테스트 목록을 작성
- 기본적인 경우, 예외 상황, 경계 조건 등을 포함

**왜 중요한가**:
- **인수 조건(Acceptance Criteria)** 을 명시 - 언제 완료인지 알 수 있음
- **진도를 측정**할 수 있음 - 전체 n개 중 m개 완료
- **행위 분석(behavior analysis)** 단계

**예시**:
```java
/**
 * [ ] 모든 투구가 거터(0점)일 때 총점은 0이다
 * [ ] 모든 투구에서 1개씩 쓰러뜨리면 총점은 20이다
 * [ ] 스페어가 있을 때 다음 투구 점수가 보너스로 추가된다
 * [ ] 스트라이크가 있을 때 다음 2번 투구 점수가 보너스로 추가된다
 * [ ] 퍼펙트 게임(모든 프레임 스트라이크)일 때 총점은 300이다
 */
class BowlingGameTest {
}
```

**이 단계에서 자주 하는 실수**:
- ❌ **구현 설계 결정(implementation design decisions)과 행위 분석을 혼합**
  - 내부를 어떻게 구현할지는 나중에 결정
  - 테스트 목록 작성에만 집중
- ❌ **테스트 목록 없이 바로 구현 시작**
  - 언제 완료인지 모름
  - 진척 정도를 측정할 수 없음

### 2. 테스트를 하나씩 구체화

> Turn exactly one item on the list into an actual, concrete, runnable test

**무엇을 하는가**:
- 테스트 목록에서 **정확히 하나의 테스트**를 선택
- AAA 패턴(Arrange-Act-Assert)으로 실행 가능한 테스트 작성
- **Assert → Act → Arrange 순**으로 작성하면 더 명확함

**예시**:
```java
@Test
@DisplayName("모든 투구가 거터일 때 총점은 0이다")
void gutterGame() {
    // Arrange
    BowlingGame game = new BowlingGame();

    // Act
    for (int i = 0; i < 20; i++) {
        game.roll(0);
    }

    // Assert
    assertEquals(0, game.score());
}
```

**설계 결정의 성격**:
- 이 단계에서 내리는 설계 결정은 주로 **인터페이스 설계(interface design)**
- **어떻게 호출되고 사용될 것인가?** 를 결정
- 내부 구현은 다음 단계에서 결정

**이 단계에서 자주 하는 실수**:
- ❌ **Assert 없는 테스트 작성** (테스트 커버리지만 높이려는 시도)
- ❌ **테스트 목록의 모든 테스트를 한번에 구체화**
  - 첫 테스트 구현으로 인해 다른 테스트들이 영향받을 수 있음
  - 성취감을 느낄 수 없음
  - **한 번에 하나씩** 추가하고 통과시켜야 함

### 3. 테스트 통과시키기

> Change the code to make the test (& all previous tests) pass

**무엇을 하는가**:
- **현재 테스트와 모든 이전 테스트**가 통과하도록 코드 변경
- 새로운 테스트가 필요하면 목록에 추가 (지금 구현하지는 않음)
- 테스트 통과 후 목록에 **완료 표시**

**"Make it Work" 원칙**:
- **빠르게 동작하도록 만드는 것이 최우선**
- 코드가 다소 지저분해도 괜찮음
- Fake Implementation, Obvious Implementation 등 모든 방법 활용
- Copilot, StackOverflow 등 활용 가능

**예시**:
```java
public class BowlingGame {
    private int score = 0;

    public void roll(int pins) {
        score += pins;  // 가장 단순한 구현으로 시작
    }

    public int score() {
        return score;
    }
}
```

**새로운 테스트 발견 시**:
- 구현 중 새로운 테스트가 필요하다는 것을 발견하면 목록에 추가
- 하지만 **지금 구현하던 테스트에 집중**
- 만약 추가된 테스트로 인해 현재 구현이 무효화된다면:
  - 💡 **다시 시작하되, 테스트 구현 순서를 변경**

**이 단계에서 자주 하는 실수**:
- ❌ **테스트를 성공시키기 위해 assert 삭제**
- ❌ **실제 계산된 값을 복사해서 expected에 붙여넣기**
  - 이중 부기의 이점을 상실
- ❌ **테스트 성공(구현)과 리팩터링을 동시에 진행**
  - Make it work → Make it right 순서 준수
  - 두뇌가 감사할 것이다

### 4. 리팩터링

> Optionally refactor to improve the implementation design

**무엇을 하는가**:
- **구현 설계(implementation design)** 를 개선
- 중복 제거, 코드 냄새 제거, 패턴 적용
- 가장 중요한 것: **가독성 확보**

**"Make it Right" 원칙**:
- "적절한 때에 좋은 설계로 개선" (Good design at good times)
- 기능이 동작하는 상태에서 안전하게 개선

**리팩터링 수준 결정**:

| 시점 | 수준 | 이유 |
|------|------|------|
| 테스트 케이스 완료 시 | 최소한 | 아직 전체 모습을 모름 |
| 테스트 리스트 모두 완료 시 | 적극적 | 전체를 보고 판단 가능 |

**리팩터링에서 하는 일**:
- ✅ 디자인 패턴 적용 (Joshua Kerievsky의 _Refactoring to Patterns_)
- ✅ 중복 제거 (Kent Beck)
- ✅ 코드 냄새 제거 (Martin Fowler)
- ✅ Composed Method 패턴 적용 (가독성)

**예시**:
```java
// Before - 중복 코드
@Test
void oneSpare() {
    game.roll(5);
    game.roll(5);  // 중복 패턴
    game.roll(3);
    for (int i = 0; i < 17; i++) {
        game.roll(0);
    }
    assertEquals(16, game.score());
}

// After - 헬퍼 메소드로 추출
@Test
void oneSpare() {
    rollSpare();
    game.roll(3);
    rollMany(17, 0);
    assertEquals(16, game.score());
}

private void rollSpare() {
    game.roll(5);
    game.roll(5);
}

private void rollMany(int n, int pins) {
    for (int i = 0; i < n; i++) {
        game.roll(pins);
    }
}
```

**중요한 원칙**:
> 💡 **리팩터링 단계에서는 새로운 테스트를 추가하지 않습니다**

이유:
- 리팩터링은 **외부 동작을 변경하지 않고** 내부 구조만 개선
- 새 테스트 추가 = 새 기능 추가 = 리팩터링이 아님
- 리팩터링 중 추가되는 테스트는 **구현 디테일을 테스트**하는 것
- 이는 테스트와 구현을 강하게 결합시킴

**이 단계에서 자주 하는 실수** {id="refactoring_mistakes"}
- ❌ **필요 이상으로 리팩터링**
  - 정리하면 기분이 좋지만, 과도한 추상화는 해로움
  - 리팩터링을 안하면 나중에 더 어려워질 것 같은 두려움
- ❌ **추상화를 너무 일찍 함 (Premature Abstraction)**
  - "중복은 힌트이지 명령이 아님"
  - Rule of Three: 세 번째 발견될 때 추상화
- ❌ **리팩터링 중 메소드 추출하고 그 메소드를 위한 테스트 추가**
  - 구현 상세에 테스트가 커플링됨

### 5. 반복

> Until the list is empty, go back to #2

테스트 목록이 비어있을 때까지 2~4단계를 반복한다.

**완료 기준**:
- ✅ 모든 테스트 리스트에 완료 표시
- ✅ 모든 테스트가 통과
- ✅ 코드가 충분히 깔끔함 (리팩터링 완료)

## TDD의 핵심 원칙

### Red-Green-Refactor 사이클

TDD의 기본 사이클을 색깔로 표현한 것이다.

```
🔴 Red (실패)
    ↓
🟢 Green (성공)
    ↓
🔵 Refactor (개선)
    ↓
    ⤴ (반복)
```

**각 단계의 의미**:
- **🔴 Red**: 실패하는 테스트 작성
  - 새로운 기능에 대한 테스트
  - 반드시 실패해야 함 (테스트가 제대로 동작하는지 확인)
- **🟢 Green**: 테스트를 통과하는 최소한의 코드 작성
  - 빠르게 동작하도록
  - 완벽함보다 동작이 우선
- **🔵 Refactor**: 코드 개선
  - 중복 제거
  - 가독성 향상
  - 설계 개선

### "Make it Work, Make it Right"

Kent Beck의 "Clean Code That Works"를 달성하기 위한 **분할 정복(divide and conquer)** 전략이다.

**문제 인식**:
- "작동하는 깔끔한 코드"를 한 번에 얻기는 어려움
- 최고의 프로그래머들도 한 번에 달성하기 힘든 목표

**해결 전략**:
1. **"작동하는"에 해당하는 부분을 먼저 해결** (Make it Work)
   - 막대를 녹색으로 빠르게 전환하는 것이 최우선
   - 동작만 하면 됨, 아름다움은 나중에
2. **그 다음 "깔끔한 코드" 부분을 해결** (Make it Right)
   - 적절한 때에 좋은 설계로 개선
   - 리팩터링을 통한 점진적 개선

**반대 접근법과의 비교**:
- ❌ **아키텍처 주도 개발(Architecture-Driven Development)**:
  - '깔끔한 코드'를 먼저 만들려 함
  - '작동하는' 부분에서 배운 것을 설계에 반영하느라 허둥거림
  - 추측과 짐작에 기반한 설계
- ✅ **TDD 접근법**:
  - 실제 동작하는 코드를 만들면서 학습
  - 학습한 내용을 기반으로 개선
  - 실제 피드백에 기반한 설계

### Interface vs Implementation 설계

TDD에서는 두 종류의 설계를 구분한다.

**Interface Design (인터페이스 설계)**:
- **"무엇"을 하는가** (What)
- 특정 행위가 **어떻게 호출되는가**
- 외부에서 어떻게 사용되는가
- **테스트 작성 단계**에서 결정

**Implementation Design (구현 설계)**:
- **"어떻게"** 구현하는가 (How)
- 시스템 내부에서 해당 행위를 어떻게 구현하는가
- **구현 단계와 리팩터링 단계**에서 결정

**예시**:
```java
// 테스트 작성 시 - Interface Design 결정
game.roll(pins);        // "roll"이라는 메소드로 호출
int result = game.score();  // "score"라는 메소드로 결과 조회

// 구현 시 - Implementation Design 결정
class BowlingGame {
    private int[] rolls = new int[21];  // 배열 vs 리스트?
    private int currentRoll = 0;         // 인덱스 관리 방법?

    public void roll(int pins) {
        rolls[currentRoll++] = pins;     // 어떻게 저장할 것인가?
    }
}
```

**왜 구분이 중요한가**:
- 테스트 작성 시 구현을 고민하면 집중력 분산
- 인터페이스를 먼저 설계하면 사용자 관점에서 편리한 API 도출
- 구현은 나중에 충분한 정보를 가지고 결정

### 행위(Behavior) 중심 테스트

TDD의 가장 중요한 원칙 중 하나이다.

> 💡 **행위를 검증하지 않고, 클래스의 오퍼레이션을 검증하는 것이 문제의 시작이다**

**올바른 접근**:
- ✅ **사용자에게 가치를 제공하는 행위(behavior)를 테스트**
- ✅ 시스템의 공개 인터페이스(Port)를 통해 행위 검증
- ✅ 테스트 케이스를 보고 어떤 행위를 검증하는지 알 수 있어야 함

**잘못된 접근**:
- ❌ 구현 디테일(implementation details)을 테스트
- ❌ private 메소드를 테스트
- ❌ 내부 상태를 직접 검증

**비교**:
```java
// ❌ BAD - 구현 상세를 테스트
@Test
void testRollsArray() {
    game.roll(5);
    int[] rolls = game.getRolls();  // 내부 구현 노출
    assertEquals(5, rolls[0]);
}

// ✅ GOOD - 행위를 테스트
@Test
@DisplayName("5개 핀을 쓰러뜨리고 나머지 모두 0이면 점수는 5이다")
void rollFiveAndRestZeros() {
    game.roll(5);
    rollMany(19, 0);
    assertEquals(5, game.score());  // 외부에서 관찰 가능한 행위
}
```

**결과**:
- ✅ 테스트가 작아지고 이해하기 쉬워짐
- ✅ 유지보수 비용이 낮아짐
- ✅ 리팩터링 시 테스트가 깨지지 않음

### 테스트 격리(Isolation)

**Kent Beck의 관점**: 테스트 **간의** 격리를 강조

- 각 테스트는 독립적으로 실행 가능
- 테스트 실행 순서에 무관하게 동작
- Shared Fixture의 nondeterministic한 사용 금지

**일반적인 오해**: 테스트 **대상**을 협력 객체들과 격리

- ❌ 각 클래스를 완전히 독립적으로 테스트
- ❌ 모든 협력 객체를 Mock으로 대체
- ❌ 과도한 모킹으로 구현과 강하게 결합

**올바른 접근**:
```java
// ✅ GOOD - 실제 협력 객체 사용
@Test
void shouldCalculateDiscount() {
    // 진짜 객체들 사용
    Product product = new Product("ITEM", 100);
    Cart cart = new Cart();
    cart.add(product);

    // 행위 검증
    assertEquals(100, cart.total());
}

// ❌ BAD - 과도한 Mock 사용
@Test
void shouldCalculateDiscount() {
    Product mockProduct = mock(Product.class);
    when(mockProduct.getPrice()).thenReturn(100);  // 구현 상세에 의존

    Cart cart = new Cart();
    cart.add(mockProduct);

    verify(mockProduct).getPrice();  // 구현 검증
}
```

## 실전 TDD 워크플로우

### 1. 테스트 리스트부터 작성

모든 TDD 세션은 테스트 리스트 작성으로 시작한다.

```java
/**
 * 테스트 리스트 (볼링 게임)
 * ------------------------
 * [ ] Gutter Game (모든 투구가 0)
 * [ ] All Ones (모든 투구가 1)
 * [x] One Spare (스페어 1개)
 * [ ] One Strike (스트라이크 1개)
 * [ ] Perfect Game (모든 프레임 스트라이크)
 */
```

**작성 순서**:
- Degenerate Case (가장 단순한 경우)부터
- General Case (일반적인 경우)
- Edge Case (경계 조건)
- Error Case (오류 상황)

### 2. 테스트 우선 작성 (AAA 패턴)

**AAA 패턴**:
- **Arrange** (준비): 테스트에 필요한 환경 설정
- **Act** (실행): 테스트하려는 행위 수행
- **Assert** (검증): 결과 확인

**Reverse AAA** (더 효과적):
- **Assert 먼저**: 원하는 결과부터 작성 (known to unknown)
- **Act 그 다음**: 어떤 행위로 그 결과를 얻을지
- **Arrange 마지막**: 그 행위에 필요한 준비

```java
@Test
void oneSpare() {
    // Assert - 원하는 결과부터
    assertEquals(16, game.score());

    // Act - 어떻게 그 결과를 얻을까?
    game.roll(5);
    game.roll(5);  // 스페어
    game.roll(3);   // 보너스
    rollMany(17, 0);

    // Arrange - 필요한 준비
    BowlingGame game = new BowlingGame();
}

// 위에서 아래로 재정렬
@Test
void oneSpare() {
    BowlingGame game = new BowlingGame();
    game.roll(5);
    game.roll(5);
    game.roll(3);
    rollMany(17, 0);
    assertEquals(16, game.score());
}
```

### 3. 최소 구현으로 테스트 통과

**구현 전략**:

1. **Fake It** (가짜 구현)
   ```java
   public int score() {
       return 16;  // 하드코딩
   }
   ```

2. **Obvious Implementation** (명백한 구현)
   ```java
   public int score() {
       int score = 0;
       for (int i = 0; i < rolls.length; i++) {
           score += rolls[i];
       }
       return score;
   }
   ```

3. **Triangulation** (삼각측량)
   - 여러 테스트를 추가하여 일반화 유도

**선택 기준**:
- 구현이 명확하면 → Obvious Implementation
- 어떻게 구현할지 모르면 → Fake It → Triangulation
- 복잡한 로직이면 → 작은 단계로 나누기

### 4. 리팩터링

**리팩터링 체크리스트**:
- [ ] 중복 코드 제거
- [ ] 의미 있는 이름 부여
- [ ] 메소드/클래스 크기 적절한가
- [ ] 추상화 수준 일관성 (SLAP - Single Level of Abstraction Principle)
- [ ] 디자인 패턴 적용 필요한가

**Composed Method 패턴**:
```java
// Before - 다양한 추상화 수준 혼재
public int score() {
    int score = 0;
    int frameIndex = 0;
    for (int frame = 0; frame < 10; frame++) {
        if (rolls[frameIndex] + rolls[frameIndex + 1] == 10) {  // 저수준
            score += 10 + rolls[frameIndex + 2];                  // 저수준
            frameIndex += 2;
        } else if (rolls[frameIndex] == 10) {                     // 저수준
            score += 10 + rolls[frameIndex + 1] + rolls[frameIndex + 2];  // 저수준
            frameIndex += 1;
        } else {
            score += rolls[frameIndex] + rolls[frameIndex + 1];   // 저수준
            frameIndex += 2;
        }
    }
    return score;
}

// After - 일관된 추상화 수준
public int score() {
    int score = 0;
    int frameIndex = 0;
    for (int frame = 0; frame < 10; frame++) {
        if (isSpare(frameIndex)) {
            score += spareScore(frameIndex);
            frameIndex += 2;
        } else if (isStrike(frameIndex)) {
            score += strikeScore(frameIndex);
            frameIndex += 1;
        } else {
            score += normalScore(frameIndex);
            frameIndex += 2;
        }
    }
    return score;
}

private boolean isSpare(int frameIndex) {
    return rolls[frameIndex] + rolls[frameIndex + 1] == 10;
}

private int spareScore(int frameIndex) {
    return 10 + rolls[frameIndex + 2];
}
```

## 자주 하는 실수와 해결책

### 실수 1: 테스트 리스트 없이 시작

**증상**:
- 언제 완료인지 모름
- 진척 정도 파악 불가
- 테스트를 놓치는 경우 발생

**해결책**:
```java
/**
 * 테스트 리스트
 * [ ] 기본 케이스
 * [ ] 경계 조건
 * [ ] 예외 상황
 * [ ] 성능 요구사항
 */
```

### 실수 2: 구현과 리팩터링을 동시에

**증상**:
- 테스트가 실패했을 때 원인 파악 어려움
- 변경 범위가 너무 커짐
- 롤백하기 어려움

**해결책**:
- 🔴 Red → 🟢 Green → 🔵 Refactor 순서 엄격히 준수
- Green 상태에서만 리팩터링
- 한 번에 한 가지만

### 실수 3: 과도한 Mock 사용

**증상**:
```java
// ❌ Mock 지옥
@Test
void complexTest() {
    ServiceA mockA = mock(ServiceA.class);
    ServiceB mockB = mock(ServiceB.class);
    ServiceC mockC = mock(ServiceC.class);

    when(mockA.doSomething()).thenReturn(something);
    when(mockB.process(any())).thenReturn(result);
    when(mockC.validate(any())).thenReturn(true);

    // ... 구현 상세에 강하게 결합된 테스트
}
```

**해결책**:
- Mock은 **모듈 경계(Port)** 에서만 사용
- 내부 협력 객체는 실제 객체 사용
- "Don't Mock What You Don't Own"

### 실수 4: 구현 상세 테스트

**증상**:
```java
// ❌ private 메소드 테스트
@Test
void testPrivateMethod() {
    Method method = Calculator.class.getDeclaredMethod("calculateInternal");
    method.setAccessible(true);
    // ...
}
```

**해결책**:
- Public API만 테스트
- Private은 Public을 통해 간접 테스트
- 정말 테스트가 필요하면 별도 클래스로 추출

### 실수 5: 너무 큰 테스트

**증상**:
- 한 테스트에 여러 시나리오
- 50줄 이상의 테스트 코드
- 실패 시 원인 파악 어려움

**해결책**:
```java
// ❌ 하나의 큰 테스트
@Test
void testEverything() {
    // 케이스 1
    // 케이스 2
    // 케이스 3
}

// ✅ 작은 테스트로 분리
@Test void case1() {}
@Test void case2() {}
@Test void case3() {}
```

## 참고 자료

- [Canon TDD](Canon-TDD.md) - Kent Beck의 정통 TDD 절차
- [Outside-In TDD](Outside-In-TDD.md) - 외부에서 내부로 진행하는 TDD
- [Advanced TDD](Advanced-TDD.md) - 고급 TDD 기법과 패턴
- [기능 구현 절차](기능-구현-절차.md) - 전체 개발 프로세스에서 TDD의 위치
