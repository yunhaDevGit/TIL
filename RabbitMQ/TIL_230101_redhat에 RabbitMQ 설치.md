# Red Hat Enterprise Linux 8에 RabbitMQ 설치

## 1. iso 삽입

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e6860316-f08e-4e29-bbb6-e15a0ef0345a/Untitled.png)

## 2. repository 등록

`/etc/yum.repos.d` 디렉토리에 repo 등록

```bash
## CentOS-AppStream.repo

[AppStream]
name=CentOS-$releasever - AppStream
#mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=AppStream&infra=$infra
#baseurl=http://vault.centos.org/$contentdir/$releasever/AppStream/$basearch/os/
baseurl=https://repo.almalinux.org/almalinux/$releasever/AppStream/$basearch/os/
gpgcheck=1
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-centosofficial
```

```bash
# CentOS-Base.repo

[base]
name=CentOS-$releasever - Base
##mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
#baseurl=http://vault.centos.org/centos/$releasever/os/$basearch/
baseurl=https://repo.almalinux.org/almalinux/$releasever/BaseOS/$basearch/os/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

#released updates 
[updates]
name=CentOS-$releasever - Updates
##mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
#baseurl=http://vault.centos.org/centos/$releasever/updates/$basearch/
baseurl=https://repo.almalinux.org/almalinux/$releasever/BaseOS/$basearch/os/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

#additional packages that may be useful
[extras]
name=CentOS-$releasever - Extras
##mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
#baseurl=http://vault.centos.org/centos/$releasever/extras/$basearch/
baseurl=https://repo.almalinux.org/almalinux/$releasever/BaseOS/$basearch/os/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

#additional packages that extend functionality of existing packages
[centosplus]
name=CentOS-$releasever - Plus
##mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus&infra=$infra
#baseurl=http://vault.centos.org/centos/$releasever/centosplus/$basearch/
baseurl=https://repo.almalinux.org/almalinux/$releasever/BaseOS/$basearch/os/
gpgcheck=1
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
```

```bash
# erlang.repo

[erlang-solutions]
name=Centos $releasever - $basearch - Erlang Solutions
baseurl=https://packages.erlang-solutions.com/rpm/centos/$releasever/$basearch
gpgcheck=1
gpgkey=https://packages.erlang-solutions.com/rpm/erlang_solutions.asc
enabled=1
```

```bash
# media.repo

[InstallMedia-BaseOS]
name=Red Hat Enterprise Linux 8.4.0 - BaseOS
metadata_expire=-1
gpgcheck=1
enabled=1
baseurl=file:///run/media/root/RHEL-8-4-0-BaseOS-x86_64/BaseOS/
gpgkey = file:///etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release
[InstallMedia-AppStream]
name=Red Hat Enterprise Linux 8.4.0 - AppStream
metadata_expire=-1
gpgcheck=1
enabled=1
baseurl=file:///run/media/root/RHEL-8-4-0-BaseOS-x86_64/AppStream/
gpgkey = file:///etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release
```

위의 파일 생성 후 repo clean all

```bash
$ dnf clean all
```

## 3. epel-release 설치

```bash
$ dnf install epel-release -y
```

## 4. erlang 설치

**erlang 설치**

클러스터 구성된 노드끼리 통신을 위해 Erlang Cookie가 필요하므로 erlang을 설치합니다.

```bash
$ dnf install erlang -y
```

**erlang 설치 확인**

`erl` 명령어 입력 시 아래와 같이 나오면 잘 설치 된 겁니다.

```bash
$ erl

Erlang/OTP 24 [erts-12.1.5] [source] [64-bit] [smp:8:8] [ds:8:8:10] [async-threads:1] [jit]

Eshell V12.1.5  (abort with ^G)
1>
```

## 5. rabbitmq-server 설치 및 실행

****************PackageCloud Yum 레포지토리 추가****************

RabbitMQ 패키지가 포함된 Repository는 PackageCloud에서 사용할 수 있습니다.

```bash
$ curl -s https://packagecloud.io/install/repositories/rabbitmq/rabbitmq-server/script.rpm.sh | sudo bash
```

**RabbitMQ 설치**

```bash
$ sudo yum makecache -y --disablerepo='*' --enablerepo='rabbitmq_rabbitmq-server'
$ sudo yum -y install rabbitmq-server
```

**********************************RabbitMQ 실행**********************************

```bash
$ systemctl start rabbitmq-server
```

## 5. RabbitMQ 계정 생성 및 권한 설정

기본 계정 - id : guest / password : guest

```bash
// 사용자 계정 추가 (rabbitmqctl add_user <사용자> <비번>
$ rabbitmqctl add_user tabcloudit tabcloudit

// 사용자에게 태그 설정 (rabbitmqctl set_user_tags <사용자> <태그>
$ rabbitmqctl set_user_tags tabcloudit administrator

// 사용자 접속 퍼미션 설정 (rabbitmqctl set_permissions -p / <사용자> <접속퍼미션>
$ rabbitmqctl set_permissions -p / tabcloudit ".*" ".*" ".*"

// 사용자 목록 확인
$ rabbitmqctl list_users
// 사용자 목록 확인 결과
Listing users ...
user	tags
tabcloudit	[administrator]
guest	[administrator]

// RabbitMQ 재시작
$ systemctl restart rabbitmq-server
```

## 6. RabbitMQ Web Console에 접속

정상적으로 RabbitMQ가 설치된 경우 아래와 같이 RabbitMQ Management를 통해 노드 및 큐 정보를 확인할 수 있습니다.

`http://{server-ip}:15672`

해당 Web Console 접속은 위에서 설정한 계정/비밀번호 사용하면 됩니다.
