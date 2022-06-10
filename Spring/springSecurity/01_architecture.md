# Spring Security


---


### 정의

: 회원 관리에 인증(Authentication)과 인가(Authorization) 처리를 위한 스프링 하위의 프레임워크

- Spring Security는 인증과 권한에 대한 부분을 Filter 흐름에 따라 처리
- Filter는 Dispatcher Servlet으로 가기 전에 적용
- Interceptor는 Dispatcher와 Controller 사이에 위치한다는 점에서 적용 시기의 차이가 있음


인증 관련 architecture
<img width="947" alt="securityarchitecture" src="https://user-images.githubusercontent.com/85109184/173053209-f90ddf6f-bdcd-431f-9f65-a5209fee917d.png">


---


### 인증(Authentication)과 인가(Authorization)

- 인증(Authentication) : 해당 사용자가 본인이 맞는지 확인하는 절차
- 인가(Authorization) : 인증된 사용자가 요청한 자원에 접근 가능한지 결정하는 절차
- "인증"    &nbsp; -- 인증 성공 후 -- > &nbsp; "인가"
- 인가 과정에서 해당 리소스에 대한 접근 권한이 있는지 확인
- Principal(접근 주체, 아이디) : 보호받는 Resource에 접근하는 대상
- Credential(비밀번호) : Resource에 접근하는 대상의 비밀번호


### Spring Security 모듈

<img width="517" alt="SecurityModules" src="https://user-images.githubusercontent.com/85109184/173054375-893c6c81-8241-4d14-a3d5-bc8ac0406242.png">

- SecurityContextHolder
  - 보안 주체의 세부 정보를 포함하여 응용프로그램의 현재 보안 컨텍스트에 대한 세부 정보 저장
  - SecurityContextHolder.MODE_INHERITABLETHREADLOCAL
  - SecurityContextHolder.MODE_THREADLOCAL
  - 위 방법을 제공


- SecurityContext
  - Authentication을 보관하는 역할
  - SecurityContext를 통해 Authentication 객체를 꺼냄


- Authentication
  - 현재 접근 주체의 정보와 권함을 담는 인터페이스
  - Authentication 객체는 Security Context에 저장
  - SecurityContextHolder를 통해 SecurityContext에 접근