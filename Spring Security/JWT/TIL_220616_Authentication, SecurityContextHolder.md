# Authentication, SecurityContextHolder

## Authentication

**Session에 저장되는 정보가 Authentication이라 생각하면 된다. Authentication은 역할에 따라 principal, credentials, authorities, details로 구성된다.** 

Authentication은 SecurityContextHolder 내부에 보관되는 SecurityContext에 저장된다.

- **Principal**
    - **식별 된 사용자 정보를 보관**. UserDetails의 인스턴스이다.
    - 시스템에 따라 UserDetails 클래스를 상속하여, 커스텀 한 형태로 유지할 수 있다.
- **Credentials**
    - **주체(사용자)가 올바르다는 것을 증명**하는 자격 증명이다.
    - 보통 비밀번호를 의미하며, AuthenticationManager와 관련된 항목일 수 있다.
- **Authorities**
    - AuthenticationManager가 **설정한 권한**을 의미한다.
    - Authentication 상태에 영향을 주지 않거나 수정할 수 없는 인스턴스를 사용해야 한다.
    

## SecurityContextHolder

![image](https://user-images.githubusercontent.com/74949294/174008733-9367d324-9a6d-4fee-b4a6-4a479d914ef5.png)

- 인증된 사용자의 구체적인 정보를 보관한다
- Spring Security는 SecurityContextHolder가 어떻게 만들어지는지에 대해 신경 쓰지 않는다
- 만약 값을 포함하고 있다면, 현재 인증된 사용자 정보로 사용된다
- 사용자가 인증 되었음을 나타내는 가장 간단한 방법은 SecurityContextHolder를 직접 설정하는 것이다

**SecurityContextHolder는 인증된 사용자 정보 저장소로 기본적으로 ThreadLocal 기반으로 사용하며, Multi thread 환경에서 기존 인증 정보를 sync 해서 사용하기 위해서는 strategy 수정이 필요하다.**

- 메소드 호출 시 인증 정보를 파라미터로 전달 받지 않더라도, SecurityContextHolder에 접근하면 인증된 사용자 정보 확인이 가능하다
- 물론 layer(Controller-Service-Repository)를 넘나들면서 SecurityContextHolder에 접근은 가능하지만, 인증 정보를 Session이라고 생각한다면, Presentation Layer의 역할인 Controller에만 SecurityContextHolder에 접근하고, 나머지 layer는 메소드 파라미터로 전달 받는 것이 바람직해 보인다.
