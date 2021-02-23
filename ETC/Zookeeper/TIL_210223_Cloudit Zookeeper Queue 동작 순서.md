# Cloudit Zookeeper Queue 동작 순서



##### ClouditCommonConfig.java

-> QueueActionProducer에 대해 bean 등록

​    (ace_admd, dna_admd, sos_admd queue path를 갖는 객체 생성)

-> QueueActionProducer 실행 (start)

![img](https://lh6.googleusercontent.com/ndOQoiqdWYoVunCGL1B9ywP7lzv-olWY2THmPKySeLj-fGDUkwGwcLFbbIigqwEAOaA23PyYDru3viUv6YaIc5agsv-tzaP5fkJMXRNnwWGFgYp43s8DJb1ZBmSopgoB7H7e54TN)



##### QueueActionProducer.java

CuratorFramework curator = client.getCuratorFramework

-> curatorframework 받아오기

builder -> 건축자(만드는 사람)

QueueBuilder.builder(curator, null, serializer,queuPath)

-> curator : curator 

client-> this : (consumer) 메세지를 소비하는 functor - null일 경우 오직 producer로만 사용된다.

-> serializer : serializer to use for items

```
serializer란?

자바 시스템 내부에서 사용되는 object 또는 data를 외부의 자바 시스템에서도 사용할 수 있도록 byte 형태로 데이터를 변환하는 기술

JVM의 메모리에 상주되어 있는 객체 데이터를 바이트 형태로 변환하는 것
```

-> queuePath : queue 경로

queue = builder.lockPath(queuePath+”-lock”).buildQueue()

- 잠금 설정(lockPath)를 하지 않은 경우 큐 consumer에게 queue item이 전송되기 전에 큐 항목이 제거된다.

-  queue -> DistributedQueue<QueueAction>

  DistributedQueue : item이 queue에 들어가는 것을 보장한다. single consumer가 queue에서 item을 가지고 가는 방식은 FIFO이다. 가져가는게 중요하면 LeaderSelector가 single consumer를 지정한다

  https://curator.apache.org/curator-recipes/distributed-queue.html

![img](https://lh3.googleusercontent.com/fHSoPgP48eVroab97Mg9y_Vazx1T3CKh6f_1P_-cA2FFDgUO2caRBjgWdGyjdAKiY2v7PziWJj3jzTFWp3m9eK-TARxC4SzZRqyIOAaMuBboQz6Z-MsQc3QuvanwQubrtbvswvDV)



##### ServerServiceImpl.java

-> queueActionProduver.put()

![img](https://lh6.googleusercontent.com/OIMfKh1tshi7f2sil4IDSp2S7Sm8yNMN33QgNj_BrpwCEbh4XEzqODXc4Rsg_Lu0oCsKlFB8SPE-gLlba7lE6zpT-cWcv-YzGSw_KMKH2F1mZFPm5SGGV4g0ybb5mcRt5qt13-Eb)



##### QueueActionProducer.java

-> put()

![img](https://lh6.googleusercontent.com/mxlqhsb5n3P5ii7rcUkrBfGoNWCLbQAydxLbBuEBQBOu9sSOKl4Bm7EPMJ6mH4Soj_Kt6aRjxnNPso7V8JCihRLNPk_VF8judEhhaNIVilcHexkja0NAyNQ353aNTzDbt8rCClyb)

![img](https://lh5.googleusercontent.com/6gF3umWl04ujUtj7huXWiaxs1BSnHXNdxgfm3oSpEIJ8PYAHJCN5NGh7DOaUjcbWnFkLsPe3-PAsA4usua0uQqYrQ8yDnJW2SlhMfGh8LMYJAhNvJqWtiwUPDVIrljQGYt0_X3-Z)

-  client.create(Constants.ACTION_RESULT_PATH=”/”+item.getActionId(), “0”);

  curator.create().creatingParentsIfNeeded().withMode(CreateMode.PERSISTENT).forPath(path, payload.getBytes(StandardCharsets.UTF_8));

  - znode 생성(result)

    creatingParentsIfNeeded() : 만약 path(/cloudit/result)가 이미 있다는 확신이 없을 경우 해당 메소드 쓰면 부모 znode가 없을 경우에 제대로 생성 된다.

    withMode : zookeeper에서 znode 생성 방법 결정

    CreateMode.PERSISTENT : 클라이언트 연결 해제 시 자동으로 사라지지 않는다

![img](https://lh3.googleusercontent.com/-SSLwH5dFQaRLd1LGPIBOeII0nRABlonj95d90fgYRpoQHHDsomjsjIMnD7N1DyH5hla_EU3q1qbXdRMljKqhKFs4JxURCVqxH84fEMCCBYWsesIaIERlII65JO_tdoSwPFWwOOE)

- queue.put  ->  queue에 item을 넣어준다

![img](https://lh5.googleusercontent.com/Lzf0P5d2Dl_esTxNh5opK9R1hdy33GQtsI_4oWwMhGzf05KPH7okQxYaR954pC3ySataz4-fwBUtFVo9vjRmEZMoCikvwm0uqYdrAAFHJkJtzzHB3zPAEvQ3QUpsCnuBZknLkUgy)

- curator.getChildren().usingWatcher(watcher).for(path)
  - path에 해당하는 znode 반환
  - 특정 노드의 하위 노드를 가지고 온다(watch 기능도 붙어 있다)
  - path에 해당하는 znode에  결과가 들어오면 반환



##### ServerServiceImpl.java

-> @Override

​    public void processDelegate ~

![img](https://lh6.googleusercontent.com/OIMfKh1tshi7f2sil4IDSp2S7Sm8yNMN33QgNj_BrpwCEbh4XEzqODXc4Rsg_Lu0oCsKlFB8SPE-gLlba7lE6zpT-cWcv-YzGSw_KMKH2F1mZFPm5SGGV4g0ybb5mcRt5qt13-Eb)



##### WatcherDelegate.java

-> @Override

​    public void process ~

​    (Watch.class의 process override)

- convertAndSend(destination, payload)
  - Object를 serialized form으로 변환하여 message로 감싸서 destination에 보내준다



##### Watch.class

-> zookeeper client는 zookeeper server와 연결되어 event를 받는다.

**![img](https://lh4.googleusercontent.com/hjpgVBc0QqNRhBmaP1s1g6XKEeF6IBsdF8u8XYFZ-NS2ZddWo9Tchb4t2JQrrVEXbMhFwzCwmhfjv9-3KUPVuzWcHTzZrZkmCAk4AmG6ngjJ-GA9eE-_HKaOdfamRIJTaJBKp6tY)**

