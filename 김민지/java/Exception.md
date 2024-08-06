## 프로그램의 오류
프로그램은 실행중 어떠한 이유로 에러가 발생하곤 하는데, 실행시점에 따라 `컴파일 에러`와 `런타임 에러`로 나뉘게 된다.

`컴파일 에러`의 경우 프로그램이 컴파일 되는 시점에 타입 오류, 선언되지 않은 변수 등 실행 이전에 개발자가 발견할수 있다.

`런타임 에러`의 경우 컴파일 시점에 오류를 발견하지 못하고, 실행중에 어떤 원인으로 발생하게 된다.

자바에서는 런타임에 발생할 수 있는 프로그램 오류를 에러(error)와 예외(exception)로 구분할수 있다.

`에러`는 메모리 부족이나 스택오버플로우와 같이 복구할 수 없는 오류이고, `예외`는 발생하더라도 수습하여 복구할 수 있는 오류이다.

에러는 발생하면 프로그램의 비정상적 종료를 막을 수 없지만 예외는 개발자가 코드를 미리 작성하여 대비할 수 있다

## 예외 종류

<img width="763" alt="스크린샷 2023-06-18 오후 7 37 23" src="https://github.com/jmxx219/CS-Study/assets/50795805/5c6af5a5-034c-4db7-9668-86d084045517">


<br>

자바의 모든 예외는 `Exception`클래스를 상속받으며 크게 `체크예외(CheckedException)`와 `언체크예외(Unchecked Exception)`로 나뉩니다.

<br>

**CheckedException**
- 컴파일러가 예외처리를 확인하는 Exception 클래스들
- Exception 클래스의 하위 클래스 중 RuntimeException이 아닌 것
- 반드시 에러 처리를 해야하는 특징(try/catch or throw)을 가짐

<br>

**UncheckedException**
- 컴파일러가 에러처리를 확인하지 않는 RuntimeException 클래스들
- RuntimeException의 하위 클래스들
- 실행 중 (Runtime) 발생할 수 있는 예외
- 체크 예외와는 달리 에러 처리를 강제하지 않음
    - 개발자들에 의해 실수로 발생하는 것들로 에러 강제하지 않음

<br>

|                    | CheckedException          | UncheckedException                                                                      |
|--------------------|---------------------------|-----------------------------------------------------------------------------------------|
| 예외 처리 여부           | 반드시 예외 처리                 | 명시적인 처리 강제하지 않음                                                                         |
| 예외 확인 시점           | 컴파일 단계                    | 실행 단계                                                                                   |
| ~~예외발생 시 트랜잭션 처리~~ | ~~roll-back 진행~~   | ~~roll-back 진행X~~                                                                       |
| 대표 예외              | IOException, SQLException | NullPointException, IllegalArgumentException, IndexOutOfBoundException, SystemException |

* **예외발생 시 트랜잭션 처리? 롤백?**
  * 트랜젝션은 00 예외에서는 어떤식으로 처리한다는 규칙이 없고, 단순히 트랜젝션이라고 하면 명확하지 않음.
  * 트랜젝션에는 DB transaction, MessageQueue transaction 등 다양하게 쓰이기 때문에 "일반적으로" 어떠하다 라고 설명 할 수 없음.
  * 해당 개념은 Spring 관점에서 기본적으로 세팅되어 있는 방식이고, 개발자가 상황에 맞게 설정이 가능한 부분이므로 고려할 필요가 없는 부분임.
  * 참고 : https://www.youtube.com/watch?v=_WkMhytqoCc
  <br>
<br>

## 예외처리 전략

예외가 발생하면 프로그램의 장애가 발생하기 때문에 개발자가 예외에 대해 대비해줘야 한다.


**try-catch 블록**
```java
try{
        //예외가 발생될만한 코드
    } catch (FileNotFoundException e){  //FileNotFoundException이 발생했다면

    } catch(IOException e) {            //IOException이 발생했다면

    } catch(Exception e){               //Exception이 발생했다면

    } finally {                         ///어떤 예외가 발생하던 말던 무조건 실행
}
```

