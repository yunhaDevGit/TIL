# Consumer Group

 Kafka Producer가 전송한 메시지는 Topic의 Partition에 나눠서 저장됩니다. \
 Partition에 저장된 이 메시지들은 Consumer들에 의해 읽혀집니다. 하나 이상의 Consumer들은 Consumer Group을 형성합니다. \
Consumer가 특정 파티션의 데이터 소비를 위한 개념이라면 Consumer Group은 Topic 전체 데이터 소비 관리를 위해 사용됩니다. \
('Consumer가 Partition을 소비한다', 'Consumer Group이 Topic을 소비한다' 라고 생각하면 됩니다)

```
왜 하나의 토픽에 여러 개의 파티션을 나눠서 메세지를 쓸까?
하나의 Topic에 하나의 파티션만 있으면 몇 천건의 메시지가 동시에 Kafka에 쓰여지면 하나의 파티션에 순차적으로 쓰여지게 됩니다. 그러면 Kafka가 처리하기 버거워지게 됩니다.
이때, 여러개의 파티션을 두어 메시지를 분산 저장을 하게 되면, 병렬 처리가 가능해지므로 처리량을 늘릴 수 있습니다.
하지만! 한 번 늘린 파티션은 다시 줄일 수 없기 때문에 잘 고려해서 늘려야 합니다.
늘어난 파티션은 메시지가 Round-robin 방식으로 쓰여져, 메시지가 순차 처리가 되지 않을 수 있기 때문에 사용에 맞게 파티션을 늘려야 합니다.
```

