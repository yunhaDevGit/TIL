# HypervisorClient (FeignClient)

Openstack API 요청 시 **`X-OpenStack-Nova-API-Version`** Header를 추가하여 특정 버전을 지정할 수 있습니다. 

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/fb95939f-86d0-435f-8622-76d0556931ec/Untitled.png)

```java
이것은 개념적으로 "Accept" 헤더와 같은 역할을 합니다. 의미적으로 이것은 다음을 의미합니다.

- X-OpenStack-Nova-API-Version또는 OpenStack-API-Version ( 계산 지정 ) 둘 다 제공되지 않으면 지원되는 최소 마이크로 버전이 지정된 것처럼 작동합니다.
- 두 헤더가 모두 제공되면 OpenStack-API-Version이 헤더가 우선 적용됩니다.
- X-OpenStack-Nova-API-Version또는 이 제공된 경우 OpenStack-API-Version해당 마이크로 버전의 API로 응답합니다. 지원되는 마이크로 버전 범위를 벗어나면 406 Not Acceptable을 반환합니다.
- X-OpenStack-Nova-API-Version또는 값 OpenStack-API-Version이 최신 (특수 키워드)인 경우 최대값이 지정된 것처럼 작동합니다.
```

`/os-hypervisors/{hypervisor_hostname_pattern}/servers` API가 2.53 버전부터 더 이상 제공하지 않으므로 해당 API 요청의 버전을 지정하여 요청

Openstack4j 라이브러리의 API가 아닌 Feign Client로 특정 버전을 지정하여 요청.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/bbbb7bb4-23a1-4b05-8ff3-9446c36300a4/Untitled.png)

```java
@FeignClient(name = "openstack-hypervisor", configuration = {FeignSSLDisableConfig.class})
public interface HypervisorClient {

    @GetMapping("/os-hypervisors/detail")
    ResponseEntity<Map<String, Object>> getHypervisorsDetailList(URI baseUrl, @RequestHeader("X-Auth-Token") String token, @RequestHeader("X-OpenStack-Nova-API-Version") String novaApiVersion);

    @GetMapping("/os-hypervisors/{hypervisorHostName}/servers")
    ResponseEntity<Map<String, Object>> getHypervisorServerList(URI baseUrl, @RequestHeader("X-Auth-Token") String token, @RequestHeader("X-OpenStack-Nova-API-Version") String novaApiVersion, @PathVariable String hypervisorHostName);

}
```

```java
ResponseEntity<Map<String, Object>> list = hypervisorClient.getHypervisorsDetailList(uri, osClient.getToken().getId(), "2.46");
ResponseEntity<Map<String, Object>> serverList = hypervisorClient.getHypervisorServerList(uri, osClient.getToken().getId(), "2.46", "redhat03");
```
