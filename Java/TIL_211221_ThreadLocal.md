ThreadLocal은 자바 Class로 오직 한 스레드에 의해서 읽고 쓰여질 수 있는 변수이다. 

ThreadLocal은 한 thread 안에서 파라미터 또는 리턴 값으로 정보를 제공하는 것이 아닌 다른 방법으로  thread 안에서의 값을 공유하는 방법을 제공합니다. 

두 쓰레드가 같은 코드를 실행하고 이 코드가 하나의 ThreadLocal 변수를 참조 하더라도 서로의 ThreadLocal 변수를 볼 수 없습니다. 

즉, ThreadLocal 변수를 선언하면 멀티 쓰레드 환경에서 각 쓰레드마다 독립적인 변수를 가지고 접근할 수 있습니다. 

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/513361de-a740-4b4a-b0e1-52f60dce61c4/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211221%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211221T090557Z&X-Amz-Expires=86400&X-Amz-Signature=edc4b46fed34eb2808195bd2a02ff5a764f5356d3851a4e391adceb6b8316099&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

위 그림에서는 동일한 코드를 실행하는 데, 쓰레드1에서 실행할 경우 관련 값이 쓰레드1에 저장되고, 쓰레드 2에서 실행할 경우 쓰레드 2에 저장된다는 것입니다.
