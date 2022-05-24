#JPA

---
### Flush
- 영속성 컨텍스트의 변경 내용을 DB에 반영하는 것
- 트랜잭션 커밋이 일어날 때 flush가 동작하는데, 
<br>이때 쓰기 지연 저장소에 쌓아 놨던 INSERT, UPDATE, DELETE SQL들이 DB에 날라간다.
- 영속성 컨텍스트를 비우는 것이 아닌 변경사항들과 DB의 상태를 맞추는 작업
- 결론
<br>
플러시 : 영속성 컨텍스트의 변경 내용을 DB에 동기 
---
### 플러시 동작 과정
1. 변경 감지 (Dirty Checking)
2. 수정된 Entity를 쓰기 지연 저장소에 등록
3. 쓰기 지연 저장소의 Query를 DB에 전송
<br><br>
   - 플러시 다음에 실제 commit이 발생함
   - 플러시가 동작할 수 있는 이유는 트랜잭션이라는 개념이 있기 때문이다
     - 트랜잭션이 시작되고 해당 트랜잭션이 commit 되는 시점 직전에만 변경 내용을 동기화
     - 그 사이에서 플러시 매커니즘의 동작이 가능

---
### 영속성 컨텍스트 플러시하는 방법
1. em.flush()을 통한 직접 호출

```java
// 영속 상태 (Persistence Context 에 의해 Entity 가 관리되는 상태)
Member member = new Member(200L, "A");
entityManager.persist(member);

entityManager.flush(); // 강제 호출 (쿼리가 DB 에 반영됨)

System.out.println("DB INSERT Query -> flush() 호출 -> Transaction commit 됨.");
tx.commit(); // DB에 insert query 가 날라가는 시점 (Transaction commit)
```


2. 트랜잭션 커밋 시 플러시 자동 호출
<br><br>
3. JPQL 쿼리 실행 시 플러시 자동 호출

```java
em.persist(memberA);
em.persist(memberB);
em.persist(memberC);

// 중간에 JPQL 실행
query = entityManager.createQuery("select m from Member m", Member.class);
List<Member> members = query.getResultList();
```
memberA, B, C를 영속성 컨텍스트에 저장한 상태에서 조회해도 조회가 되지 않는다.
<br>
DB에 INSERT Query가 아직 날라가지 않은 상태


참고 : https://gmlwjd9405.github.io/2019/08/07/what-is-flush.html