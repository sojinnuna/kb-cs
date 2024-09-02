Controller)
## 프론트 컨트롤러 도입 - v1
![](https://velog.velcdn.com/images/wxxhyeong/post/042cdda8-0960-494c-a5f7-b3471ee7ea69/image.png)

```java
public interface ControllerV1 {
    void process(HttpServletRequest request, HttpServletResponse response)
        throws ServletException, IOException;
}
```

**회원 목록 컨트롤러**
```java
public class MemberListControllerV1 implements ControllerV1 {
private MemberRepository memberRepository = MemberRepository.getInstance();
    @Override
    public void process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    List<Member> members = memberRepository.findAll();
    request.setAttribute("members", members);
    String viewPath = "/WEB-INF/views/members.jsp";
    RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
    dispatcher.forward(request, response);
    }
}
```
**프론트 컨트롤러**

```java
@WebServlet(name = "frontControllerServletV1", urlPatterns = "/front-controller/v1/*")
public class FrontControllerServletV1 extends HttpServlet {
    private Map<String, ControllerV1> controllerMap = new HashMap<>();
    public FrontControllerServletV1() {
        controllerMap.put("/front-controller/v1/members", new
        MemberListControllerV1());
}

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        System.out.println("FrontControllerServletV1.service");
        String requestURI = request.getRequestURI();
        ControllerV1 controller = controllerMap.get(requestURI);
        if (controller == null) {
            response.setStatus(HttpServletResponse.SC_NOT_FOUND);
            return;
        }
        controller.process(request, response);
    }
}
```

## 프론트 컨트롤러 v2
![](https://velog.velcdn.com/images/wxxhyeong/post/a254c673-2822-4658-9f7a-d6639581f36e/image.png)

```java
public class MyView {
    private String viewPath;
    public MyView(String viewPath) {
        this.viewPath = viewPath;
    }
    
    public void render(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
    RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
    dispatcher.forward(request, response);
    }
}
```
**회원 목록 컨트롤러**
```java
public class MemberListControllerV2 implements ControllerV2 {
    private MemberRepository memberRepository = MemberRepository.getInstance();
    
    @Override
    public MyView process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        List<Member> members = memberRepository.findAll();
        request.setAttribute("members", members);
        return new MyView("/WEB-INF/views/members.jsp");
}
}
```
**프론트 컨트롤러**
```java
protected void service(HttpServletRequest request, HttpServletResponse response)
    throws ServletException, IOException {
    String requestURI = request.getRequestURI();
    ControllerV2 controller = controllerMap.get(requestURI);
    if (controller == null) {
        response.setStatus(HttpServletResponse.SC_NOT_FOUND);
        return;
    }
    
    MyView view = controller.process(request, response);
    view.render(request, response);
    }
}
```
## 프론트 컨트롤러 v3
![](https://velog.velcdn.com/images/wxxhyeong/post/2001102b-19ef-40c2-b24b-a56bc4c91a66/image.png)

- 뷰 이름 중복 제거
- 서블릿 종속성 제거
  <br>

**ModelView**

```java
public class ModelView {
    private String viewName;
    private Map<String, Object> model = new HashMap<>();
    public ModelView(String viewName) {
    this.viewName = viewName;
    }

    public String getViewName() {
        return viewName;
    }

    public void setViewName(String viewName) {
        this.viewName = viewName;
    }
    
    public Map<String, Object> getModel() {
        return model;
    }
    public void setModel(Map<String, Object> model) {
        this.model = model;
    }
}
```
**ControllerV3**
```java
public interface ControllerV3 {
    ModelView process(Map<String, String> paramMap);
}
```
**회원 목록 컨트롤러**
```java
public class MemberListControllerV3 implements ControllerV3 { 
    private MemberRepository memberRepository = MemberRepository.getInstance();
    
    @Override 
    public ModelView process(Map<String, String> paramMap) {
        List<Member> members = memberRepository.findAll();
        ModelView mv = new ModelView("members");
        mv.getModel().put("members", members);
        return mv;
    }
}
```
**프론트 컨트롤러**
```java
@Override
protected void service(HttpServletRequest request, HttpServletResponse 
        response) throws ServletException, IOException {
    String requestURI = request.getRequestURI();
    ControllerV3 controller = controllerMap.get(requestURI);
    
    if (controller == null) {
        response.setStatus(HttpServletResponse.SC_NOT_FOUND);
        return;
    }
    
    Map<String, String> paramMap = createParamMap(request);
    ModelView mv = controller.process(paramMap);
    String viewName = mv.getViewName();
    MyView view = viewResolver(viewName);
    view.render(mv.getModel(), request, response);
}
    
private Map<String, String> createParamMap(HttpServletRequest request) {
    Map<String, String> paramMap = new HashMap<>();
    request.getParameterNames().asIterator()
            .forEachRemaining(paramName -> paramMap.put(paramName, 
                    request.getParameter(paramName)));
    return paramMap;
}

private MyView viewResolver(String viewName) {
    return new MyView("/WEB-INF/views/" + viewName + ".jsp");
}
```

## 프론트 컨트롤러 v4
![](https://velog.velcdn.com/images/wxxhyeong/post/348dbdc1-37c1-46e8-b930-850a5b7c7ed1/image.png)

- 컨트롤러가 `ModelView` 를 반환하지 않고, `ViewName` 만 반환한다.

**ControllerV4**
```java
public interface ControllerV4 {
/**
* @param paramMap
* @param model
* @return viewName
*/
    String process(Map<String, String> paramMap, Map<String, Object> model);
}
```
**회원 목록 컨트롤러**
```java
public class MemberListControllerV4 implements ControllerV4 { 
    private MemberRepository memberRepository = MemberRepository.getInstance();
    
    @Override 
    public String process(Map<String, String> paramMap, Map<String, Object> model) {
        List<Member> members = memberRepository.findAll();
        model.put("members", members);
        return "members";
    }
}
```
**프론트 컨트롤러**
```java
@Override
protected void service(HttpServletRequest request, HttpServletResponse 
        response) 
        throws ServletException, IOException {
    String requestURI = request.getRequestURI();
    ControllerV4 controller = controllerMap.get(requestURI);
    
    if (controller == null) {
        response.setStatus(HttpServletResponse.SC_NOT_FOUND);
        return;
    }
    Map<String, String> paramMap = createParamMap(request);
    Map<String, Object> model = new HashMap<>(); //추가
    String viewName = controller.process(paramMap, model);
    MyView view = viewResolver(viewName);
    view.render(model, request, response);
}

private Map<String, String> createParamMap(HttpServletRequest request) {
    Map<String, String> paramMap = new HashMap<>();
    request.getParameterNames().asIterator()
            .forEachRemaining(paramName -> paramMap.put(paramName, 
                    request.getParameter(paramName)));
    return paramMap;
}

private MyView viewResolver(String viewName) {
    return new MyView("/WEB-INF/views/" + viewName + ".jsp");
}
```
## 프론트 컨트롤러 V5
![](https://velog.velcdn.com/images/wxxhyeong/post/0cb63bba-fca2-411c-bef6-87e35120d729/image.png)


- **핸들러 어댑터**: 중간에 어댑터 역할을 하는 어댑터가 추가되었는데 이름이 핸들러 어댑터이다. 여기서 어댑터 역할을 해주는 덕분에 다양한 종류의 컨트롤러를 호출할 수 있다.

- **핸들러**: 컨트롤러의 이름을 더 넓은 범위인 핸들러로 변경했다. 그 이유는 이제 어댑터가 있기 때문에 꼭 컨트롤러의 개념 뿐만 아니라 어떠한 것이든 해당하는 종류의 어댑터만 있으면 다 처리할 수 있기 때문이다.

# Dispatcher Servlet
### Dispatcher Servlet
**개념**
- HTTP 프로토콜로 들어오는 모든 요청을 가장 먼저 받아 적합한 컨트롤러에 위임해주는 프론트 컨트롤러

![](https://velog.velcdn.com/images/wxxhyeong/post/b10407f4-b8f6-4f83-81a7-85aa3d987c24/image.png)

1. **핸들러 조회**: 핸들러 매핑을 통해 요청 URL에 매핑된 핸들러(컨트롤러)를 조회한다.

2. **핸들러 어댑터 조회**: 핸들러를 실행할 수 있는 핸들러 어댑터를 조회한다.
3. **핸들러 어댑터 실행**: 핸들러 어댑터를 실행한다.
4. **핸들러 실행**: 핸들러 어댑터가 실제 핸들러를 실행한다.
5. **ModelAndView 반환**: 핸들러 어댑터는 핸들러가 반환하는 정보를 ModelAndView로 **변환**해서 반환한다.
6. **viewResolver 호출**: 뷰 리졸버를 찾고 실행한다.
   JSP의 경우: `InternalResourceViewResolver` 가 자동 등록되고, 사용된다.
7. **View 반환**: 뷰 리졸버는 뷰의 논리 이름을 물리 이름으로 바꾸고, 렌더링 역할을 담당하는 뷰 객체를 반환한다.
   JSP의 경우 `InternalResourceView(JstlView)` 를 반환하는데, 내부에 `forward()` 로직이 있다.
8. **뷰 렌더링**: 뷰를 통해서 뷰를 렌더링 한다