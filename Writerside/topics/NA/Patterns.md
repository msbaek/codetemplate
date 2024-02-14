# Patterns

## Wrapper Patterns

#### Proxy

- 원본 클래스를 변경하지 않은 상태로 프록시 클래스를 도입해서 원본 클래스와 무관한 새로운 기능을 추가
- 용처
    - 주요 비즈니스와 무관한 요구 사항 개발
    - RPC
    - Cache

### decorator

- 프록시와 유사하지만 원본 클래스와 관련이 깊은 기능을 추가

### adapter

- 호환되지 않는 인터페이스를 변환

## bridge

- decouple an abstraction from its implementation so that two can vary independently
- 합성으로 폭발적인 상속 문제 해결
- 예. 차 옵션. 선루프 m개, 휠 n개 → m x n 개의 조합. 상속 클래스 n

## composite

- 트리 구조의 데이터 처리에 사용됨
    - GMS collection 다룰 때 ???
- 예제가 훌륭