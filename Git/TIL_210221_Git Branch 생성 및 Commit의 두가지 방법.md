# Git Branch 생성 및 commit의 두가지 방법



### 방법 1. 원격에서 branch 생성 후 local에 원격 branch 동기화



1. 원격에서 branch 생성

   <img src="https://lh3.googleusercontent.com/fZq-SSHrO5VI5iPBdJxhog3b1p7g1dtRGyqicG-uN2MyDRiDe8Uzr9wGAY70Slx6jPVLRxvjlBI5lwAVlNraqDoeoMbkBanDLUxJBvYtNJ_chCnQYFuL79c7nRPpcU_7nQVx0Ore" alt="img" style="zoom:67%;" />

2. git clone 받기

   <img src="https://lh4.googleusercontent.com/0p3wz2QsPBhPNy-TEsq6QodzI4b8S4HlTFtRjgOE6_x_74Fx9-9QJNiuG-kSxWWVR27P10gZzuUzdamOI3CamAmQhAZfq2SGQkRZhh3Vfg0-hd62V9AAWonAZWvDgTU_PkeJfOlF" alt="img" style="zoom:67%;" />

   

3. remote를 local에 동기화

   ```git
   $ git fetch
   ```

   <img src="https://lh3.googleusercontent.com/4sBx1IEB3DdUCWq0955SOtf_9p09eg3IJU6GF9NRsY4TNPQcJaJshUtkydBv-0fVkkTRX4-09PzHws0xykAc0UOPgZpe_iK4QEEVba9YGGm4QgvT75PvpNg65iu4tj45pvunjmKP" alt="img" style="zoom:67%;" />

   

   바라볼 수 있는 branch가 어떤건지 확인할 수 있는 명령어

   ```git
   $ git branch -a
   ```

   <img src="https://lh5.googleusercontent.com/FQKyW9YWZtVzBegORVIvsrOtKDnKqLeRVCbMnkUCvqpqC8hYQ35Z4aaJ3UrvkDoOsKgrgaQgAvtHJ0UIyKD4dslHEydxgmiqSM-HPZPvwd0sk9eQ0_q9puiLbHjRDSIOf-Kc_Pj-" alt="img" style="zoom: 80%;" />



4. local에 branch 만들기

   : remote와 같은 branch를 만들고 난 후에, 그 branch를 바라보도록 설정해주는 명령어

   ```git
   $ git checkout -t <원격저장소 branch 이름>
   ```

   <img src="https://lh6.googleusercontent.com/v-dVtRe_QZGbTI3sekr1bgrCbQTFcs7bm2axsGNx-KKhnLqGJVj0lPiocfUMk549dQLgrKDSN-hg5k-9_ueqlol7wFth1uKKgVPFSMLww5AvfnsrwVuFGKB_iMSPO7baG_8uZFsN" alt="img" style="zoom: 67%;" />

   ​			:arrow_forward: 원하는 이름으로 했더니 에러 발생. 원격 저장소의 branch와 같은 이름으로!!

<img src="https://lh6.googleusercontent.com/9vZ7UtPo5kPLCJ6Jm-Pm9-qOcYs2bDQfvcdFIeqTsGLXFGvJBVnZidvcYZisIgf6ihzMQlSvNOY0XkcL6GBBkG9yXXt708hNRWtSkNMFs2sY8Spwv7e9bhY3plwyud0KlBtjmViO" alt="img" style="zoom: 80%;" />





5. 소스 코드 변경 후 add & commit

   ```git
   $ git add .                  => 현재 디렉토리의 변경 사항 add
   $ git add *                  => 모든 변경 사항 add
   $ git add <파일명 / 폴더명>    => 원하는 파일 또는 폴더만 add
   ```

   ```git
   $ git commit	             => commit
   $ git commit -m <커밋 메세지>  => 메세지와 함께 commit
   ```

   ![img](https://lh6.googleusercontent.com/zug0PSUJbtlObOTCzMUtZNHxZFfLO27wtdiI0Y4yrKoApULrHRvVrHE8f2KpCEJ5GuGMw2o4s-sqJKunV6o0pRwbvzSyQCJQZHJsIH80_9WO1Hr1YSe79q-ojudC_YcFgUH2btaK)

![img](https://lh6.googleusercontent.com/rpSMXIBr_HVVJAl7WzjEz6sNKukznqxrNVgqpyssMrkXfzFqaXwjRAA9t0cSW-mgmIW4PThV832kYdxUoGCOp_0cjSdKjcee_gP1pvQppbXlYuPz7MHO9srqqLhld-rp0-Vt6H0K)



6. 원격 저장소에 push

   ```git
   $ git push
   ```

   <img src="https://lh3.googleusercontent.com/7dbuMlVB8CYhRIqLRCIySXyO7zOAfzmkpdGPAbfuNsjCkKY8bLBJlA-7s7UoOm0q17QR1mdsWaUthtNZqrHHaK_VQmGfgL-PQBqlSQLEJ2s3949ZTjaIUf_oWO5ZTj-mWfylZsjm" alt="img" style="zoom:80%;" />







### 방법 2. local에서 branch 생성 후 원격 저장소에 branch update



1. branch 생성

   ```git
   $ git branch <브랜치 이름>
   ```

   <img src="https://lh5.googleusercontent.com/CRat6YezELObgA8kBJe5f0V-XQEFoqIe4YORFkyzImGbFAvFvkX-ZrXY4vdJ6Xkyk7m9r0R8Y5P2PD9viw6jtB9j4O5HIjtSDcaWlbdROwOtBGF5S3HUBSzdKWYLDYKnyLKhKPnX" alt="img" style="zoom:80%;" />



2. branch 전환

   ```git
   $ git checkout <브랜치 이름>
   ```

   <img src="https://lh4.googleusercontent.com/STcEXNXmiHHAzH0gyQZXTFCb-LO9vJ0r9zvuwpHMlyxJo7lJ1b0zelw7tqV-nBL4RKrGqavbjglPE7vCjy0UHgdWHfrvCWtowxhPOYGKia7TwHujt651GbcCjzQHrGFWAMNMWdH1" alt="img" style="zoom:80%;" />



3. 원격 저장소에 branch 생성

   ```git
   $ git push origin <브랜치 이름>
   ```

   <img src="C:\Users\The Jeong\AppData\Roaming\Typora\typora-user-images\image-20210221215543792.png" alt="image-20210221215543792" style="zoom:80%;" />



4. local branch와 remote branch 연동

   ```git
   $ git branch --set-upstream-to <remote branch 이름>
   ```

   <img src="https://lh6.googleusercontent.com/CTVIw4OBE3uVmwxtRWMldfXsbMP89HOwf3h2g4TfutW869jcxXpAZ4ygWUQt4_8wCJwEL4R5iiQvnEc6YCqOW2NztHrQnbULgVJlI7w2SWb6XZS8zLjfSX_kEgo9MtSlz9cz-2C_" alt="img" style="zoom:80%;" />



5. 소스 코드 변경 후 add & commit & push

   

