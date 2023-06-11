## 실시간 통신

### 실시간 트릭 기술

- Polling
- Long Polling

### 실시간 기술

**양방향(bidirectional)**

- Web Socket

**단방향(unidirectional)**

- SSE


---

## 폴링 / 롱폴링

### HTTP

HTTP의 특징

- 무상태(Stateless)
    - 클라이언트의 상태를 보존하지 않는다.
- 비연결성(Connectionless)
    - 요청을 주고받을 때만 연결을 유지하고 응답을 주고 나면 연결을 끊는다.

### 폴링(Polling)

가장 기본적인 데이터 처리방식으로, 특정 주기를 가지고 서버에 HTTP Request를 하는 방식

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e3627f28-6c83-441c-8bd8-6e7b1679f886/Untitled.png)

Pollong 방식은 언제 통신이 발생할지 예측이 불가능하기 때문에 클라이언트는 HTTP Request를 일정한 주기로 서버에 요청하여 이벤트를 전달받는 방식이다.

즉, 언제 통신이 발생할지 모르기 때문에 일정한 간격으로 클라이언트가 계속해서 요청을 보내게 된다.

이 말은,

- 클라이언트가 많아지면 서버에 대한 부하가 증가하고, 매번 요청을 처리하고 응답해야 하기 때문에 서버 리소스의 낭비가 발생할 수 있다.
- 일정한 주기마다 요청을 보내서 이벤트를 전달받기 때문에 실시간(real-time)의 빠른 응답을 기대하기는 어렵다.
- ~~HTTP 오버헤드가 발생한다.~~
    - 여기서 말하는 오버헤드는 데이터 양에 비해 HTTP 요청과 응답에 포함되는 헤더의 크기가 상대적으로 커서 발생하는 것을 의미한다.
        
        ex) 작은 양의 데이터를 주고받을 때마다 매번 큰 헤더가 함께 전송되는 경우
        
        - *요청 헤더에는 요청의 유형, 쿠키, 인증 등의 다양한 부가적인 정보를 포함해야 하는데 이러한 부가적인 정보보다 데이터의 크기가 큰 경우가 오버헤드가 문제가 된다고 하는데,,,, 이건 **폴링 방식의 문제라기보다 HTTP 프로토콜의 통신에서 발생하는 문제라고 생각됨***

### 롱 폴링(Long Polling)

롱 폴링 방식은 폴링 방식의 변형으로 클라이언트가 요청을 보내도 즉시 응답을 보내지 않고 일정 시간동안 연결을 유지하는 방식이다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ae1bd86b-465f-4481-8cf4-ec9d163f8eb5/Untitled.png)

따라서 연결이 유지되는 동안 이벤트가 발생하여 전달할 이벤트가 있다면 그 즉시 응답 메시지를 전달하여 응답을 보내게 된다. 클라이언트는 응답을 받은 후 다시 새로운 요청을 서버로 보내 다음 이벤트를 기다리게 되는 방식으로 동작되게 된다.

- 클라이언트가 서버에게 요청을 보낸다.
- 서버는 응답을 즉시 보내않고 연결을 유지한 상태로 대기한다.
- 서버에 이벤트가 발생하거나 특정 조건(타임 아웃 등)이 되면 응답을 보낸다.
- 클라이언트는 응답을 받은 후 새로운 요청을 보내 다음 이벤트를 기다린다.

일정 시간마다 요청을 보내는 폴링 방식보다는 서버의 부담이 일부 감소하겠지만, 클라이언트가 많아지면 결국 폴링 방식처럼 요청과 응답이 자주 일어나기 때문에 롱 풀링 방식 또한 서버의 부담이 증가하게 된다.

또한, 폴링 방식과 마찬가지로 HTTP를 통해 통신하기 때문에 HTTP 오버헤드가 발생한다는 단점도 존재한다.

**그래도 롱 풀링 방식이 풀링 방식보다는 좋지 않을까?**

**일반적으로 좋을 수는 있겠지만, 모든 경우는 아니다.**

예를 들어, 단체 오픈 채팅방에 1000명의 사용자(클라이언트)가 있고 롱 풀링으로 구현되어있다고 가정해보자. 

