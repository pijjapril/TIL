# AOP


### AOP (Aspect Oriented Programming)

- 관점 지향 프로그래밍
- 비즈니스 로직 & 공통 모듈을 분리 -> 비즈니스 로직에만 집중해서 처리
- 공통 모듈을 적용해서 의존 관계의 복잡성과 코드 중복 해소
- 공통 모듈( ex) 로깅, 보안인증, 트랜잭션 ) 별도 작성, 코드 밖에서 비즈니스 로직 사이에 삽입


- AOP 용어
  - Target
    - 핵심 기능 모듈, 공통 모듈을 부여할 대상
  - Advice
    - 모듈, Target에 제공할 부가 기능
  - Join Point
    - Advice 적용 위치
    - Target 객체가 구현한 인터페이스의 모든 메소드
  - Pointcut
    - Advice 적용할 Target의 메소드를 선별하는 정규식
  - Aspect
    - AOP 기본 모듈
    - Aspect = Advice + Pointcut
    - Singleton 형태의 객체
  - Advisor
    - Advisor == Aspect = Advice + Pointcut
  - Weaving
    - Pointcut에 의해 결정된 Target의 Join Point에 Advice를 삽입하는 과정
    - AOP가 핵심기능(Target) 코드에 영향을 주지 않으면서 공통모듈(Advice)를 추가할 수 있게 해주는 처리 과정

