# Extract Existing Blocks

![extract-blocks.png](extract-blocks.png)

1. 변경을 하려는 코드 블록을 메소드로 추출
2. 테스트 추가
3. 기능을 추가, 변경
4. 완료 후
    - inline: 본래의 코드 블록이 있던 곳에 새로운 기능이 있는 것이 더 좋은 경우
    - 유지: 새로운 메소드로 유지(이 메소드는 **Sprout Method**가 됨)
    - 위임: 새로운 클래스로 위임(**Extract Delegate**): 새로운 클래스로 이동이 더 적합한 경우