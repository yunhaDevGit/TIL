# Zookeeper 메소드

##### QueueActionProducer

public void create(String path, String payload) throws Exception {  	

   curator.create().creatingParentsIfNeeded().withMode(CreateMode.*PERSISTENT*).forPath(path,           payload.getBytes(StandardCharsets.*UTF_8*));

}

-> znode 생성(/result) (withMode : zookeeper에서 znode 생성 방법 결정 

​    PERSISTENT : 클라이언트 연결 해제시 자동으로 삭제되지 않는다)

-> 만약 path(/cloudit/result)가 이미 있다는 확신이 없을 경우 creatingParentNeeded() 하면 제대로 생성 된다. 

-> znode 생성할 때 또는 나중에 데이터를 넣어줄 수 있는데, 생성하면서 데이터 넣을 경우 설정할 데이터의 byte를 넘겨준다



##### curator.getChildren().usingWatcher(watcher).forPath(path);

-> path의 znode 반환

-> 특정 노드의 하위 노드를 가지고 온다(이 함수에는 watch기능도 붙어 있다)



##### builder.lockPath 

-> 잠금 설정이 되지 않은 경우 큐 consumer에게 queue item이 전송되기 전에 큐 항목이 제거된다



##### QueueActionConsumer
start() -> client.start() -> CuratorClient : curator.start() : start client -> curator.getZookeeperClient().blockUntilConnectedOrTimeOut() : zookeeper 연결이 성공될 때까지 block 되어있고, 관리되는 zookeeper client 반환



##### QueueBuilder.builder(curatorFramework, this, serializer, queuPath)

-> curatorFramework : curator client

-> this : (consumer) 메시지를 소비하는 functor

-> serializer : serializer to use for items

-> queuePath : queue 경로



##### LeaderShipHolder

LeaderSelector 

-> zookeeper 클러스터에 연결된 여러 개 중 리더를 선택하기 위한 추상화.

leaderSelector.autoRequeue -> leaderSelectorListener.takeLeadership이 반환될 때 인스턴스가 requeue되지 않는다. 이 메서드 호출 시 leaderSelector가 항상 자기 자신을 requeue에 넣는다