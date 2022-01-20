# @NotNull, @NotEmpty, @NotBlank 의 차이점

## @NotNull

`@NotNull` 은 Null만 허용하지 않습니다.

따라서 `""` 이나 `" "` 는 허용하게 됩니다. 

그렇기 때문에 `""` 이나 `" "` 을 허용하지 않을 경우 사용하면 안됩니다. 

Null이 들어오게 되면, `로직에 예상치 못한 오류` 가 발생하거나 `문제가 생길 경우` 사용해야 합니다. 

## @NotEmpty

`@NotEmpty` 는 `null` 과 `""` 둘 다 허용하지 않습니다. 

`@NotNull` 에서 `""` validation이 추가된 것입니다.

즉, `@NotEmpty` 는 `null` 과 `""` 은 막히되, `" "` 은 허용이 됩니다.

## @NotBlank

`@NotBlank` 는 `null` 과 `""` 과 `" "` 모두 허용하지 않습니다.

`@NotEmpty` 에서 `" "` validation 이 추가된 것입니다.

즉, 세개 중 가장 validation 강도가 높은 것으로,`@NotBlank` 는 `null` 과 `""` 과 `" "` 모두 허용하지 않습니다.
