# Terms

## DTO

- [Data Transfer Object (DTO) in Spring Boot | by Anand Rathore | Nov, 2023 | Towards Dev](https://towardsdev.com/data-transfer-object-dto-in-spring-boot-c00678cc5946)
- DTO
    - design pattern
    - 서로 다른 계층 간에 데이터를 전송하거나 캡슐화하기 위해 사용
    - 비즈니스 로직을 포함하지 않음
    - 예. FE/BE간, MSA간 데이터 전송
- DTO의 잇점
    - data isolation
    - reduced overhead
    - versioning and compatibility
    - improved security
    - enhanced testing
- mapping
    - manual
    - modelMapper
    - lombok
- formatting different types of values in DTOs
    - @JsonFormat
    - custom method
- Additional Considerations and Best Practices
    - Validation in DTOs

```Java
  @PostMapping("/create")
  public ResponseEntity createUser(@Valid @RequestBody UserDTO userDTO) {
  // Your validation logic is automatically triggered
```

## Event and Commands

- [이벤트와 명령](https://gyuwon.github.io/blog/2023/12/31/events-commands.html)
- 이벤트: 시스템 상에서 발생한 사실을 묘사
- 명령: 생산자가 소비자에게 바라는 바를 기술

## Module

- Application is organized into modules. Each module solves a distinct part of the business problem.
- Modules are loosely coupled. No cyclic dependencies
- Controller, Service, Repository etc of a thing should be in the same package (if package is used for module boundary)
  as they always need to change together when building a new feature aka package-by-feature.
- Packaging by layers (packages like controllers, models, repositories etc) are by definition low in cohesion and would
  not be modular
- from [“Structured Design”](https://www.amazon.com/Structured-Design-Fundamentals-Discipline-Computer/dp/0138544719)