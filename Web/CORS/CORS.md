# CORS

## CORS와 SOP

---

### 오리진

프로토콜과 호스트 이름, 포트의 조합

### SOP

Same-Origin-Policy

동일한 오리진에서만 리소스를 공유할 수 있다는 규칙

즉, 동일 출처(Same-Origin) 서버에 있는 리소스는 자유로이 가져올수 있지만, 다른 출처(Cross-Origin) 서버에 있는 이미지나 유튜브 영상 같은 리소스는 상호작용이 불가능하다

### CORS

Cross-Origin-Resource-Sharing

서버가 웹 브라우저에서 리소스를 로드할 때, 다른 오리진을 통해 로드하지 못하게 하는 HTTP 헤더 기반 메커니즘

### SOP가 필요한 이유

1. 사용자가 악성 사이트에 접속한다.
2. 이때 해커가 몰래 심어놓은 악의적인 자바스크립트가 실행되어, 사용자가 모르는 사이에 어느 포털 사이트에 요청을 보낸다.
3. 그럼 포털 사이트에서 해당 브라우저의 쿠키를 이용하여 로그인을 하거나 등 상호작용에 따른 개인 정보를 응답 값을 받은뒤, 사이트에서 해커 서버(hacker.example.com)로 재차 보낸다.
4. 이외에도 사용자가 접속중인 내부망의 아이피와 포트를 가져오거나, 해커가 사용자 브라우저를 프록시처럼 악용할 수도 있다.

## 브라우저의 CORS 기본 동작

---

1. 클라이언트에서 HTTP 요청의 헤더에 Origin을 담아 전달
    1. 기본적으로 웹은 HTTP 프로토콜을 이용하여 서버에 요청을 보내게 되는데,
    2. 이때 브라우저는 요청 헤더에 Origin 이라는 필드에 출처를 함께 담아 보내게 된다.
2. 서버는 응답헤더에 `Access-Control-Allow-Origin`을 담아 클라이언트로 전달한다.
    1. 이후 서버가 이 요청에 대한 응답을 할 때 응답 헤더에 `Access-Control-Allow-Origin`이라는 필드를 추가하고 값으로 '**이 리소스를 접근하는 것이 허용된 출처 url**'을 내려보낸다.
3. 클라이언트에서 Origin과 서버가 보내준 `Access-Control-Allow-Origin`을 비교한다.
    1. 이후 응답을 받은 브라우저는 자신이 보냈던 요청의 Origin과 서버가 보내준 응답의 `Access-Control-Allow-Origin`을 비교해본 후 차단할지 말지를 결정한다.
    2. 만약, 유효하지 않다면, 그 응답을 사용하지 않고 버린다. 여기서 CORS 에러가 발생한다.

따라서 서버에서는 `Access-Control-Allow-Origin`헤더에 허용할 출처를 기재해서 클라이언트에 응답해야한다.

## CORS 작동 방식 시나리오 - Preflight

---

브라우저는 요청을 보낼때 한번에 바로 보내지않고, 먼저 **예비 요청**을 보내 서버와 잘 통신되는지 확인한 후 **본 요청**을 보낸다. 예비 요청의 역할은 본 요청을 보내기 전에 브라우저 스스로 안전한 요청인지 미리 확인하는 것이다.

이런 예비 요청을 Preflight라고 부르며, 이런 예비 요청에는 OPTIONS http 메소드를 사용한다.

![Untitled](CORS%20fac073211ff944579dfd7528ca56e6bf/Untitled.png)

1. 자바스크립트의 `fetch()` 메서드를 통해 리소스를 받아오려고 한다.
2. 브라우저는 서버로 HTTP OPTIONS 메소드로 예비 요청(Preflight)을 먼저 보낸다.
    1. Origin 헤더에 자신의 출처를 넣는다.
    2. `Access-Control-Request-Method` 헤더에 실제 요청에 사용할 메소드를 설정한다.
    3. `Access-Control-Request-Headers` 헤더에 실제 요청에 사용할 헤더들을 설정한다.
3. 서버는 이 예비 요청에 대한 응답으로 어떤 것을 허용하고 어떤것을 금지하고 있는지에 대한 헤더 정보를 담아서 브라우저로 보내준다.
    1. `Access-Control-Allow-Origin` 헤더에 허용되는 Origin들의 목록을 설정한다.
    2. `Access-Control-Allow-Methods` 헤더에 허용되는 메소드들의 목록을 설정한다.
    3. `Access-Control-Allow-Headers` 헤더에 허용되는 헤더들의 목록을 설정한다.
    4. `Access-Control-Max-Age` 헤더에 해당 예비 요청이 브라우저에 캐시 될 수 있는 시간을 초 단위로 설정한다.
