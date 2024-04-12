# Aggregate

```java
@Getter
public class Hold {
    private final HoldId id;
    private final Book.Barcode onBook;
    private final LocalDate dateOfHold;

    private Hold(PlaceHold placeHold) {
        this.id = new HoldId(UUID.randomUUID());
        this.onBook = placeHold.inventoryNumber();
        this.dateOfHold = placeHold.dateOfHold();
    }

    public static Hold placeHold(PlaceHold command) {
        return new Hold(command);
    }

    public Hold then(UnaryOperator<Hold> function) {
        return function.apply(this);
    }

    public record HoldId(UUID id) implements Identifier {
    }

    public record PlaceHold(Book.Barcode inventoryNumber, LocalDate dateOfHold) {
    }
}
```
- Hold는 Entity
- Hold를 생성하기 위해 필요한 여러 인자를 PlaceHold(Value Object)로 묶어서 전달
- Hold, PlaceHold가 Aggregate로 묶임