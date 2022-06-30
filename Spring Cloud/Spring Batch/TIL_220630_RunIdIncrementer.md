# RunIdIncrementer

Spring Batch의 경우 일반적으로 **동일 Job Parameter로 실행 시** 어떻게 처리할 것 인지에 대한 여러가지 옵션을 제공합니다. 

- 해당 파라미터로 최근 실패한 이력이 있다면 이어서 실행
- 해당 파라미터로 최근 실패 혹은 성공한 이력이 있다면 실행 안함
- 해당 파라미터로 최근 실행한 이력이 있어도 무시하고 다시 실행
- 등등

대부분의 경우 동일 Job Parameter 실행을 막곤 하는데(중복 데이터가 쌓일 수 있으므로) 일부 배치에서는 **동일 Job Parameter로 계속 실행될 수 있도록 하길 원합니다.** (특정 데이터에 대한 검증 로직 또는 데이터 갱신 배치 등)

즉, Job을 여러 번 수행 시키기 위해 `incrementer()`를 사용합니다. Job의 구현체를 구분하는 방법은 `Job` + `Job Parameter`이기 때문에 `incrementer()`를 사용하여 Job Parameter를 변경합니다.

## RunIdIncrementer

Spring Batch에서는 **동일 파라미터인데 다시 실행하고 싶을 때** 사용하라는 의미로 `RunIdIncrementer`를 제공합니다. 

아래와 같이 Job의 `Incrementer` 옵션에 추가하여 사용할 수 있습니다.

```java
public Job job() {
	return jobBuilderFactory.get(JOB_NAME)
						.start(step())
						.incrementer(new RunIdIncrementer())
						.build();
)
```

이렇게 사용할 경우 Spring Batch에서 전달 받은 Job Parameter 외에 `[run.id](http://run.id)`라는 **임의의 파라미터를 추가**로 사용해 매번 `run.id` 값을 변경해줍니다.

매 실행마다 `run.id`가 변경되어 재실행 될 수 있는 것입니다.
