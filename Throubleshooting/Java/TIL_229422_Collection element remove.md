# Collection element remove (UnsupportedOperationException)

`Arrays.asList()`를 사용하여 리스트를 생성 한 후에 특정 값에 해당하는 리스트를 삭제하도록 하였다. 

```java
@Override
  public List<String> getAvailableIpBySubnet(Subnet subnet) {

    SubnetUtils subnetUtils = new SubnetUtils(subnet.getAddress() + "/" + subnet.getPrefix());
    List<String> addresses = Arrays.asList(subnetUtils.getInfo().getAllAddresses());
    List<SubnetPort> subnetPortList = subnetPortRepository.findBySubnet(subnet);
    for (SubnetPort subnetPort : subnetPortList) {
      addresses.remove(subnetPort.getIp());
    }
    addresses.remove(subnet.getGateway());

    return addresses;
  }
```

실행하였더니 remove를 지원하지 않는 연산이라며 exception이 발생...

```java
java.lang.UnsupportedOperationException: remove
	at java.base/java.util.Iterator.remove(Iterator.java:102) ~[na:na]
	at java.base/java.util.AbstractCollection.remove(AbstractCollection.java:299) ~[na:na]
	at com.innogrid.cloudit6.network.domain.subnet.service.impl.SubnetPortServiceImpl.getAvailableIpBySubnet(SubnetPortServiceImpl.java:68) ~[main/:na]
	at com.innogrid.cloudit6.network.domain.subnet.service.impl.SubnetServiceImpl.getAvailableIpBySubnetId(SubnetServiceImpl.java:165) ~[main/:na]
```

List는 remove 연산이 있는데,,,,

```java
// 이전
List<String> addresses = Arrays.asList(subnetUtils.getInfo().getAllAddresses());

// 수정
List<String> addresses = new ArrayList<>(Arrays.asList(subnetUtils.getInfo().getAllAddresses()));
```

위와 같이 `new ArrayList`로 감싸준 후에 다시 실행하였더니 정상적으로 동작하였다. 

## new ArrayList<>() vs Arrays.asList

### 1. return type

`Arrays.asList`는 Arrays.java에서 ArrayList가 구현되어 있는데 반면 `new ArrayList<>()`는 ArrayList에 구현되어 있다. 즉, 두 클래스는 **다른 클래스**이다. (다른 리턴 타입을 가지고 있다)

```java
import java.util.ArrayList; // new ArrayList<>();
import java.util.Arrays; // Arrays.asList(); -> Arrays의 정적 클래스인 ArrayList를 리턴
```

### 2. 원소 추가 및 삭제

- `new ArrayList<>()` → 추가 O, 삭제 O
- `Arrays.asList()` → 추가 X, 삭제 X

Arrays.asList()는 [javadoc](https://docs.oracle.com/javase/7/docs/api/java/util/Arrays.html#asList(T...))에 아래와 같이 설명되어 있다.

> *Returns a fixed-size list backed by the specified array.
특정한 배열에 의해 백업된 고정 크기 List로 반환.*
> 

즉. `Arrays.asList()`는 고정된 List이기 때문에 List에 원소를 **추가, 삭제**를 할 수 없다

때문에 원소 추가 및 삭제 하려고 할 경우 위와 같은 Exception이 발생하는 것이다. 

`Arrays.asList()`를 사용하여 생성한 리스트에 원소를 추가 및 삭제하고 싶은 경우 아래와 같이 `new ArrayList<>`로 묶어주면 된다.
```java
new ArrayList<>(Arrays.asList(list));
```
