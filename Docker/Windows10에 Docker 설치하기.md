# Windows10에 Docker 설치하기

<img src="https://blog.kakaocdn.net/dn/bHIQ1y/btqD1PtC7MS/K6c0tDovIBLqECSadQDAc0/img.jpg" alt="img" style="zoom: 25%;" />





1. **도커 설치 전 환경 설정하기**

   - 도커를 사용하기 위해 가상화 기술인 Hyper-V를 활성화시킨다.

   - 작업 관리자에서 가상화를 **사용함**으로 설정했는지 확인한다.

     (안되어 있다면 BIOS에서 사용함으로 설정해준다)

     <img src="https://lh3.googleusercontent.com/S81yiy2uL1iXvzZyYtPrFQaCUz0T4yzkBp35E3y5vZAShzRTKG0DjpCF9r7Vay-mTBjuSlSkNGOeBQKXWhzNkpU8hk1HojCuc8-kK_mAyTw8i3UHaaNspYJmAwLqMaKrMRoWcWyP" alt="img" style="zoom: 67%;" />

     

   - 제어판 -> 모든 제어판 항목 -> 프로그램 및 기능 -> Windows 기능 켜기/끄기 -> Hyper-V 선택

     <img src="https://lh6.googleusercontent.com/AlSdqTLdQR-4T8DA7RmbefFc5E0BDlwV0Z4nlzxEw1f3fvr6fxIdCoiJ7Bin_UQWZTqtinhM9mao7KLlWsj2Mi6p1fYk1l5Isv1oOmXdjyp815tONESBG8ga9SFt5M-Lf-EMxBMm" alt="img" style="zoom:80%;" />

     

   - 재부팅



2. **도커 설치파일 다운로드 및 설치**

   - 아래의 사이트 접속하여 Get Docker선택하여 다운로드

     https://hub.docker.com/editions/community/docker-ce-desktop-windows/

     <img src="https://lh3.googleusercontent.com/hZv0cmm3dl-qhUCym-Ph_2yCNYNIYho2lf05YhVJ7gVi9r2vXCVk5pnAbwlgklOpCjJj09gctYvg2kxVpXWb7mqV-hKO-hAU8xhMvr_GoBLIr9bkXzw_7lTK_3kKHuMJZyUB5BYD" alt="img" style="zoom: 67%;" />

     

   - 설치 파일 실행

     <img src="https://lh6.googleusercontent.com/gwsBBB32E-LNBsRjCNDpvrswn1OkScQOpK_Cie9UvWiOczFM4YwskSyQHLthOlAg_nNfbkm42D_NTb6UlJjlhMN4wmgD3Z1qjBKbn1WDJLgy63HLSBGhi4dNYu0qU-cUYCMe5ONl" alt="img" style="zoom: 67%;" />

     <img src="https://lh6.googleusercontent.com/x_Lt4fTAOaBCJTj55yfCae2D5aFlulFfQbhjkme_ndIZnXgA2tNxWj-d-Umuzw8tmJzgALLWZQEkWInEdHI3k3DIBySXTcRdLoj7LjmCg7v463w0ufM8yYS0h0uDRB6yU1b3IF_L" alt="img" style="zoom:67%;" />

     <img src="https://lh4.googleusercontent.com/e5IkbHy3Em5Bf7GfkaFeF2A5fILfsyr5fpXGoS5tXKgixL0oHlGYpBQXCxj-tGHUPrx7NqvS-O6ANKEJWnQ51Uo3IIijCKD1RtfalD9w0YuYFt0ZWKfXPPck9u8GsqzKkVo_aUGQ" alt="img" style="zoom:67%;" />

     

   - Close and restart 



