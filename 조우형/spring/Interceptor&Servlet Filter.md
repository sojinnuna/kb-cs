### Cross-Cutting Concerns (횡단 관심사)

# 서블릿 필터
**배경**
로그인한 사용자만 상품 관리 페이지에 들어갈 수 있어야 한다. 로그인 하지 않은 사용자가 다음 URL을 직접 호출하면 상품 관리 화면에 들어갈 수 있다는 문제가 있다.
`http://localhost:8080/items`
상품 관리 컨트롤러에서 로그인 여부를 체크하는 로직을 하나하나 작성하면 되겠지만 번거롭다.

스프링의 AOP로도 해결할 수 있지만, 웹과 관련된 공통 관심사는 지금부터 설명할 서블릿 필터 또는 스프링 인터셉터를 사용하는 것이 좋다.

웹과 관련된 공통 관심사를 처리할 때는 HTTP의 헤더나 URL의 정보들이 필요한데, 서블릿 필터나 스프링 인터셉터는 `HttpServletRequest` 를 제공한다.

**필터 흐름**
`HTTP 요청 -> WAS -> 필터 -> 서블릿 -> 컨트롤러`

**필터 제한**
```
HTTP 요청 -> WAS -> 필터 -> 서블릿 -> 컨트롤러 // 로그인 사용자
HTTP 요청 -> WAS -> 필터(적절하지 않은 요청이라 판단, 서블릿 호출X) //비 로그인 사용자
```
**필터 체인**
`HTTP 요청 -> WAS -> 필터1 -> 필터2 -> 필터3 -> 서블릿 -> 컨트롤러`

**필터 인터페이스**
```java
public interface Filter {
	public default void init(FilterConfig filterConfig) throws ServletException
    {}
	
    public void doFilter(ServletRequest request, ServletResponse 
    	response, FilterChain chain) throws IOException, ServletException;

	public default void destroy() {}
}
```
- **init()** : 필터 초기화 메서드, 서블릿 컨테이너가 생성될 때 호출된다.
- **doFilter()** : 고객의 요청이 올 때 마다 해당 메서드가 호출된다. 필터의 로직을 구현하면 된다.
- **destroy()** : 필터 종료 메서드, 서블릿 컨테이너가 종룔될 때 호출된다.

**LogFilter - 로그 필터**
```java
@Slf4j
public class LogFilter implements Filter {
	@Override
	public void init(FilterConfig filterConfig) throws ServletException {
    	log.info("log filter init");
	}
	@Override
	public void doFilter(ServletRequest request, ServletResponse response, 
    	FilterChain chain) throws IOException, ServletException {

		HttpServletRequest httpRequest = (HttpServletRequest) request;
		String requestURI = httpRequest.getRequestURI();
		String uuid = UUID.randomUUID().toString();
	
    	try {
    		log.info("REQUEST [{}][{}]", uuid, requestURI);
        	chain.doFilter(request, response);
     	} catch (Exception e) {
     		throw e;
        	} 
     	finally {
     		log.info("RESPONSE [{}][{}]", uuid, requestURI);
        	}
		}
	
    @Override
    public void destroy() {
    	log.info("log filter destroy");
        }
    }
```
**`chain.doFilter(request, response);`**  다음 필터가 있으면 필터를 호출하고, 필터가 없으면 서블릿을 호출한다. 만약 이 로직을 호출하지 않으면 다음 단계로 진행되지 않는다.

**WebConfig - 필터 설정**
```java
@Configuration
public class WebConfig {

	@Bean
	public FilterRegistrationBean logFilter() {
		FilterRegistrationBean<Filter> filterRegistrationBean = 
        new FilterRegistrationBean<>();
	filterRegistrationBean.setFilter(new LogFilter());
	filterRegistrationBean.setOrder(1);
	filterRegistrationBean.addUrlPatterns("/*");
	
    return filterRegistrationBean;
	}
}
```

# 스프링 인터셉터(Interceptor)
서블릿 필터가 서블릿이 제공하는 기술이라면, 스프링 인터셉터는 스프링 MVC가 제공하는 기술이다. 둘다 웹과 관련된 공통 관심 사항을 처리하지만, 적용되는 순서와 범위, 그리고 사용방법이 다르다.

`HTTP 요청 -> WAS -> 필터 -> 서블릿 -> 스프링 인터셉터 -> 컨트롤러`
- 스프링 인터셉터는 디스패처 서블릿과 컨트롤러 사이에서 컨트롤러 호출 직전에 호출 된다.
- 스프링 인터셉터는 스프링 MVC가 제공하는 기능이기 때문에 결국 디스패처 서블릿 이후에 등장하게 된다. 스프링 MVC의 시작점이 디스패처 서블릿이라고 생각해보면 이해가 될 것이다.

- 스프링 인터셉터에도 URL 패턴을 적용할 수 있는데, 서블릿 URL 패턴과는 다르고, 매우 정밀하게 설정할 수 있
  다.

