# TDD with VSA, Graphql, JPA(Outside-in)
- [msbaek/vsa-tdd: Vertical Slicing Architecture, TDD, GraphQL, JPA 등을 이용한 웹 어플리케이션 예제ㅐ](https://github.com/msbaek/vsa-tdd)
- spring-modulith, graphql, jpa 등을 이용하여 간단한 기능을 TDD로 구현한다
- 구현하는 순서에 집중하면 좋겠다.

## GraphQL 관련 설정

### build.gradle

```groovy
implementation 'org.springframework.boot:spring-boot-starter-graphql'
implementation 'org.springframework.boot:spring-boot-starter-webflux'
implementation 'com.graphql-java:graphql-java-extended-scalars:21.0'

testImplementation 'io.projectreactor:reactor-test'
testImplementation 'org.springframework.graphql:spring-graphql-test'
```

### graphql-java-extended-scalars
```java
@Bean
public RuntimeWiringConfigurer runtimeWiringConfigurer() {
    return wiringBuilder -> wiringBuilder
            .scalar(ExtendedScalars.GraphQLBigDecimal)
            .scalar(ExtendedScalars.DateTime)
            .scalar(ExtendedScalars.Date);
}
```

### src/main/resources/application.yml

```yaml
  graphql:
    graphiql:
      enabled: true
```

### src/main/resources/schema.graphqls

```graphql
type Query {
    pagedGoodsCollection(request: SearchDto = { sort: { by: createdAt, direction: desc } }) : GoodsCollectionSlice
}

type GoodsCollectionSlice {
    totalElements: Int
    content: [GoodsCollectionDto]
}

type GoodsCollectionDto {
    id: ID!,
    name: String!,
    createdBy: Int!,
    createdAt: String!,
    updatedBy: Int,
    updatedAt: String
    goodsCollectionItems: [GoodsCollectionItemDto]
}

type GoodsCollectionItemDto {
    goodsNo: Int!,
    goodsId: String!,
    barcode: String,
}

type Mutation {
    createGoodsCollection(request: CreateGoodsCollectionRequest) : ID!
}

input CreateGoodsCollectionRequest {
    name: String!
    ids: [String!]!
}

input SearchDto {
    keyword: String = ""
    type: String = "name"
    page: Int = 0
    size: Int = 20
    sort: Sort = { by: createdAt, direction: desc }
}

input Sort {
    by: SortBy = createdAt
    direction: SortDirection = desc
}

enum SortBy {
    createdAt, barcode, id, name
}

enum SortDirection {
    asc, desc
}

scalar BigDecimal
scalar Date
scalar DateTime
```

## GoodsCollection
- GoodsCollection, GoodsCollectionItem을 조회하는 기능을 구현

### HttpGraphQlTester을 이용한 테스트

```java
@AutoConfigureHttpGraphQlTester
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.MOCK)
public class GoodsCollectionAcceptanceTest {
    @Autowired private HttpGraphQlTester graphQlTester;

    @Test
    public void query_pagedGoodsCollection() throws Exception {
        String queryString = """
                query {
                  pagedGoodsCollection(request: {
                    keyword: "name",
                    type: "type",
                    page: 0,
                    size: 10
                  }) {
                    content {
                      id
                      name
                      goodsCollectionItems {
                        goodsNo
                        goodsId
                        barcode
                      }
                    }
                  }
               }
                """;

        List<GetGoodsCollection.GoodsCollectionDto> result = request(queryString, "pagedGoodsCollection.content")
                .entityList(GetGoodsCollection.GoodsCollectionDto.class)
                .get();
        Approvals.verify(YamlPrinter.printWithExclusions(result, "createdBy" ,"createdAt" ,"updatedBy" ,"updatedAt"));
    }

    private GraphQlTester.Path request(String queryString, String requestName) {
        return this.graphQlTester
                .mutate()
                .header("x-tester-no", "1001")
                .build()
                .document(queryString)
                .execute()
                .path(requestName);
    }
}
```

- [Approvals Teste](https://approvaltests.com/)와 YamlPrinter를 사용하기 위한 설정(build.gradle)

```groovy
repositories {
	mavenCentral()
	maven { url 'https://jitpack.io' }
}

dependencies {
    testImplementation 'com.approvaltests:approvaltests:23.0.1'
    testImplementation 'com.github.HMInternational:ktown4u-utils:v1.5.0'
}
```

- approved.txt
```yaml
---
- id: 1
  name: "name1"
  createdBy: 1
  createdAt: "2024-04-05T07:16:55"
  goodsCollectionItems:
  - goodsNo: 112216
    goodsId: "gd00112216"
    barcode: "9000000112216"
  - goodsNo: 112216
    goodsId: "gd00112216"
    barcode: "9000000112216"
  - goodsNo: 112216
    goodsId: "gd00112216"
    barcode: "9000000112216"
- id: 2
  name: "name2"
  createdBy: 1
  createdAt: "2024-04-05T07:16:55"
  goodsCollectionItems:
  - goodsNo: 112215
    goodsId: "gd00112215"
    barcode: "9000000112215"
  - goodsNo: 112215
    goodsId: "gd00112215"
    barcode: "9000000112215"
  - goodsNo: 112215
    goodsId: "gd00112215"
    barcode: "9000000112215"
...
```
- 위와 같이 **약간 UI를 보면서 확인 가능한 수준의 텍스트로 테스트 결과를 확인** 가능함

### 더미 구현

- 먼저 동작하는 코드를 빠르게 보기 위해 더미 데이터로 구현을 완성한다.

```java
@Slf4j
@RequiredArgsConstructor
@Transactional(readOnly = true)
@Controller
@PrimaryAdapter
public class GetGoodsCollection {
    @QueryMapping("pagedGoodsCollection")
    public Page<GoodsCollectionDto> pagedGoodsCollection(@Argument final SearchDto request) {
        List<GoodsCollectionDto> result = List.of(
                // 더미 데이터 생성
                // ObjectMapper를 이용해서 json 파일을 읽는 방식이 좋다.
        );
        long total = result.size();
        return new PageImpl<>(result, PageRequest.of(request.page(), request.size()), total);
    }

    public record GoodsCollectionDto(Long id, String name, Long createdBy, String createdAt, Long updatedBy, String updatedAt) {
    }
}
```
## CreateGoodsCollection

### HttpGraphQlTester을 이용한 테스트

```java
    @Test
    public void create_goods_collection() throws Exception {
        String queryString = """
                mutation {
                    createGoodsCollection(request: {
                        name: "상품군 이름"
                        ids: ["GD00111839", "GD00111838", "GD00111836"]
                    })
                }
                """;

        Long result = request(queryString, "createGoodsCollection")
                .entity(Long.class)
                .get();
        assertThat(result).isGreaterThan(0L);
    }
```

### 더미 구현

```java
j
@RequiredArgsConstructor
@Transactional
@Controller
@PrimaryAdapter
public class CreateGoodsCollection {
    @MutationMapping("createGoodsCollection")
    public Long createGoodsCollection(@Argument final CreateGoodsCollectionRequest request) {
        log.info("request: {}", request);
        return 1l;
    }

    record CreateGoodsCollectionRequest(String name, List<String> ids) {
    }
}
```
