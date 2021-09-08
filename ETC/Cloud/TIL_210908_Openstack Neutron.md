# OpenStack Network , Neutron

## 1. OpenStack Network
![](https://ssup2.github.io/images/theory_analysis/OpenStack_Network_Neutron/OpenStack_Network.PNG)
- OpenStack Network는 Openstack을 이용하여 Cloud를 제공하는 Provider 관점에서의 Network와 Cloud를 이용하는 User 관점의 Network로 접근할 수 있다. 


### Provider 관점에서의 Network
  - **Management Network ** ***(Cloudit에서 mgmt Network)*** : OpenStack을 구성하는 Service들이 이용하는 Network. 일반적으로  Node 사이의 물리 Network(VLAN)을 이용한다. 
  - **Guest Network** ***(Cloudit에서 data Network)*** : VM 사이의 통신에 사용되는 Network. 
  - **External Network** ***(Cloudit에서 external Network)*** : VM이 외부와 통신 시 이용되는 Network
  - **API Network** ***(Cloudit에서 아마 mgmt? Network)*** : OpenStack의 Service API를 User에 노출 시키는 통로가 되는 Network이다. 

### User 관점에서의 Network
  - **Provider Network** : Provider가 생성하는 Network. Provider는 물리 Network(VLAN) 기반의 Network, VXLAN/GRE 기반의 가상 Network 모두 생성할 수 있다. Provider 관점에서의 Guest, External Network가 해당 된다. 
  - **Self-service Network** : User가 생성한 가상 Network이다. User는 VXLAN/GRE 기반의 Network만 생성할 수 있다. Provider 관점에서의 Guest Network가 해당 된다.


## 2. OpenStack Neutron
![](https://ssup2.github.io/images/theory_analysis/OpenStack_Network_Neutron/Neutron_Architecture.PNG)
- OpenStack의 모든 Network를 담당하는 Service ***(cloudit-cow-consoled, cloudit-cow-apid, cloudit-dna-admd, cloudit-dna-netd)***
- Network, Subnet, Router, LB 등 Infra 구성에 필요한 대부분의 Network 구성 요소를 Provider 또는 User가 쉽게 생성하고 설정 할 수 있도록 도와준다. 
- Neutron은 Master 역할을 하는 Neutron Server와 Slave 역할을 하는 ML2 Plugin, L3 Agent, DHCP Agent, Meta Agent로 구성되어 있다. 
  Neutron Server와 Agent 간의 통신은 Message Queue를 이용하며 Message Queue와 통신할 때 RPC(Remote Procedure Call)을 이용한다. 
  별도의 SDN Service가 Neutron과 협력하여 Network를 제어하는 경우 Neutron과 SDN Service는 REST API 방식으로 통신한다.

- Neutron Server : Neutron Server는 Controller Node에서 동작하며 Provider 또는 User에게 Network API를 제공하고 요청에 따라 전반적인 OpenStack Network를 제어하는 역할을 수행한다. ***(Cloudit에서 cloudit-cow-consoled, cloudit-cow-apid)***
- ML2 Plugin Agent : Network Node 또는 Compute Node에서 동작하며 Neutron Server의 명령에 따라 VLAN, Bridge, OVS와 같은 Network L2를 제어한다. 
- L3 Agent : Network Node에서 동작하여 Neutron Server의 명령에 따라 Router, Firewall과 같은 Network L3를 제어한다. 
- Meta Agent : VM 내부에서 VM 초기화를 진행하는 Cloud-Init  또는 Cloudbase에게 VM 초기화에 필요한 Metadata를 Nova에게 얻어와 전달한다. 
-  DHCP Agent : VM에게 IP를 부여하기 위한 DHCP Server를 제어한다. 모든 플러그인에서 동일하며 DHCP 구성을 유지 관리한다.