### Consumer Group과 Partition 수와의 관계
Consumer Group과 Partition의 관계에 대해 먼저 알아보겠습니다.\
바로는 본론부터 시작하면 이해하기 힘드니, 예를 들어보겠습니다.\
4개의 파티션으로 구성된 'Topic 1'가 있고, 이를 'Consumer Group A'라는 Consumer Group이 소비를 한다고 할 때, 이 Consumer Group에 속한 Consumer는 'Consumer1' 하나뿐이므로 'Consumer1'이 모든 파티션의 데이터를 소비합니다.\ 
![](https://lh3.googleusercontent.com/tN2s2zYDeLXjGkYoYbmnPS2wwe4gjdlBuKU2bo8Zq6iJoq2s5wkeqM5b96AQePehzSlAGei8DYOCHrZQM9Fl6zTVsgIFKq7I2JAkXjOEXyKhEFDLJA4MBPRbOE6ZcMgXdJz2dj5Pv-s)

하지만 이렇게 Consumer를 구성할 경우 Producer에서 생산하는 메시지를 하나의 Consumer가 전부 처리하기 때문에 처리가 버거울 수 있습니다. \
그렇게 되면 Kafka 파티션에 처리되지 않은 메시지가 쌓이고 이러한 지연 발생은 파이프라인의 실시간성을 떨어트리고, 결국에는 장애를 발생시킬 가능성이 높아집니다. 

이러한 문제를 Consumer 쪽의 Scale-out을 통해 해결 할 수 있습니다. \
![](https://lh4.googleusercontent.com/5wRJflQ-wAta3tKGJCnAhb-f6kq5dTeD5czjYvN5gBhX6-G_LLWanKJyiVZ51KjaRSjLs7LGH6ekpwSDG077hWITVm-2P3xI1M6YV2Yxyat-UElln-7PmQQqOIf49xLAjFYa7vDn1kw)

위와 같이 Consumer Group에 Consumer를 추가해줘서 'Topic 1'에 쌓이는 데이터 처리량을 늘릴 수 있습니다. \
'Consumer Group 1'에 'Consumer 2'가 추가되면 'Consumer 1'이 소유하던 파티션들의 소유권들을 나눠 갖게 됩니다. \
이를 **"리밸런싱(Reblanacing)"** 라고 하는데 나중에 자세히 알아보겠습니다. 

그럼 여기서 Consumer 숫자를 늘리면 계속해서 처리량이 늘어나는건가? 라는 생각을 하게 될 수 있습니다. \
결론부터 말하자면 아닙니다!!

4개의 Consumer로 구성된 Consumer Group이 4개의 파티션이 있는 'Topic 1'을 소비하게 된다면 아래와 같이 각 Consumer가 파티션을 하나씩 소유하여 데이터를 읽어가게 될 것입니다. \

![](https://lh5.googleusercontent.com/kV3f-k7tODG6g7Gw14oOjA_ZB5suXQ565e72KgOBHsWYtcSnS5GPILQY3hn_Cj8LubagvZao-Ow-xH8Ve9Tm-cagmUacPxf20s3TmBJU6HrTaaJ_14jsAAxhDNGc0EhYRmpI0RG2FrQ)

하지만 이럼에도 불구하고 지연이 발생한다면 Consumer를 추가해 처리량을 증가시키려고 할 것입니다.\
아래와 같이 Consumer Group에 Consumer를 추가하더라도 **하나의 파티션을 두 개 이상의 컨슈머가 소비할 수 없다**는 규칙(?)에 따라 각각의 Consumer가 하나의 파티션의 데이터만을 처리하고 Consumer 하나는 남게  됩니다.\

![](https://lh4.googleusercontent.com/SjbJU-fKKHHuZkC2leOjQnbhgyCKRq2bvchKZawGxArs34iUUjymNgOyg71tEY8XsILY3kj8GAuycTRC0lPK_fchVEapUn28zyXev7BMXI-KRX5sT18wGctcmpRsi5lNOOO9-dW-TgI)

Kafka가 이와 같은 제약을 만든 이유는 "파티션 내에서의 순서 보장"을 위해서 입니다. Kafka는 Topic 수준에서의 순서는 보장하지 않지만, 같은 파티션에서의 순서는 보장합니다. \
즉, 프로듀서가 1,2,3번 파티션에  순차적으로 메시지를 전송했더라도 각각의 파티션을 소비하는 Consumer가 다를 수 있기 때문에 소비되는 순서가 보장되지 않습니다. 하지만 같은 파티션에서는 메시지가 전송된 순서대로 소비됨이 보장됩니다. 

이러한 특성을 이용해 순서가 상관없는 메시지들은 서로 다른 파티션으로 전송하여 처리량과 고가용성을 높이고, 순서가 중요한 메시지는 키 값과 파티셔너를 이용해 한 파티션으로 몰아서 전송할 수 있습니다. \
또한, Consumer Group에서 하나의 Consumer만이 동작하던 중 장애가 발생하여 작업이 중단되게 되면, 제 역할을 하지 못하고 데이터를 가져오는 작업도 중단되게 됩니다. 하지만 여러 개의 Consumer로 구성된 Consumer Group에서 하나의 Consumer가 장애가 발생하면 해당 Consumer를 제외한 채, 나머지 Consumer로 계속해서 작업을 이어갈 수 있습니다. \
이렇듯 Consumer Group을 사용하게 되면 가용성과 안정성 측면에서 매우 유리해집니다. 





### 다중 Consumer Group

Kafka의 큰 장점 중 하나는, 하나의 Topic에 여러 Consumer Group이 동시에 접속해서 메시지를 가지고 올 수 있다는 것입니다. 일반적인 메시지 큐는 한 Consumer가 메세지를 읽으면 큐에서 삭제되어 다른 Consumer는 해당 메세지를 가져갈 수 없고 그 다음 메세지를 읽어가게 됩니다. \
하지만 Kafka에서는 Consumer Group 단위로 독립적으로 Topic에서 메세지를 소비할 수 있습니다.  즉, 동일한 메세지를 각각의 Consumer Group에서 소비할 수 있다는 것입니다. 이러한 기능 제공이 가능한 이유는, Kafka는 파일 시스템 방식을 채택했고, Consumer Group마다 각각의 오프셋을 별도로 관리하기 때문에 다른 Consumer Group에 영향 없이 메세지를 가지고 갈 수 있습니다. \
(추가!! Kafka는 메시지를 메모리에 저장하는 기존 메시징 시스템과는 달리, **파일 시스템**에 저장하므로 별도의 설정 없이도 **데이터의 영속성**이 보장됩니다.)

이러한 방식은 같은 Topic의 메세지를 **다수의 애플리케이션이 읽어야 하는 경우** 매우 유용하게 사용할 수 있습니다. \
예를 들어보겠습니다. \
만약 토픽에 a,b,c,d,e라는 메시지가 들어왔고 Consumer는 한 번에 하나의 메시지 밖에 처리를 못할 경우, Consumer는 총 5번의 Pull을 해야 합니다. 점차 들어오는 메시지가 많아지면 해당 Consumer의 처리 지연이 점점 커질 것입니다. 이때, 하나의 Consumer Group의 하나의 Consumer가 아닌 여러 Consumer 그룹에 포함시켜 들어오는 메시지를 적절하게 나눠 처리하면 처리량을 올릴 수 있습니다. 

![](https://lh5.googleusercontent.com/A-aud993cvrIPSgaFhX4olxvFYar11VRBSnEqqHBNWjseld2OT8ZsoFvQA7gI7Z7tAUZbnyK69tZHP6eisESO42K3aSTXh5asw5j-7axgr3ZRHJ6-mv4gzl7rz9Gr_wAP6KYHZIhpXY)





https://soft.plusblog.co.kr/29
https://medium.com/@umanking/%EC%B9%B4%ED%94%84%EC%B9%B4%EC%97%90-%EB%8C%80%ED%95%B4%EC%84%9C-%EC%9D%B4%EC%95%BC%EA%B8%B0-%ED%95%98%EA%B8%B0%EC%A0%84%EC%97%90-%EB%A8%BC%EC%A0%80-data%EC%97%90-%EB%8C%80%ED%95%B4%EC%84%9C-%EC%9D%B4%EC%95%BC%EA%B8%B0%ED%95%B4%EB%B3%B4%EC%9E%90-d2e3ca2f3c2


