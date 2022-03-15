# Spring Boot Validation annotation 정리

@RequestBody 옆(오른쪽, 왼쪽 상관 없음)에 @Valid 어노테이션을 쓴 뒤에 해당 DTO의 필드 위에 유효성 검증을 원하는 대상에 부합하는 annotation을 써줍니다.

- @NotNull: 해당 값이 null이 아닌지 검증함
- @Null: null만 입력 가능
- @NotEmpty: 해당 값이 null이 아니고, 빈 스트링("") 아닌지 검증함(" "은 허용됨)
- @NotBlank: 해당 값이 null이 아니고, 공백(""과 " " 모두 포함)이 아닌지 검증함
- @AssertTrue: 해당 값이 true인지 검증함
- @AssertFalse: 해당 값이 false인지 검증함
- @Size(min=,max=) : 해당 값이 주어진 값 사이에 해당하는지 검증함(String, Collection, Map, Array에도 적용 가능)
- @Min: 해당 값이 주어진 값보다 작지 않은지 검증함
- @Max: 해당 값이 주어진 값보다 크지 않은지 검증함
- @Pattern: 해당 값이 주어진 패턴과 일치하는지 검증함
- @Range: 범위 안의 값인지 검증함
- @Positive: 해당 값이 양수인지 검증함
- @PositiveOrZero: 양수와 0만 가능
- @Negative: 음수만 가능
- @NegativeOrZero: 음수와 0만 가능
- @Email: 이메일 형식만 가능함
- @Digits(integer=, fraction = ): 대상 수가 지정된 정수와 소수 자리 수 보다 작은지 검증함
- @DecimalMax(value=): 지정된 값(실수) 이하인지 검증함
- @DecimalMin(value=): 지정된 값(실수) 이상인지 검증함

[https://reflectoring.io/bean-validation-with-spring-boot/](https://reflectoring.io/bean-validation-with-spring-boot/)
