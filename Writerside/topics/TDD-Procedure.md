# TDD 절차

테스트 주도 개발(Test-Driven Development, TDD)을 실천하기 위한 구체적인 절차와 워크플로우를 정리한 실전 가이드이다.

> 💡 **TDD의 개념과 철학**은 [Canon TDD](Canon-TDD.md)를 참고하라.

## Canon TDD 5단계

Kent Beck이 제시한 정통 TDD 절차 ([상세 내용](Canon-TDD.md)):

```
1. 테스트 시나리오 목록 작성
   ↓
2. 목록에서 하나를 실행 가능한 테스트로 전환
   ↓
3. 테스트 통과시키기 (모든 이전 테스트도 통과)
   ↓
4. 리팩터링 (선택적)
   ↓
5. 목록이 빌 때까지 2~4 반복
```

## 상세한 과정

![tdd-procedures.png](tdd-procedures.png)

### Start

- 사용자의 요구사항을 개발하기 적합한 형태의 문서(spec., use case, use story 등)로 작성
- 명세에 대한 시나리오 작성
  1. 명세 대한 예제 작성
  2. 예제를 테스트 시나리오로 변환

### 1. Write a list of the test scenarios you want to cover

- 인터페이스 설계와 구현 설계를 혼합하는 실수 금지(Mistake: mixing in implementation design decisions)
- Interface/Implementation Split
- TDD의 첫번째 오해: 모든 설계를 하나로 묶어야 한다
  - 2가지 종류의 설계가 존재함
  - 특정 행위가 호출(invoke)되는 방법(interface): test list, test를 작성할 때
  - 시스템의 해당 행위를 구현하는 방법(implementation): refactoring할 때

### 2. Turn exactly one item on the list into an actual, concrete, runnable test

- Picking the next test is an important skill, & one that only comes with experience
- Most Degenerate Case first
  - Guarded Claude 유도되어 Indentation Depth가 감소하고, Pre-Condition이
    명확해짐
- Assert → Act → Arrange 순으로 작성
  - Interface 설계 단계임
  - 테스트를 작성할 때는 오퍼레이션의 완벽한 Interface에 대해 상상해야 함
- Mistake: write tests without assertions just to get code coverage
- Mistake: convert all the items on the Test List into concrete tests, then make them pass one at a time
- 테스트가 실패하는지 반드시 확인
  - 그렇지 않으면 추가할 때부터 성공하는 테스트인지, 구현이 제대로 되어 테스트가
    성공하게 되었는지 알 수 없음

## 실전 워크플로우

### 1단계: 테스트 리스트 작성

**시작 전 반드시 테스트 목록을 작성한다.**

```java
/**
 * 테스트 리스트 (볼링 게임)
 * ------------------------
 * [ ] Gutter Game (모든 투구가 0)
 * [ ] All Ones (모든 투구가 1)
 * [ ] One Spare (스페어 1개)
 * [ ] One Strike (스트라이크 1개)
 * [ ] Perfect Game (모든 프레임 스트라이크)
 */
```

**작성 순서**:

- Degenerate Case (가장 단순한 경우)
- General Case (일반적인 경우)
- Edge Case (경계 조건)
- Error Case (오류 상황)

**효과**:

- ✅ 인수 조건(언제 완료?) 명확화
- ✅ 진척도 측정 가능
- ✅ 놓치는 케이스 방지

### 2단계: 테스트 우선 작성

> 💡 **이 단계에서는 Interface Design을 결정한다.**
>
> - "무엇을" 하는가 (What)
> - "어떻게 호출되는가" (How to use)
> - 사용자 관점에서 편리한 API 설계

**작성 방법: Reverse AAA (선택적)**

테스트 작성 시 **Assert → Act → Arrange** 순으로 역순으로 생각하면:

- 원하는 결과(Assert)가 먼저 명확해짐
- 그 결과를 얻기 위한 행위(Act)를 도출
- 꼭 필요한 준비(Arrange)만 추가

