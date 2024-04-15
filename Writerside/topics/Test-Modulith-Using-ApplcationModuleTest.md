# Test Modulith Using ApplicationModuleTest

## [How to Test Spring Application Events](https://www.baeldung.com/spring-test-application-events)

### `@ApplicationModuleTest`

- Scenario API를 이용해서 테스트를 선언적으로 작성할 수 있도록 해줌
- 다음과 같은 기능을 제공을 통해 테스트를 쉽게 개시하고, 결과를 확인할 수 있음
    - performing method calls
    - publishing application events
    - verifying state changes
    - capturing and verifying outgoing events
- 또한 API는 다음과 같은 추가 기능을 제공함
    - polling and waiting for asynchronous application events
    - defining timeouts
    - perform filtering and mapping on the captured events
    - creating custom assertions

### Test Code

```java

@ApplicationModuleTest
public class SpringModulithScenarioApiUnitTest {
    @Autowired
    OrderService orderService;

    @Autowired
    LoyalCustomersRepository loyalCustomers;

    @Test
    void whenPlacingOrder_thenPublishOrderCompletedEvent(Scenario scenario) {
        /**
         * andWaitforStateChange()
         * accepts a lambda expression and it retries executing it until it returns a non-null object or a non-empty Optional.
         * This mechanism can be particularly useful for asynchronous method calls.
         */
        scenario.stimulate(() -> orderService.placeOrder("customer-1", "product-1", "product-2"))
                .andWaitForEventOfType(OrderCompletedEvent.class)
                .toArriveAndVerify(evt -> assertThat(evt)
                        .hasFieldOrPropertyWithValue("customerId", "customer-1")
                        .hasFieldOrProperty("orderId")
                        .hasFieldOrProperty("timestamp"));
    }

    @Test
    void whenReceivingPublishOrderCompletedEvent_thenRewardCustomerWithLoyaltyPoints(Scenario scenario) {
        scenario.publish(new OrderCompletedEvent("order-1", "customer-1", Instant.now()))
                .andWaitForStateChange(() -> loyalCustomers.find("customer-1"))
                .andVerify(it -> assertThat(it)
                        .isPresent().get()
                        .hasFieldOrPropertyWithValue("customerId", "customer-1")
                        .hasFieldOrPropertyWithValue("points", 60));
    }
}
```

### Model Code

```java
class Order {
    private String id;
    private String customerId;
    private final List<String> productIds;
    private Instant timestamp;

    public String id() {
        return id;
    }

    public String customerId() {
        return customerId;
    }

    public Instant timestamp() {
        return timestamp;
    }

    public Order(String customerId, List<String> productIds) {
        this.customerId = customerId;
        this.productIds = productIds;
    }
}

@Service
class OrderService {
    private final OrderRepository repository;
    private final ApplicationEventPublisher eventPublisher;

    public OrderService(OrderRepository orders, ApplicationEventPublisher eventsPublisher) {
        this.repository = orders;
        this.eventPublisher = eventsPublisher;
    }

    public void placeOrder(String customerId, String... productIds) {
        Order order = new Order(customerId, Arrays.asList(productIds));
        // business logic to validate and place the order

        Order savedOrder = repository.save(order);

        OrderCompletedEvent event = new OrderCompletedEvent(savedOrder.id(), savedOrder.customerId(), savedOrder.timestamp());
        eventPublisher.publishEvent(event);
    }
}

@Repository
class OrderRepository {
    Order save(Order order) {
        return order;
    }
}

record OrderCompletedEvent(String orderId, String customerId, Instant timestamp) {
}

@Component
class LoyalCustomersRepository {

    private List<LoyalCustomer> customers = new ArrayList<>();

    public Optional<LoyalCustomer> find(String customerId) {
        return customers.stream()
                .filter(it -> it.customerId().equals(customerId))
                .findFirst();
    }

    public void awardPoints(String customerId, int points) {
        var customer = find(customerId).orElseGet(() -> save(new LoyalCustomer(customerId, 0)));

        customers.remove(customer);
        customers.add(customer.addPoints(points));
    }

    public LoyalCustomer save(LoyalCustomer customer) {
        customers.add(customer);
        return customer;
    }

    public boolean isLoyalCustomer(String customerId) {
        return find(customerId).isPresent();
    }

    public record LoyalCustomer(String customerId, int points) {

        LoyalCustomer addPoints(int points) {
            return new LoyalCustomer(customerId, this.points() + points);
        }
    }
}

@Slf4j
@Service
class LoyaltyPointsService {

    public static final int ORDER_COMPLETED_POINTS = 60;
    private final LoyalCustomersRepository loyalCustomers;

    public LoyaltyPointsService(LoyalCustomersRepository loyalCustomers) {
        this.loyalCustomers = loyalCustomers;
    }

    @EventListener
    public void onOrderCompleted(OrderCompletedEvent event) {
        log.error("LoyaltyPointsService::onOrderCompleted: {}", event);
        // business logic to award points to loyal customers
        loyalCustomers.awardPoints(event.customerId(), ORDER_COMPLETED_POINTS);
    }
}
```