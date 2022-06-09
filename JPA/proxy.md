# JPA Hibernate Proxy

### JPA Proxy


---


JPA는 연관된 모든 객체를 처음부터 조회하는 것이 아니라 실제 사용 시점에서 DB에서 조회할 수 있다.
이와 관련된 기술이 프록시이고 사용 시점에 데이터베이스에서 조회하는 것을 지연로딩(LAZY)
연관된 모든 객체를 즉시로딩(EAGER)이라고 한다.

```kotlin
@Entity
class Member() {
    @Id
    @GeneratedValue
    val id : Long
    val name: String
    
    @OneToMany(mappedBy = "member", cascade = CascadeType.ALL, orphanRemoval = true)
    val orders : HashSet<>()

    // 연관관계 편의 메소드
    fun addOrder(orderB: Orders) {
        this.orders.add(orderB)
        orderB.setMember(this)
    }
}
```
```kotlin
@Entity
class Orders() {
    @Id
    @GeneratedValue
    val id: Long
    val orderDate: LocalDateTime

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "MEMBER_ID")
    val member: Member
}
```

ManyToOne, OneToOne의 기본 fetch 전략은 EAGER 이어서 항상 Join하지만 성능 상의 이유로 Lazyloading 처리
JPA에서 지연로딩은 Proxy로 구현한다.

<br>

<img width="649" alt="jpaLazyloading" src="https://user-images.githubusercontent.com/85109184/172807211-4e66e844-4886-4502-a3f9-4f6eacc5fc96.png">

JPA에서는 Order를 호출할 때 Member 대신 값에 Proxy를 넣어 놓고 Member 객체 사용 시점에 영속성 컨텍스트에서 조회한다.

```java
Orders order1 = new Orders(LocalDateTime.now(), Status.PREPARE);
em.persist(order1);

Member member = new Member();
member.addOrder(order1);
em.persist(member);

em.flush();
em.clear();

Orders orders = em.find(Orders.class, order1.getId());

System.out.println("orders = " + orders.getClass().getName());
System.out.println("orders.getMember() = " + orders.getMember().getClass().getName());
```

em.find()를 호출 했을 때 select 쿼리가 나간다. Lazy Loading 이기 때문에 Member와 join하지 않는다.
Order에서 Member를 Join하지 않았기 때문에 Proxy로 둘러싼 객체를 호출함

Member가 실제로 사용될 때 영속성 컨텍스트가 member를 찾고, Hibernate에서는 Hibernate.initialize(entity)로 초기화한다.