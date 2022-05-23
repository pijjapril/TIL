# JPA

---
### 1차 캐시와 2차 캐시

네트워크를 통해 DB에 접근하는 시간 비용은 서버 내부 메모리에 접근하는 시간보다 비싸다.
따라서 캐시를 사용해 DB 접근 횟수를 줄이면 성능 개선이 가능하다.

---
### 1차 캐시
- 영속성 컨텍스트 내부에 엔티티를 보관하는 저장소
- 같은 엔티티가 있으면 객체 동일성을 보장
<br><br>
![jpa_persistence_1](src/main/resources/img/jpa_persistence_1.png)


### 2차 캐시
- 애플리케이션 범위의 캐시 -> 공유 캐시
- 동시성 극대화를 위해 캐시 한 객체를 직접 반환하지 않고 복사본을 만들어 반환
- 캐시 한 객체를 그대로 반환하면 여러 곳에서 객체를 동시에 수정하는 동시성 문제 발생
- 영속성 컨텍스트가 다르면 객체 동일성을 보장하지 않는다.
<br><br>
<img src="/Users/camel/IdeaProjects/TIL/src/main/resources/img/cache_1.png">

참고 : https://willseungh0.tistory.com/77