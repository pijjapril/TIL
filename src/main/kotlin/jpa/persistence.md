#JPA

---
### 영속성 컨텍스트

- 엔티티를 영구 저장하는 환경, 논리적인 개념
- 애플리케이션 - DB 사이에서 객체를 보관하는 가상의 DB 역할(-> 플러시 시점에 DB에 반영)
  - 플러시 : 영속성 컨텍스트의 변경 내용을 DB에 반영 (추후 학습 예정)
- 엔티티 매니저로 영속성 컨텍스트에 접근 -> 버퍼링이나 캐싱을 위함

EntityManager에 의해서 관리되는 상태 -> 영속성 상태

---
### 엔티티의 생명주기
1. 비영속 상태(new/transient)
   - 객체 생성, 엔티티와 연결 X
<br><br>
2. 영속 상태(managed) -> @Id로 매핑된 키값이 있어야 함
   - 객체 생성, 저장한 상태
   - 영속성 컨텍스트가 관리하는 엔티티(1차 캐시/쓰기 지연 저장소에 있는 상태)
<br><br>
3. 준영속 상태(detached)
    - 엔티티가 영속성 컨텍스트에서 분리
<br><br>
4. 삭제(removed)
    - 엔티티 삭제

+ Status Change
1. 비영속 -> 영속 -> 삭제
2. 영속 -> 준영속
<br><br>
<img src="/Users/camel/IdeaProjects/TIL/src/main/resources/img/jpa_persistence_1.png">
---
###Schema
- 1차 캐시
<br>
: 영속성 컨텍스트 내부에 엔티티를 보관하는 저장소
<br>
: 트랙잭션의 시작과 종료까지만 유효
<br>
: 같은 엔티티가 있으면 객체 동일성을 보장

### 영속성 컨텍스트의 장점
1. 1차 캐시
   1. 1차 캐시에서 조회
   2. 1차 캐시에 없을 경우 DB에서 조회(1차 캐시에 저장 후 반환)
   <br><br>
   - 이때, EntityManager는 트랜잭션 단위이기에 트랜잭션이 끝나면 1차 캐시도 지워짐
   + 2차 캐시는 애플리케이션 전체에서 공유하는 캐시(추후 학습 예정)
<br><br>
2. 영속 엔티티의 동일성 보장 -> 1차 캐시
<br><br>
<img src="/Users/camel/IdeaProjects/TIL/src/main/resources/img/jpa_persistence_2.png">
```java
Member a = em.find(Member.class, "member1");
Member b = em.find(Member.class, "member1");

System.out.println(a==b)    // 동일성 보장
```
- 같은 호출을 반복해도 1차 캐시에 있는 같은 엔티티를 반환하여 동일성 보장
- 동일성 보장의 문제점 -> 
3. 엔티티 등록 시 쓰기 지연
- 트랜잭션을 커밋하는 순간 -> 한 번에 DB에 SQL을 보냄
- 그 전까지는 쓰기 지연 SQL 저장소에 모았다가 한 번에 보냄
```java
EntityManager em = emf.createEntityManager();
EntityTransaction transaction = em.getTransaction();
//엔티티 매니저는 데이터 변경시 트랜잭션을 시작해야 한다.
transaction.begin();
em.persist(memberA);
em.persist(memberB);

//여기까지 INSERT SQL을 데이터베이스에 보내지 않는다.
//커밋하는 순간 데이터베이스에 INSERT SQL을 보낸다.
transaction.commit();
```
4. 엔티티 수정할 때 : 변경 감지(더티 체킹)
```java
EntityManager em = emf.createEntityManager();
EntityTransaction transaction = em.getTransaction();
transaction.begin();
// 영속 엔티티 조회
Member memberA = em.find(Member.class, "memberA");
// 영속 엔티티 데이터 수정
memberA.setUsername("hi");
memberA.setAge(10);

//em.update(member)
transaction.commit();
```
<br><br>
<img src="/Users/camel/IdeaProjects/TIL/src/main/resources/img/jpa_persistence_3.png">