4. 이후 브라우저는 보낸 요청과 서버가 응답해준 정책을 비교하여, 해당 요청이 안전한지 확인하고 본 요청을 보내게 된다.
5. 서버가 본 요청에 대한 응답을 하면 최종적으로 이 응답 데이터를 자바스립트로 넘겨준다.

### Preflight의 문제점과 캐싱

실제 요청에 걸리는 시간이 늘어나게 되어, 어플리케이션 성능에 영향을 줄 수 있다.

특히, 수행하는 API 호출 수가 많으면 많을 수록 예비 요청으로 인해 서버 요청을 배로 보내게 되니 비용 적인 측면에서 폐가 될 수 있다.

따라서, 브라우저의 캐시를 이용해서, `Access-Control-Max-Age` 헤더에 캐시될 시간을 명시해 주면, 이 Preflight 요청을 캐싱 시켜 최적화를 시켜줄 수 있다.

**Preflight 캐시**

1. 브라우저는 예비(Preflight) 요청을 할 때마다, 먼저 Preflight 캐시를 확인하여 해당 요청에 대한 응답이 있는지 확인한다.
2. 만일 응답이 캐싱 되어 있지 않다면, 서버에 예비 요청을 보내 인증 절차를 밟는다.
3. 만일 서버로 부터 Access-Control-Max-Age 응답 헤더를 받는다면 그 기간 동안 브라우저 캐시에 결과를 저장한다.
4. 다시 요청을 보내고 만일 응답이 캐싱 되어 있다면, 예비 요청을 서버로 보내지 않고 대신 캐시된 응답을 사용한다.

## CORS 작동 방식 시나리오 - 단순 요청 Simple Request

---

단순 요청은 예비 요청을 보내지 않고 바로 서버에게 본 요청부터 보낸 후, 서버가 이에 대한 응답의 헤더에 `Access-Control-Allow-Origin`과 같은 값을 보내주면 그때 브라우저가 CORS 정책 위반 여부를 검사하는 방식이다. 

즉, `Preflight`와 `Simple Request`는 전반적인 로직 자체는 같지만, `Preflight`의 존재 유무만 다르다.

![Untitled](CORS%20fac073211ff944579dfd7528ca56e6bf/Untitled%201.png)

이 Simple Request는 아래 조건을 만족하는 경우에 적용된다. ( Preflight가 생략된다.)

