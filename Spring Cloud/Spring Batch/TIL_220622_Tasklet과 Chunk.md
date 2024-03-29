# Tasklet과 Chunk

**배치를 처리할 수 있는 방법은 크게 2가지로 나뉩니다.**

Tasklet으로는 단순하게 처리할 수 있다는 장점이 있으나, 대용량을 감당하기엔 부하가 많이 발생합니다. 따라서 Chunk를 통해 쪼개서 넣을 필요가 있는데, Tasklet으로도 쪼개서 넣을 수 있지만, 가독성에서 떨어지기 때문에 권장하지 않습니다. 

각각의 방식에 대해 좀 더 살펴보겠습니다. 

- **Tasklet을 사용한 Task 기반 처리**
    - 배치 처리 과정이 비교적 쉬운 경우 쉽게 사용
    - 대량 처리를 하는 경우 더 복잡
    - 하나의 큰 덩어리를 여러 덩어리로 나누어 처리하기 부적합
    
- **Chunk를 사용한 chunk(덩어리) 기반 처리**
    - ItemReader, ItemProcessor, ItemWriter의 관계 이해 필요
    - 대량 처리를 하는 경우 Tasklet 보다 비교적 쉽게 구현
    

> **Example : 10,000개의 데이터 처리**
→ Taklet : 10,000개를 한 번에 처리하거나, 수동으로 1,000개씩 분할
→ Chunk : 10,000개의 데이터 중 1,000개씩 10개의 덩어리로 수행
> 

---
