# Spring Container & DI

### Spring Bean Scopes

- 개요 : Bean 정의를 통해 객체에 다양한 종속성 및 설정 값을 주입할 수 있을 뿐만 아니라 객체의 범위(scope)를 정의
- 스프링은 모든 Bean을 singleton으로 생성하여 관리
- 애플리케이션 구동 시, JVM 안에서 스프링이 bean마다 하나의 객체를 생성하는 것을 의미


- singleton
  - 스프링 컨테이너에 한 개의 빈 인스턴스만 생성(default)
- prototype
  - 스프링 컨테이너에서 빈을 요청할 때마다 새로운 빈 인스턴스 생성
- request
  - HTTP 요청마다 빈 객체를 생성, WebApplicationContext 에서만 적용
- session
  - HTTP 세션마다 빈 객체를 생성, WebApplicationContext 에서만 적용
- application
  - 웹 응용 프로그램에 대하여 하나의 객체만 생성 WebApplicationContext 에서만 적용
- websocket
  - WebSocket의 전체 수명주기 동안 단일 인스턴스가 생성, 웹 인식 Spring 에서만 유효


---


### Annotation 기반 Bean 설정
1) Field Injection
   - ex)
   ```java
   public class Sample {
        @Autowired
        private Example example;
   }
    ```
   
2) Setter Injection
    - ex)
   ```java
   public class Sample {
        private Example example;
        @Autowired
        public void setExample(Example example) {
            this.example = example;
        }
   }
    ```
   
3) Constructor Injection
   ```java
   public class Sample {
        private Example example;
        @Autowired
        public Example(Example example) {
            this.example = example;
        }
   }
    ```
   

- Dependency Injection Annotation
  - @Autowired
    - 주입하려는 객체의 타입이 일치하는 객체를 자동 주입
    - 반드시 기본 생성자 정의
  - @Qualifier
    - ex)

    ```java
    @Autowired
    @Qualifier("beanName")
    ```
    
  - @Resource
    - 주입하려는 객체의 이름(id)이 일치하는 객체를 자동 주입
    - 반드시 기본 생성자 정의
    - ex)
    ```java
    @Resource   // name 속성 지정하지 않은 경우 필드명과 동일한 빈 주입
    
    @Resource(name="beanName")
    ```
  - @Inject
  - @Named
  - @Required
  - @Value
  - @Primary


- Stereotype Annotation
  - @Component
    - 스테레오타입 어노테이션의 조상
  - @Controller
  - @RestController
    - @Controller + @ResponseBody
  - @Service
  - @Repository