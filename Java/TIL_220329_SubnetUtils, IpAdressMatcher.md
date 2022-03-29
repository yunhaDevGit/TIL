ip가 특정 ip 대역에 속하는 지 확인하려고 할 때 ip address, netmask, gateway까지 모든 것들을 다 고려하여 검증하는 방법은 생각보다 복잡합니다. 

예를 들면, 192.168.120.130/24일 경우 192.168.120.156이 포함되는지 여부를 바로 알기 어렵습니다. 

사용자로부터 입력 받은 ip 주소가 유효한 지 확인하는 로직을 구현하려다 너무 복잡하고 헷갈려서 분명 이건 누군가 미리 만들어 둔 함수가 있을 것이라고 생각하여 찾아보았습니다. 

## SubnetUtils

SubnetUtils는 자바에서 CIDR 주소 체계 계산을 도와주는 유틸 클래스입니다. 

SubnetUtils는 네트워크 주소와 서브넷 마스크가 주어지면 일부 서브넷 계산을 해줍니다. 

**build.gradle**

```java
implementation 'commons-net:commons-net:3.8.0'
```

**maven**

```html
<dependency>
    <groupId>commons-net</groupId>
    <artifactId>commons-net</artifactId>
    <version>3.8.0</version>
</dependency>
```

### 사용법

```java
SubnetUtils(String cidrNotation) // CIDR 표기법을 사용
SubnetUtils(String address, String mask) // 10진수와 점으로 구분된 주소와 마스크를 사용
```

```java
package subnet;
 
import org.apache.commons.net.util.SubnetUtils;
 
public class SubnetTest {
    public static void main(String[] args) {
 
        SubnetUtils subnetUtils = new SubnetUtils("120.131.5.130/26"); 
        subnetUtils.setInclusiveHostCount(true);    //network,broadcast ip 포함, false:불포함
 
        System.out.println("넷마스크:" + subnetUtils.getInfo().getNetmask());
        System.out.println("네트워크 IP:" + subnetUtils.getInfo().getNetworkAddress());
        System.out.println("브로드캐스트 IP:" + subnetUtils.getInfo().getBroadcastAddress());
 
        System.out.println("시작 IP:" + subnetUtils.getInfo().getLowAddress());
        System.out.println("끝 IP:" + subnetUtils.getInfo().getHighAddress());
        System.out.println("120.131.5.130 해당 대역의 포함여부: " + subnetUtils.getInfo().isInRange("120.131.5.130"));
        System.out.println("120.131.5.234 해당 대역의 포함여부: " + subnetUtils.getInfo().isInRange("120.131.5.234"));
        String[] allAddresses = subnetUtils.getInfo().getAllAddresses();
        System.out.println("===사용가능한 IP 리스트 시작===");
        for (String allAddress : allAddresses) {
            System.out.println(allAddress);
        }
    }
}
```

## IpAdressMatcher

**build.gradle**

```java
implementation 'org.springframework.security:spring-security-web'
```

**maven**

```html
<dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-web</artifactId>
    <version>5.6.2</version>
</dependency>
```

**사용법**

```java
private void checkIpMatch() { 
	matches("192.168.2.1", "192.168.2.1"); // true 
	matches("192.168.2.1", "192.168.2.0/32"); // false 
	matches("192.168.2.5", "192.168.2.0/24"); // true 
	matches("92.168.2.1", "fe80:0:0:0:0:0:c0a8:1/120"); // false 
	matches("fe80:0:0:0:0:0:c0a8:11", "fe80:0:0:0:0:0:c0a8:1/120"); // true 
	matches("fe80:0:0:0:0:0:c0a8:11", "fe80:0:0:0:0:0:c0a8:1/128"); // false 
	matches("fe80:0:0:0:0:0:c0a8:11", "192.168.2.0/32"); // false 
} 
private boolean matches(String ip, String subnet) { 
	IpAddressMatcher ipAddressMatcher = new IpAddressMatcher(subnet); 
	return ipAddressMatcher.matches(ip); 
}

```

**참조**

[https://warpgate3.tistory.com/96](https://warpgate3.tistory.com/96)

[https://ontoidea.tistory.com/80](https://ontoidea.tistory.com/80)
