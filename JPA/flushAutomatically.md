### @Modifying


# flushAutomatically


이 Attribute는 @Query와 @Modifying을 통한 쿼리 메소드를 사용할 때, 해당 쿼리를
실행하기 전, 영속성 컨텍스트의 변경 사항을 DB에 flush할 것인지를 결정하는 Attribute이다.
Default 값은 clearAutomatically와 마찬가지로 false이다.

- @Modifying Attribute의 Default는 false
- 쿼리 메소드를 실행하고 쿼리를 날리기 전에 flush를 자동으로 하지 않는다.
- false을 때, 영속성 컨텍스트와 DB 싱크가 맞지 않을 수 있는 경우를 테스트

```java
@Entity
@Getter
@Setter
public class Article {
    @Id
    @GeneratedValue
    private Long id;
    
    private String title;
    
    private String content;
    
    private Boolean isPublished = false;
    
    public void publish() {
        isPublished = true;
    }
}
```

```java
public interface ArticleRepository extends JpaRepository<Article, Long> {
    @Modifying
    @Query("DELETE FROM Article a WHERE a.isPublished = true")
    int deletePublic();
}
```

```java
@Test
@DisplayName("Spring Data JPA를 통한 테스트")
@Rollback(false)
void delete1() {
    Article article = new Article();    // Transient
    articleRepository.save(article);    // Persistent
        
    Optional<Article> byId = articleRepository.findById(1l);    // get By Persistence Context(Not db)
    byId.get().publish();
    
    int delete = articleRepository.deletePublic();  // Execute DELETE Query
        
    assertThat(delete).isEqualTo(0);
}
```
- 시나리오
1. Article 엔티티 생성(Transient 상태)
2. Article 엔티티 영속화(Persistence 상태 - DB에 저장되지 않음)
3. DB가 아닌 영속성 컨텍스트에 있는 ARticle을 가져옴
4. 해당 Article을 publish() 메소드를 통해 isPublished 값을 true로 변경
5. deletePublic()을 통해 isPublished 값이 true인 Article을 모두 삭제


- 실행 결과
- DELETE 쿼리 실행 전, flush 실행 안됨
- INSERT, UPDATE 쿼리 실행
- 그 데이터를 DELETE 했기 때문에 쿼리 메소드 return 값 1 출력


---


### Hibernate의 FlushModeType


- Spring Date JPA는 구현체로 Hibernate를 사용
- FlushModeType enum class
- AUTO, COMMIT, Default는 AUTO
- AUTO(Default) : flaush가 쿼리 실행 시 발생
- COMMIT : flush가 트랜잭션이 commit 될 때 발생한다.
- Hibernate의 FlushModeType이 Default로 AUTO였기 때문에 flushAutomatically가 false여도
쿼리 실행 전 flush가 나가게 됨