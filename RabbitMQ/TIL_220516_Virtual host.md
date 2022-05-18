# Virtual Host

Queue와 계정을 그룹핑 하는 개념입니다. 즉, Queue 접근에 대한 그룹핑입니다.

smsService와 pushService가 있다고 가정해보자.

이 둘을 서로 관계가 없기 때문에 서로의 Queue나 설정을 알 필요도, 관심도 없습니다. 

그러니 서비스 별로 서로 독립된 구역을 나누어 주는게 좋은데 그 단위로 Virtual Host를 사용할 수 있습니다.
