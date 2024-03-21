# IntelliJ

## Settings

### View

- Enable ligatures
- View | Appearance: Details in Tree View
- View | Active Editor: Show Indent Guides
- Show tree indent guides
- Show Members
    - ![show-members.png](../images/show-members.png)
    - before
        - ![show-members-before.png](../images/show-members-before.png)
    - after
        - ![show-members-after.png](../images/show-members-after.png)
- Jump to colors and fonts
    - ![jump-to-colors.png](../images/jump-to-colors.png)
- 커서가 위치한 식별자들에 적용되는 컬러 변경하기
    - ![color-under-cursor.png](../images/color-under-cursor.png)
- Show BreadCrumbs
    - ![show-breadcrumbs.png](../images/show-breadcrumbs.png)
- tooltip이 안보일 때
  - `Preferences | Appearance & Behavior | Appearance, Accessibility > Support screen readers` off

### Formatting

- Align when multiline
    - ![align-when-multiline.png](../images/align-when-multiline.png)
- Show Whitespaces
    - ![show-white-spaces.png](../images/show-white-spaces.png)
- 코드블록 포매팅 방지
    - ![formetter-off.png](../images/formetter-off.png)
  ```java
  //@formatter:off
  reviewDao.updateLikeCount(reviewId,true?1:-1);
  //@formatter:on
  ```
- [Annotations on the same line – IDEs Support (IntelliJ Platform) | JetBrains](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206947645-Annotations-on-the-same-line)
- ![intellij-annotations.png](../images/intellij-annotations.png)
- Comment alignment
  - ![comment-alignment.png](../images/comment-alignment.png)

### Edit

- Postfix Completion
    - ![postfix-completion.png](../images/postfix-completion.png)
- 변수 생성시 final 지정되도록
    - ![image_2.png](../images/declare-var-final.png)
- Template
    - New Method Body
        - ![new-method-body.png](../images/new-method-body.png)

### Navigation

- previous / next highlighted usage
    - opt + ctrl + 화살표
- Add Selection for Next Occurrences: ctrl + cmd + G
- Select All Occurrences: ctrl + G

### Etc

- before commit
    - ![before-commit.png](../images/before-commit.png)

## Commands

- create launcher script
- Preview file content
    - project view에서 파일 선택하고 spacebar
- Locate Duplicates...

## UML
- You can view your VCS local changes as a diagram. Select VCS | Uncommitted Changes| Show Local Changes as UML - `⌘⌥⇧D`
- list up element in diagram -  `⌘F12`
- When you click through classes in the graph, IntelliJ IDEA greys out classes that do not reside in the same package.
- You can select the the Edge Creation mode button icon on the diagram toolbar to draw relationship links between elements in your graph. To delete the existing links, select the ones you don't need and press ⌫ IntelliJ IDEA will update the source code accordingly.
- Show Implementations ⌘⌥B, Show Parents ⌘⌥P
- Analyze graph: 클래스들을 선택하고, 우클릭해서 Analyze graph
- Measure diagram centrality
  - You can use this action to identify the important nodes in the graph.
  - In the diagram editor, right-click anywhere in the editor to open the context menu.
  - From the context menu, select Analyze Graph | Measure Centrality.
## Plugins

- [IdeaVim - IntelliJ IDEs Plugin | Marketplace](https://plugins.jetbrains.com/plugin/164-ideavim)
- [GitHub Copilot - IntelliJ IDEs Plugin | Marketplace](https://plugins.jetbrains.com/plugin/17718-github-copilot)
- [CodeMetrics - IntelliJ IDEs Plugin | Marketplace](https://plugins.jetbrains.com/plugin/12159-codemetrics)
- [SonarLint - IntelliJ IDEs Plugin | Marketplace](https://plugins.jetbrains.com/plugin/7973-sonarlint)
- [IntelliJDeodorant - IntelliJ IDEs Plugin | Marketplace](https://plugins.jetbrains.com/plugin/14016-intellijdeodorant)
- [Presentation Assistant for 2023.2 - IntelliJ IDEs Plugin | Marketplace](https://plugins.jetbrains.com/plugin/7345-presentation-assistant-for-2023-2)
- [DTO generator - IntelliJ IDEs Plugin | Marketplace](https://plugins.jetbrains.com/plugin/7834-dto-generator)
- [Lombok - IntelliJ IDEs Plugin | Marketplace](https://plugins.jetbrains.com/plugin/6317-lombok)
- [Grep Console - IntelliJ IDEs Plugin | Marketplace](https://plugins.jetbrains.com/plugin/7125-grep-console)
    - ![grep-console.png](../images/grep-console.png)
    - p6spy를 위한 설정
      ![gc-p6spy.png](gc-p6spy.png)

## References
- [victorrentea/intellij-tips](https://github.com/victorrentea/intellij-tips)
- [인텔리J 활용 꿀팁 42가지 정리 | Popit](https://www.popit.kr/%EC%9D%B8%ED%85%94%EB%A6%ACj-%ED%99%9C%EC%9A%A9-%EA%BF%80%ED%8C%81-42%EA%B0%80%EC%A7%80-%EC%A0%95%EB%A6%AC/)
- [IntelliJ Tips, 익숙지 않은 분들을 위한](https://gist.github.com/aafwu00/e48a5b16318ca2c5b3c0f8e32f9da886)
- 
