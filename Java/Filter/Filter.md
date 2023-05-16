# Filter

## Filter 필터

![Filter는 기본적으로 Spring Context에 속해있지 않고, Dispatcher Servlet 이전에 처리된다.](Filter%2023ce2358eb50458eb3dc4e7d31ea2d29/Untitled.png)

Filter는 기본적으로 Spring Context에 속해있지 않고, Dispatcher Servlet 이전에 처리된다.

![Dispatcher Servlet 전 후로, Filter와 Interceptor가 핸들링된다.](Filter%2023ce2358eb50458eb3dc4e7d31ea2d29/Untitled%201.png)

Dispatcher Servlet 전 후로, Filter와 Interceptor가 핸들링된다.

필터는 Dispatcher Servlet에 요청이 전달되기 전, 후에 url 패턴에 맞는 모든 요청에 대해 부가 작업을 처리할 수 있는 기능을 제공한다.

필터는 Web Context에서, 즉 톰캣과 같은 웹 컨테이너(서블릿 컨테이너)에서 관리가 된다. Dispatcher Servlet이 존재하는 Spring Context 즉 스프링 컨테이너에서 관리되지 않는다. 

단, 스프링 빈으로 등록은 된다. 이유는 하단에서 다룬다.

필터는 아래와 같은 기능을 수행하기 좋다.

- 로그인여부나 권한 검사와 같은 인증 기능
- 요청이나 응답에 대한 로그(기록) 기능
- 오류 처리 기능
- 데이터 압축이나 변환 기능
- 인코딩 처리 기능

필터를 사용하려면, Filter 인터페이스를 구현해야한다. ( implements )

Filter 인터페이스는 아래와 같은 3개의 메소드를 가지고 있다.

```java
public interface Filter {

    public default void init(FilterConfig filterConfig) throws ServletException {}

    public void doFilter(ServletRequest request, ServletResponse response,
            FilterChain chain) throws IOException, ServletException;

    public default void destroy() {}
}
```

### init 메소드

필터 객체를 초기화하고 서비스에 추가하기 위한 메소드이다. 

웹 컨테이너(서블릿 컨테이너)가, 필터 객체가 생성 및 초기화 될 때에 최초에 1번 init 메소드를 호출하여 필터 객체를 초기화하면, 이후의 요청들은 doFilter를 통해 처리된다.

> Called by the web container to indicate to a filter that it is being placed into service. The servlet container calls the init method exactly once after instantiating the filter. The init method must complete successfully before the filter is asked to do any filtering work.

웹 컨테이너가 서비스를 시작하고 있음을 필터에 알리기 위해 호출됩니다. 서블릿 컨테이너는 필터를 인스턴스화한 후 정확히 한 번 init 메서드를 호출합니다. 필터가 필터링 작업을 수행하도록 요청받기 전에 init 메서드가 성공적으로 완료되어야 합니다.
> 

**FilterConfig**

필터가 초기화 되는 시점에 호출되는 init() 메서드의 매개변수로 전달되는 객체이다.

> The configuration information associated with the filter instance being initialised.
초기화 중인 필터 인스턴스와 관련된 구성 정보
> 

FilterConfig는 아래와 같은 메소드를 사용할 수 있다.

- `getFilterName()` : 필터 등록시 지정한 필터명을 리턴
- `getInitParameter(String name)` : 주어진 이름에 해당하는 파라미터를 설정 파일에서 불러와 리턴
- `getServletContext()` : 필터가 속해있는 웹 어플리케이션의 ServletContext를 리턴
- `getInitParameterNames()` : 필터에 설정되어 있는 초기화 설정값들의 설정명들을 Enumeration 으로 리턴

### doFilter 메소드

url-pattern에 맞는 모든 HTTP 요청이 Dispatcher Servlet으로 전달되기 전에 웹 컨테이너에 의해 실행되는 메소드이다. 

해당 필터에서 필요한 작업을 마치고, FilterChain의 doFilter를 호출하고, FilterChain에 따라 다음에 존재하는 필터로 요청을 전달한다.

> The doFilter method of the Filter is called by the container each time a request/response pair is passed through the chain due to a client request for a resource at the end of the chain. The FilterChain passed in to this method allows the Filter to pass on the request and response to the next entity in the chain.
필터의 doFilter 메서드는 체인 끝에 있는 리소스에 대한 클라이언트 요청으로 인해 요청/응답 쌍이 체인을 통과할 때마다 컨테이너에 의해 호출됩니다. 필터 체인이 이 메서드에 전달되면 필터가 요청 및 응답을 체인의 다음 엔티티로 전달할 수 있습니다.
> 

**FilterChain**

![Untitled](Filter%2023ce2358eb50458eb3dc4e7d31ea2d29/Untitled%202.png)

여러 개의 Filter들이 사슬 처럼 연결되어 있는 것이다.

