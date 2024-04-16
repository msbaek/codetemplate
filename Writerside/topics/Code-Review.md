# Code Review
- 이 페이지에서는 Code Review에서 보이는 리팩터링 케이스를 모아 본다

## 자주하는 리팩터링
- `Inline Method`
- 읽는 순서(Reading Order)
  - Step Down Rule, Composed Method를 고려해서 
  - 함수가 하는 일을 각각의 스텝/블록이 나타내도록 순서를 변경
  - `Slide Statement`, `Chunk Statemments`
    - `Slide Statement`: 라인의 순서 변경
    - `Chunk Statements`: 블록 간의 빈 행 삽입
  - 의도가 드러나도록 구성
    - public 메소드가 하는 일을
    - public 메소드의 각 라인(블럭)이 잘 표현해야
    - 각 라인(블럭)은 메소드보다 하나 낮은 수준의 추상화를 가져야
- 응집도 순서
- `Extract Method`

## 추출한 메소드 이름
- IntelliJ는 `Extract Method`를 하면 거의 모든 메소드 이름을 `getXXX`로 제안한다.
- 이 이름은 대개 명확치 않다.
- 의도(intention, what, purpose 등)를 나타내는 이름을 부여하는 노력이 필요하다.
