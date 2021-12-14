## Telegraf + InfluxDB 2.1 를 사용한 시스템 모니터링

1. docker image pull
[https://portal.influxdata.com/downloads/](https://portal.influxdata.com/downloads/)

```bash
$ docker pull influxdb:2.1.1
$ docker pull telegraf
```

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/ee17b284-c5e5-4a48-a212-06e14e515eb3/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211214%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211214T015302Z&X-Amz-Expires=86400&X-Amz-Signature=a4cb933749130c34346d2fc00feea4c0e60714e2cc40801b7fa85c1f8ced3883&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

2. docker network create (bridge 생성)

    
    > Docker 네트워크 종류
    bridge : 호스트 컴퓨터 내에서 여러 컨테이너들이 서로 소통할 수 있도록 해줍니다.
    host : 컨테이너를 호스트 컴퓨터와 동일한 네트워크에서 돌리기 위해 사용합니다.
    overlay : 여러 호스트에 분산 되어 돌아가는 컨테이너들 간에 네트워킹을 위해 사용됩니다.
    > 
    
    ```bash
    # docker network bridge 생성
    $ docker network create {이름}
    
    # --driver : 드라이버 지정
    $ docker network create --driver bridge {bridge 이름}
    
    # docker network 상세 정보
    $ docker network inspect {bridge 이름}
    ```
    

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/3165f4fb-7d1b-4c97-bdf1-0c9aecfdf533/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211214%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211214T015337Z&X-Amz-Expires=86400&X-Amz-Signature=b73844371625abc92f94cc46a778ced1784cc1f4f424754328a48aaee356a027&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

3. influxdb 컨테이너 실행 및 설정
    
    3.1 influxdb 컨테이너 실행
    

  ![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/a4e99d89-5ae6-4a0f-a7a6-4aa2698a725a/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211214%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211214T015345Z&X-Amz-Expires=86400&X-Amz-Signature=6851d871fcad7cecc4df605c22150a67d6c98532063c1dd147f65c2eced0fe4b&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

  3.2 influxdb UI 접속

  `http://localhost:8086`

  ![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/46914bf0-8dd1-4ba2-9755-45970a5e3b14/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211214%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211214T015355Z&X-Amz-Expires=86400&X-Amz-Signature=5b97467b2dab6b890b923d8ec87a33e6f587d6c57012b7ff228037b6cfbe715e&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

  
  3.3 초기 설정 (user, password, organization, bucket)

  ![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/ec8bae6c-16d4-49f9-9e33-469ea92bc7bf/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211214%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211214T015403Z&X-Amz-Expires=86400&X-Amz-Signature=7f1a2051809e03d683f6599961d8bbd3de71dda20ce559e1443b2335cb44df6a&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

  
  3.4 Telegraf Configuration 생성

  Data > Telegraf > Create Configuration

  ![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/69025e9c-d1bb-4ab7-9c54-81a901c2120d/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211214%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211214T015447Z&X-Amz-Expires=86400&X-Amz-Signature=bf6d99dec968c59690d56a1cb60d43040b3b29560c67ba36e3842e506fc0d591&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)
  
  ![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/2339b627-568f-4811-8332-fe22d1c0371d/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211214%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211214T015454Z&X-Amz-Expires=86400&X-Amz-Signature=bc23552c639e4558873dacb50c5c94c7de7df8f7e2a9345f8c6f9c08401d11ef&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

  ![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/83b8d907-e08d-4c07-bacb-b987c0d4f45a/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211214%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211214T015504Z&X-Amz-Expires=86400&X-Amz-Signature=c385a7aec4b6e70b23779b400316c4c524f44214e8d0a517e6f69a3610251917&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

  
  3.5 API Token 받기

  ![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/2df6efb0-e293-4a0e-b501-74c569dc1622/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211214%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211214T015708Z&X-Amz-Expires=86400&X-Amz-Signature=e02f013dcbeda097a1d02fd3431c9b6ecac78a45cd436b894e1876cc91f52c32&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)


4. host에 telegraf.conf 작성
    
    Data > Telegraf > 생성한 config 선택
    
    ![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/4957edfd-0456-41cc-911e-178b2ad181a5/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211214%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211214T015516Z&X-Amz-Expires=86400&X-Amz-Signature=dbc9d0c3fdc49066662d2fbb894bf7d6048852a07f7ddd2571ef634e19c3c834&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)
    
    ![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/a756b00f-780f-45ba-9355-3b1e2604e6bc/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211214%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211214T015530Z&X-Amz-Expires=86400&X-Amz-Signature=0f5fb5a6b1ecf8f06662db6068328b766ca36af35a50e340c3519ef542c01701&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)
    
    ![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/c1c05d3c-0ba0-4322-b026-6d87f9651316/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211214%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211214T015538Z&X-Amz-Expires=86400&X-Amz-Signature=3c9e9e2e4e5dbc2945c398afdfdc3323ccd4da395412a6c48f8d858a5c10d83b&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)
    
    /docker/telegraf/telegraf.conf 파일에 해당 내용 동일하게 기재
    

5. telegraf 컨테이너 실행

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/20741fb0-8c26-4e9f-9ccd-2cb7b98f59e2/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211214%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211214T015550Z&X-Amz-Expires=86400&X-Amz-Signature=9fbd1cbf711a23e9de1afce72e6cae4b299e81bbee4591d199fc70fee108fd6d&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)


6. log로 telegraf 정상 동작 여부 확인
    
    ![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/c89ca1c3-35c9-487c-9468-a3557bd9086f/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211214%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211214T015608Z&X-Amz-Expires=86400&X-Amz-Signature=13b297a7129af09bd4560da6669c466219511c328e7bb392d946bc99fe2141d6&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)
    

7. Influxdb 그래프 확인 (Boards > 생성한 bucket)
    
    ![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/e288808a-2b38-4e4d-b49f-27ab0630e975/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211214%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211214T015615Z&X-Amz-Expires=86400&X-Amz-Signature=f9fa3b40e7b2759197727a998b19a008e95197264a41341829b8ec92eba944b5&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)
    

## References

[1] Install InfluxDB & Telegraf [https://www.influxdata.com/blog/running-influxdb-2-0-and-telegraf-using-docker/](https://www.influxdata.com/blog/running-influxdb-2-0-and-telegraf-using-docker/)
