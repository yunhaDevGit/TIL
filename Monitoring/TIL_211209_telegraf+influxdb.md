## Telegraf + InfluxDB 2.1 를 사용한 시스템 모니터링

1. docker image pull
[https://portal.influxdata.com/downloads/](https://portal.influxdata.com/downloads/)

```bash
$ docker pull influxdb:2.1.1
$ docker pull telegraf
```

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/ee17b284-c5e5-4a48-a212-06e14e515eb3/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211228%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211228T061225Z&X-Amz-Expires=86400&X-Amz-Signature=8dc1a68b57e80d2717cf366a43728a472daaefaff4054fda2d561210d95d3c70&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

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
    

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/3165f4fb-7d1b-4c97-bdf1-0c9aecfdf533/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211228%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211228T061247Z&X-Amz-Expires=86400&X-Amz-Signature=3ec3ed75a6c49e6288bd4640873cff5c00b9b40fe92c2fee5aa38cec85bafc1b&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

3. influxdb 컨테이너 실행 및 설정
    
    3.1 influxdb 컨테이너 실행
    

  ![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/a4e99d89-5ae6-4a0f-a7a6-4aa2698a725a/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211228%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211228T061302Z&X-Amz-Expires=86400&X-Amz-Signature=6100dc5932c52c53560af3307df596d6aa3ff2edb2b485ee3aa05f6cbf663ea0&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

  3.2 influxdb UI 접속

  `http://localhost:8086`

  ![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/46914bf0-8dd1-4ba2-9755-45970a5e3b14/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211228%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211228T061311Z&X-Amz-Expires=86400&X-Amz-Signature=541e1da450deccf1ce38bb721d88c35017085aae0853057c7319aba7ffb26fe7&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

  
  3.3 초기 설정 (user, password, organization, bucket)

  ![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/ec8bae6c-16d4-49f9-9e33-469ea92bc7bf/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211228%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211228T061321Z&X-Amz-Expires=86400&X-Amz-Signature=04391515f99a10bf142c29fabd0a4b05647c78b8f6a59fd7ed94a97541b76196&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

  
  3.4 Telegraf Configuration 생성

  Data > Telegraf > Create Configuration

  ![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/69025e9c-d1bb-4ab7-9c54-81a901c2120d/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211228%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211228T061334Z&X-Amz-Expires=86400&X-Amz-Signature=a19d8c25efef0f8cf3e8e5ea22682d9cf84d15f2a986a7ca737daa9c0d6e609c&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)
  
  ![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/2339b627-568f-4811-8332-fe22d1c0371d/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211228%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211228T061342Z&X-Amz-Expires=86400&X-Amz-Signature=a132669d4c7a11a8e4a0ed2029e0f5ac8d77ffe85b4ed58a7b9c457cce899741&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

  ![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/83b8d907-e08d-4c07-bacb-b987c0d4f45a/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211228%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211228T061353Z&X-Amz-Expires=86400&X-Amz-Signature=a8139ed8e5d6c800664633c2d75a70e305c25bcf94967d26011fec9412038e40&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

  
  3.5 API Token 받기

  ![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/2df6efb0-e293-4a0e-b501-74c569dc1622/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211228%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211228T061402Z&X-Amz-Expires=86400&X-Amz-Signature=0376daa21a999e80068af010f7c4ef6b40ee36bdfe21d2d6ac2a5e1415345d67&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)


4. host에 telegraf.conf 작성
    
    Data > Telegraf > 생성한 config 선택
    
    ![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/4957edfd-0456-41cc-911e-178b2ad181a5/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211228%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211228T061412Z&X-Amz-Expires=86400&X-Amz-Signature=9bb25e5e4b04799f7b401a31a3d750f8cef10414d25fa00f24a68f9470449721&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)
    
    ![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/a756b00f-780f-45ba-9355-3b1e2604e6bc/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211228%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211228T061424Z&X-Amz-Expires=86400&X-Amz-Signature=a3228b64418b0e2a8720d405cb69505a8dd80182ac523b65a48c99faab74f866&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)
    
    ![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/c1c05d3c-0ba0-4322-b026-6d87f9651316/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211228%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211228T061433Z&X-Amz-Expires=86400&X-Amz-Signature=8104f0ee3c2ecfedc69bc2576eb809c97bd1ec5d560ca7760736145dd4bad27e&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)
    
    /docker/telegraf/telegraf.conf 파일에 해당 내용 동일하게 기재
    

5. telegraf 컨테이너 실행

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/20741fb0-8c26-4e9f-9ccd-2cb7b98f59e2/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211214%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211214T015550Z&X-Amz-Expires=86400&X-Amz-Signature=9fbd1cbf711a23e9de1afce72e6cae4b299e81bbee4591d199fc70fee108fd6d&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)


6. log로 telegraf 정상 동작 여부 확인
    
    ![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/c89ca1c3-35c9-487c-9468-a3557bd9086f/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211214%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211214T015608Z&X-Amz-Expires=86400&X-Amz-Signature=13b297a7129af09bd4560da6669c466219511c328e7bb392d946bc99fe2141d6&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)
    

7. Influxdb 그래프 확인 (Boards > 생성한 bucket)
    
    ![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/e288808a-2b38-4e4d-b49f-27ab0630e975/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211214%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211214T015615Z&X-Amz-Expires=86400&X-Amz-Signature=f9fa3b40e7b2759197727a998b19a008e95197264a41341829b8ec92eba944b5&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)
    

## References

[1] Install InfluxDB & Telegraf [https://www.influxdata.com/blog/running-influxdb-2-0-and-telegraf-using-docker/](https://www.influxdata.com/blog/running-influxdb-2-0-and-telegraf-using-docker/)
