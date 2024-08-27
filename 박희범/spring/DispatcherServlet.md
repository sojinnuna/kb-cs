### 14. DispatcherServlet 의 역할에 대해 설명해 주세요.
![](https://velog.velcdn.com/images/uiurihappy/post/25184886-967d-482f-ac8a-3f56ab4e174d/image.png)
- 모든 요청을 처리하는 중앙 집중식 `servlet`
    > 프론트 컨트롤러 패턴 : 요청 처리의 중앙 관리, 공통 로직의 재사용 => 유지보수성 및 확장성을 크게 향상
- `special bean`에게 요청의 프로세스와 적절한 응답의 렌더링을 위임
    > special bean : 프레임워크 규약을 구현하는 Spring managed Obejct instance(HandlerMapping, HandlerAdapter, ViewResolver etc...)
- 요청을 처리하기 위한 공통 알고리즘을 제공
-  DispatcherServlet은 요청 매핑, 뷰 해석, 예외 처리 등 다양한 작업을 수행
-  여러 구성 요소를 자동으로 탐지하고, 적절한 컴포넌트에 작업을 위임
```java
public class MyWebAppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {

    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class[] { RootConfig.class }; // Root WebApplicationContext 설정
    }

    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class[] { WebConfig.class }; // DispatcherServlet을 위한 WebApplicationContext 설정
    }

    @Override
    protected String[] getServletMappings() {
        return new String[] { "/" }; // 모든 요청을 DispatcherServlet이 처리하도록 설정
    }
}
```

#### Context Hierarchy(컨텍스트 계층 구조)
![](https://docs.spring.io/spring-framework/reference/_images/mvc-context-hierarchy.png)

##### Servlet WebApplicationContext 
- Servlet WebApplicationContext는 Spring MVC의 DispatcherServlet과 연결된 컨텍스트
- 각 서블릿이 고유한 설정과 빈을 가짐
- 서블릿 컨텍스트 내에서만 사용되는 빈들을 정의
- 각 서블릿에 특화된 컨트롤러, 뷰 리졸버, 로컬 서비스 빈 등을 정의
- 생성 시점: 각 DispatcherServlet이 초기화될 때 생성 
- 용도: 각 서블릿에 독립적인 설정이 필요한 경우 사용
- 하나의 웹 애플리케이션 내에서 여러 DispatcherServlet을 사용하면서, 각 서블릿에 다른 설정을 적용해야 하는 경우에 유용함
##### Root WebApplcationContext
- Spring 웹 애플리케이션의 최상위 컨텍스트 
- 보통 애플리케이션의 전반적인 설정과 공통 리소스를 관리
- 인프라스트럭처 빈을 관리(데이터베이스 연결, 서비스 계층 빈, 전역적인 보안 설정 등)
- 웹 애플리케이션 전체에서 공유되는 빈들을 정의
- 생성 시점 : ContextLoaderListener에 의해 서블릿 컨텍스트 초기화 시점에 생성됩니다. 이 컨텍스트는 ServletContext에 바인딩되어 있어, 웹 애플리케이션 전반에서 접근 가능
- 용도 : 여러 DispatcherServlet 인스턴스가 공통적으로 사용하는 빈을 정의하여, 중복 정의를 피하고 애플리케이션의 유지보수성을 높일 수 있음
### 여러 요청이 들어온다고 가정할 때, DispatcherServlet은 한번에 여러 요청을 모두 받을 수 있나요?
네 받을 수 있습니다.   
Dispatcher servlet은 각 요청마다 서로 다른 thread가 할당되게끔 설계되어있습니다.   
정확히는 WAS로 요청이 들어올 때마다 Thread pool에서 thread를 할당하거나 새로운 thread를 생성하여 요청을 처리합니다.   

### 수많은 @Controller를 DispatcherServlet은 어떻게 구분 할까요?
HandlerMapping : 요청을 처리할 수 있는 `handler`를 찾는 작업 수행   
RequestMappingHandlerMapping는 : @RequestMapping을 기반으로 요청과 handler를 매핑   
DispatcherServlet은 요청이 들어오면 HandlerMapping에게 작업을 위임 => handlerMapping은 작업을 수행하고 handler의 정보를 DispatcherServlet에게 반환   
DispatcherServlet은 HandlerAdapter에게 작업을 위임 => HandlerAdapter는 해당 handler를 실행 
이후 리턴받은 것을 DispatcherServlet으로 반환

