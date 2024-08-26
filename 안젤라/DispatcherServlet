# **DispatcherServlet**

## 기술면접 질문

**DispatcherServlet 의 역할에 대해 설명해 주세요.**

- **여러 요청이 들어온다고 가정할 때, DispatcherServlet은 한번에 여러 요청을 모두 받을 수 있나요?**
    
    네, DispatcherServlet은 여러 요청을 동시에 받아 처리할 수 있습니다. 이는 내부적으로 멀티스레딩을 통해 가능해집니다.
    
- **수많은 @Controller 를 DispatcherServlet은 어떻게 구분 할까요?**
    
    DispatcherServlet은 요청 URL과 컨트롤러에 정의된 매핑(@RequestMapping)을 비교하여, 해당 요청을 처리할 컨트롤러 메서드를 찾아서 연결합니다.
    

## 1. 한줄정리

스프링 MVC 기술에서 **HTTP 요청 및 응답과 관련,** 핵심 서블릿으로 웹 요청을 받아 적절한 컨트롤러에 전달하고 결과를 반환하는 역할을 한다.

## **2. 개념**

**프론트 컨트롤러(Front Controller)**라고도 불리는 ***DispatcherServlet***은 **HTTP 프로토콜로 들어오는 모든 요청을 먼저 받아서 적합한 컨트롤러에 위임**(Delegate request)는 역할을 한다.

## **3. 장점**

- 과거 DispatcherServlet이 등장하기 전에는 모든 서블릿에 대해 URL을 매핑하기 위해 관련 설정을 해주어야 하는 번거로움이 존재했다.
- DispatcherServlet이 등장한 이후에는 **DispatcherServlet이 모든 요청을 먼저 받아서 직접 적합한 컨트롤러로 위임**을 해주기 때문에 위와 같은 작업은 필요 없어졌고, 이로 인해 **개발이 굉장히 편리해졌다.**

## **4. 동작 과정**

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/8bd2ac1f-1618-484c-8027-2b799474753f/6505ceae-848e-4330-97f8-32699864ff9a/image.png)

1. DispatcherServlet이 클라이언트의 모든 요청을 받습니다.
2. 요청 정보에 대해 **HandlerMappinng**에 위임하여 처리할 Handler(Controller)을 찾습니다.
3. 2번에서 찾은 Handler을 수행할 수 있는 **HandlerAdapter**를 찾습니다.
4. HandlerAdapter는 Controller에 비즈니스 로직 처리를 호출합니다.
5. Controller는 비즈니스 로직을 수행하고, 처리 결과를 Model에 설정하며 HandlerAdapter에 view name을 반환합니다.
    - 모델을 반환하면 View가 렌더링이 되고, 그렇지 않은 경우(ex. @RestController 등) View가 렌더링이 되지 않습니다.
6. 5번에서 반환받은 view name을 ViewResolver에게 전달하고, ViewResolver은 해당하는 View 객체를 반환합니다.
7. DispatcherServlet은 View에게 Model을 전달하고 화면 표시를 요청합니다.
8. 최종적으로 서버의 응답을 클라이언트에게 반환합니다.

## 5. 계층구조

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/8bd2ac1f-1618-484c-8027-2b799474753f/5e5bf949-39ca-4f57-8422-d3439bc23bcc/image.png)

- [**DispatcherServrlet**](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/DispatcherServlet.html)은 Spring MVC의 핵심 요소로 위 사진과 같은 계층 구조를 가지고 있습니다.
    
    ***(DispatcherServlet -> FrameworkServlet -> HttpServletBean -> HttpServlet -> GenericServlet -> Servlet)***
    

### ***Servlet***

- Servlet은 **웹 서버 내에서 실행되는 자바 프로그램**
- HTTP 통신을 사용하여 **웹 클라이언트로부터 요청을 받고 응답하는 기술**
- 일반적인 서블릿을 상속하는 GenericServlet, HTTP 서블릿을 상속하는 HttpServlet이 존재
- 서블릿의 생명주기 - init(), service(), destory()

### ***GenericServlet***

- **프로토콜에 독립적인 일반 서블릿**.
- 웹에서 사용할 HTTP 서블릿을 작성하려면 HttpServlet을 상속받는다.

### ***HttpServlet***