```java
// 1. Assert부터 - 원하는 결과 (known to unknown)
assertEquals(16,game.score());

// 2. Act - 어떻게 그 결과를 얻을까?
        game.

roll(5);
game.

roll(5);  // 스페어
game.

roll(3);

rollMany(17,0);

// 3. Arrange - 필요한 것들
BowlingGame game = new BowlingGame();

// 최종: 순서 재정렬
@Test
void oneSpare() {
    // Arrange
    BowlingGame game = new BowlingGame();

    // Act
    game.roll(5);
    game.roll(5);
    game.roll(3);
    rollMany(17, 0);

    // Assert
    assertEquals(16, game.score());
}
```

**장점**:

- 목표가 명확해져 불필요한 준비를 피할 수 있음
- Interface Design에 집중 (구현 고민 최소화)
- "known to unknown" 접근 (Kent Beck)

**테스트 작성 체크리스트**:

- [ ] 테스트 이름이 검증하는 행위를 명확히 표현하는가?
- [ ] Assert가 구체적이고 명확한가?
- [ ] 테스트가 한 가지만 검증하는가?
- [ ] 구현이 아닌 행위를 검증하는가?
- [ ] 구현 방법이 아닌 사용 방법(Interface)을 설계했는가?

### 3단계: 최소 구현 전략

> 💡 **이 단계에서는 Implementation Design을 결정한다.**
>
> - "어떻게" 구현하는가 (How to implement)
> - 내부 구조, 알고리즘, 데이터 구조
> - 하지만 빠르게 Green을 보는 것이 최우선

**빠르게 Green을 보는 것이 최우선이다.**

#### 전략 1: Fake It (가짜 구현)

```java
public int score() {
    return 16;  // 하드코딩으로 일단 통과
}
```

#### 전략 2: Obvious Implementation (명백한 구현)

```java
public int score() {
    int score = 0;
    for (int i = 0; i < rolls.length; i++) {
        score += rolls[i];
    }
    return score;
}
```

#### 전략 3: Triangulation (삼각측량)

여러 테스트를 추가하여 일반화를 유도한다.

**전략 선택 기준**:

- 구현이 명확하면 → Obvious Implementation
- 어떻게 구현할지 모르면 → Fake It
- 복잡한 로직이면 → 작은 단계로 나누기

### 4단계: 리팩터링

**Green 상태에서만 리팩터링한다.**

#### 리팩터링 수준 결정

| 시점                       | 수준   | 이유                  |
| -------------------------- | ------ | --------------------- |
| 테스트 케이스 완료 시      | 최소한 | 아직 전체 모습을 모름 |
| 테스트 리스트 모두 완료 시 | 적극적 | 전체를 보고 판단 가능 |

#### 리팩터링 체크리스트

**로컬 리팩터링** (테스트 하나 완료 시):

- [ ] 중복 코드 제거
- [ ] 의미 있는 이름 부여
- [ ] Magic Number 제거

**전역 리팩터링** (테스트 리스트 완료 시):

- [ ] Composed Method 패턴 적용
- [ ] 추상화 수준 일관성 (SLAP)
- [ ] 디자인 패턴 적용 검토
- [ ] 코드 냄새 제거

#### Composed Method 패턴 예시

```java
// Before - 여러 추상화 수준 혼재
public int score() {
    int score = 0;
    int frameIndex = 0;
    for (int frame = 0; frame < 10; frame++) {
        if (rolls[frameIndex] + rolls[frameIndex + 1] == 10) {
            score += 10 + rolls[frameIndex + 2];
            frameIndex += 2;
        } else if (rolls[frameIndex] == 10) {
            score += 10 + rolls[frameIndex + 1] + rolls[frameIndex + 2];
            frameIndex += 1;
        } else {
            score += rolls[frameIndex] + rolls[frameIndex + 1];
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
```

### 5단계: 완료 확인

**완료 기준**:

