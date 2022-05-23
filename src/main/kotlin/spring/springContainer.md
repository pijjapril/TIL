# Spring Framework


### 제어의 역전


---


- IoC(Inversion Of Control)
  - 객체의 생성, 생명주기 관리를 컨테이너가 담당함
  - 컴포넌트 의존 관계 결정, 설정 및 생명주리극 해결하기 위한 디자인 패턴
  - 객체 간 결합도를 줄이고 유연한 코드를 통해 가독성, 중복 방지, 확장성


- IoC 구현
  - **DL(Dependency Lookup, 의존성 검색)**
    - Bean에 접근하기 위해 컨테이너에서 제공되는 API를 통해 Bean을 Lookup
    - EJB, Spring etc.
    <br><br>
  - **DI(Dependency Injection, 의존성 주입)**
    - 객체가 아닌 프레임워크에 의해 의존성이 주입되는 설계 패턴
    - 클래스 간 의존 관계를 Bean Definition 정보를 바탕으로 컨테이너가 자동 연결
    - 의존성 주입 방법
      - Constructor Injection
      - Setter Injection
      - Field Injection
      - Spring, PicoContainer
    - 장점
      - Dependency Reduction
      - Reusable Structure
      - Readability
      - Loose Coupling & Easy to Change


---


### Container


- Container
  - 어플리케이션을 관련 라이브러리 및 종속 항목과 함께 패키지로 묶어 소프트웨어 구동을 위한 격리 환경 제공
  - 인스턴스의 생명주기 관리와 추가적인 기능 제공
    - ex) Servlet 컨테이너 : Servlet의 생성, 초기화, 실행, 소멸에 대한 권한
  - 스프링 컨테이너 : 종속 객체 주입을 이용하여 컴포넌트 관리


- 컨테이너 종류
  - BeanFactory 인터페이스
    - 객체 생성 및 관리하는 기본적인 컨테이너
    - 컨테이너 구동 시 클라이언트 요청에 의해서만 bean으로 설정된 클래스의 객체가 생성되는 Lazy Loading


  - ApplicationContext 인터페이스
    - BeanFactory의 하위 인터페이스
    - BeanFactory의 빈 관리 기능 이외, 자원 처리 추상화, 메시지 지원 및 국제화, 이벤트 지원
    - 컨테이너 구동 시 바로 해당 클래스의 객체를 생성하는 pre-loading 방식

  
- ApplicationContext 주요 구현 클래스
  - GenericXmlApplicationContext
    - 파일 시스템, 클래스 경로에 있는 XML 파일을 설정으로 사용하는 스프링 컨테이너
    - _ex) String config = "classpath:config/applicationContext.xml"_
    
  - AnnotationConfigApplicationContext
    - 자바 코드를 설정 정보로 사용하는 스프링 컨테이너
    
  - XmlWebApplicationContext
    - ApplicationContext를 상속 받은 WebApplicationContext의 구현 클래스

  - AnnotationConfigWebApplicationContext
    - 스프링 컨테이너로 자바 코드를 설정 정보로 사용


- Spring Bean
  - Bean = Spring Bean Container에 존재하는 객체
  - Bean Container는 의존성 주입을 통해 Bean 객체를 사용


- Bean 설정 방법
  - XML Configuration
  - Annotation Configuration
  - Java Configuration