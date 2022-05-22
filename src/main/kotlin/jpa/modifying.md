# 벌크 연산

---
### 벌크연산
- 다건의 UPDATE, DELETE 연산을 하나의 쿼리로 작성
- JPA에서 단건 UPDATE 같은 경우에는 더티체킹을 통해서 수행되거나 save()로 가능
- @Modifying과 @Query를 사용한 벌크 연산
  - 자유롭게 JQPL를 정의해서 사용할 수 있고, 하나의 쿼리로 많은 데이터를 변경할 수 있음
- 벌크 연산 쿼리 작성 후 @Modifying을 붙이지 않으면, 
- InvalidDataAccessApiUsage exception이 발생

참고 : https://www.baeldung.com/spring-data-jpa-modifying-annotation

---
# @Modifying

---
### clearAutomatically
- @Modifying이 붙은 쿼리 메서드 실행 직후, 영속성 컨텍스트를 clear 할 것인지를 지정하는 Attribute
- default 값은 false 
- 영속성 컨텍스트의 1차 캐시와 관련한 문제점 발생

---

- 벌크 연산을 통해 데이터 변경 쿼리를 실행하고, 해당 엔티티를 조회하는 시나리오

    1. 엔티티 객체를 생성 (transient 상태)

    2. 객체의 초기값을 설정

    3. 해당 엔티티의 Repository를 통해 해당 엔티티 객체를 save() (해당 엔티티는 persistent 상태)

    4. 벌크 연산을 통해 기존에 설정한 초기값을 변경 (SQL 실행)

       (이 때, flush가 발생하여 해당 쿼리 메서드가 실행 되기 전의 쿼리들이 모두 flush됨

    5. findById()를 통해 해당 엔티티를 조회

### 예제 1)
```kotlin
@Entity
class Article {
    @Id
    @GeneratedValue
    val id: Long
    val title: String
    val content: String
}
```

- @Modifying 과 @Query를 통해 직접 JPQL 정의 
- clearAutomatically는 default로 false
```kotlin
interface ArticleRepository : JpaRepository<Article, Long> {
    @Modifying
    @Query("UPDATE Article a SET a.title = ?2 WHERE id = ?1")
    fun updateTitle(id: Long, title: String) : Int
}
```

```kotlin
class Test {
    @Autowired
    private val articleRepository: ArticleRepository
    
    @Rollback(false)
    fun update() {
      val article = Article()
      article.title = "before"
      articleRepository.save(
          article.title
        )
      
      println("**************************")
      val result = articleRepository.updateTitle(1l, "after")
      assertThat(result).isEqualTo(1)
      println("**************************")
      println(articleRepository.findById(1l).get().getTitle())
    }
}
```

1. Article 엔티티 생성
2. title 값 세팅
3. articleRepository를 통해 영속화
4. 벌크 연산 쿼리 메서드를 통해 title 값 UPDATE
5. findById()를 통해 기존의 Id값으로 엔티티 조회
<img src="/Users/camel/IdeaProjects/TIL/src/main/resources/img/bulkOperation_1.png">
- 정리
  - 결과는 변경된 값, "after"가 아닌 변경 전의 값 "before"
  - 실제 DB에서는 벌크 연산의 실행 결과로 title이 "after"로 변경
---
### 예제 2)
```kotlin
interface ArticleRepository : JpaRepository<Article, Long> {
    @Modifying(clearAutomatically = true)
    @Query("UPDATE Article a SET a.title = ?2 WHERE id = ?1")
    fun updateTitle(id: Long, title: String) : Int
}

```
<img src="/Users/camel/IdeaProjects/TIL/src/main/resources/img/clearAutomaticallyTrue.png">

- 정리
  - title은 벌크 연산에서 실행했듯이 "after"로 변경 
  - 예제 1과 예제2 모두 테스트 코드(findById)는 동일
  - 예제1 에서는 SELECT Query가 실행되지 않음
  - 예제2 에서는 SELECT Query가 실행
  - 실제 DB에는 변경된 after 값
---
### 결론
- JPA 에서 조회를 실행할 시에 1차 캐시를 확인해서 해당 엔티티가 1차 캐시에 존재한다면 DB에 접근하지 않고, 
1차 캐시에 있는 엔티티를 반환
- 벌크 연산은 1차 캐시를 포함한 영속성 컨텍스트를 무시하고 바로 Query를 실행하기 때문에 영속성 컨텍스트는 데이터 변경을 알 수 없음
- 벌크 연산 실행 시, 1차 캐시(영속성 컨텍스트)와 DB의 데이터 싱크가 맞지 않게 됨
- Spring Data JPA는 @Modifying의 clearAutomatically = true일 때 벌크 연산 직후 자동으로 영속성 컨텍스트를 clear 해줌
- 조회를 실행하면 1차 캐시에 해당 엔티티가 존재하지 않기 때문에 DB 조회 쿼리를 실행
  <br><br>
참고 : https://devhyogeon.tistory.com/4


 