이때, 한 사람이 하나의 메시지만 보내면 1,000명의 사용자가 응답을 받아야 하고, 그와 동시에 1,000명이 연결을 맺는 1,000개의 요청을 보내기 때문에 **순간적인 부하**가 발생할 수 있다.

참고한 포스팅에서는 폴링 방식과 롱 폴링 방식의 선택 기준을 다음과 같이 정리하고 있다.

- 폴링 방식
    - 응답을 실시간으로 받지 않아도 되는 경우
    - 다수의 사용자가 동시에 사용하는 경우
    - ex) 전체 채팅이 필요한 웹 게임
- 롱 폴링 방식
    - 응답을 실시간으로 받아야하는 경우
    - 메신저 같이 1 on 1, 혹은 적은 수의 사용자가 동시에 사용하는 경우
    - ex) Facebook 웹 채팅, Google 메신저, MSN 웹 메신저

**일반적으로 스프링은 블럭킹, 동기 방식인데 이러면 무한정 대기상태에 걸리는거 아닌가?**

**맞다.**

직접 구현을 해야 한다면 커넥션을 위한 쓰레드 관리를 해주어야 하고, 여간 쉬운일이 아니다.

그래서 스프링 3.2부터 비동기 처리를 수행하는 DeferredResult 클래스를 제공하고 있다.

- [Baeldung](https://www.baeldung.com/spring-mvc-long-polling)
- [참고](https://junhyunny.github.io/information/spring-boot/polling-long-polling-and-spring-example/)

**정리**

서버에서 이벤트가 발생하면 클라이언트에게 보내주어야 하는 것은 동일하지만,

- 클라이언트에서 주기적으로 체크(요청)하는게 폴링
- 서버에서 체크해서 보내주는게 롱 폴링

---

## 웹 소켓

웹소켓은 클라이언트와 서버를 연결하고 실시간 통신이 가능하게 하는 통신 프로토콜로 단방향 통신만 가능한 HTTP와는 다르게 웹 소켓은 양방향 통신을 지원한다. 

클라이언트와 서버간의 초기 연결을 설정한 이후에는 지속적으로 연결을 유지하고, 실시간으로 데이터를 주고받을 수 있게 된다. 따라서 채팅과 같이 연속적인 통신이 이루어져도 한 번 연결을 맺고 나면 추가적인 요청/응답 과정을 필요로 하지 않는다.

기존의 TCP 기반의 HTTP 프로토콜은 TCP 계층에서 Handshake 과정을 거쳐 연결이 이루어진 반면, 

웹 소켓은 HTTP Request를 기반으로 Handshake 과정을 거쳐 연결이 이루어진다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/fbe6cd1f-d777-4a98-96e1-337c333de3cd/Untitled.png)

웹소켓 연결을 거치기 위해 `Upgrade 헤더`와 `Connection 헤더`를 포함한 HTTP 요청을 보내면 101번의 상태 코드를 응답받아 웹소켓으로 통신 가능한 상태가 되게 된다.

![  |—-HTTP 요청](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0b7b929d-53cd-4341-83ba-156200eea85d/Untitled.png)

  |—-HTTP 요청

![ |—- HTTP 응답](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/7ede6e70-2d81-4935-bb60-687ba39fbe77/Untitled.png)

 |—- HTTP 응답

```java
1XX : Information responses
서버가 요청을 받았으며, 서버에 연결된 클라이언트는 작업을 계속 진행해도 된다는 상태코드

100 Continue
현재까지의 진행상태에 문제가 없으며, 클라이언트가 계속해서 요청을 하거나 
이미 요청을 완료한 경우에는 무시해도 되는 것을 알려준다.

101 Switching Protocol
클라이언트가 보낸 Upgrade 요청 헤더에 대한 응답이 들어가며 서버에서 프로토콜을 변경할 
것임을 알려준다. 해당 코드는 Websocket 프로토콜 전환 시에 사용된다.

출처: [MDN Web Docs](https://developer.mozilla.org/ko/docs/Web/HTTP/Status)

----

HTTP/1.0은 1xx 상태 코드를 정의하지 않았기 때문에 코드를 정의하지 않았으므로, 
서버는 실험적 조건을 제외하고는 HTTP/1.0 클라이언트에 1xx 응답을 보내서는 안됩니다.

출처: [RFC 2616](https://datatracker.ietf.org/doc/html/rfc2616#section-10)

[GPT 답변]
[[ME]]
HTTP/1.0 에서는 상태코드 100번에서는 지원되지 않는다고 하는데,
만약 100번대의 요청을 보내면 어떤 결과를 받게 돼?

[[GPT]]
HTTP/1.0에서는 상태 코드 100번대에 대한 처리가 지원되지 않습니다. 
일반적으로 HTTP/1.0 클라이언트는 상태 코드 100번대를 받을 경우, 
이 응답을 이해하지 못하고 무시하거나 오류로 처리할 수 있습니다. 

[[ME]]
오류로 처리하게 되면 어떻게 돼? 응답 예시를 보여줘.

[[GPT]]
구체적인 오류 처리 방식은 클라이언트의 구현에 따라 다를 수 있으며, 
클라이언트의 개발자가 정의한 오류 처리 메커니즘에 따라 동작합니다. 
따라서 특정 예시를 제시하기는 어렵습니다.

다만, 오류로 처리되는 경우 일반적으로 클라이언트는 해당 응답을 무시하고 
원래 요청에 따른 다음 응답을 기다리지 않습니다.
```

**정리하면,**

1. 웹소켓 프로토콜은 연결을 잇는 최초의 요청에는 HTTP를 사용한다.
2. Handshake 과정이 정상적으로 이루어지면 http -> websocket으로 프로토콜을 변경하는 프로토콜 스위칭이 발생한다.
3. 그후의 통신에서는 웹소켓 프로토콜을 통해 통신이 이루어진다.

### **HTTP vs 웹소켓**

|  | HTTP | Web Socket |
| --- | --- | --- |
| URL | http://www.mumjae.com | ws://www.mumjae.com |
| 연결 | 단방향 요청-응답 연결 방식 | 양방향 통신을 제공하는 연결 방식 |
| 연결 방식 | 요청마다 완전한 핸드셰이킹을 수행 | 초기 연결에서만 핸드셰이킹을 수행 |

이러한 장점도 존재하지만, 웹소켓 특성으로 인해 발생하는 문제점과 단점도 있다.

- Stateful한 프로토콜이기 때문에 연결이 끊어졌을 때의 상황에 대응을 해야 하는 문제
- 웹소켓을 연결을 지원하지 않을 수도 있다.
    - ex) HTTP/1.1 미만
    - ex) IE(Internet Explorer) 10 미만
