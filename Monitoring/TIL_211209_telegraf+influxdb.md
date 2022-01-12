## Telegraf + InfluxDB 2.1 를 사용한 시스템 모니터링

1. docker image pull
[https://portal.influxdata.com/downloads/](https://portal.influxdata.com/downloads/)

```bash
$ docker pull influxdb:2.1.1
$ docker pull telegraf
```

![](https://lh6.googleusercontent.com/M54VppweyhcRCH3rKcCOMYr7uKFbXjAiuXdGZAzZIQ3F7XOQWHTl9XvAhMZKI9sGNtj1F8MR642Qs00kuqA5NYNCV9aPAbcR6VTZfKQqsCjTOglFFsajJ7YV-2VfxKQ8O-x7KM0j)

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
    

![](https://lh6.googleusercontent.com/t0H3Z2KxE0hsTetvoVvw-M0nL9APpHsm-nZ0DF-2yx56Mu8R2p4VcKrKj30eEhaKGarZF9-aU6XVj23hhqwt5Ba-Fk8sKwSCswPQEruq94RB0EXwTBtkEBrQ9PhEyFjVasWYcHQ3)

3. influxdb 컨테이너 실행 및 설정
    
    3.1 influxdb 컨테이너 실행
    

  ![](https://lh6.googleusercontent.com/-01OiTSnh2zXq9Vt97BOyFQL5rTWfgrDF4MnyfFcWMU0du5X6l9BC_TfahN30SFVMfP6V_LZvBBqIsCJg0pKG2jkCHRusKXUqmt7QtUdNoAHEoUGvAtWrHnNDRc7NMQSqRVAvlnj)

  3.2 influxdb UI 접속

  `http://localhost:8086`

  ![](https://lh4.googleusercontent.com/q9YmR1GjqUnJhkv1LY3_HsPcwzPlaYCmlGlzm6fGoWpcJt5vrZMI70hZ-rzXLaSoEidOj0k-uPI2BeFMKb76FNtF9LY8N_8kojJCZ-elP14RWlc5Rf8bR7RB7fdopbEYOuhBQJmu)

  
  3.3 초기 설정 (user, password, organization, bucket)

  ![](https://lh6.googleusercontent.com/HiCOgJY71tg7B2Xgvzufl8sCg978zcYfZiXviVeyTl7FV4thZpA9Ks5_WPFLmTxC60owqVgL6hdyJ90bZ8EwR5DL4Qge5eBAOsTWG4-uDSTQYGFPOj75jESPg7lO_8IWcAD6Xxx7)


  
  3.4 Telegraf Configuration 생성

  Data > Telegraf > Create Configuration

  ![](https://lh4.googleusercontent.com/rFNaXCPnEk2MaEjaQyiQdHniZ7RHxo0ZbrAA2ZSWeIqSTOO0Tpb1ofYttrV3JvVrWBJhe0fWUoQgjhXxefha9GunmNX9zNU_t8mF26h7Gpn0-eEzTG6rrHjyvDFGWLWu5p06dVch)

  ![](https://lh3.googleusercontent.com/5W2GUeJu1a9D-bIGBJyMcH30FMW_vUE317za7DincWoDUnCrwreuiXeg6EUHnZOFntdUSeNkk_UgBlQwsMqpDqPDS9xHYQC9gl9TdrSjOPNnReKjF9JpcNDnCKwsa62Wdh1lgKuc)

  ![](https://lh4.googleusercontent.com/_z9RR5tcwO66VfejGaotbwCjW5eDNogGkLoEnOEbdoUNkLCRnqUbHTpymY7I2FU_SZM4KisMDUoc6nAo53xmglbXD97AEfjU30ur7nPGZ3GYcxBCQsdq8gB1kyPL_V3-unbE0H1d)

  
  3.5 API Token 받기

  ![](https://lh6.googleusercontent.com/8gXALBS81uUEDUHIuADeBHCFWyZuro3GEK1X-HuSBUCpPI3CVWGmoCZsp3MSBFDUULG261DxqMtTEKWQGnY2VE1Dg-QkX_vkwHo15CdjfxHaRPbYzN0Akh5e0LJZKNhUGcS-EOsj)


4. host에 telegraf.conf 작성
    
    Data > Telegraf > 생성한 config 선택
    
    ![](https://lh4.googleusercontent.com/qRw68YbEQ_SsHsZvZ1L0ubaa3ks5pob6ZBXzrBNrNCQrSMBTFEJTXYsaP-aop-1eGAq30pLGpzumGdVk335bHSP4FnQDsG-Gi0CSu58HrP0AXcbQMUCXPczltCA2BA6FbkA_pMsU)
    
    ![](https://lh6.googleusercontent.com/kREXgrFqP9i-UOLE8YINDSaeMJMjIrKgLyZLuVfUIrFv70pBRKWvxhU2c5GOdIku7AJKyD7fHJU1EnAs6rc1fbXe8ZEVzX_TWi6OwDEnLoWSwRdHVObvygkCcc8fqzQO4HXzZvcl)
    
    ![](https://lh5.googleusercontent.com/dxFoR6VH6EfP12T7ngL26UFHv_TuT6b51PMedJMQ8NSsYSf9Ok3iiMC7JX8xUz14j1jR1Az4vza8D7s_9J_qCp1lFWAJYhHE1qQIwzLAavA_uQCSP_kVwatzFTvNLdR8zrYda-1V)
    
    /docker/telegraf/telegraf.conf 파일에 해당 내용 동일하게 기재
    

5. telegraf 컨테이너 실행

![](https://lh4.googleusercontent.com/GaPYIm-693QVcZeJXobmnwpdrjUTyUMt5Dnt17dWdoDnAKJmk9-ti-OvwGwaGFiy189CsAy4dpBdE9yvp1ntqMhIHxNYHSryiPbaw8hRNQ2Vx3TfJUgJNsjq-1bCYV-TIdd5rWzy)


6. log로 telegraf 정상 동작 여부 확인
    
    ![](https://lh5.googleusercontent.com/WZnJ0nREn2hRLLrQGnyxB1uhc94mosS0tnHMjooffkEyxFlbAmKxc2P2MnPiJorZXv1nM0IL_Zb1l80sxPD4bMHdGb5qg-nWJ4Av_4alx2ZKgtYIT2GpnATIuzgc0dAqXD6cVaqX)
    

7. Influxdb 그래프 확인 (Boards > 생성한 bucket)
    
   ![](https://lh5.googleusercontent.com/550NrnpItcLGr4vWJIkqc2nBVoCgjiFnM0tgBDBP6r2xJyALJgh1j-8NnniFr2naUtUikOgr4sM3cj9v0pownb2_5bOFTWcNy83lUNa-saRYWLOIYfG1U9hqT0I9x-XFt9mceB_U)
    

## References

[1] Install InfluxDB & Telegraf [https://www.influxdata.com/blog/running-influxdb-2-0-and-telegraf-using-docker/](https://www.influxdata.com/blog/running-influxdb-2-0-and-telegraf-using-docker/)
