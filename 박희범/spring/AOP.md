# AOP에 대해 설명해 주세요.
#### AOP란?
![image](https://github.com/user-attachments/assets/c7a240a3-19c3-491e-a37a-addc99d2cc56)
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
#### @Aspect 란?
- AspectJ 프로젝트에서 제공하는 어노테이션
- 프록시를 통한 AOP를 가능하게 함
- 포인트컷과 어드바이스로 구성되어 있는 어드바이저를 생성
- 포인트 컷 : 어드바이스가 적용될 지점을 정의  
   `execution(* com.example.service.*.*(..))`란 해당 패키지 내의 모든 클래스의 모든 메서드가 실행될 때 어드바이스를 실행하겠다는 뜻
- 어드바이스 : 실제로 실행되는 행동을 정의하는 코드   
- 어노테이션을 사용해서 시점도 정의할 수 있음(@Around, @After 등등)
- 이 어노테이션를 활용하는 주체는 자동 프록시 생성기이다.
#### 자동 프록시 생성기의 역할
- @Aspect를 어드바이저로 변환하고 저장
- 프록시를 생성
- 어드바이저로 변환되는 과정
  1. 스프링 어플리케이션이 로딩될 때 자동 프록시 생성기를 호출한다. 
  2.  스프링 컨테이너에 등록된 @Aspect 빈을 조회하며 어드바이저 생성
- 프록시 생성 과정
  1. @Bean의 객체 생성
  2. 빈 후처리기가 어드바이저 조회
  3. 프록시 생성
  4. 원본대신 프록시를 스프링 빈으로 등록
## 코드 예시
### 글쓰기 기능

- Service

```java
    @Override
    public Board detail(int boardNo) {
        SqlSession session = sessionFactory.openSession();
        BoardMapper mapper = session.getMapper(BoardMapper.class);
        Board board = mapper.selectById(boardNo);
        if(board == null){
            throw new IllegalArgumentException();
        }
        return board;
    }
```

- Controller

```java
 		@GetMapping("/{boardNo}")
    public ResponseEntity<Board> detail(@PathVariable int boardNo) {
        Board board = boardService.detail(boardNo);
        return ResponseEntity.ok(board);
    }
```

- Advice

```java
@RestControllerAdvice
public class MyControllerAdvice {

    @ExceptionHandler(IllegalArgumentException.class)
    public ResponseEntity IllegalArgumentExceptionHandler(IllegalArgumentException e){
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).build();
    }
}

```

Controller 부분에서 정상적인 케이스에 대한 것만 구성을 하고 비정상적인 것은 Advice에서 처리 

### @Transactional

특정 예외가 발생하면 롤백 설정할 수 있음

- 설정

```java
@EnableTransactionManagement
public class AppConfig{
...
    @Bean
    public PlatformTransactionManager transactionManager(DataSource dataSource) {
        return new DataSourceTransactionManager(dataSource);
    }
}
```

- 적용(Service layer)

```java
    @Override
    @Transactional(rollbackFor = IllegalArgumentException.class)
    public void modify(Board b) {
        SqlSession session = sessionFactory.openSession();
        BoardMapper mapper = session.getMapper(BoardMapper.class);
        int rowCnt = mapper.update(b);
        if(rowCnt == 0){
            throw new IllegalArgumentException();
        }
        if(b.getBoardTitle() == null || b.getBoardTitle().equals("")){
            throw new IllegalArgumentException();
        }
    }
```

service layer의 코드 부분에서 commit, rollback, try ~ catch를 직접 작성하지 않아도 적용됨

- 테스트 결과

![image (1)](https://github.com/user-attachments/assets/bfd5a43b-8f62-41e5-9005-36a5711a551b)

boardTitle의 값을 빈 상태로 PUT 요청을 보내게 되면 400 ERROR를 내보내고, 트랜잭션은 던진 예외를 통해 롤백 것을 확인할 수 있음
