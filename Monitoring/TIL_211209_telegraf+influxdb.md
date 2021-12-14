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

  ![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/69025e9c-d1bb-4ab7-9c54-81a901c2120d/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211209%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211209T025422Z&X-Amz-Expires=86400&X-Amz-Signature=93950f24c9be99b9bc9bc0b3602ed9bd30cbb2226e7bf109a813b46b62674a84&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)
  
  ![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/2339b627-568f-4811-8332-fe22d1c0371d/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211209%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211209T025432Z&X-Amz-Expires=86400&X-Amz-Signature=6926e1267bb0d0195667ac66896d40af794447a6d189d4ebb187fe4becb620ed&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

  ![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/83b8d907-e08d-4c07-bacb-b987c0d4f45a/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211209%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211209T025444Z&X-Amz-Expires=86400&X-Amz-Signature=711ceac707ca4012cab83add66482707f79c93d2a1ebf72786a5d6eb29ca043f&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

  
  3.5 API Token 받기

  ![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/2df6efb0-e293-4a0e-b501-74c569dc1622/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211209%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211209T025453Z&X-Amz-Expires=86400&X-Amz-Signature=9c3a3a8ec14b7788fd9dc4ef9828709c29b68ca83b5664f5b09045e5fabf4028&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)


4. host에 telegraf.conf 작성
    
    Data > Telegraf > 생성한 config 선택
    
    ![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/4957edfd-0456-41cc-911e-178b2ad181a5/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211209%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211209T025506Z&X-Amz-Expires=86400&X-Amz-Signature=a4eda1c0005a80d8d50c4111674a87f0c13fbbe593a4101b51e5db691de3323c&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)
    
    ![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/a756b00f-780f-45ba-9355-3b1e2604e6bc/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211209%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211209T025518Z&X-Amz-Expires=86400&X-Amz-Signature=385892cac4b94528c0aeb4769368ce1d2ad9febcf67f536b666f3b91d6125595&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)
    
    ![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/c1c05d3c-0ba0-4322-b026-6d87f9651316/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211209%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211209T025527Z&X-Amz-Expires=86400&X-Amz-Signature=f0fa2a88bedeff565806b212ae35c29a693f5f07e9f2b6139adfbe36ed567b70&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)
    
    /docker/telegraf/telegraf.conf 파일에 해당 내용 동일하게 기재
    

5. telegraf 컨테이너 실행

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/20741fb0-8c26-4e9f-9ccd-2cb7b98f59e2/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211209%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211209T025544Z&X-Amz-Expires=86400&X-Amz-Signature=ce7bb92f07b518ca4f6b9a05d31dc47cec4e848c83e75019eb6609c18e57cb8f&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)


6. log로 telegraf 정상 동작 여부 확인
    
    ![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/c89ca1c3-35c9-487c-9468-a3557bd9086f/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211209%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211209T025554Z&X-Amz-Expires=86400&X-Amz-Signature=5e84372e7e6239dd08f4477846719677c5dfd01f01eb964b79ad87c6436a65ee&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)
    

7. Influxdb 그래프 확인 (Boards > 생성한 bucket)
    
    ![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/e288808a-2b38-4e4d-b49f-27ab0630e975/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20211209%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20211209T025621Z&X-Amz-Expires=86400&X-Amz-Signature=f557cf2c94646faa01a30029afafb9ae6ff64573dfc12cb66e7fd184999fceb2&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)
    

## References

[1] Install InfluxDB & Telegraf [https://www.influxdata.com/blog/running-influxdb-2-0-and-telegraf-using-docker/](https://www.influxdata.com/blog/running-influxdb-2-0-and-telegraf-using-docker/)