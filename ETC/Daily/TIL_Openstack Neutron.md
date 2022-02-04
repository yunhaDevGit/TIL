## 1. OpenStack Network

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1bde2a37-61e2-432c-98d7-d5ec8d45e189/Untitled.png)

OpenStack Network은 OpenStack을 이용하여 Cloud를 제공하는 Provider 관점에서의 Network와 Cloud를 이용하는 User 관점에서의 Network로 접근 가능합니다. 

Provider 관점에서의 Network는 Management, Guest, External, API 4가지로 구분된다. 

- Management Network : OpenStack을 구성하는 Service들이 이용하는 Network이다. 일반적으로 Node 사이의 물리 Network(VLAN)를 이용한다.
- Guest Network : VM 사이의 통신에 이용되는 Network이다. 일반적으로 VXLAN/GRE 기반의 가상 Network를 이용하지만, 물리 Network(VLAN)으로도 구성 가능하다.
- External Network : VM이 외부 통신할 경우 이용되는 Network이다. 일반적으로 Node 사이의 물리 Network(VLAN)를 이용한다.
- API Network : OpenStack의 Service API를 User에 노출시키는 통로가 되는 Network이다. 일반적으로 Node 사이의 물리 Network(VLAN)을 이용한다.

User 관점에서의 Network는 Provider, Self-service Network 2가지로 구분된다. 

- Provider Network : Provider가 생성하는 Network이다. Provider는 물리 Network(VLAN) 기반의 Network 또는 VXLAN/GRE 기반의 가상 Network 모두 생성할 수 있다. Provider 관점에서의 Guest, External Network라고 생각하면 된다. VM이 물리 Network 기반의 Provider Network와 연결되기 위해 해당 물리 Network가 Compute Node에도 연결되어 있어야 한다.
- Self-service Network : User가 생성한 가상의 Network이다. User는 VXLAN/GRE 기반의 가상 Network만 생성 할 수 있다. Provider 관점에서의 Guest Network가 Self-service Network라고 할 수 있다.

## 2. OpenStack Neutron

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f63f0904-8021-4dfd-8475-a4d56cf1ba3c/Untitled.png)

OpenStack의 모든 Network를 담당하는 Service.

Network, Subnet, Router, LB 등 Infra 구성에 필요한 대부분의 Network 구성 요소를 Provider 또는 User가 쉽게 생성하고 설정할 수 있도록 도와준다. 

Neutron은 Master 역할을 하는 Neutron Server와 Slave 역할을 수행하는 ML2 Plugin Agent, L3 Agent, DHCP Agent, Meta Agent 등으로 구성되어 있다.

Neutron Server와 Agent들 사이의 통신은 Message Queue를 이용한다. 이때 RPC(Remote Procedure Call)를 이용한다. 

별도의 SDN Service가 Neutron과 협력하여 Network를 제어할 경우 Neutron과 SDN Service는 REST API 방식으로 통신한다.

- Neutron Server : Neutron Server는 Controller Node에서 동작. Provider 또는 User에게 Network API 제공하고 요청에 따라 전반적인 OpenStack Network를 제어하는 Master 역할을 수행한다. Neutron Server는 Plugin으로 구성되어 있는데 Core Plugin, Service Plugin으로 구분된다. Core Plugin은 Network, Subnet과 연관된 기능을 수행한다. Service Plugin은 Router, LB, Firewall 같은 Network L3와 연관된 기능을 수행한다.
- ML2 Plugin Agent : Network Node 또는 Compute Node에서 동작. Neutron Server의 명령에 따라 VLAN, Bridge, OVS(Open V Switch)와 같은 Network L2를 제어한다.
- L3 Agent : Network Node에서 동작. Neutron Server의 명령에 따라 Router, Firewall 같은 Network L3를 제어한다.
- Meta Agent : VM 내부에서 VM 초기화를 진행하는 Cloud-Init 또는 Cloudbase에게 VM 초기화에 필요한 VM Metadata를 Nova에게 얻어와 전달한다. Cloud-Init 또는 Cloudbase로부터 오는 VM Metadata 요청은 Router의 Routing Rule 및 각 Router에 한개씩 존재하는 Meta Proxy를 통해 Meta Agent에게 전달된다. Meta Agent는 VM Metadata 요청이 온 Router의 ID와 VM Metadata 요청 Packet에 있는 VM IP 정보를 Neutron Server에게 전달하여 VM ID를 얻는다. 그 후 Meta Agent는 VM ID를 이용하여 Nova에게 얻은 VM Metadata를 해당 VM의 Cloud-Init 또는 Cloudbase에게 전달한다.
- DHCP Agent : VM에게 IP를 부여하기 위한 DHCP Server를 제어한다. 또한 Router의 Meta Proxy를 대신하는 별도의 Meta Proxy를 관리하는 역할도 수행한다. DHCP Agent의 Meta Proxy는 VM이 Router와 연결되어 있지 않는 고립된 네트워크에 연결되어 있는 경우, 고립된 Network ID와 VM Metadata 요청 Packet에 있는 VM IP 정보를 Meta Agent에게 전달하여 Meta Agent가 VM Metadata를 얻도록 도와준다.
