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
