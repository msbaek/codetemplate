# Collections

## HashSet vs TreeSet
- HashSet 
  - 순서 보장 안됨
  - 삽입, 검색, 삭제가 빠름
- TreeSet
  - 정렬된 순서로 저장
  - HashSet보다 느림
  
## ArrayList vs LinkedList
- ArrayList
  - 랜덤 검색이 빠름
  - 맨 앞이나 중간에 삽입, 삭제가 느림

## 컬렉션을 동기화하는 방법
- `Collections.synchronizedList(new ArrayList<>());`