# Test Printer

## 개요

- [I REGRET Not Telling Dave Farley THIS about Approval Testing](https://www.youtube.com/watch?v=jOuqE_o9rmg)
- 코드에서 특정 정보나 결과를 텍스트 형태로 정렬하고 포맷하는 기능을 가진 클래스나 메서드를 의미
- "printer"에 관한 주요 내용은 다음과 같습니다:
    1. **기능**
        - "printer"는 프로덕션 코드의 중요한 정보나 상태를 읽어와서 그것을 문자열로 포맷팅하는 역할을 함
        - 이 포맷팅된 문자열은 테스트의 결과와 비교됨
    2. **용도**
        - Emily는 approval testing에서 "printer"를 사용하면 결과를 쉽게 읽고, 비교할 수 있게 해주며, 크고 복잡한 출력에서도 오류를 쉽게 발견할 수 있다고 설명함
    3. **예제**
        - 글에서 Emily는 "vending machine" 코드 예제를 사용하여 "printer"의 작동 방식을 보여줌
        - 이 "printer"는 "vending machine"의 주요 상태, 예를 들면 디스플레이에 무엇이 표시되고 있는지, 기계의 잔액은 얼마인지, 기계에 어떤 동전들이 들어있는지 등의 정보를 문자열로
          포맷팅함
    4. **결과 비교**
        - "printer"에 의해 생성된 출력은 테스트의 기대 결과와 비교됨
        - Emily는 이 방법을 사용하여 테스트가 "**self-referential**"하지 않음을 강조함
        - 그 이유는 실제 출력을 독립적인 기대 결과(그녀가 만든 스케치)와 비교하기 때문임
- 결론적으로, "printer"는 approval testing에서 중요한 역할을 하는 도구로, 코드의 상태나 결과를 텍스트 형태로 잘 정리하여 테스트의 기대 결과와 비교하는 데 사용됨

## Not Self referential

- 예

```
1. 어떤 계산기 앱의 이전 버전이 2 + 2 = 5라는 잘못된 결과를 출력했다고 가정합니다.
2. 새 버전의 계산기 앱을 테스트할 때, 2 + 2의 결과가 5인지만 확인한다면, 이 테스트는 self-referential하다고 할 수 있습니다.
3. 왜냐하면 테스트는 올바른 수학적 결과(4)를 기대하는 것이 아니라, 단순히 이전 버전의 결과와 일치하는지만을 확인하기 때문입니다.
```

- 테스트의 결과가 단순히 이전의 출력과 일치하는지 확인하는 것이 아니라, **독립적인 기대 결과**(예: 그녀의 스케치)와 일치하는지를 확인한다는 점을 강조
- Emily Bate는 "self-referential"에 대한 오해를 풀기 위해 "vending machine" 코드 예제를 사용함
    - 그녀의 주장은 **approval test가 자신의 이전 결과를 참조하여 검증하는 것이 아니라, 사전에 정의된 기대치(그녀의 경우 스케치)와 실제 결과를 비교한다는 것임**
        - **characaterization test**는 self referential함
- 구체적인 예는 다음과 같음
    1. **스케치 생성**: Emily는 "vending machine"에서 페니(coin)를 넣었을 때의 예상 동작을 "스케치"로 미리 그려놓습니다. 이 스케치는 테스트의 기대 결과를 나타냅니다.
    2. **테스트 실행**: 그녀는 페니를 기계에 넣었을 때의 실제 동작을 테스트합니다. 처음 실행 시에는 예상 결과가 없으므로 테스트는 실패합니다.
    3. **결과 비교**: 실패한 테스트의 결과는 자동으로 생성된 diff 툴을 통해 그녀의 스케치와 비교됩니다. 이 시점에서, 그녀는 테스트 결과가 스케치와 일치하는지 확인합니다.
    4. **중요 포인트**: Emily는 이 비교 과정에서 테스트가 "self-referential"하지 않다고 강조합니다. 왜냐하면 결과가 이전 실행의 출력이나 다른 테스트의 결과를 참조하여 확인되는 것이
       아니라, 그녀가 사전에 만들어 놓은 스케치와 비교되기 때문입니다.
- 이렇게, Emily는 "printer"를 사용하여 생성된 결과와 사전에 정의된 스케치를 비교함으로써 approval test가 자체 참조적이지 않다는 것을 실제 예제를 통해 보여줍니다.

## 예제 코드

### 수작업으로 만든 Printer

```Java
    String print(){
        Map<String, String> fields = new LinkedHashMap();
        fields.put("Display", machine.display());
        fields.put("Balance", machine.balance().toString());
        fields.put("Coins", formatCoins(machine.coins()));

        StringBuilder lines = new StringBuilder("Vending Machine\n");
        fields.forEach(
                (key, value) -> lines.append(formatLineWithWhitespace(key, value))
        );

        return lines.toString();
    }

    private String formatCoins(Integer[] coins) {
        // join coins with commas, start with [, end with ]
        String joined = Arrays.stream(coins)
                .map(Object::toString)
                .reduce((a, b) -> a + ", " + b)
                .orElse("");
        return String.format("[%s]", joined);
    }

    /** Convenience function that lays out a name and a value at either ends of a fixed-width line.
     * eg if you call it with name="Foo" value="Bar" it will return
     * Foo                                       Bar
     */
    private String formatLineWithWhitespace(String name, String value){
        int whitespaceSize = columns - name.length() - value.lengt↓h();
        return String.format("%s%s%s\n", name, " ".repeat(Math.max(0, whitespaceSize)), value);
    }
```

### ObjectMapper를 이용한 PrettyPrinter

```Java
import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.datatype.jsr310.JavaTimeModule;
import com.fasterxml.jackson.datatype.jsr310.deser.LocalDateDeserializer;
import com.fasterxml.jackson.datatype.jsr310.deser.LocalDateTimeDeserializer;
import com.fasterxml.jackson.datatype.jsr310.ser.LocalDateSerializer;
import com.fasterxml.jackson.datatype.jsr310.ser.LocalDateTimeSerializer;

import java.time.LocalDate;
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;
import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;

public class PrettyJsonWriter {
    private ObjectMapper mapper;

    public PrettyJsonWriter() {
        this.mapper = createMapper();
    }

    private ObjectMapper createMapper() {
        final ObjectMapper mapper = new ObjectMapper();
        final JavaTimeModule javaTimeModule = new JavaTimeModule();
        javaTimeModule.addSerializer(LocalDate.class, new LocalDateSerializer(DateTimeFormatter.ofPattern("yyyy-MM-dd")));
        javaTimeModule.addDeserializer(LocalDate.class, new LocalDateDeserializer(DateTimeFormatter.ofPattern("yyyy-MM-dd")));
        javaTimeModule.addSerializer(LocalDateTime.class, new LocalDateTimeSerializer(DateTimeFormatter.ofPattern("yyyy-MM-dd")));
        javaTimeModule.addDeserializer(LocalDateTime.class, new LocalDateTimeDeserializer(DateTimeFormatter.ofPattern("yyyy-MM-dd")));
        mapper.registerModule(javaTimeModule);
        return mapper;
    }

    private String filterOut(final String statements, final String... fields) {
        final List<String> fieldList = Arrays.asList(fields);
        return Arrays.stream(statements.split("\n"))
                .filter(line -> fieldList.stream().noneMatch(line::contains))
                .collect(Collectors.joining("\n"));
    }

    public String prettyPrint(Object obj) {
        try {
            return mapper.writerWithDefaultPrettyPrinter().writeValueAsString(obj);
        } catch (JsonProcessingException e) {
            throw new RuntimeException(e);
        }
    }

    public String prettyPrintWithFilters(Object obj, String... filters) {
        String prettyStatements = prettyPrint(obj);
        return filterOut(prettyStatements, filters);
    }
}
```

- 위 프린터를 사용하는 테스트 코드

```Java
class NewInventoryTransferServiceTest {
    private PrettyJsonWriter prettyJsonWriter;

    @BeforeEach
    void setUp() {
        this.prettyJsonWriter = new PrettyJsonWriter();
    }

    @Test
    void create() {
        // ...

        InventoryTransfer result = create(request);

        String prettyStatements = prettyJsonWriter.prettyPrintWithFilters(result, 
            "createdAt", "createdUserId", "deletedAt", "id", "inventoryTransferId", "serialNumber", "updatedAt", "updatedUserId");
        Approvals.verify(prettyStatements);
    }
```

- approved.txt

```json
{
  "fromWarehouseId": 1,
  "toWarehouseId": 2,
  "requestedDate": "2024-01-18",
  "note": "note",
  "status": "ACCEPTED",
  "items": [
    {
      "goodsId": 1,
      "requestedQuantity": 10,
      "status": "OPEN"
    },
    {
      "goodsId": 2,
      "requestedQuantity": 20,
      "status": "OPEN"
    }
  ]
}
```