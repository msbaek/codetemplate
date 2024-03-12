# Overall Application Development Process

## 분석

- 산출물 = 요구사항 명세

## 설계

- 산출물 = 클래스 설계
- 요구사항 명세를 클래스 설계로 변환하는 작업
- 4단계
    1. 책임과 기능을 나누고 어떤 클래스가 있는지 확인
        - 요구사항 명세를 단일 책임 기능으로 분해
        - 기능 목록 작성
    2. 클래스, 속성, 메소드 정의
    3. 클래스 간의 상호 작용 정의: 일반화, 실체화, 집합, 합성, 연관, 의존
    4. 클래스를 연결하고 **실행 엔트리 포인트** 제공
        - 모든 세부 사항을 캡슐화한 다음
        - 최상위 인터페이스인 ApiAuthenticator를 설계하고,
        - 외부에서 사용할 API를 노출해야

## 행위 구현

- 🔺️ step down rule 준수
- 🔺️ Review Draft 시행: 라이프싸이클 후반 변경 비용 줄이기 위해
- high level acceptance test(target design) / comment it: obsidian:
  //open?vault=Documents&file=ctemplate%2F101.Ideas%2F%EC%95%A0%ED%94%8C%EB%A6%AC%EC%BC%80%EC%9D%B4%EC%85%98%20%EA%B0%9C%EB%B0%9C%20%EC%A0%88%EC%B0%A8
    - sociable test not solitary test
- TDD: spec → examples → test case(yaml printer) - Usage first TDD
    - TDD 에서 테스트는 1:1이 아님. Sociable.
    - test data builder: dsl 방식으로 gtp 로 구현
- Vertical Slicing TDD
    - implement logic spring-boot-test
    - extract delegate
- split by layers of abstraction, split unrelated complexity
    - SoC: 본질적 복잡성(domain logic)과 우연한 복잡성(application logic, ui, infra 등)을 분리
- PR 작성
    - 🔺️ Code Walk Through
    - Code Review
- Deploy
- Monitoring

## 지속적인 리팩터링

- valuable value object
    - entity에서 불변을 찾아서 value object로
    - 모든 entity의 필드들은 value object의 후보

## 정적 데이터 모델링

- 행위를 구현하기 전에 정적 데이터 모델링을 어느 정도 수행해야 함
- 먼저 클래스 다이어그램 작성: 클래스명, 주요 데이터, 관계(관계에서 역할의 이름, 방향성, cardinality 표기 필수)
- 이 클래스 다이어그램 검증을 위한 객체 다이어그램: 엑셀로 작성하고 이걸 markdown table로 작성해서 gpt에서 plantuml로 작성해달라고 함
- 이게 있으면 유스케이별 before, after를 yaml printer로 표현. approval test를 할 수 있다. xxx-approved.txt를 보면 이 유스케이스(테스트)가 무엇을 하는 것인지 이해할
  수 있다.

## 뭘 assert할건가 ?

- 예전
    - 디버그를 하면서 변수를 조사하거나
    - 로그를 많이 남겨서 로그를 조사했다
    - 이때 필요하면 쿼리를 실행해서 DB의 상태를 조사했다.
- 지금도 이런 조사를 수작업으로 한번하고 이후 assert에 추가하여 자동으로 검증해야
- test first할 때 AAA에서 assert부터 하라고 한다
    - 이건 needs driven으로 불필요한 코드를 하나도 작성하지 않기 위함이 큰 것 같다
    - 이땐 필요한 객체, 그 객체에게 보낼 메시지 등을 찾는다.
    - 하지만 제대로 assert를 하려면 최초 assert로는 부족한 점이 있을 수 있다
    - 특히 side-effect가 있는 경우 assert에서는 놓친다.

## Overall Use Case Architecture

- 지금이라면 spring-modulith를 이용한 모듈러 모듈리스로 시작하는 것이 답

## tip

- [jakarta validation and test](https://gist.github.com/msbaek/948c42956033dc7794132f3116a09028)
- [Unit Testing Log Messages Made Easy - DZone](https://dzone.com/articles/unit-testing-log-messages-made-easy)
- [test querydsl query.js](https://gist.github.com/msbaek/8a71d5ccf47d96830c6ccd3a0ab6b19a)
- [Spring Data JPA Repository for Database View | Baeldung](https://www.baeldung.com/spring-data-jpa-repository-view)