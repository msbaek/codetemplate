# Inbox

## Stream API

- [Learn Java Stream API Practically | by Amirhosein Gharaati | Stackademic](https://blog.stackademic.com/learn-java-stream-api-practically-5e8bbcfb84be)
- Stream API Advantages
    - Laziness: terminal operation이 호출될 때까지
    - (Mostly) stateless: immutable state
        - Most intermediate operations are stateless. except for distinct(), sorted(...), limit(...), and skip(...).
    - Optimizations included: 융합, 불필요한 연산 제거, 어떤 경우는 short-circuited되기도
    - Non-reusable
    - Less boilerplate
    - parallelization
- Best Practices
    - Smaller operations
        - One-line expressions, Method references
    - Method references
    - Code formatting: 각 파이프라인을 한 라인으로
    - Order of operations
        ```Java
        Stream.of("ananas", "oranges", "apple", "pear", "banana")
              .map(String::toUpperCase)       // 1. Process
              .sorted(String::compareTo)      // 2. Sort
              .filter(s -> s.startsWith("A")) // 3. Filter
              .forEach(System.out::println);
        ```
        - 이 코드는
          - map x 5
          - sorted x 8
          - filter x 5
          - forEach x 2
        - 2개의 결과 값을 만들기 위해 20번 연산 수행
    - reorder
        ```Java
        Stream.of("ananas", "oranges", "apple", "pear", "banana")
              .filter(s -> s.startsWith("A")) // 1. Filter first
              .map(String::toUpperCase)       // 2. Process
              .sorted(String::compareTo)      // 3. Sort
              .forEach(System.out::println);
        ```
        - 이 코드는
            - filter x 5
            - map x 2
            - sorted x 1
            - forEach x 2
        - 2개의 결과 값을 만들기 위해 10번 연산 수행
    

## Spring Tips: DataSources
- [Spring Tips: DataSources](https://www.youtube.com/watch?v=rt_cUtb8LnQ&list=WL&index=8&t=11s)
- 
## Multitenancy jdbc

PostreSQL Driver
Docker Compose
JDBC API
H2 Database

4:11

```Bash
→ LIVE cat ~/josh-env/bin/postgres.sh
#!/usr/bin/env bash
NAME=$｛1：-default｝-postgres
PORT=${2:-5432}
docker ps -aq -f "name=$(NAME}" | while read 1; do docker rm -f $1 ; echo "stop ping $1"; done docker run --name $NAME
-p ${PORT} :5432 \
-e POSTGRES_USER=user \
-e PGUSER=user \
-e POSTGRES_PASSWORD=pw \ postgres:latest
```

2개의 db를 실행하고 클라이언트로 연결

```Java
record Customer (Integer id, String name) {
}
```

8:13

```Java
aBean
RouterFunction<ServerResponse> routes(JdbcTemplate template) E
return route()
•GET(Ov" /customers", request → {
var results: List<Customer > = template query( sql: "select * from customer"
(rs, rowNum) →
› new Customerrs getInt( columnLabel: "id"), rs getString( columnLabel: "name"
return ServerResponse. ok).body(results);
- build();
```

11:19

```Java
class MultitenantDataSource extends AbstractRoutingDataSource f
private final AtomicBoolean initialized = new AtomicBoolean();
@Override
protected DataSource determineTargetDataSource) i
if (this.initialized.compareAndSet expectedValue: false, newValue: true)) f
this.afterPropertiesSet();
｝
return super .determinetargetDataSource();
@Override
protected Object determineCurrentLookupKey() {
var authentication: Authentication = SecurityContextHolder.getContext().getAuthentication() ;
return null;
```

## Vaadin Flow

https://github.com/spring-tips/vaadin-flow-24

Vaadin
Spring Reactive Web
Spring Security

```Java
@Route("")
@PermitAll
class ChatView extends VerticalLayout 1
  ChatView(ChatService chatService) {
    var messageList = new MessageList();
    var textInput = new MessageInput();
    
    setSizeFull();
    add (messageList, textInput);
    expand (messageList);
    textInput.setWidthFull);
    
    service. join().subscribemessage -> {
      var l = new ArrayList<> ( messageList. getItems()) ;
      n.add (new MessageListItem message.text() ,message.time() , message. username()));
      getUI().ifPresent( ui -> {
        ui access ((Command) () -> messageList.setItems(nl));
      });
    }) ;
    
    textInput.addSubmitListener(event -> service.add(event.getValue()));
}

record Message (String username, String text, Instant time) {}

@Service
class ChatService {
  private final Sinks.Many<Message> messages = Sinks.many().multicast().directBestEffort();
  private final Flux<Message> messagesFlux = messages.asFlux();
  private final AuthenticationContext ctx;
  
  ChatService(AuthenticationContext ctx) {
    this.ctx = ctx;
  }

  Flux<Message> join() {
    return this.messagesFlux;
  }
  
  void add (String message) {
    var username = this.ctx.getPrincipalName().orElse( "Anonymous") ;
    this.messages.tryEmitNext(new Message(
      username, message, Instant. now()));
  }
｝
```

application.properties

```
vaadin.whitelisted-packages=com.vaadin,org.vaadin, com. example.vaadin
```

localhost:8080/login
spring-security comment out

localhost:8↓080/
채팅이 가능해짐

spring-security 다시 enable

```Java
@configuration
class SecurityConfiguration extends VaadinWebSecurity {
  @Override
  protected void configure(HttpSecurity http) throws Exception {
    super.configure(http);
    setLoginView(http, LoginView.class);
  }

  @Bean
  UserDetailsManager userDetailsManager() {
    var users = Set.of( "marcus", "josh", "tiffany")
          .stream()
          .map(name -> User.withDefaultPasswordEncoder().username(name).password("pw").roles("USER").build())
          .toList();|
    return new InMemoryUserDetailsManager(users);
  }
}

@Route("login" )
class LoginView extends VerticalLayout {
  LoginView() {
    var form = new LoginForm);
    form.setAction ("login" );
    add(form) ;
  }
}
```

## Spring Tips: Spring Data JDBC

- [Spring Tips: Spring Data JDBC](https://www.youtube.com/watch?app=desktop&v=srBYXhhLVV4)

GraalVM Native Support
Spring Data JDBC
PostgreSQL Driver
Docker Compose Support

compose.yml에서 ports를 "5432" → "5432:5432"로 변경

docker compose up
docker ps -a

# application.properties X

```
spring.datasource.username=user
spring.datasource.password=secret
spring.datasource.url=jdbc:postgresql://localhost/mydatabase
spring.sql.init.mode=always
```

src/main/resources/schema.sql

```SQL
-- internal
create table if not exists customer
(
  id serial primary key,
  name text
 );
 
create table if not exists customer_orders
(
  id serial primary key,
  customer bigint not null references customer (id),
  name varchar (255)
);

create table if not exists customer_profile (
  id serial primary key,
  customer bigint not null references customer (id) ,
  username text not null , 
  password text not null
  );
  
delete from customer_profile;
delete from customer_orders;
delete from customer:
```

XXXApplication.java

```Java
record Customer (@Id Integer id, String name){} // read only data

interface CustomerRepository extends ListCrudRepository <Customer , Integer> {}
```

```Java
@Bean
ApplicationRunner demo(CustomerRepository repository) {
  return args → {
    var customer = repository save(new Customer (id: null, name: "A"));
    repository.findAlL(.forEach(System.out::println); 
  }
}
```

를 XXXApplication에 메소드로 추가

```Java
record Profile (@Id Integer id, String username, String password) {}
```

ddl은 안 만들어 주고, dml은 만들어 주는 듯. @Query도 가능하고