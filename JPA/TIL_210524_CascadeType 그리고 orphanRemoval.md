# CascadeType 그리고 orphanRemoval

orphanRemoval = true은 CascadeType.REMOVE 비슷한 역할을 한다
: 객체 내에 다른 객체를 가리키는 aggregation 관계에 있는 변수를 가질 경우, 그 객체를 삭제할 때 내부에 포함된 객체들도 삭제 할 지에 대한 여부를 지정하는 내용이다.
- 동일한 점 : 부모 객체가 삭제 되었을 때 자식 객체도 함께 삭제
- 차이점 : 관계 끊음에 대한 응답
	- orphanRemoval = true : 관계가 끊어진 child를 자동으로 제거
	- CascadType.REMOVE : 관계가 끊어진 child를 자동으로 제거 x (관계가 끊어진 것을 제거로 보지 않기 때문)

Cascade
- @OneToMany, @ManyToOne의 옵션 값(parent-child 관계 시)
- Entity의 상태가 변화했을 때, 관계가 있는 Entity에도 상태 변화를 전파 시키는 옵션
- ORM에서 부모 객체와 그 내부의 인스턴스로 포함된 자녀 객체의 ORM 동작에 대한 설정이다
- Entity 상태
	- transient : 객체에 관해 아무것도 모르는 상태. 최초에 객체를 생성한 상태로 영속성 컨텍스트와 전혀 관계가 없는 새로운 상태
	- persistent : 영속성 컨텍스트에 저장되어 JPA가 관리하는 상태. 필요한 시점에 DB에 적용된다
	- detached : 영속성 컨텍스트에 저장되었다가 분리된 상태
	- removed : 삭제된 상태
-  CascadeType
	- type 명시 x : 모든 연관 관계 무시
	- save-update : save(), update() 하면 연관 관계를 탐색하여 새로 생성된 인스턴스 저장, 준영속 인스턴스에서 발생한 변경사항 영속화
	- persist : 부모 객체가 저장되면 자식 객체도 자동으로 저장되게 하는 설정. save(), persist()
	
		  private static void saceWithCascas(EntityManager em){
		     Child child1 = new Child();
		     Parent parent = new Parent();
		     child1.setParent(parent);
		     
		     parent.getChildrent().add(child1);
		     
		     entityManager.persist(parent); 
		     // 부모 엔티티만 영속화를 했지만 CascadeType.PERSIST 속성으로 자식 엔티티까지 함께 영속화 되어 저장된다
		  }
		    
	- merge : 트랜잭션이 종료되고 detach 상태에서 연관 엔티티를 추가하거나 변경된 이후에 부모 엔티티가 merge()를 수행하게 되면 변경 사항이 적용된다(연관 엔티티의 추가 및 수정 모두 반영됨)
	- remove : 삭제 시 연관 엔티티도 같이 삭제.
	- detach : 부모 엔티티가 detach()를 수행하게 되면, 연관된 엔티티도 detach() 상태가 되어 변경사항이 반영 되지 않는다.
	- all : 모든 cascade 적용