```HTTP 요청 -> WAS -> 필터 -> 서블릿 -> 스프링 인터셉터 -> 컨트롤러 // 로그인 사용자
HTTP 요청 -> WAS -> 필터 -> 서블릿 -> 스프링 인터셉터(적절하지 않은 요청이라 판단, 컨트롤러 호출X) // 비로그인 사용자
```

**스프링 인터셉터 체인**
`HTTP 요청 -> WAS -> 필터 -> 서블릿 -> 인터셉터1 -> 인터셉터2 -> 컨트롤러`

- 서블릿 필터의 경우 단순하게 `doFilter()` 하나만 제공된다. 인터셉터는 컨트롤러 호출 전( `preHandle` ), 호출 후( `postHandle` ), 요청 완료 이후( `afterCompletion` )와 같이 단계적으로 잘 세분화 되어 있다.
- 서블릿 필터의 경우 단순히 `request` , `response` 만 제공했지만, 인터셉터는 어떤 컨트롤러( `handler` )가 호출되는지 호출 정보도 받을 수 있다. 그리고 어떤 `modelAndView` 가 반환되는지 응답 정보도 받을 수 있다.

![](https://velog.velcdn.com/images/wxxhyeong/post/26eea2bb-9a30-4d20-898f-314c2aaa5ecb/image.png)

**정상 흐름**
- `preHandle` : 컨트롤러 호출 전에 호출된다. (더 정확히는 핸들러 어댑터 호출 전에 호출된다.)

    - `preHandle` 의 응답값이 `true` 이면 다음으로 진행하고, `false` 이면 더는 진행하지 않는다. `false` 인 경우 나머지 인터셉터는 물론이고, 핸들러 어댑터도 호출되지 않는다. 그림에서 1번에서 끝이 나버린다.
- `postHandle` : 컨트롤러 호출 후에 호출된다. (더 정확히는 핸들러 어댑터 호출 후에 호출된다.)
- `afterCompletion` : 뷰가 렌더링 된 이후에 호출된다.

**스프링 인터셉터 예외 상황**
![](https://velog.velcdn.com/images/wxxhyeong/post/f4368980-a50f-4b1e-822e-cf3131d3fea7/image.png)

**예외가 발생시**
- `preHandle` : 컨트롤러 호출 전에 호출된다.
- `postHandle` : 컨트롤러에서 예외가 발생하면 `postHandle` 은 호출되지 않는다.
- `afterCompletion` : `afterCompletion` 은 항상 호출된다. 이 경우 예외( `ex` )를 파라미터로 받아서 어떤 예
  외가 발생했는지 로그로 출력할 수 있다.

**afterCompletion은 예외가 발생해도 호출된다.**
- 예외가 발생하면 `postHandle()` 는 호출되지 않으므로 예외와 무관하게 공통 처리를 하려면 `afterCompletion()` 을 사용해야 한다.
- 예외가 발생하면 `afterCompletion()` 에 예외 정보( `ex` )를 포함해서 호출된다.

**필터**
- 보안 및 인증/인가 관련 작업
- 모든 요청에 대한 로깅 또는 검사
- 이미지/데이터 압축 및 문자열 인코딩
- Spring과 분리되어야 하는 기능

**인터셉터**
- 세부적인 보안 및 인증/인가 공통 작업
- API 호출에 대한 로깅 또는 검사
- Controller로 넘겨주는 정보(데이터)의 가공

# 필터(Filter)와 인터셉터(Interceptor)의 차이

### 필터(Filter)

**동작 레벨**: 필터는 서블릿 레벨에서 동작하며, 스프링 MVC 이전의 요청 처리 과정에서 적용됩니다. 즉, 톰캣 같은 웹 컨테이너가 요청을 받자마자 필터가 가장 먼저 호출됩니다.

**주요 용도**:
- **요청 및 응답 변환**: 요청이나 응답의 내용을 변환하거나 조작할 때 사용됩니다. 예를 들어, 입력 스트림을 읽거나 응답 헤더를 변경할 수 있습니다.
- **보안 및 인증 처리**: 공통적인 보안 처리(예: CORS 처리, IP 차단 등), 인코딩 설정, 로깅 등 요청이 컨트롤러에 도달하기 전에 반드시 처리되어야 하는 작업에 적합합니다.
- **비동기 처리**: 필터는 서블릿 스펙에 따라 동작하므로, 비동기 처리나 스트리밍 작업에도 잘 맞습니다.

### 인터셉터(Interceptor)

**동작 레벨**: 인터셉터는 스프링 MVC 내부에서 동작합니다. DispatcherServlet이 요청을 처리할 때, 컨트롤러에 요청이 도달하기 직전과 응답이 완료된 직후에 인터셉터가 호출됩니다.

**주요 용도**:
- **로깅 및 감사**: 요청이 컨트롤러에 도달하기 직전 또는 응답 직후에 필요한 작업을 처리할 수 있습니다.
- **권한 체크**: 특정 요청에 대한 권한 검사를 수행하거나, 컨트롤러가 실행되기 전에 특정 조건을 확인할 때 유용합니다.
- **뷰 관련 처리**: 요청 처리 결과를 가로채어 뷰(view) 렌더링 전에 데이터를 추가하거나 변경할 때 적합합니다.