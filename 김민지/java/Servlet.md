# Servlet

## Servlet(서블릿)이란?

<img width="500px" src="https://github.com/jmxx219/CS-Study/assets/64017307/28863883-c348-4ee1-b05c-8b78363d59e7"/>


- 클라이언트의 요청을 처리하고 그 결과를 반환하는 Servlet클래스의 구현 규칙을 지킨 자바 웹 프로그래밍 기술
- 동적 웹페이지를 만들 때 사용되는 **자바 기반의 웹 애플리케이션** 프로그래밍 기술
- 서블릿은 서버에서 실행되다 웹 클라이언트가 요청을 하면, 해당 기능을 수행 후 웹 클라이언트에 결과 전송
- 서블릿의 생성은 Java Servlet API를 통해 구현

<br>
<br>

## Servlet(서블릿)의 특징
- 클라이언트의 Request에 대해 동적으로 작동하는 웹 애플리케이션 컴포넌트
- 주로 HTML을 사용하여 Response 함 (XML, JSON, 일반 텍스트 모두 가능)
- HTML 변경 시 Servlet을 재 컴파일해야 하는 단점이 있다
- JAVA의 스레드를 활용하여 동작
- HTTP 프로토콜 서비스를 지원하는 javax.servlet.http.HttpServlet 클래스를 상속받음
- Spring MVC에서 컨트롤러로 사용될 수 있음(DispatcherServlet)
- 보안기능을 적용하기 쉬움

<br>  
<br>

## Servlet의 동작과정

<img width="500px" src="https://github.com/jmxx219/CS-Study/assets/64017307/60a686cb-a46d-4c4d-9ba4-315ba9bfd110"/>

- 클라이언트가 웹서버에 요청하면 웹 서버는 JAR or WAS에 위임
- WAS는 요청에 해당하는 서블릿을 실행하고 그 서블릿은 요청에 대한 기능을 수행 후 결과를 반환하고 클라이언트에 전송
- Tomcat, Jetty같은 웹 애플리케이션은 서블릿의 라이프사이클을 담당함

```
1. 클라이언트 요청
2. HttpServletRequest, HttpServletResponse 객체 생성
3. Web.xml이 어느 서블릿에 대해 요청한 것인지 탐색
4. 해당하는 서블릿에서 service() 메소드 호출 
5. doGet() 또는 doPost() 호출 
6. 동적 페이지 생성 후 ServletResponse 객체에 응답 전송
7. HttpServletRequest, HttpServletResponse 객체 소멸
```

<br>

### 정적 웹 페이지와 서블릿 요청의 비교
- `정적` 웹 페이지 :
    - 브라우저가 요청하면 WEB에서는 저장된 페이지를 제공함
- `동적` 웹 페이지 :
    - 과거 정적 웹페이지에서 업그레이드 되면서 연산기능을 가지게 됨
    - 과거에 서버라고 불리던 것은 WEB과 WAS(Web Application Server)로 나뉘게 됨
    - **WAS**는 연산결과를 WEB서버에 제공, **WEB서버**는 정적 페이지를 만들어 사용자에게 전달함
      -> 이때 WAS에 연산을 당당하는 것이 **'Servlet'** 이다.

  `서블릿은 WAS안에 있는 서블릿 컨테이너 or 웹 컨테이너 공간에서 활동함.`

<br>
<br>

## 서블릿의 생명주기

<img width="500px" src="https://velog.velcdn.com/images%2Fcorone_hi%2Fpost%2Fd118c7e4-af36-444d-ad13-f60cc7d20814%2Fimage.png"/>

서블릿의 생명주기는 서블릿 로딩 ~ 소멸될때까지의 수명 의미

1. 서블릿 로딩
2. 서블릿 인스턴스 생성
3. init() 메소드 호출
4. 각 클라이언트 요청에 대해 반복적으로 service() 메소드를 호출
5. 소멸하기 위해 destroy() 메소드 호출

<br>

### 주요 메소드

- init()
    - 서블릿의 일생동안 단 한번 호출
    - 디폴트 생성자를 이용하여 서블릿 객체를 생성하고 init() 메소드를 사용하여 서블릿 객체를 초기화 할때 사용함
        ```java
        public void init(ServletConfig config) throws ServletException
        ```
- service()
    - 요청이 있을때 마다 호출
    - Http 메소드를 보고 doGet() or doPost() 호출할지 결정
        ```java
        public void service(ServletRequest request, ServletResponse response) throws ServletException, IOException
        ```
- destroy()
    - 서블릿의 일생동안 단 한번 호출
    - 서블릿이 소멸할때 호출하며, 자원해제와 관련된 작업 수행
      ```java
      public void destroy()
      ```
<br>
<br>

## 서블릿 컨테이너

### 개념
- 구현되어 있는 서블릿 클래스의 규칙에 맞게 `서블릿을 담고 관리해주는 컨테이너`

- 클라이언트의 요청(Request)을 받고 응답(Response)할 수 있게, 웹 서버와 소켓으로 통신하며 대표적으로 **톰캣**(Tomcat)이 있음
    - 클라이언트에서 요청을 하면 컨테이너는 HttpServletRequest, HttpServletResponse 두 객체를 생성하여 post, get여부에 따라 동적인 페이지를 생성하여 응답을 보냄
- 톰캣은 웹 서버와 통신하여 JSP(Java Server Page)와 Servlet이 작동하는 환경을 제공

<br>

### 역할
- `웹 서버와 통신지원`
    - 일반적으로 소켓 만들고 listen, accept등을 하고 웹 서버와 통신하지만, 서블릿 컨테이너는 이러한 기능을 API로 제공하여 생략할 수 있게 함
    - 개발자가 서블릿에 구현해야할 비즈니스 로직에만 초점을 둘 수 있게 지원
- `서블릿 생명주기 관리`
    - 서블릿 컨테이너는 서블릿 클래스 로딩하여 인스턴스화하고 init() 메소드를 호출, 적절한 서블릿 메소드 등을 호출하게 도와줌
    - 수명이 다 된 서블릿을 적절하게 가비지 콜텍터를 호출하여 필요없는 자원 낭비를 막아줌
- `멀티스레드 지원 및 관리`
    - 서블릿 컨테이너는 Request가 올때마다 **새로운 자바 스레드를 하나씩 생성**하고, 이후 HTTP 서비스 메소드를 실행하고 난 후 **자동으로 소멸**함
    - 따라서 동시에 여러 요청이 들어와도 멀티쓰레딩 환경에서 동시다발적인 작업을 관리할 수 있음
- `선언적인 보안 관리`
    - 서블릿 컨테이너는 보안 관련 기능을 제공하여 많은 보안 작업을 자동으로 처리해줌
    - 인증(Authentication), 권한 부여(Authorization), SSL/TLS 지원
    - 일반적으로 보안관리는 XML 배포 서술자에다가 기록하므로, 보안에 대해 수정할 일이 생겨도 자바 소스 코드를 수정하여 다시 컴파일 하지 않아도 보안관리가 가능
 
<br>
<br>
<br>