3. **재부팅 되어 docker를 실행 했을 때 아래와 같은 에러(?) 메시지가 뜨면 다음과 같은 과정을 진행해준다**

   <img src="https://lh5.googleusercontent.com/y58Pxdsl3bO8dA1f1En1d2G-vj5ysMz7UAmUVpstur6B3sFh3Fmt4y68ezqxP2F6eWNgCpbi1WcV_3BRw-dQIbwDB0LjZD4mb6EHGmMV0fAjxxksz3CIeYByhULnEydIjFN5ykAr" alt="img" style="zoom:80%;" />

   

   - 다음과 같은 메시지가 뜨면 리눅스 커널 업데이트를 해줘야 한다.

     - WSL은 Windows Subsystem for Linux로 리눅스용 윈도우 하위시스템이다. 이는 윈도우에서 리눅스를 구동할 수 있도록 도와주는 기능이다. 

     - 명령 프롬프트를 **관리자 권한**으로 열고 아래의 명령어를 입력한다.

       - 리눅스 서브 시스템 활성 명령어 입력

         ```
         dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart 
         ```

         ![img](https://lh5.googleusercontent.com/87Blfg22Q-q9SfS5ZyHV2GAPsGhVmNSMY3lmlF9a77aw-2P0HbL0dbPfLReMiETcZJM-qPUrUnuovf4K9rh4fiVsnve2QltY-YMPLF2nFn6cz5tjSpKYz12ujsIuSi1O1Y8fk4cP)

         

       - 가상 머신 플랫폼 기능 활성화 명령어 입력

         ```
         dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart 
         ```

         ![img](https://lh3.googleusercontent.com/y1wd-U3DRo7v50lAV2NXcXkMfkOB764-t5E8DWa7F8oPzqanMPbPWOf2cYBoKt5Del07DvicuVldqdAFroXoBvPaXRr89WYVRjS1dbtT60Y2ZReJ5YZn8B5-yiP0rXIpBxDUvU9m)

       

       - 아래 링크에서 x64 머신용 최신 WSL2 Linux 커널 업데이트 패키지를 다운로드 받은 후 설치한다

         Download the latest package : [WSL2 Linux kernel update package for x64 machines](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi)

       ![img](https://blogfiles.pstatic.net/MjAyMTAxMTVfNTcg/MDAxNjEwNjkzNjQxOTEz.eNxO_hz-1TN4wp2ljjo3l7klq9NjQrrPPBiczv_x_20g.ttK6t7jdAtrouM0mrY8ztKkh8jGwpt_MPTR9kiekxygg.PNG.brainkorea/capture_20210115_155332.png?type=w2)

       ![img](https://blogfiles.pstatic.net/MjAyMTAxMTVfMTIz/MDAxNjEwNjkzNjQyMjMz.6gEAppuLE3y-swOQ8NOoLqh6e2p9LEcCAcg1Muhm3bYg.1VxqpmG_qCRhzpRsQG9F2vP_FAYjH4_6XWyOQZA5OB4g.PNG.brainkorea/capture_20210115_155342.png?type=w2)

       

       - 아래의 명령어를 쳤을 때 다음과 같이 잘 나오면 정상적으로 설치된거다

         ```
         wsl --set-default-version 2
         ```

         ![img](https://lh6.googleusercontent.com/UJbZl-F2bxH7hbsTa4ie4IoiTpt-1ZGXy1C6fs_LmCCuVxSnRChqkw1-jLxi11QZIf8MO09qzw1jU1aFQmyxnB9OOEunZaQg7GDhfJK72DVXJOzLubx2G2znWL4DExJDYfDbeFPB)

         



4. **실행**

   <img src="https://lh6.googleusercontent.com/p54F_1nQbb9RB605NCpgCvynWwMbjDznTKEOVSZh2VOUCgkQZJmrrtOl_s8T7KMJXpbVtaUSQr5s4FGJR2FHCnxklv-1y-dFoGoJKiDPgr-y1MX0RshZqeOnAKAWsyhvyhXSeWxw" alt="img" style="zoom: 50%;" />





참조 : https://goddaehee.tistory.com/251

​	       https://blog.nachal.com/1691