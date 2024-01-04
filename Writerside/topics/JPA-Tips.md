# JPA Tips

## Using JPA Specification
- [The best way to use the Spring Data JPA Specification](https://vladmihalcea.com/spring-data-jpa-specification/)

- Repository
```Java
public interface MemeberRepository extends JpaRepository<Member, Long>, JpaSpecificationExecutor<Member> {
    interface Specs {
        static Specification<Member> byName(String name) {
            return (root, query, builder) ->
                    builder.equal(root.get("name"), name);
        }

        static Specification<Member> byMemberLevel(MemberLevel level) {
            return (root, query, builder) ->
                    builder.equal(root.get("level"), level);
        }

        static Specification<Member> orderByName(Specification<Member> spec) {
            return (root, query, builder) -> {
                query.orderBy(builder.asc(root.get("name")));
                return spec.toPredicate(root, query, builder);
            };
        }
    }
}
```

- Usage
```java
        memeberRepository.findAll(
                orderByName(
                        byName("백명석")
                                .and(byMemberLevel(MemberLevel.GOLD))
                )
        );
```

## Paging Query

- Repository
    ```java
    public interface ShoppingCartRepository extends JpaRepository<ShoppingCart, Long> {
        @Query(
                value = """
                        select s from ShoppingCart s inner join s.cartItems cartItems
                        where s.member.id = :memberId and cartItems.product.id = :productId""",
                countQuery = """
                        select count(s) from ShoppingCart s inner join s.cartItems cartItems
                        where s.member.id = :memberId and cartItems.product.id = :productId"""
        )
        Page<ShoppingCart> memberAndProduct(Long memberId, Long productId, Pageable pageable);
    }
    ```
- Client
    ```java
    Pageable pageable = PageRequest.of(0, 10, Sort.by("id"));
    shoppingCartRepository.memberAndProduct(memberId, productId, pageable);
    ```
  
## Custom JPA Repository

- JPA를 사용할 때 interface만 있는 jpa repository와 impl이 있는 커스텀 리파지토리를 하나의 인터페이를 통해 사용하는 방법

```java
public interface CustomShoppingCartRepository {
    ShoppingCart findByMemberId(Long memberId);
}

public interface ShoppingCartRepository extends 
        JpaRepository<ShoppingCart, Long> , 
        CustomShoppingCartRepository {
}

public class CustomShoppingCartRepositoryImpl implements CustomShoppingCartRepository {
    private final EntityManager entityManager;

    public CustomShoppingCartRepositoryImpl(EntityManager entityManager) {
        this.entityManager = entityManager;
    }

    @Override
    public ShoppingCart findByMemberId(Long memberId) {
        return entityManager.createQuery(
                        "select new pe.msbaek.tddcases.cart.domain. sc from ShoppingCart sc " +
                                "join fetch sc.member m " +
                                "join fetch sc.cartItems ci " +
                                "join fetch ci.product p " +
                                "where m.id = :memberId", ShoppingCart.class)
                .setParameter("memberId", memberId)
                .getSingleResult();
    }
}
```

- 위와 같이 Custom Repository를 정의(CustomShoppingCartRepository)/구현(CustomShoppingCartRepositoryImpl)하고, EntityRepository(ShoppingCartRepository)를 정의하고
- 클라이언트에서는 EntityRepository를 주입받아 사용하면 됨
- 주의사항
  - CustomerEntityRepository의 구현체는 Impl을 붙여야 함(spring-boot의 관행). CustomerEntityRepositoryImpl

- 이렇게 하면 구현은 별도의 클래스로 하지만...
  - 사용하는 곳에서는 하나의 인터페이스에만 의존성을 갖으면 될 것 같아요.
  - 그럼 모든 엔터티마다 repository를 만들지 않고, aggregate root 역할을 하는 녀석에게만 repository를 만들 수 있지 않을까 합니다.