1. 요청의 메소드는 `GET`, `HEAD`, `POST` 중 하나여야 한다. 
2.  `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, `DPR`, `Downlink`, `Save-Data`, `Viewport-Width`, `Width`를 제외한 헤더를 사용하면 안된다. 
3. 만약 `Content-Type`를 사용하는 경우에는 `application/x-www-form-urlencoded`, `multipart/form-data`, `text/plain`만 허용된다.

대부분 HTTP API 요청은 `text/xml`이나 `application/json` 으로 통신하기 때문에 3번째 규칙인 Content-Type이 위반되기 때문에, 단순 요청이 일어나는 상황은 드물다.

## CORS 작동 방식 시나리오 - 인증된 요청 Credentialed Request

---

인증된 요청은 클라이언트에서 서버에게 **자격 인증 정보(Credential)**를 실어 요청할때 사용되는 요청이다.

여기서 말하는 **자격 인증 정보**란 세션 ID가 저장되어있는 쿠키(Cookie) 혹은 Authorization 헤더에 설정하는 토큰 값 등을 일컫는다.

즉, 클라이언트에서 일반적인 JSON 데이터 외에도 쿠키 같은 인증 정보를 포함해서 다른 출처의 서버로 전달할때 CORS의 세가지 요청중 하나인 인증된 요청으로 동작된다는 말이며, 이는 기존의 단순 요청(Simple Request)이나 예비 요청(Preflight)과는 살짝 다른 인증 형태로 통신하게 된다.

### 1. 클라이언트에서 인증 정보를 보내도록 설정

기본적으로 브라우저가 제공하는 비동기 리소스 요청 API인 `XMLHttpRequest` 객체나 `fetch` API는 별도의 옵션 없이 브라우저의 쿠키 정보나 인증과 관련된 헤더를 함부로 요청에 담지 않는다. 이때 요청에 인증과 관련된 정보를 담을 수 있게 해주는 옵션이 바로 `credentials` 옵션이다.

credentials 옵션의 3가지 값

1. same-origin : 기본값으로, 같은 출처 간 요청에만 인증 정보를 담을 수 있다.
2. include : 모든 요청에 인증 정보를 담을 수 있다.
3. omit : 모든 요청에 인증 정보를 담지 않는다.

만약 여러분이 `same-origin`이나 `include`와 같은 옵션을 사용하여 리소스 요청에 인증 정보가 포함된다면, 이제 브라우저는 다른 출처의 리소스를 요청할 때 단순히 `Access-Control-Allow-Origin`만 확인하는 것이 아니라 좀 더 빡빡한 검사 조건을 추가하게 된다.

### 2. 서버에서 인증된 요청에 대한 헤더 설정하기

서버도 인증된 요청에 대해, 일반적인 CORS 요청과 다르게 대응해야한다.

1. 응답 헤더의 `Access-Control-Allow-Credentials` 항목을 true로 설정해야 한다.
2. 응답 헤더의 `Access-Control-Allow-Origin` 의 값에 와일드카드 문자("*")는 사용할 수 없다.
    1. 분명한 Origin으로 설정되어야한다.
3. 응답 헤더의 `Access-Control-Allow-Methods` 의 값에 와일드카드 문자("*")는 사용할 수 없다.
4. 응답 헤더의 `Access-Control-Allow-Headers` 의 값에 와일드카드 문자("*")는 사용할 수 없다.

![스크린샷 2023-04-06 오후 2.25.52.png](CORS%20fac073211ff944579dfd7528ca56e6bf/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2023-04-06_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_2.25.52.png)

## CORS 체험

---

[CORS Tutorial](https://chuckchoiboi.github.io/cors-tutorial/)

## CORS 문제를 해결하는 방법

---

### 프록시 서버 구축

프록시 서버는 클라이언트가 프록시 서버 자신을 통해서 다른 네트워크 서비스에 간접적으로 접속할 수 있게 해준다.

쉽게 말해 브라우저와 서버 간의 통신을 도와주는 중계서버이다.

[CORS 프록시 서버 만들기 (cors-anywhere)](https://skyksit.com/programming/javascript/javascript-cors-proxy/)

### **Access-Control-Allow-Origin 세팅하기**

서버에서 `Access-Control-Allow-Origin` 헤더에 알맞은 값을 세팅해주는 것이 정석이다.

**Spring에서 해결하는 방법**

스프링에서는 어노테이션으로 쉽게 CORS 문제를 해결할 수 있다.

`Access-Control-Allow-Origin` 헤더를 세팅해 해결하는 방법이다.

**스프링 서버 전역적으로 설정하는 코드**

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**")
        	.allowedOrigins("http://localhost:8080", "http://localhost:8081") // 허용할 출처
            .allowedMethods("GET", "POST") // 허용할 HTTP method
            .allowCredentials(true) // 쿠키 인증 요청 허용
            .maxAge(3000) // 원하는 시간만큼 pre-flight 리퀘스트를 캐싱
    }
}
```

**특정 컨트롤러에만, 특정 메소드에만 적용하는 경우**

```java
@Controller
@CrossOrigin(origins = "*", methods = RequestMethod.GET) 
public class customController {

	// 특정 메소드에만 CORS 적용 가능
    @GetMapping("/url")  
    @CrossOrigin(origins = "*", methods = RequestMethod.GET) 
    @ResponseBody
    public List<Object> findAll(){
        return service.getAll();
    }
}
```

**AWS S3 호스팅에서 해결하는 방법**

1. S3 콘솔 메뉴에 들어가 버킷을 선택한다.
2. 권한(Permissions) 탭을 선택한다.
3. 교차 출처 리소스 공유 창에서 [편집] 선택한다.
4. 텍스트 상자에 아래 JSON CORS 규칙을 입력한다.
    
    ```json
    [
      {
        "AllowedHeaders": [
          "Authorization"
        ],
        "AllowedMethods": [
          "GET",
          "HEAD"
        ],
        "AllowedOrigins": [
          "http://www.example.com"
        ],
        "ExposeHeaders": [
          "Access-Control-Allow-Origin"
        ]
      }
    ]
    ```
    

## Reference

---

[CORS는 왜 이렇게 우리를 힘들게 하는걸까?](https://evan-moon.github.io/2020/05/21/about-cors/)

[🌐 악명 높은 CORS 개념 & 해결법 - 정리 끝판왕 👏](https://inpa.tistory.com/entry/WEB-📚-CORS-💯-정리-해결-방법-👏)

---

[교차 출처 리소스 공유 (CORS) - HTTP | MDN](https://developer.mozilla.org/ko/docs/Web/HTTP/CORS)