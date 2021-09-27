
#### APIC - Cisco가 만든 SDN 전용 장비

### SDN(Software Defined Network)

Control Plane과 Data Plane 영역이 나눠져 있는 것

**Control Plane**
: L4로부터 넘어온 패킷이 데이터그램으로 캡슐화 되었을 때, 이 데이터그램이 출발지에서 목적지까지의 경로를 어떤 라우터를 거쳐 지나가는지 결정.

**Data Plane**
:  라우터마다 있는 기능으로, 특정한 데이터그램이 입력 포트로 들어왔을 때, 어떤 출력 포트로 forwarding 할 지 정해준다


### ACI Physical Design

**ACI 물리적 구성요소**
: CISCO ACI는 Data Plane을 담당하는 Nexus 9000 series와 Control Plane을 담당하는 APIC으로 구성되어 있다.

**![](https://lh3.googleusercontent.com/s6OjiDuCMJ9d13vcVEorc21h826MMgv8fCDIz_4fTjwUv3f1tsjxrfit6Y_inb0gKsu2-pRzuU1GYEdE7rW5SBUedA42qmDtFIkzYfJMB8fh-zyJFh9NxLqRPcOOQ-zvLSihQd9u=s0)**


### ACI FABRIC Design 구조
SPINE-LEAF 의 2-Tier로 구성한다

SPINE-LEAF간 Full Mesh로 구성한다

APIC은 Cluster로 구성한다


**![](https://lh6.googleusercontent.com/lyQp-0WWKPB4vLetK7kyQWs6c9iKuw-_eUKjDkP9I-jVsemiwrSCOS5X38YUAadflomGb_njRLdSLcbA6QiNhjzzXr23TZV03sux8AGXxCuMOhq8lJkpb1IwD_y3P1TvN72Cp7om=s0)**

### APIC SPINE 디자인 구조

SPINE to SPINE간 Interlink를 연결하지 않는다

SPINE 스위치와 LEAF 스위치의 역할은 완전히 다르며, 이로 인해 L4-L7, VM등 직접적인 연결은 불가능하다 (host는 LEAF 스위치와 연결된다)

**![](https://lh4.googleusercontent.com/3lHtgyQQmQ8xONtXoa8WksfqkBRT3YRTKMnbAkwhJhGzRIUH_D6eznTS974WT7ZPM9oMdvPTTrEe4K_vONzC5NtPqHdKX0Np3chnDCsNqt-x6RaHiPomV8BBolV9lT3QO4EwrzSG=s0)**


### ACI Logical Diagram

ACI는 크게 두가지 논리적 구성으로 구분할 수 있다

TENANT : 논리적으로 독립적인 객체를 만들고 관리할 수 있는 것

FABRIC : 물리적인 구성을 관리하는 것

**![](https://lh5.googleusercontent.com/r_6bqk2wtYlA2vgpy_qmO07BKoWlZrCsEzKWih17hfIIPeOMSVdZH4Q6rhMIq_FEoRl25ywE2XovyETv7-nuRocM5SETTv87KWhtBiZWxMBzUU6vA63ruiYuIzngyJTV2s1rPTZv=s0)**


### TENANT
Tenant는 Private Network와 Bridge Domain으로 구성된다

Tenant는 상황에 따라 독립된 회사/부서/서비스가 될 수 있다

Private Network는 Context 혹은 VRF(Virtual Routing and Forwarding)로 이해하면 쉽다

Bridge Domain은 Private Network에 포함된 Broadcasting Domain Network로 단, Default GW 역할을 한다


ACI는 VLAN을 기반으로 분리를 수행하지 않고, 테넌트 및 VRF(Private Network)를 사용하여 IP 주소 격리를 제공합니다. 

End Point Groups(EPG)는 Entity에 대한 정책을 그룹화 하는데 사용됩니다.

Bridge Domain은 VLAN과 같은 멀티캐스트 및 브로드캐스트 격리를 제공하는데 사용됩니다.

**![](https://lh6.googleusercontent.com/04aFoOdNz7iaDMDTPH4ERbBHeF_rm9N9eQElAQpx9nch0ozrgnrFL7lWfrPMMculhVl5TbClBYH74viiyJQGsWhtFus3HnQusEc_UJw7-_svFUXJjtgldADSHr_CE1zVQ5RxI0vG=s0)**


### VRF(Virtual Routing and Forwarding)
하나의 라우터를 여러 개의 가상 라우팅 도메인으로 나누어 하나의 물리적 네트워크를 여러 개 프로토콜을 가진 논리적 네트워크로 구성하는 '네트워크 가상화'를 제공하는 기술

한 라우터에서 여러 라우팅 테이블을 생성하여 마치 라우터가 여러 개인 것처럼 구성할 수 있어서 각각의 VRF에 동일한 IP가 있더라도 서로 다른 VRF이므로 다르게 동작한다 

**![](https://lh5.googleusercontent.com/L7TFu18uUJ7vNZYbyIoUHmYbNHuC1MVzjpYMsAqKQ-AN6Fvr5s2qRpv691agg_wdUFbzTsr0NBgBDS-QpIqAZVMDxRUgOEF4By26NWOyevXkIh9dg4Zc4JbkbGA4FYJxsrJAJYhe=s0)**
https://multicloud.tistory.com/m/59

### EPG(End Point Group)
EPG는 유사한 특성 혹은 역할과 Policy를 갖는 End Point Group을 의미

End Point Group 내에는 다양한 단말이 존재할 수 있다(VM, Direct 연결된 Bare Metal 서버, L2 Switch를 통한 Bare Metal 서버)

**![](https://lh3.googleusercontent.com/ql9Gg9KeFrwjuQ0mP2W2qYhkU1My0RY_zd06coXgHBJKFG9pLApBpibLHesO3_-ZU3TUgerWYf05PCsdRcuf8cVHFDrfYwpIycRCGR_pjfD5a_EmHBdvyf-vSBsMeWYDfjyE-n4S=s0)**


### Application Profile
관련된 여러 EPG의 조합과 트래픽 정책

EPG를 관리하는 하나의 폴더로 생각하면 된다

**![](https://lh3.googleusercontent.com/_XXcjhQDduiYv02S3R_r_Or1-tUdUMYnVKGA12epO3uzTXw9g1RG1Ou6Q4sjUnggYNoCTQXuOMIb9zJs3hrqKeZMwGYgl4caxy1y11jGppuDCWDoNf096Mc8NMJfb82zEHJ-BeTd=s0)**

Create TENANT

Create Private Network

Create Bridge Domation

Application Profile

EPG를 관리할 수 있는 관리 탭

Create EPG 

https://gojibang.tistory.com/13