- **웹 사이트에 적합한 HTTP 서블릿**
- HttpServlet의 하위 클래스는 아래 메서드 중 최소 하나의 메서드는 재정의를 해야 한다.
    - doGet(), doPost(), doPut(), doDelete()
    - init(), destory(): 서블릿 라이프 사이클의 초기화 및 파기
    - getServletInfo(): 서블릿에 대한 정보

### ***HttpServletBean***

- **HttpServlet의 단순 확장 클래스**로 Spring이 구현한 모든 서블릿 유형에 적합한 서블릿 (spring 패키지)

### ***FrameworkServlet***

- **스프링 웹 프레임워크의 기본 서블릿**
- 하위 클래스는 요청을 처리하기 위해 doService() 메서드를 구현해야 한다.

### ***DispatcherServlet***

- HTTP 요청을 처리하는 **중앙 집중형 Dispatcher (Front-Controller)**
- 적절한 어댑터 클래스를 통해 어떠한 workflow와도 사용이 가능할 만큼 매우 유연합니다.
- Mapping, Adapter, Exception 관련 클래스는 다음과 같습니다.
    - HandlerMapping - *RequestMappingHandlerMapping*
    - HandlerAdapter - *RequestMappingHandlerAdapter*
    - HandlerExceptionResolver - *ExceptionHandlerExceptionResolver*

## **6. 내부 코드**

Spring 팀에서 개발한 여러 디자인 패턴이 존재한다.

### 1) 어댑터 패턴

- 어댑터 패턴은 서로 다른 인터페이스를 가진 클래스들이 함께 작동할 수 있도록 중간에서 변환해주는 역할을 한다.
- DispatcherServlet은 다양한 핸들러(예: 컨트롤러)를 처리하기 위해 핸들러 어댑터를 사용하여, 서로 다른 방식으로 작동하는 여러 핸들러들을 통합해 처리할 수 있다.
- **예시**: 게임 콘솔이 다양한 게임 카트리지를 인식하고 실행하기 위해 어댑터를 사용하는 것과 같다.
- **요약**: 어댑터 패턴은 서로 다른 방식으로 동작하는 핸들러를 일관된 방식으로 처리할 수 있도록 돕는다.

### 2) 탬플릿 메서드 패턴

- 탬플릿 메서드 패턴은 어떤 알고리즘의 골격을 정의하고, 세부적인 구현은 서브클래스에서 할 수 있도록 하는 패턴이다.
- DispatcherServlet은 요청 처리의 전체 흐름을 정의하고, 구체적인 핸들러 매핑, 핸들러 호출, 뷰 처리 등은 다른 객체에서 구현한다.
- **예시** : 컴퓨터 게임에서 기본 플레이 방식은 동일하지만, 각 캐릭터가 사용하는 기술이나 무기는 다르게 설정할 수 있는 것과 비슷하다.
- **요약**: 탬플릿 메서드 패턴은 전체 처리 흐름을 고정하고, 세부 구현을 다른 부분에서 처리하도록 한다.

### 3) 전략 패턴

- 전략 패턴은 알고리즘을 캡슐화하여, 런타임 시에 동적으로 알고리즘을 선택할 수 있도록 하는 패턴이다.
- DispatcherServlet은 요청을 처리할 때 사용할 핸들러 매핑 전략이나 뷰 해결 전략을 상황에 따라 선택할 수 있다.
- **예시**: 게임에서 상황에 따라 무기를 바꿔가며 싸우는 것처럼, 특정 상황에 맞는 핸들러를 선택한다.
- **요약**: 전략 패턴은 상황에 맞는 핸들러나 알고리즘을 동적으로 선택하여 사용할 수 있게 한다.

### 4) 컴포지트 패턴

- 컴포지트 패턴은 객체들을 트리 구조로 구성하여, 개별 객체와 그 객체의 그룹을 동일하게 처리할 수 있게 하는 패턴이다.
- DispatcherServlet은 여러 핸들러나 뷰 리졸버를 트리 구조로 관리하고, 이를 통해 요청을 처리한다.
- **예시**: 회사에서 각 부서가 독립적으로 일을 하면서도 전체 회사의 일부분으로 작동하는 것과 비슷하다.
- **요약**: 컴포지트 패턴은 개별 객체와 그 객체들이 모인 그룹을 동일하게 처리하여 복잡한 구조를 단순화한다.
