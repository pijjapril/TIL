# JPA의 동일성 보장

---

### 1차 캐시
- JPA는 1차 캐시를 통해 데이터를 캐싱하여 매번 DB에 접근하는 비용을 줄여줌
- Map 형태로 데이터를 캐싱
- Key == @Id, Value == '엔티티 인스턴스'
- 해당 데이터에 대한 최초 접근 시, DB에 직접 접근하고, 1차 캐시에 캐싱
- 최초 접근 이후, DB에 직접 접근하지 않고 , 1차에 있는 엔티티 인스턴스를 접근


### 동일성 보장
- 동일성 비교 : 인스턴스의 참조값 비교 -> "=="를 통한 비교
- 동등성 비교 : 인스턴스의 내부값 비교 -> ".equals()"를 통한 비교
- 1차 캐시는 Map으로 엔티티 인스턴스를 캐싱하므로 같은 식별자(@Id 값)에 대해 매번 같은 인스턴스에 접근
- JPA는 1차 캐시를 통해 동일성 보장

-> 동일성 보장 때문에 발생되는 예상치 못한 문제

```kotlin
class User {
    @Id
    @GeneratedValue
    val id: Long
    val name: String
}
```

- @Modifying과 @Query를 이용하여 벌크연산을 하는 쿼리 메소드
```kotlin
interface UserRepository: JpaRepository<User, Long> {
    @Modifying
    @Query("UPDATE User u SET u.name = ?2 WHERE u.id= ?1")
    fun updateName(id: Long, name: String): Int
}
```

```kotlin
@Test
@Rollback(false)
@DisplayName("JPA 동일성 보장으로 인한 문제 - UPDATE")
fun identity1() {
    val user = User()
    user.name = "hyo"
    userRepository.save(user)
    
    println("****************************")
    // bulk operation
    val update = userRepository.updateName(1l, "testName")
    assertThat(update).isEqualTo(1)
    println("****************************")
    val user: List<User>
    userRepository.findAll()
    println("Name = " + user.get(0).getName())
}
```

### 테스트 시나리오
1. User 엔티티를 새로 생성하고, 이름을 설정
2. UserRepository.save()를 통해 생성한 User를 저장
3. 벌크 연산을 통해 User의 이름을 변경
<br> 
(벌크 연산 쿼리가 실행되기 전 flush가 되어, INSERT 쿼리도 실행)
4. finAll()을 통해 DB에서 직접 데이터를 조회
5. findAll()을 통해 조회한 users에서 해당 유저의 이름을 확인

<img src="/Users/camel/IdeaProjects/TIL/src/main/resources/img/bulk.png">

### 결과
- 벌크 연산이 실행될 때, save()의 쿼리인 INSERT가 먼저 실행
- 그 이후 UPDATE쿼리가 실행
- findAll()을 통해 SELECT 쿼리 실행, 데이터를 조회
- findAll()을 통해 직접 쿼리를 해서 조회한 데이터인데, 변경 전의 이름이 출력
- DB에는 변경 된 이름이 잘 저장되어 있다.

### 원인
- 벌크 연산과 같이 쿼리를 직접 실행하였을 때는 영속성 컨텍스트, 1차 캐시를 무시하고 쿼리를 실행
-> 이는 영속성 컨텍스트의 엔티티 값을 변경할 수 없다.
- 여기서 DB와 1차 캐시의 데이터 동기화가 깨짐
- JPA는 쿼리를 직접 실행해 얻은 결과 값 중 같은 식별자를 가진 엔티티가 영속성 컨텍스트, 1차 캐시에 존재한다면 그 값을 버리고, 기존의 엔티티를 남겨둠
- 예제에서 findAll()을 통해 직접 SELECT 쿼리를 실행하고 가져온 데이터는 버려지고, 기존에 영속성 컨텍스트, 1차 캐시에 있던 엔티티를 사용

### 해결책
- 영속성 컨텍스트를 무시하고 DB 데이터를 직접 변경하는 쿼리 실행 시, 실행 직 후 영속성 컨텍스트를 초기화하는 방법으로 해결
- 영속성 컨텍스트를 초기화하면 다음 데이터 접근은 DB에 직접 접근하고 영속성 컨텍스트를 갱신하게 되므로, 데이터가 동기화
- clearAutomatically = true로 인해 실행 직후 영속성 컨텍스트를 초기화
```kotlin
interface UserRepository: JpaRepository<User, Long> {
    @Modifying(clearAutomatically = true)
    @Query("UPDATE User u SET u.name = ?2 WHERE u.id= ?1")
    fun updateName(id: Long, name: String): Int
}
```
<img src="/Users/camel/IdeaProjects/TIL/src/main/resources/img/clear_1.png">
- users.(0).getName()하면 갱신된 이름 확인 가능


### 결론
벌크 연산과 같이 영속성 컨텍스트를 무시하고, DB에 직접 데이터 변경 쿼리를 실행하게 되면 
DB와 영속성 컨텍스트의 데이터 동기화가 깨지게 된다.

이후에 DB에 직접 접근을 하더라도, JPA의 동일성 보장 원칙 때문에 
기존의 영속성 컨텍스트, 1차 캐시에 있는 엔티티를 사용하게 된다.

즉, 변경 된 데이터가 아닌 기존의 데이터를 사용한다.

이를 해결하기 위해서는 영속성 컨텍스트를 무시하고 DB에 직접 쿼리 실행 시,
실행 직후 영속성 컨텍스트를 초기화 해주어야 한다.
이를 통해 이 후 데이터 접근은 DB에 직접 접근하고 영속성 컨텍스트를 갱신하게 된다.
