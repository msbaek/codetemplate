# JPA Buddy

## A Guide to JPA Buddy

- [출처](https://www.baeldung.com/jpa-buddy)

### Create an Entity

- 폴더에서 우클릭 > New > JPA Entity

### JPA Explorer

![jpa-explorer.png](../images/jpa-explorer.png)

### JPA Designer

![jpa-designner.png](../images/jpa-designer.png)

## JPA Buddy – From Zero to Hero

- [JPA Buddy – From Zero to Hero](https://www.youtube.com/watch?v=TpD6bT9M1CE)

### JPA Buddy에 접근하는 4가지 방법

- JPA Explorer view
- Context Dependent: 파일 에디터 상단
    - ![conext-dependent.png](../images/conext-dependent.png)
- JPA Designer
- Generate Menu
    - ![generate-menu.png](../images/generate-menu.png)

### 필드 추가

- OwnerType이라는 enum 타입을 필드로 추가하는 경우
- JPA Designer에서 Attributes / Enum을 더블클릭
    - ![add-enum-field.png](../images/add-enum-field.png)

### User:Media = 1:n 관계 추가하기

- Context Dependent에서 Association 클릭
- ![context-association.png](../images/context-association.png)
- 타입에서 기존 타입을 선택하거나, 오른쪽 + 버튼을 클릭해서 신규 타입 추가
- ![onetomany.png](../images/onetomany.png)

### add named query(jpql)

- ![add-named-query.png](../images/add-named-query.png)
- Entity에 named query가 추가됨

### FlyWay

- 의존성 추가
  ```
  implementation 'org.flywaydb: flyway-core'
  implementation 'org.flywaydb:flyway-mysql'
  annotationProcessor 'org.hibernate.orm:hibernate-jpamodelgen'
  ```
- ![flyway-versioned-migration.png](../images/flyway-versioned-migration.png)
- ![flyway-diff-versioned-migration.png](../images/flyway-diff-versioned-migration.png)
- ![flyway-1.png](../images/flyway-1.png)

### JPA Entity 사용하기

- ![show-available-queries.png](../images/show-available-queries.png)
- ![create-repository.png](../images/create-repository.png)

### 자동으로 생성된 긴 jpa query method 이름 줄이기

- 줄이면 jpql 쿼리가 `@Query`로 추출됨
- 메소드 명에서 show context action하고 extract jpql query and configure 선택 후 이름 변경
- ![extract-query.png](../images/extract-query.png)
  ```java
  public interface ShoppingCartRepository extends JpaRepository<ShoppingCart, Long> {
      @Query("""
              select s from ShoppingCart s inner join s.cartItems cartItems
              where s.member.id = ?1 and cartItems.product.id = ?2""")
      List<ShoppingCart> memberAndProduct(Long id, Long id1);
  }
  ```

### Create DTO

- ![create-dto.png](../images/create-dto.png)
- 이후 모델에서 필드명 변경하면 DTO에도 자동으로 반영됨. 동기화가 이뤄짐