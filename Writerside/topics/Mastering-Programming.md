# Mastering Programming

- [Mastering Programming - by Kent Beck](https://tidyfirst.substack.com/p/mastering-programming?publication_id=256838&post_id=141377046&isFreemail=false)

## Overview

- 견습공은 한번에 많은 문제를 풀어서 큰 문제를 해결하는 것을 배움
- 장인은 한번에 더 적은 문제를 풀어서 (견습공의 문제보다) 더 큰 문제를 해결하는 것을 배움
- 핵심은 분리된 해결책의 통합 비용이 본래 문제를 푸는 비용보다 더 적어진다는 것임
- The journeyman learns to solve bigger problems by solving more problems at once.
- The master learns to solve even bigger problems than that by solving fewer problems at once.
- Part of the wisdom is subdividing so that integrating the separate solutions will be a smaller problem than just
  solving them together.

## Time

- Slicing
    - 큰 프로젝트를 작게 나누고 당신의 맥락에 맞게 조각을 재정렬하라
    - 저자는 언제나 프로젝트를 작은 조각으로 나눌수 있고, 다양한 요구사항을 수용할 수 있는 새로운 조합을 찾을 수 있음
    - Take a big project, cut it into thin slices, and rearrange the slices to suit your context.
    - I can always slice projects finer and I can always find new permutations of the slices that meet different needs.
- One thing at a time
    - 효율성에 너무 집중한 나머지 오버헤드를 줄이기 위해 피드백 주기 횟수를 줄임
    - 이로 인해 얻은 이익이 더 크고 어려운 디버깅 상황을 발생시킴
    - We’re so focused on efficiency that we reduce the number of feedback cycles in an attempt to reduce overhead.
    - This leads to difficult debugging situations whose expected cost is greater than the cycle overhead we avoided.
- Make it run, make it right, make it fast.
    - 한 번에 한 가지씩, 슬라이싱 및 쉬운 변경의 예
    - (Example of One Thing at a Time, Slicing, and Easy Changes)
- Easy changes(MCETMEC)
    - 어려운 변경을 해야 할 때 먼저 쉽게 바꾸고(이게 어려울 수 있음), 그 다음에 쉬운 변화를 하라
    - When faced with a hard change, first make it easy (warning, this may be hard), then make the easy change.
    - (e.g. slicing, one thing at a time, concentration, isolation). Example of slicing.
- Concentration
    - 여러 요소를 변경해야만 한다면 먼저 코드를 재정렬하여 변경이 하나의 요소에서만 일어나도록 하라
    - If you need to change several elements, first rearrange the code so the change only needs to happen in one element.
- Isolation
    - 요소의 일부만 변경해야 하는 경우 해당 부분을 추출하여 전체 하위 요소가 변경되도록 하라(변경을 하기 전에 변경될 부분을 추출)
    - If you only need to change a part of an element, extract that part so the whole subelement changes.
- Baseline Measurement
    - 현재의 상태를 측정하고 프로젝트 시작
    - 무언가를 고치기 시작하려는 엔지니어링 본능에 위배됨. 하지만 기준선을 측정하면 실제로 문제를 고치고 있는지 여부를 알 수 있음
    - Start projects by measuring the current state of the world
    - This goes against our engineering instincts to start fixing things, but when you measure the baseline you will actually know whether you are fixing things.

## Learning

- Call your shot
    - 코드를 실행하기 전에 어떤 일이 일어날지 예측해 보라
    - Before you run code, predict out loud exactly what will happen.
- Concrete hypotheses
    - 프로그램인 잘못 동작하는 경우, 수정하기 전에 무엇이 잘못되었다고 생각하는지 정확하게 설명해 보라
    - 2가지 이상의 가설이 있는 경우 차별진단(differential diagnosis)을 찾아보라
    - When the program is misbehaving, articulate exactly what you think is wrong before making a change.
    - If you have two or more hypotheses, find a differential diagnosis.
- Remove extraneous detail.
    - 버그 리포팅을 할 때는 가장 짧은 재현 단계를 찾아보라
    - 버그를 격리할 때는 가장 짧은 테스트 케이스를 찾아보라
    - 새로운 API를 사용할 때는 가장 기본적인 예제부터 시작하라
    - "이 모든 것들은 문제가 될 가능성이 낮아"라는 생각은 잘못된 가정일 경우 비용이 많이 드는 가정임
    - When reporting a bug, find the shortest repro steps. When isolating a bug, find the shortest test case.
    - When using a new API, start from the most basic example.
    - “All that stuff can’t possibly matter,” is an expensive assumption when it’s wrong.
    - E.g. see a bug on mobile, reproduce it with curl

- Multiple scales
    - 스케일 간 자유롭게 이동하라
    - 테스트 문제가 아니라 설계 문제일 수도 있음
    - 기술적 문제가 아니라 사람의 문제일 수도 있음
    - Move between scales freely
    - Maybe this is a design problem, not a testing problem
    - Maybe it is a people problem, not a technology problem [cheating, this is always true].

## Transcend Logic(논리를 초월하다)

- Symmetry(대칭)
    - 거의 동일한 사물은 완전히 동일한 부분과 분명히 다른 부분으로 나눌 수 있음
    - Things that are almost the same can be divided into parts that are identical and parts that are clearly different.
- Aesthetics(미학)
    - 미학은 오를 수 있는 강력한 경수면임. 또한 여러 기능을 하나의 거대한 엉망진창으로 인라인하는 것처럼 어긋나게 만들 수 있는 자유로운 경사면이기도 함
    - Beauty is a powerful gradient to climb. It is also a liberating gradient to flout (e.g. inlining a bunch of functions into one giant mess).
- Rhythm
    - 적절한 순간까지 기다리면 에너지를 보존하고 혼란을 피할 수 있음. 행동할 때가 되면 강렬하게 행동하라.
    - Waiting until the right moment preserves energy and avoids clutter. Act with intensity when the time comes to act.
- Tradeoffs
    - 모든 결정에는 트레이드오프가 있음. 어떤 답을 선택해야 하는지(혹은 어제 어떤 답을 선택했는지)를 아는 것보다 결정의 무엇에 의존하는지 아는 것이 더 중요함
    - All decisions are subject to tradeoffs. It’s more important to know what the decision depends on than it is to know which answer to pick today (or which answer you picked yesterday).

## Risk

- Fun list
    - 현재 작업과 밀접하지 않은 아이디거가 떠오르면 메모해 두고 빠르게 현재 업무에 복귀하라
    - 현 작업을 멈출 지점에 도달하면 이 목록을 다시 살펴보라
    - When tangential ideas come, note them and get back to work quickly
    - Revisit this list when you’ve reached a stopping spot.
- Feed Ideas
    - 아이디어는 겁먹은 작은 새와 같음
    - 겁을 주면 더 이상 찾아오지 않음
    - 아이디어가 떠오르면 조금씩 먹이를 주라
    - 가능한 한 빨리 무효화하되 자존감이 아닌 데이터에 기반하여 무효화하라
    - Ideas are like frightened little birds
    - If you scare them away they will stop coming around
    - When you have an idea, feed it a little
    - Invalidate it as quickly as you can, but from data not from a lack of self-esteem.
- 80/15/5
    - 80%의 시간을 저위험/합리적 보상을 제공하는 작업에 투자하라
    - 고위험/고수익 관련 업무에 15%의 시간을 투자하라
    - 보상에 관계없이 자신을 자극하는 일에 5%의 시간을 투자하라
    - 다음 세대가 당신 업무의 80%를 할 수 있도록 가르치라
    - 누군가 이 일을 이어받을 준비가 되었을 즈음 15%의 실험 중 하나(혹은 덜 자주하는 5%의 실험 중 하나)가 성과를 거두어 새로운 80%가 될 거임. 반복하라
    - Spend 80% of your time on low-risk/reasonable-payoff work
    - Spend 15% of your time on related high-risk/high-payoff work
    - Spend 5% of your time on things that tickle you, regardless of payoff
    - Teach the next generation to do your 80% job
    - By the time someone is ready to take over, one of your 15% experiments (or, less frequently, one of your 5% experiments) will have paid off and will become your new 80%. Repeat.
