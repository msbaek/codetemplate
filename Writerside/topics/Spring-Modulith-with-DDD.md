# Spring Modulith with DDD

## Building Modular Monolith Applications with Spring Boot and Domain Driven Design

- https://github.com/xsreality/spring-modulith-with-ddd/tree/part-1-ddd-solution
- https://itnext.io/building-modular-monolith-applications-with-spring-boot-and-domain-driven-design-d3299b300850

### Writing Modular Code의 목표

- Application is organized into modules. Each module solves a distinct part of the business problem.
- Modules are loosely coupled. No cyclic dependencies
- Complete application is deployed as a single unit at runtime
- Module’s public interface (behaviour exposed to other modules) is flexible and can be changed atomically.
    - Unlike microservices, when we need to change a module’s public interface, other modules consuming the interface
      can be changed and rolled out together.

### What is a Module?

- “High cohesion, low coupling”
    - “Things that change together should stay together”
- Controller, Service, Repository etc of a thing should be in the same package (if package is used for module boundary)
  as they always need to change together when building a new feature aka package-by-feature.
- Packaging by layers (packages like controllers, models, repositories etc) are by definition low in cohesion and would
  not be modular.0
- This definition comes from the book “Structured Design”.

### Identifying Modules

- identification of boundaries is still important
- How do we identify the module boundaries? In my experience, the patterns of Domain Driven Design are one of the best
  tools to solve this problem.

### A Business Problem

- ex. library and book borrowing process
- requirements:
    - The library consists of thousands of books. There can be multiple copies of the same book.
    - Before being included in the library, every book receives a barcode stamped at the back or one of the end pages.
      This barcode number uniquely identifies the book.
    - A patron of the library can check out a book if it is available. Usually the patron locates the book in the
      library and goes to the circulation desk to check out. Sometimes the patron can directly go to the desk and ask
      for a book by title.
    - The book is checked out for a fixed period of 2 weeks.
    - To check in the book, the patron can go to the circulation desk or drop it in the drop zone.