- 클라이언트와 서버간에 소켓 연결을 유지해야 한다.
    - 클라이언트가 많은 대용량 트래픽인 경우에 부담이 될 수 있다.

---

## SSE (Server Sent Events)

SSE는 서버의 이벤트 데이터를 구독하여 실시간으로 수신하는 기술이다. 서버로 요청을 보내지 않아도(rather than pulled, or requested) 데이터를 받을 수 있다는 특징을 가진다. 

또한, 웹소켓과 동일하게 푸시(Push) 방식의 통신이지만 클라이언트가 서버로부터만 데이터를 받을 수 있는 양방향이 아닌 단방향(Server -> Client)이다.

![이미지 출처: https://ably.com/blog/websockets-vs-sse](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/cecb97b2-a7ec-47c6-a3ef-e503aad0f73d/Untitled.png)

이미지 출처: https://ably.com/blog/websockets-vs-sse

기존에는 서버의 데이터를 가져오기 위해서는 

- 지속적인 요청을 보내 응답(Polling)을 받거나
- 페이지를 새로고침하거나
- HTTP 통신이 아닌 웹소켓 통신으로 데이터를 받아올 수 있었다.

SSE도 하나의 통신 기술 중 하나로 다음과 같은 특징을 가진다.

- 뉴스티커나 알림과 같이 서버에서 클라이언트로 푸시(Push)하는 단방향 작업에서 용이하다
    - 이미지
        
        ![https://chpofo.tistory.com/58](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1997efdc-07db-47b9-9837-b73267d79ec6/Untitled.png)
        
        https://chpofo.tistory.com/58
        
- HTTP 프로토콜을 통해 통신하므로 다른 프로토콜이 필요없다.
- 웹소켓과 달리 재접속 처리(recovery)를 지원한다.
- *스프링의 경우 Spring 4.2부터 SSE 통신을 지원하는 SseEmitter API를 기본적으로 제공한다.*

SSE 는 다음과 같은 과정을 통해 연결이 이루어진다.

- 클라이언트는 서버의 이벤트를 구독한다는 요청을 보낸다.
    
    ```yaml
    GET /connect HTTP/1.1
    Accept: text/event-stream
    Cache-Control: no-cache
    ```
    
    - W3C SSE specification에 따르면 `Accept 헤더`에 `text/event-stream`으로 지정하도록 정해져 있다. ([링크](https://html.spec.whatwg.org/multipage/iana.html#text/event-stream))
    - 이벤트를 캐싱을 하지 않고, 지속적인 연결을 하도록 해야 한다.
        - 캐싱을 허용하면 클라이언트가 이벤트를 즉시 수신하지 못하고 캐시된 이전 데이터를 받을 수 있기 때문

- 서버는 구독 요청에 대한 응답을 보낸다. (구독이 완료된 상태)
    
    ```yaml
    HTTP/1.1 200
    Content-Type: text/event-stream;charset=UTF-8
    Transfer-Encoding: chunked
    ```
    
- 서버에 이벤트가 발생하면 구독하는 클라이언트에게 비동기적으로 데이터를 전송한다.
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/43ce58cd-a499-4fcf-ab59-f55eb4069c94/Untitled.png)
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c3a42de8-09d6-4a4c-baf5-34f60471a06f/Untitled.png)
    

정리하면 다음과 같다.

- 서버로 별도의 요청없이 데이터를 받을 수 있다.
- 서버에서 클라이언트로 단방향의 Push Notification을 하는 기술이다.
- HTTP/1.1 프로토콜 사용시 최대 동시 접속자수가 6개까지 가능하고, HTTP/2.0 프로토콜 사용시에는 최대 100개까지 유지가 가능하다.
- 이벤트 데이터는 UTF-8 인코딩된 문자열만 지원합니다. 서버에서 이벤트 데이터를 담은 객체를 JSON으로 마샬링하여 전송하는 것이 일반적이다.
- 스프링은 4.2부터 SseEmitter 클래스를 기본적으로 제공하여 별도의 라이브러리나 프로토콜 학습없이 SSE 통신 구현이 가능하다.
    - 웹소켓은 웹소켓 프로토콜(ws)을 사용했던 것과 달리 SSE는 흔히 사용하는 HTTP 프로토콜을 사용함
    - 웹소켓은 프로토콜 처리를 위한 별도의 소켓 서버가 필요한 반면 SSE는 서버 구현이 필요하지 않음

웹소켓과 SSE의 선택 기준은 다음과 같다.

- 웹소켓 방식
    - 실시간 양방향 통신인 경우
    - ex) 채팅
- SSE 방식
    - 실시간 단뱡항 통신인 경우
    - ex) 푸시 알림, 뉴스 레터, SNS 친구 요청

**참고**

- [링크](https://warmth424.tistory.com/18)
- [링크](https://www.notion.so/Network-6b3b31c397ff477f9346b00c3b0af5fa?pvs=21)
- [링크](https://tecoble.techcourse.co.kr/post/2021-08-14-web-socket/)
- [링크](https://developer.mozilla.org/ko/docs/Web/HTTP/Status)
- [링크](https://ably.com/topic/websockets-vs-http)
- [링크](https://inpa.tistory.com/entry/WEB-%F0%9F%93%9A-Polling-Long-Polling-Server-Sent-Event-WebSocket-%EC%9A%94%EC%95%BD-%EC%A0%95%EB%A6%AC)
- [링크](https://blog.bitsrc.io/websockets-vs-server-sent-events-968659ab0870)
