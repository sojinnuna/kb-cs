# AOP에 대해 설명해 주세요.
#### AOP란?
AOP는 OOP의 프로그램 구조를 보완하는 개념
- OOP 모듈화의 핵심 단위는 클래스
- AOP의 모듈화의 핵심 단위는 관점(aspect)
- AOP는 관심사 중심의 모듈화가 가능   
- 흩어진 관심사를 하나의 관점으로 묶어 모듈성을 증가
  ex) 트랜잭션, 로깅 기능등에 사용
  - OOP에서의 트랜잭션 처리
  ```java
  void foo(){
    try{
        c.beginTransaction();
        logic();
        c.commit();
    }catch(Exception e){
        c.rollback();
        throw e;
    } 
  }
  ```
  - AOP에서의 트랜잭션 처리
  ```java
  @Transactional
  void foo(){
    logic();
  }
  ```
  `@Transactional`은 내부적으로 AOP proxy를 사용하여 구현된다.
  ![](https://docs.spring.io/spring-framework/docs/5.3.37/reference/html/images/aop-proxy-call.png)
  1. @Transactional이 선언되어있으면 위와 같은 프록시 객체를 생성
  2.  트랜잭션이 시작되고 내부적으로 정상종료, 예외, 설정에 따라 commit과 rollback이 수행됨
### @Aspect는 어떻게 동작하나요?