<br>

* try 블록
    - 예외가 발생할 수 있는 코드를 감싸는 구문

* catch (예외종류) 블록
    - 예외의 종류 지정하고, 예외가 발생했을 때 실행될 코드 정의
    - 블록은 여러 개 사용할 수 있으며, 여러 종류의 예외를 다르게 처리가능
    - 맨 처음 catch 블록에서 잡히지 않는 예외라면 다음 catch의 예외 검사


* finally 블록
    - 예외발생 여부와 상관없이 공통으로 수행되어야할 코드

<br>

> 상속관계에 있는 예외에선 부모예외가 자식예외 다음(밑)에 존재해야함
> - 부모예외가 먼저 존재한다면 자식 예외에는 존재할 수 없음
>```java
> try{
>        //예외가 발생될만한 코드
>    } catch (FileNotFoundException e){  //FileNotFoundException이 발생했다면
>
>    } catch(Exception e) {              //Exception이 발생했다면
>
>    } catch(IOException e) {            // 부모예외가 무조건 발생하므로 해당 catch문은 실행안됌
>
>    } finally {                         //어떤 예외가 발생하던 말던 무조건 실행
>}
>```


<br>
<br>

## throw와 throws

<br>

**throw**
: 프로그래머가 강제로 예외를 발생시키는 것

- Exception을 발생시킬 때 사용하는 키워드
```java
    public void someMethod() {
    if (someCondition) {
        throw new SomeException("Something went wrong.");
    }
}
```

<br>

**throws**
: 자신을 호출하는 메소드에 예외처리의 책임을 떠넘기는 것

```java
    public class ExceptionExample {
    public static void main(String[] args) {
        try {
            func1();
        } catch (Exception e) {
            System.out.println(e.getMessage());
        }
    }

    public static void func1() throws Exception {
        throw new Exception("예외 발생");
    }
}
```

- 메소드를 정의할 때 사용하며, 해당 메소드에서 발생할 수 있는 Exception을 명시적으로 정의 시 사용
- throws를 통해 잠재적으로 어떤 Exception이 발생될 수 있는지 알 수 있음
- 기본적으로 Checked Exception 전략
- throws 사용 시 호출한 메서드에서 반드시 try-catch구문으로 메서드 호출구문을 감싸줘야함
    - 감싸지 않을 경우, 예외처리하는 부분이 없어 오류처리를 하지 않게되는 것임

<br>
<br>

### 예외처리의 3가지 방법

<br>

**예외복구**
: 예외 상황을 파악하고 문제를 해결해 정상 상태로 돌려놓는 방법

- Exception 발생해도 어플리케이션 정상 동작
- 반복문을 이용해 일정 수만큼 재시도를 하여 예외 복구를 시도, 최대 재시도 횟수 넘길 경우 예외 발생 또는 다른 방법으로 문제해결 시도
- 네트워크 연결 등의 로직에서 유용

```java
final int MAX_RETRY = 100;

public Object someMethod() {
    int maxRetry = MAX_RETRY;
    
    while(maxRetry > 0) {
        try {
            // ...
            return; // 성공시 바로 리턴
        } catch(Exception e) {
            // 예외 발생시 로그를 출력
        } finally {
            // 리소스 반납 및 정리 작업
        }
        
        --maxRetry; // 실패하면 1000번 반복
    }
    
    // 최대 재시도 횟수를 넘기면 직접 예외를 발생
    throw new RetryFailedException();
}
```

<br>

**예외 처리 회피**
: 예외 처리를 직접 담당하지 않고 호출한 쪽으로 던져 회피하는 방법 (throws)

- 호출한 쪽에서 예외를 처리하는 것이 바람직할때 사용하며, 추천되는 방법은 아님
- 해당 로직에서 예외가 발생했을 때 처리하지않고 회피하는 것이 최선의 방법일때만 사용

```java
public void add() throws SQLException {
    try {
        // ... 생략
    } catch(SQLException e) {
    	e.printStackTrace(); // 로그만 출력하고
        throw e; // 다시 날림
}
```

