## SubnetUtils란?

SubnetUtils는 자바에서 CIDR 주소 체계 계산을 도와주는 유틸 클래스이다. 

SubnetUtils는 네트워크 주소와 서브넷 마스크가 주어지면 일부 서브넷 계산을 해준다


## 문제 상황

IP, Netmask, Gateway에 대한 유효성 검사 로직을 직접 구현하려고 하니 너무 복잡하고 이진수 계산법이 많이 필요 해서 SubnetUtils를 사용하여 유효성 검증 로직을 구현하였다. 

사용자가 입력한 CIDR 주소 체계를 바탕으로 시작 주소와 끝 주소를 구한 후 for 문을 통해 해당 주소가 다른 곳에서 사용 중인지 여부를 검증 하는 로직을 구현하였는데 계속해서 잘못된 ip라는 에러가 발생하였다. 

```java
SubnetUtils subnetUtils = new SubnetUtils("192.168.120.130/27"); 

// 시작 주소
String startIp = subnetUtils.getInfo().getLowAddress();
String endIp = subnetUtils.getInfo().getHighAddress()

// 사용 가능한 주소 범위
String[] allAddresses = subnetUtils.getInfo().getAllAddresses();
```

디버깅을 통해 시작 주소와 끝 주소, 주소 범위를 확인해보니 내가 생각한 것과 달랐다.

이런식으로 구현을 하였는데 나는 당연히 192.168.120.130 부터 주소 범위가 시작 될 것이라고 생각했는데 아니었다. 

SubnetUtils 내부 로직을 확인해보니 network 계산 시 이진수를 & 연산을 하기 때문에 반드시 사용자가 입력한 주소부터 시작하지 않고 그 연산에 따라 시작 주소가 달라졌다.

그러나 사용자 입장에서는 당연히 자기가 작성한 IP 주소부터 시작될 것이라고 생각하기 때문에 이를 해결해야겠다고 생각했고, 두가지 방법이 있었다. 

하나는 SubnetUtils를 사용하지 않는 방법, 다른 하나는 Exception을 발생 시키는 방법이었다. 

나는 SubnetUtils를 사용하기로 결정했고 아래와 같이 Exception 처리를 해주었다.