각 필터에서는 처리를 마치고, FilterChain의 doFilter를 호출해, 그 다음 순서의 Filter로 요청을 전달한다. 만약 더이상 수행할 Filter가 없다면 Servlet이 최종적으로 요청을 전달받는다. 

FilterChain 내의 속한 Filter는 Filter 타입의 @Beans에 @Order를 붙이거나 Orderd를 구현하여 순서를 정할 수 있다.

### destroy 메소드

필터 객체를 서비스에서 제거하고 사용하는 자원을 반환하기 위한 메소드이다. 

웹 컨테이너에 의해 최종 1번 호출되며, 이후에는 이제 doFilter에 의해 처리되지 않는다.

> Called by the web container to indicate to a filter that it is being taken out of service. This method is only called once all threads within the filter's doFilter method have exited or after a timeout period has passed. After the web container calls this method, it will not call the doFilter method again on this instance of the filter.
웹 컨테이너가 필터에 의해 호출되어 필터가 서비스를 중단하고 있음을 나타냅니다. 이 메서드는 필터의 doFilter 메서드 내의 모든 스레드가 종료되었거나 시간 초과 기간이 경과한 후에만 호출됩니다. 웹 컨테이너가 이 메서드를 호출한 후 이 필터 인스턴스에서 doFilter 메서드를 다시 호출하지 않습니다.
> 

## 서블릿 필터가 스프링 빈으로 등록 가능한 이유

서블릿 컨테이너에서 관리되는 필터는, 스프링 범위가 아닌 서블릿 범위에서 관리된다.

따라서, 과거에는 필터가 스프링 컨테이너에 의해 관리되지 않았고, 빈으로도 등록될 수 없고, 다른 빈을 주입받을 수도 없었다.

### Spring : DelegatingFilterProxy

하지만 필터에서도 DI와 같은 스프링 기술을 필요로 하는 상황이 발생하면서, 스프링 개발자는 필터도 스프링 빈을 주입받을 수 있도록 대안을 마련하였는데, 그것이 바로 `DelegatingFilterProxy`이다.

Spring 1.2부터 `DelegatingFilterProxy`가 나오면서 서블릿 필터(Servlet Filter) 역시 스프링에서 관리가 가능해졌다.

`DelegatingFilterProxy`는 **서블릿 컨테이너에서 관리되는** 프록시용 필터로서, 우리가 만든 필터(커스텀 필터)를 가지고 있다. 

커스텀 필터는 스프링 컨테이너의 빈으로 등록되는데, 요청이 오면 `DelegatingFilterProxy`가 요청을 받아서 스프링 빈으로 등록된 커스텀 필터로 요청을 위임한다.

아래는 동작 원리이다.

1. Filter 구현체가 스프링 빈으로 등록됨
2. ServletContext가 Filter 구현체를 갖는 DelegatingFilterProxy를 생성함
3. **ServletContext**가 DelegatingFilterProxy를 서블릿 컨테이너에 필터로 등록함
4. 요청이 오면 DelegatingFilterProxy가 (커스텀)필터 구현체에게 요청을 위임하여 (커스텀)필터 처리를 진행함
    1. (커스텀) 필터는 스프링 컨테이너에 빈으로 먼저 등록된 후에, **DelegatingFilterProxy에 감싸져** 서블릿 컨테이너로 등록이 되는 것이다.
    
    ```java
    // Spring 기준 코드 ( Spring Boot 아님 )
    public class MyWebApplicationInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {
    
       public void onStartup(ServletContext servletContext) throws ServletException {
          super.onStartup(servletContext);
          servletContext.addFilter("myFilter", DelegatingFilterProxy.class);
    			// myFilter라는 빈을 찾아서 DelegatingFilterProxy로 감싸서 Servlet Container로 등록된다.
    			// Servlet Containter는 myFilter가 아닌 DelegatingFilterProxy를 통해 myFilter에 요청을 위임하게 된다.
       }
    }
    ```
    

따라서, 커스텀 필터도 스프링 빈으로 등록되며, 스프링 컨테이너에서 관리되기 때문에 빈 등록뿐만 아니라 빈의 주입까지 가능하다.

### Spring Boot : DelegatingFilterProxy도 필요가 없다.

SpringBoot가 내장 웹서버를 지원하면서, 톰캣과 같은 서블릿 컨테이너까지 SpringBoot가 제어가능하다.

따라서, **SpringBoot에서는 ServletContext에 필터(Filter) 빈을 DelegatingFilterProxy로 감싸서 등록하지 않아도 된다.** 

SpringBoot가 서블릿 필터의 구현체 **빈을 찾으면 DelegatingFilterProxy 없이 바로 필터 체인(Filter Chain)에 필터를 등록해주기 때문이다.**

## 다음 글

인터셉터, 인터셉터와 AOP의 비교 그리고 필터와 인터셉터의 비교

## 레퍼런스

[https://javaee.github.io/javaee-spec/javadocs/](https://javaee.github.io/javaee-spec/javadocs/)