<br>

**예외 전환**
: `예외 처리 회피`와 비슷하게 메서드 밖으로 예외를 던지지만, 그냥 무작정 던지지 않고 적절한 예외로 필터링해서 넘기는 방법

- 조금 더 명확한 의미로 전달되기 위해 적합한 의미를 가진 예외로 변경해서 throws
- 예외 처리를 상위 클래스로 단순하게 퉁치기 위해 포장(wrap) 하는 방법을 뜻하기도 함

```java
// 조금 더 명확한 예외 던짐
public void add(User user) throws DuplicateUserIdException, SQLException {
    try {
        // ...
    } catch(SQLException e) { // SQLException 예외가 발생하면
        if(e.getErrorCode() == MysqlErrorNumbers.ER_DUP_ENTRY) { // 그리고 정확히 어떠한 에러인걸 알았다면
            throw DuplicateUserIdException(); // 상위 클래스가 아닌 정확한 예외클래스를 던진다
        }
        else {
        	throw e;
        }
    }
}
```

```java
// 예외 단순 포장
public void someMethod() throws EJBException {
    try {
        // ...
    }
    catch(NamingException | SQLExceptionne | RemoteException e) { // 상세한 예외가 들어와도
        throw new EJBException(e); // 상위 예외클래스로 퉁쳐서 포장해서 던진다
    }
}
```
<br>
<br>

### 예외처리와 성능

- 예외처리는 비용이 많이드는 작업으로 성능에 영향을 미친다.
    - 예외 발생할때 마다 스택 추적(stack trace) 정보를 생성하고 예외 핸들러를 찾는 과정을 거침          
      → 예외 발생, 처리 작업은 실행시간과 자원 소모
      - `스택 추적`이란?
        * 프로그램이 시작된 시점부터 현재 위치까지의 메서드 호출 목록으로, 예외가 발생할 경우 JVM이 어디서 예외가 발생했는지 알려주는 역할
        * 예외가 발생한 시점부터 시작하여 프로그램이 시작하는 곳까지 출력
        * 하나의 예외를 stack trace에 채우는데 1~5ms이 걸리고, stack trace가 깊을 수록, 더 오랜 시간이 걸린다.
        ```java
        Exception in thread "main" java.lang.NullPointerException
            at hello.jdbc.exception.basic.StackTraceTest$Class3.method3(StackTraceTest.java:23)
            at hello.jdbc.exception.basic.StackTraceTest$Class2.method2(StackTraceTest.java:17)
            at hello.jdbc.exception.basic.StackTraceTest$Class1.method1(StackTraceTest.java:11)
            at hello.jdbc.exception.basic.StackTraceTest.main(StackTraceTest.java:6)
        ```


* 성능을 향상시키고 예외 처리의 부하를 줄이는 방법
    * 특정 예외에 대한 처리는 한 곳에서만 진행
    * catch 진행 시 적절한 처리 진행
        * 단순 로그찍고 다시 throw하는 쓸모없는 처리하지 않기
    * 디버깅용 로그를 남길 땐, 필요한 정보만 가독성있게 로그 남기기 
    * 스택 추적의 비용을 해소하는 방법
      * 예상가능한 exception이 있다면 그 exeption(NumberFormatException) 을 던지지 말고 대신 null을 리턴하는 방식
      * stack trace가 정확하지 않아도 된다면, static final로 예외를 저장해두고 쓸 수도 있다. 자주쓰이는 라이브러리의 메서드거나 엄청나게 많이 변경하지 않아도 되는 경우에 이 방법을 활용
      * stack trace를 채우는 것에 많은 시간을 보내므로 Throwable의 fillInStackStrace 오버라이딩을 통해 그 과정을 없앨 수 있음
      * 참고 : https://january-diary.tistory.com/entry/JAVA-%EC%98%88%EC%99%B8%EC%B2%98%EB%A6%AC%EC%9D%98-%EB%B9%84%EC%9A%A9#2._How_to_avoid_the_stack_trac_penalty?