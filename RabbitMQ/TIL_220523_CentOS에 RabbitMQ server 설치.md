# RabbitMQ 설치하기

```powershell
$ sudo yum -y install epel-release
```

```powershell
$ sudo yum install erlang
```

```powershell
$ erl
```

```powershell
Erlang/OTP 23 [erts-11.2] [source] [64-bit] [smp:1:1] [ds:1:1:10] [async-threads:1] [hipe]

Eshell V11.2  (abort with ^G)
1>
```

```powershell
$ sudo yum install rabbitmq-server
```

```powershell
$ systemctl start rabbitmq-server
```

```powershell
$ sudo rabbitmq-plugins enable rabbitmq_management
```

```powershell
$ sudo rabbitmqctl add_user admin
```

```powershell
$ sudo rabbitmqctl set_user_tags admin administrator
```

```powershell
$ sudo rabbitmqctl list_users
```

```powershell
$ sudo rabbitmqctl set_permissions -p / admin ".*" ".*" ".*"
```

```powershell
$ systemctl restart rabbitmq-server
```

---

### Cluster 구성