- ✅ 모든 테스트 리스트에 완료 표시 (`[x]`)
- ✅ 모든 테스트가 통과
- ✅ 코드가 충분히 깔끔함

## 자주 하는 실수와 해결책

### 실수 1: 테스트 리스트 없이 시작

**문제점**:

- 언제 완료인지 모름
- 진척도 파악 불가
- 테스트 누락 발생

**해결책**:

```java
/**
 * [ ] 기본 케이스
 * [ ] 경계 조건
 * [ ] 예외 상황
 */
```

### 실수 2: 여러 테스트를 한 번에 작성

**문제점**:

- 첫 테스트 구현이 다른 테스트에 영향
- 성취감 저하
- 재작업 발생

**해결책**:

- **한 번에 하나씩** 추가하고 통과
- 2 → 3 → 4 → 2 사이클 반복

### 실수 3: 구현과 리팩터링 동시 진행

**문제점**:

- 실패 원인 파악 어려움
- 변경 범위 과다
- 롤백 어려움

**해결책**:

```
🔴 Red → 🟢 Green → 🔵 Refactor
```

순서를 **엄격히** 준수한다.

### 실수 4: 과도한 Mock 사용

**문제점**:

```java
// ❌ Mock 지옥
@Test
void complexTest() {
    ServiceA mockA = mock(ServiceA.class);
    ServiceB mockB = mock(ServiceB.class);
    ServiceC mockC = mock(ServiceC.class);
    // ... 구현 상세에 강하게 결합
}
```

**해결책**:

- Mock은 **모듈 경계**에서만
- 내부 협력 객체는 실제 객체 사용
- "Don't Mock What You Don't Own"

### 실수 5: 구현 상세 테스트

**문제점**:

```java
// ❌ private 메소드 테스트
Method method = Calculator.class.getDeclaredMethod("internal");
method.

setAccessible(true);
```

**해결책**:

- **Public API만** 테스트
- Private은 Public을 통해 간접 테스트
- 정말 필요하면 별도 클래스로 추출

### 실수 6: 리팩터링 중 테스트 추가

**문제점**:

- 구현 상세에 테스트 커플링
- 리팩터링이 아님 (새 기능 추가)

**해결책**:

> 💡 **리팩터링 단계에서는 절대 테스트를 추가하지 않는다.**

### 실수 7: 테스트 성공을 위해 Assertion 조작

**문제점**:

```java
// ❌ 계산된 값을 그대로 복사
int actual = calculator.calculate(5, 3);

assertEquals(actual, calculator.calculate(5, 3));  // 무의미
```

**해결책**:

- Expected 값은 **직접 계산**하거나 **명시**
- 이중 부기의 이점 활용

## 빠른 참조 체크리스트

### TDD 세션 시작 시

- [ ] 테스트 리스트 작성
- [ ] Degenerate 케이스부터 시작

### 각 테스트마다

- [ ] 테스트 하나 선택
- [ ] Reverse AAA로 작성 (Assert → Act → Arrange)
- [ ] Red 확인 (반드시 실패하는지)
- [ ] 최소 구현으로 Green
- [ ] 로컬 리팩터링 (중복, 네이밍)
- [ ] 테스트 리스트에 완료 표시

### 테스트 리스트 완료 시

- [ ] 전역 리팩터링
- [ ] Composed Method 패턴
- [ ] 코드 냄새 제거
- [ ] 모든 테스트 재실행

## 참고 자료

### 기본 개념

- [Canon TDD](Canon-TDD.md) - Kent Beck의 정통 TDD 절차 상세
- [TDD](TDD.md) - TDD 개요 및 핵심 개념
- [Advanced TDD](Advanced-TDD.md) - 고급 TDD 기법과 패턴

### 실전 적용

- [Outside-In TDD](Outside-In-TDD.md) - 외부에서 내부로 진행하는 TDD
- [기능 구현 절차](기능-구현-절차.md) - 전체 개발 프로세스에서 TDD의 위치
- [테스트 추가하기](테스트-추가하기.md) - 테스트 추가 전략
