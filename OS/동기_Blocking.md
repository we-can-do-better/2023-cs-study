# Blocking,Non-Blocking 그리고 동기,비동기

## Block과 Non-Block

: 다른 주체가 작업할 때 자신에게 자신의 작업에 대한 제어권이 있는가?

```jsx
function 호출자() {
	functionA();
	functionB();
	functionC();
}
```

```jsx
function functionA(){
	/* 특정 로직 */
	return something;
}
```

### Block

코드 예시

1. 호출자()가 실행되면, 호출자가 가지고 있던 **제어권**은 functionA에게 흘러간다. 이 때, 제어권이 functionA에게 갔으므로, 호출자는 아무것도 할 수 없다. ⇒ 호출자의 행동을 막아버림
2. functionA의 결과값과 함께 제어권이 다시 호출자로 돌아온다. 따라서 이제 호출자는 functionB를 실행할 수 있고, 제어권은 functionB로 흘러간다. 
3. functionC까지 반복한다. 

**⇒ 다른 작업을 하는동안, 자신의 작업에 대한 제어권이 없는 상태로 자신의 작업을 진행하다가, 다른 주체의 작업이 시작되면 다른 작업이 끝날 때까지 기다렸다가 자신의 작업을 시작하는 방식**

### Non-Block

코드 예시

1. 호출자()가 실행되면, 호출자가 가지고 있던 **제어권**은 functionA가 실행되면서 functionA에게 **제어권이 주어졌다가 다시 호출자로 돌아온다.** 
2. functionA는 다른 제어권을 별도로 갖던지, 다른 스레드를 갖던지 해서, functionA대로 실행된다. 
3. functionA가 실행되고 있음과 상관없이, 호출자는 functionB를 실행한다. 

**⇒ 다른 주체의 작업에 관련없이 자신의 작업을 할 수 있는 상태, 즉 제어권을 위임하지 않는 방식**

## Synchronous과 Asynchronous ( 동기 비동기 )

: 결과를 돌려주었을 때 순서와 결과에 관심이 있는가? ( Sync는 결과에 관심이 있고, Async는 없음 )

*데이터 베이스 동기화는 같은 시간에, 다른 DB 저장소에서 같은 데이터를 가지고 있도록 맞춰주는 것 → DB의 시간이 동일해서 같은 데이터를 가지고 있는가?*

*⇒ 동기 : 대상들의 시간이 맞춰지는 가?*

### Synchronous : 시간을 일치시킨다

함수A가 끝난 뒤에, 함수 B가 실행되는 것 그리고 **제어권의 반환과 결과값의 전달의 시간을 맞춰주는 것**(작업을 동시에 수행하거나, 동시에 끝나거나, 끝나는 동시에 시작함)

### Asynchronous : 시간을 맞추지 않는다

함수 간에 실행, 결과 반환 시간이 다른 것 그리고 결과값도 전달 안했는데, 제어권을 반환하는 것.

**⇒ 시작, 종료가 일치하지 않으며, 끝나는 동시에 시작하지 않는 방식**

## Sync / Async + Blocking / Non-Blocking 조합

### 1. Sync Blocking (Java)

![https://media.vlpt.us/images/guswns3371/post/70c4e034-7c0a-497b-9aea-b679ca54aef1/image.png](https://media.vlpt.us/images/guswns3371/post/70c4e034-7c0a-497b-9aea-b679ca54aef1/image.png)

**Blocking** 👉 다른 작업이 시작하게되면, 원래의 작업을 멈춘다

**Sync** 👉 결과를 반환받으면, 바로 처리한다

ex. 자바에서 입력요청시 제어권이 입력으로 넘어간다. 그래서 입력을 받기 전까지 다음 코드가 실행되지 않고, 입력을 받으면 제어권과 함께 결과를 같이 받아 처리한다.

ex. 대부분의 함수 호출

### 2. Sync Non-blocking

non블록시 결과값이 어디로 가는가? Application -> kernel(안끝났는데 바로 반환(결과값없이.))

(완료된 상태만 결과값이 아니라 ''완료되지 않음'이라는 상태도 결과값이기 때문에 이걸 반환함.')

결과값이 완료되면 바로 처리한다.

컨텍스트 스위칭이 계속일어남(비용이 발생함.)

![https://media.vlpt.us/images/guswns3371/post/e7f9715b-a3e9-424a-83f8-684701086aee/image.png](https://media.vlpt.us/images/guswns3371/post/e7f9715b-a3e9-424a-83f8-684701086aee/image.png)

동기 논블로킹은 작업들의 시작 시간, 종료시간이 서로 영향을 받으면서 + 다른 작업이 끝나기를 기다리지 않는 경우이다. A작업이 자신의 작업을 수행하면서 B작업이 완료여부를 지속적으로 확인하는 경우가 이에 속한다. Java의 Future.isDone()이 이에 속한다고 한다.

**Non-Blocking** 👉 다른 작업이 시작하게되어도, 원래의 작업에 대한 제어권을 가지고 진행한다

**Sync** 👉 결과를 반환받으면, 바로 처리한다

- A 작업이 자신의 작업을 수행하면서 B작업의 완료여부를 지속적으로 확인함
- 결과값을 주지 않는데 왜 Sync인가?
    - **결과값에는 함수의 완료된 상태 뿐만 아니라, 완료되지 않음이라는 상태도 그걸 결과값으로 본다.**
    - 따라서, 함수의 실행이 완료되지 않은 경우에는 ‘완료되지 않음’이라는 상태를 결과값으로 반환한다.
- 컨텍스트 스위칭이 계속 일어나 비용이 발생한다.

ex. Java의 Future.isDone()

### 3. Async Blocking

![https://media.vlpt.us/images/guswns3371/post/3e7e01e5-bd0c-404e-a4fa-ac3dcbbb198f/image.png](https://media.vlpt.us/images/guswns3371/post/3e7e01e5-bd0c-404e-a4fa-ac3dcbbb198f/image.png)

**Blocking** 👉 다른 작업이 시작하게되면, 원래의 작업을 멈춘다

**Async** 👉 결과를 반환받아도, 바로 처리하지 않을 수 있다.

ex. Node.js에서 MySQL호출시, MySQL 드라이버는 Blocking 방식이라 Node.js의 나머지 코드는 동작할 수 없다. ([관련블로그글](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=jungwan82&logNo=221173406494))

### 4. Async Non-Blocking (Javascript)

![https://media.vlpt.us/images/guswns3371/post/d4101e32-e416-44c7-b143-753e705c6bf6/image.png](https://media.vlpt.us/images/guswns3371/post/d4101e32-e416-44c7-b143-753e705c6bf6/image.png)

**Non-Blocking** 👉 다른 작업이 시작하게되어도, 원래의 작업에 대한 제어권을 가지고 진행한다

**Async** 👉 결과를 반환받아도, 바로 처리하지 않을 수 있다.

- **성능과 자원의 효율적 사용 관점에서 가장 유리한 모델은 Non-Blocking/Async 모델이다.**

ex. 자바스크립트에서 API 요청을 한뒤 다른 작업을 수행하다가 callback 함수를 통해 추가적 작업을 처리할 때 사용된다.