- Let’s break down this library domain
  into [subdomains](https://stackoverflow.com/questions/73077578/what-actually-is-a-subdomain-in-domain-driven-design)
    - "process of checking out (borrowing) a book"
    - "inventory of books"

### Building the Solution

- solve the problem of the subdomain by designing
  a [Bounded Context](https://martinfowler.com/bliki/BoundedContext.html)
    - bounded contexts are also the modules in our Modular Monolith application.
  ```
  src/main/java
  └── example
      ├── borrow
      │   ├── Loan
      │   ├── LoanController
      │   ├── (+) LoanDto
      │   ├── (+) LoanManagement
      │   ├── LoanMapper      
      │   ├── LoanRepository
      │   └── LoanWithBookDto
      └── inventory
          ├── Book
          ├── BookController
          ├── (+) BookDto
          ├── (+) BookManagement
          ├── BookMapper
          └── BookRepository
  ```

### inventory bounded context

- with the help of [Aggregate Pattern](https://martinfowler.com/bliki/DDD_Aggregate.html)
- ![inventory-bounded-context.png](../images/inventory-bounded-context.png)
-

```Java 
@Entity
@Getter
@NoArgsConstructor
@Table(uniqueConstraints = @UniqueConstraint(columnNames = {"barcode"}))
class Book {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String title;
    @Embedded
    private Barcode inventoryNumber;
    private String isbn;
    @Embedded
    @AttributeOverride(name = "name", column = @Column(name = "author"))
    private Author author;
    @Enumerated(EnumType.STRING)
    private BookStatus status;
    @Version
    private Long version;

    /* aggregate root 
     * any changes to this aggregate (e.g. modifying the status of the Book) must happen 
     * via the Book entity only and limited to the module itself
     */
    public Book(String title, Barcode inventoryNumber, String isbn, Author author) {
        this.title = title;
        this.inventoryNumber = inventoryNumber;
        this.isbn = isbn;
        this.author = author;
        this.status = BookStatus.AVAILABLE;
    }

    public boolean isAvailable() {
        return BookStatus.AVAILABLE.equals(this.status);
    }

    public boolean isIssued() {
        return BookStatus.ISSUED.equals(this.status);
    }

    public Book markIssued() {
        if (this.status.equals(BookStatus.ISSUED)) {
            throw new IllegalStateException("Book is already issued!");
        }
        this.status = BookStatus.ISSUED;
        return this;
    }

    public Book markAvailable() {
        this.status = BookStatus.AVAILABLE;
        return this;
    }

    /* thress value objects */
    public record Barcode(String barcode) { }

    public record Author(String name) { }

    public enum BookStatus {
        AVAILABLE, ISSUED
    }
    /* thress value objects */
}
```

- Repository

```Java
interface BookRepository extends JpaRepository<Book, Long> {
    Optional<Book> findByIsbn(String isbn);
    Optional<Book> findByInventoryNumber(Book.Barcode inventoryNumber);
    List<Book> findByStatus(Book.BookStatus status);
}
```

- BookmanagementService

```Java
@Transactional
@Service
@RequiredArgsConstructor
public class BookManagement {
    private final BookRepository bookRepository;
    private final BookMapper mapper;

    public BookDto addToInventory(String title, Book.Barcode inventoryNumber, String isbn, String authorName) {
        var book = new Book(title, inventoryNumber, isbn, new Book.Author(authorName));
        return mapper.toDto(bookRepository.save(book));
    }

    public void removeFromInventory(Long bookId) {
        var book = bookRepository.findById(bookId)
                .orElseThrow(() -> new IllegalArgumentException("Book not found!"));
        if (book.issued()) {
            throw new IllegalStateException("Book is currently issued!");
        }
        bookRepository.deleteById(bookId);
    }

    public void issue(String barcode) {
        var inventoryNumber = new Book.Barcode(barcode);
        var book = bookRepository.findByInventoryNumber(inventoryNumber)
                .map(Book::markIssued)
                .orElseThrow(() -> new IllegalArgumentException("Book not found!"));
        bookRepository.save(book);
    }

    public void release(String barcode) {
        var inventoryNumber = new Book.Barcode(barcode);
        var book = bookRepository.findByInventoryNumber(inventoryNumber)
                .map(Book::markAvailable)
                .orElseThrow(() -> new IllegalArgumentException("Book not found!"));
        bookRepository.save(book);
    }

    @Transactional(readOnly = true)
    public Optional<BookDto> locate(Long id) {
        return bookRepository.findById(id)
                .map(mapper::toDto);
    }

    @Transactional(readOnly = true)
    public List<BookDto> issuedBooks() {
        return bookRepository.findByStatus(Book.BookStatus.ISSUED)
                .stream()
                .map(mapper::toDto)
                .toList();
    }
}
``` 

- 주목할 사항
    - BookManagement service returns DTO instead of the Book entity
        - By returning only DTOs at the service layer, we protect the domain model (entity) to leak out to the
          Controller and presentation layers.
        - for small projects this may seem overkill, but for reasonably large projects, your future self will thank you
          for restricting the domain within the Service layer.
    - BookManagement(Public API) is the only class accessible by other modules apart from the DTO
- REST API for clients

```Java
@RestController
@RequiredArgsConstructor
class BookController {
    private final BookManagement books;

    @PostMapping("/books")
    ResponseEntity<BookDto> addBookToInventory(@RequestBody AddBookRequest request) {
        var bookDto = books.addToInventory(request.title(), new Barcode(request.inventoryNumber()), request.isbn(), request.author());
        return ResponseEntity.ok(bookDto);
    }

    @DeleteMapping("/books/{id}")
    ResponseEntity<Void> removeBookFromInventory(@PathVariable("id") Long id) {
        books.removeFromInventory(id);
        return ResponseEntity.ok().build();
    }

    @GetMapping("/books/{id}")
    ResponseEntity<BookDto> viewSingleBook(@PathVariable("id") Long id) {
        return books.locate(id)
                .map(ResponseEntity::ok)
                .orElse(ResponseEntity.notFound().build());
    }

    @GetMapping("/books")
    ResponseEntity<List<BookDto>> viewIssuedBooks() {
        return ResponseEntity.ok(books.issuedBooks());
    }

    record AddBookRequest(String title, String inventoryNumber,
                          String isbn, String author) {
    }
}
```      

- With the Inventory bounded context, we have fulfilled the first two requirements listed earlier. The remaining
  requirements will be met with the Borrow Bounded context.
- [source in github](https://github.com/xsreality/spring-modulith-with-ddd/blob/part-1-ddd-solution)
- The Author is not turned into another entity because we do not have any business requirements around it

### borrow bounded context

- ![loan-bounded-context.png](../images/loan-bounded-context.png)
- loan : long lasting entity

```Java
@Entity
@Getter
@Setter
@NoArgsConstructor
public class Loan {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    @Embedded
    @AttributeOverride(name = "barcode", column = @Column(name = "book_barcode"))
    private Book book;
    private Long patronId;
    private LocalDate dateOfIssue;
    private int loanDurationInDays;
    private LocalDate dateOfReturn;
    @Enumerated(EnumType.STRING)
    private LoanStatus status;
    @Version
    private Long version;

    Loan(String bookBarcode) {
        this.book = new Book(bookBarcode);
        this.dateOfIssue = LocalDate.now();
        this.loanDurationInDays = 14;
        this.status = LoanStatus.ACTIVE;
    }

    public static Loan of(String bookBarcode) {
        return new Loan(bookBarcode);
    }

    public boolean isActive() {
        return LoanStatus.ACTIVE.equals(this.status);
    }

    public boolean isOverdue() {
        return LoanStatus.OVERDUE.equals(this.status);
    }

    public boolean isCompleted() {
        return LoanStatus.COMPLETED.equals(this.status);
    }

    public void complete() {
        if (isCompleted()) {
            throw new IllegalStateException("Loan is not active!");
        }
        this.status = LoanStatus.COMPLETED;
        this.dateOfReturn = LocalDate.now();
    }

    public enum LoanStatus {
        ACTIVE, OVERDUE, COMPLETED
    }

    public record Book(String barcode) {
    }
}
```

- 주목할 사항
    - book entity에 대한 foreign key 참조가 없음
    - 대신 Book을 barcode만 갖는 value object로 모델링
    - 메모리 참조 → repository룰 통한 참조(id)
    - no JPA Many-to-One relation modeled between Loan and Book. It is intuitively defined in the domain model and
      enforced by the aggregate invariants.
- LoanManagement service

```Java
@Transactional
@Service
@RequiredArgsConstructor
public class LoanManagement {
    private final LoanRepository loanRepository;
    private final BookManagement books;
    private final LoanMapper mapper;

    public LoanDto checkout(String barcode) {
        books.issue(barcode);
        var loan = Loan.of(barcode);
        var savedLoan = loanRepository.save(loan);
        return mapper.toDto(savedLoan);
    }

    public LoanDto checkin(Long loanId) {
        var loan = loanRepository.findById(loanId)
                .orElseThrow(() -> new IllegalArgumentException("No loan found"));
        books.release(loan.getBook().barcode());
        loan.complete();
        return mapper.toDto(loanRepository.save(loan));
    }

    @Transactional(readOnly = true)
    public List<LoanWithBookDto> activeLoans() {
        return loanRepository.findLoansWithStatus(LoanStatus.ACTIVE);
    }

    @Transactional(readOnly = true)
    public Optional<LoanDto> locate(Long loanId) {
        return loanRepository.findById(loanId)
                .map(mapper::toDto);
    }
}
```

- 주목할 점
    - First, LoanManagement service depends on BookManagement service
    - Secondly, the implementation of checkout and checkin do not perform any invariants check at all.
        - They simply call the methods on the Loan aggregate or the BookManagement service which then perform the
          invariants check.
        - This results in a very clear and easy to understand implementation of the LoanManagement service.
    - Finally, similar to BookManagement, this service returns Loan DTO only and not the entity itself.

### few things that can be improved.

- Tight Coupling between Bounded Contexts
    - borrow BC가 inventory BC와 긴밀하게 연결. inventory가 불용할 때 borrow도 작동 불가
    - 결제 요청은 단일 txn에서 2개의 aggregate를 갱신함. 하나의 txn은 하나의 aggregate를 갱신해야 함
- Independent Testing of Bounded Contexts
    - 통합 테스트
  ```Java
  @Transactional
  @SpringBootTest
  class LoanManagementIT {
      @Autowired LoanManagement loans;
      @Autowired BookManagement books;

      @Test
      void shouldCreateLoanAndIssueBookOnCheckout() {
          var loanDto = loans.checkout("13268510");
          assertThat(loanDto.status()).isEqualTo(LoanStatus.ACTIVE);
          assertThat(loanDto.book().barcode()).isEqualTo("13268510");
          assertThat(books.locate(1L).get().status()).hasToString("ISSUED");
      }

      @Test
      void shouldCompleteLoanAndReleaseBookOnCheckin() {
          var loan = loans.checkin(10L);
          assertThat(loan.status()).isEqualTo(LoanStatus.COMPLETED);
          assertThat(books.locate(2L).get().status()).hasToString("AVAILABLE");
      }
  }
  ```
- Controlling the Interface of Bounded contexts

## Improving Modular Monolith Applications with Spring Modulith

- https://itnext.io/improving-modular-monolith-applications-with-spring-modulith-edecc787f63c
- https://github.com/xsreality/spring-modulith-with-ddd/tree/part-2-spring-modulith

### New Business Requirements

- 고객의 도서 대출을 요청하면 바로 issued(발급됨) 상태가 됨
- 하지만 고객의 실제로 수령했는지 알 수 없음
- 따라서 보류됨(hold) 상태 추가가 필요해짐

### Rethinking the Domain Model

- 기존의 서브도메인
- ![existing-subdomains.png](../images/existing-subdomains.png)
- 기존의 동기식 강결합(tightly coupling)을 비동기식 약결합(loosely coupling)으로 변경하고자 함
- new-subdomain-emerges
- ![new-subdomain-emerges.png](../images/new-subdomain-emerges.png)
- Catalog 서브도메인은 책의 가용여부에 대한 걱정 없이 책의 메타데이터를 표현함
- 도메인 이벤트를 이용해서 의존성 개선
    - Borrow BC는 BookCheckoutRequested 이벤트를 발생
    - Inventory BC는 해당 이벤트를 수신하면 책이 가용한지 확인하고 BookAvailable나 BookUnavailable 이벤트 발생
    - Borrow BC는 Inventory BC가 발생시킨 이벤트에 따라 대출 상태를 갱신(ACTIVE or REJECTED)

### Two Subdomains, one Bounded context?

- ![multiple-domains-in-one-bc.png](../images/multiple-domains-in-one-bc.png)
- BC는 언어적 경계(linguistic boundary)임
- 모델의 의미는 BC 내에서 변하지 않음
- 대여와 인벤토리에서 도서의 의미는 동일함
- Inventory BC에서 도서의 가장 중요한 속성은 사용 가능 상태임
- Borrow BC에서 책을 빌리고 반납할 때 책의 사용 가능 상태가 영향을 받음
- 도서라는 모델은 두 BC 간에 동일함
- When a new book is added to the library (catalog), Borrow BC must know about it to keep the inventory updated. We can
  model this with events as below:
    - The Catalog BC generates BookAddedToCatalog event which is consumed by Borrow BC to update its local inventory to
      indicate a new book is available for borrowing.
    - The Catalog and Borrow BC are loosely coupled when communicating asynchronously via events because the process of
      borrowing a book is no longer dependent on the Catalog.

### The Borrow Bounded Context (with new Insights)

- ![eventual-consistency-btw-loan-book.png](../images/eventual-consistency-btw-loan-book.png)
- LoanManagement service fires the events as the use cases are executed

```Java
@Transactional
@Service
@RequiredArgsConstructor
public class LoanManagement {
    private final LoanRepository loans;
    private final BookRepository books;
    private final ApplicationEventPublisher events;
    private final LoanMapper mapper;

    public LoanDto hold(String barcode, Long patronId) {
        var book = books.findByInventoryNumber(new Barcode(barcode))
                .orElseThrow(() -> new IllegalArgumentException("Book not found!"));

        if (!book.available()) {
            throw new IllegalStateException("Book not available!");
        }

        var dateOfHold = LocalDate.now();
        var loan = Loan.of(barcode, dateOfHold, patronId);
        var dto = mapper.toDto(loans.save(loan));
        events.publishEvent(
                new BookPlacedOnHold(
                        book.getId(),
                        book.getIsbn(),
                        book.getInventoryNumber().barcode(),
                        loan.getPatronId(),
                        dateOfHold));
        return dto;
    }
}

public record BookPlacedOnHold(Long bookId, 
                               String isbn, 
                               String inventoryNumber,
                               Long patronId, 
                               LocalDate dateOfHold) {
}
```

- InventoryManagement service listens to the event and marks the book as on hold

```Java
@Transactional
@Service
@RequiredArgsConstructor
public class InventoryManagement {
    private final BookRepository books;

    @ApplicationModuleListener
    public void on(BookPlacedOnHold event) {
        var book = books.findById(event.bookId())
                .map(Book::markOnHold)
                .orElseThrow(() -> new IllegalArgumentException("Book not found!"));
        books.save(book);
    }
}
```

- Notice the annotation @ApplicationModuleListener
    - = @TransactionalEventListener, @Async, @Transactional(propagation = Propagation.REQUIRES_NEW)
    - ensures that the event listener method is executed in a different thread and in a separate transaction.
        - The code triggering the event is always completed irrespective of the listener.
- 새로운 이슈
    - 이벤트를 트리거한 코드이 이미 완료된 상태에서 리스너 실행이 실패되면...

### Spring Modulith Event Publication Registry

- spring-modulith-starter-core, spring-modulith-starter-jpa 의존성 사용
    - spring-modulith-starter-jpa dependency enables the Event Publication Registry
        - It creates a table EVENT_PUBLICATION
            - ID, COMPLETION_DATE, EVENT_TYPE, LISTENER_ID, PUBLICATION_DATE, SERIALIZED_EVENT
    - COMPLETION_DATE를 이용해서 리스너 실행이 실패하면 이벤트가 손실되지 않고 리스터 실행이 성공할 때까지 다시 트리거됨

### Isolated Testing of Modules

- Employing events as communication pattern between the modules facilitates independent testing.

```Java
@Transactional
@ApplicationModuleTest // automatically restricts the Spring Application context to the package (representing the module) under test and nothing else
class LoanIntegrationTests {
    @DynamicPropertySource
    static void initializeData(DynamicPropertyRegistry registry) {
        registry.add("spring.sql.init.data-locations", () -> "classpath:borrow.sql");
    }

    @Autowired
    LoanManagement loans;

    @Test
    void shouldCreateLoanOnPlacingHold(Scenario scenario) {
        scenario.stimulate(() -> loans.hold("13268510"))
                .andWaitForEventOfType(BookPlacedOnHold.class)
                .toArriveAndVerify((event, dto) -> {
                    assertThat(event.inventoryNumber()).isEqualTo("13268510");
                    assertThat(dto.status()).isEqualTo(LoanStatus.HOLDING);
                });
    }
}
```

- [integration testing with Spring Modulith](https://docs.spring.io/spring-modulith/reference/testing.html)

### Stronger Module boundaries with Spring Modulith

- Spring 모듈리스에서는 모든 최상위 패키지가 모듈로 간주
    - API 패키지라고 함
    - 이 패키지의 모든 클래스는 다른 모듈(최상위 패키지)에서 자동으로 사용할 수 있음
    - 최상위 패키지의 하위 패키지는 내부에 있으며 다른 모듈에서 액세스할 수 없음

```
src/main/java
└── example
    ├── borrow <-- API Package(module)
    │   ├── book
    │   │   ├── Book
    │   │   ├── BookCollected
    │   │   ├── BookPlacedOnHold
    │   │   ├── BookRepository
    │   │   ├── BookReturned
    │   │   └── InventoryManagement
    │   └── loan
    │       ├── BorrowController
    │       ├── Loan        
    │       ├── LoanDto
    │       ├── LoanManagement
    │       ├── LoanMapper      
    │       └── LoanRepository      
    ├── catalog <-- API Package(module)
    │   ├── internal
    │   │   ├── BookDto
    │   │   ├── BookMapper
    │   │   ├── CatalogBook
    │   │   ├── CatalogController
    │   │   ├── CatalogManagement
    │   │   └── CatalogRepository
    │   └── BookAddedToCatalog
    └── LibraryApplication
```

- The restriction can be enforced with the help of a test as below.

```java
class SpringModulithTests {
    ApplicationModules modules = ApplicationModules.of(SpringModulithWithDddApplication.class);

    @Test
    void verifyPackageConformity() {
        modules.verify();
    }
}
```

- If you try to access an internal class of a module

```java
org.springframework.modulith.core.Violations: - Module 'borrow' depends on non-exposed type example.catalog.internal.BookAddedToCatalog within module 'catalog'!
BookAddedToCatalog declares parameter BookAddedToCatalog.on(BookAddedToCatalog) in (InventoryManagement.java:0)
```

### Documenting Modules

- With Spring Modulith, it is possible to generate documentation snippets and C4 diagrams describing the relationships
  between the modules.

```Java
class SpringModulithTests {
    ApplicationModules modules = ApplicationModules.of(SpringModulithWithDddApplication.class);

    @Test
    void createModulithsDocumentation() {
        new Documenter(modules).writeDocumentation();
    }
}
```

- C4 diagram of our application modules
- ![c4-diagram.png](../images/c4-diagram.png)
- The test also generates documentation snippets for each module (bounded context).
- ![module-canvas.png](../images/module-canvas.png)