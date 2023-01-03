# Java 재시도 논리 구현

### try-catch를 사용한 for 루프

```java
@Slf4j
@Service
@Transactional
@RequiredArgsConstructor
public class ExampleServiceImpl implements ExampleService {
		
		private static final int MAX_RETRIES = 3;
		...

		@Override
    public Map<String, Object> getExampleMetho(String parameter){

        Map<String, Object> result = new ConcurrentHashMap<>();
        for(int i=0; i < MAX_RETRIES ; i++) {
            try {
	              ...
                result.put("test", testName);

								// 성공 시 재시도 안함 (for loop 탈출)
                break;
            } catch (Exception e) {
								// error 출력
                log.error(e.toString());

								// 지정 횟수만큼 exception 발생 시 throw
                if (i == MAX_RETRIES) {
                    throw new ClientException("getExampleMetho " + e.toString());
                }
            }
        }

				// 성공 시 result 반환
        return result;
    }
}
```
