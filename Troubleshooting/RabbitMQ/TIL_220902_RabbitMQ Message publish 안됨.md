# RabbitMQ 트러블슈팅

## 문제 상황

특정 액션을 했을 경우 mqtt 를 사용하여 작업 완료 여부에 대한 토스트 메세지를 띄우는 기능이 정상적으로 실행되지 않았다. 

확인해보니 queue에 message 조차 제대로 쌓이지 않는 상태였다.

아래의 `amq.topic`은 모든 topic을 담은 요청(?)을 다 받아오는 exchange로 해당 exchange 에 연결된 queue에 message가 쌓여야 하지만 요청에도 불구하고 모든 queue가 쌓이지 않았다. 

![image](https://user-images.githubusercontent.com/74949294/188093720-f4cd9c60-13b5-4a46-9a7a-980bc9eb75ca.png)

![image](https://user-images.githubusercontent.com/74949294/188093871-9299f93d-6e69-4099-bcc7-66dc2da0e7c7.png)

queue, exchange 설정이 잘못 된 듯 하여 아래와 같이 test exchange와 test queue를 생성하여 확인해보았다. 

## 테스트

`amq.topic`이라는 exchange는 모든 topic 요청을 다 받아준다. 

`amq.topic`에 들어오는 모든 요청을 새로 만든 `[test.exchange](http://test.exchange)` 에 binding 시킨 후 해당 요청 중 `test.*`에 해당하는 topic을 가진 요청일 경우 `test.queue`에 publish 한다. 

![image](https://user-images.githubusercontent.com/74949294/188094039-8e12fd35-1f71-4837-8dd5-e47f5053fd4a.png)

### Routing key 패턴과 일치하는 topic을 publish 했을 경우

아래와 같이 `test.test`라는 topic으로 publish를 했을 경우, 정상적으로 `test.queue`에 메세지가 쌓이는 것을 확인할 수 있었다. 

![image](https://user-images.githubusercontent.com/74949294/188094166-993107f6-9d94-4da3-8130-f286172fb7ba.png)

![image](https://user-images.githubusercontent.com/74949294/188094324-d1f876a6-5e03-4745-9d07-1f70fda3e6f9.png)

메세지도 정상적으로 들어 온 것을 확인할 수 있다.

![image](https://user-images.githubusercontent.com/74949294/188094370-35539a40-a1cf-4ed7-ad8c-77b39680f987.png)

### Routing key 패턴과 일치하지 않는 topic을 publish 했을 경우

반면  topic을 `admin.test`로 변경했을 경우 `topic.queue`에 아무런 변화가 생기지 않는 것을 확인할 수 있었다.

![image](https://user-images.githubusercontent.com/74949294/188094425-1c5920d6-592c-4cd0-a042-2c37c265e583.png)

![image](https://user-images.githubusercontent.com/74949294/188094475-37137b6e-bb91-4625-a51a-c3fc4ef598a8.png)

아무래도 Routing Key 값이 설정이 되어 있지 않아 발생한 문제인 것 같다.
