# Spring & Spring Boot

### Spring

- Spring Framework는 자바 플랫폼을 위한 오픈소스 애플리캐이션 프레임워크
- DI, IoC를 통해 재사용 및 유지보수가 용이한 코드, 확장성을 갖도록 설계 가능
- 스프링의 제공 기능을 통해 개발자는 비즈니스 로직에 집중 가능


### Spring Boot

- 스프링 사용 시 여러 세팅 요소가 많은 불편함을 개선

### 개선점
1. 의존성 관리
2. 자동 설정
3. 내장 WAS


---


### 의존성 관리

기존 Spring은 개발에 필요한 모듈의 의존성을 각각 다운 받아야 하며 버전 역시 일일이 명시해야했다.
Spring Boot는 spring-boot-starter를 통해 의존성 관리를 해준다.

- ex) maven의 경우 spring-boot-starter-parent를 통해 각 모듈의 현재 스프링부트 버전에 가장 적합한 버전을 제공
- ex) gradle의 경우 io.spring.dependency-management 플러그인을 통해 적합한 버전을 제공


---


### 자동 설정

기존 스프링은 DB 연결할 때 datasource 설정을 매번 직접 bean 생성을 통해 진행했다. 
스프링부트는 이러한 환경 설정을 @SpringBootApplication 을 통해 자동 설정 해준다.


ex)
```java
@SpringBootApplication
public class ExampleApplication {
    public static void main(String[] args) {
        SpringApplication.run(ExampleApplication.class, args);
    }
}
```

- @SpringBootConfiguration
  - SpringBoot의 설정을 나타내는 어노테이션으로, Spring의 @Configuration을 대체
- @EnableAutoConfiguration
  - 자동 설정의 핵심 어노테이션으로 클래스 경로에 지정된 내용을 기반으로 설정 자동화를 수행
- @ComponentScan
  - basePackages 프로퍼티 값에 별도의 경로를 설정하지 않으면 해당 어노테이션이 위치한 패키지가 루트 경로가 되어 빈으로 등록할 클래스들을 탐색


---


### SpringBoot에 Bean 등록 과정

SpringBoot는 Bean을 두번에 걸쳐 등록한다.

1. ComponentScan


처음에는 Spring과 마찬가지로 Component-scan을 통해 component를 찾고 bean 생성


2. @EnableAutoConfiguration


그 후에는 @EnableAutoConfiguration 으로 추가적인 Bean들을 읽어서 등록하게 된다.
메인 클래스를 실행하면, 위 어노테이션에 의해 META-INF/spring.factories 안에 들어 있는 
수많은 자동 설정들이 조건에 따라 적용이 되어 수 많은 Bean들이 생성되고 스프링부트 어플리케이션이 실행된다.


SpringBoot는 Component Scan을 통해 모은 Component들의 정보와 META-INF/spring.factories 파일에 
사전 정의한 auto-configuration 내용에 의해 bean 생성을 진행한다.


---


3. 내장 WAS

기존 Spring 웹 프로젝트는 war(Web Application Archive)파일로 배포할 수 있다.
war 파일은 웹 어플리케이션을 압축한 파일로 tomcat과 같은 was에서 돌아갈 수 있는 구조다.

따라서 Spring 프로젝트는 압축된 war 파일을 실행할 was가 필요하다.


그러나 SpringBoot는 Tomcat이나 Jetty같은 내장 서버를 가지고 있기 때문에 jar 파일로 배포할 수 있다.
