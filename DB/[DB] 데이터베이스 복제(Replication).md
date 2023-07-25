## Master-Slave

데이터베이스 레플리케이션은 데이터베이스에 대한 트래픽 분산, 백업 등을 위해 데이터베이스를 복제하여 Master-Slave 구조로 구성하는 스케일 아웃(Scale-Out) 솔루션

원본 데이터베이스 서버는 Master, 원본 데이터베이스를 복제한 서버는 Slave라고 부른다.

- Master DB는 데이터 동시성이 요구되는 트랜잭션(CUD)을 담당
- Slave DB는 데이터 동시성이 꼭 보장될 필요는 없는 읽기 전용으로 데이터를 읽어옴

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ab810130-8da5-4755-9bea-6a247625fb67/Untitled.png)

> 🤑(달러): 세드님, 저희 프로젝트에 부하 분산을 위해 Master/Slave Replication을 적용해보는건 어떨까요?
😂(세드): 달러님, 의견 감사합니다. 하지만 지금 당장 데이터가 잘 저장되고, 읽어오고 있는데 굳이 서버를 늘려가면서 데이터베이스 복제 구성을 해야 하는 이유가 있을까요?
> 

### Scale-out Solutions

대부분의 서비스가 그렇듯 결국 데이터베이스에 접근해서 처리해야 하는 것이 대부분이다.

그런데 만약, 읽기, 쓰기, 수정, 삭제의 연산이 하나의 데이터베이스에서 일어난다면 트래픽이 증가함에 따라 병목 현상이 생길 수 밖에 없다.

그래서 **쓰기 작업은 원본 서버에서**만 수행하게 하고, **읽기 작업은 복제 서버에서** 수행하게 하여 쓰기기능과 읽기 기능의 성능을 향상 시키고, 병목을 최소화 시킬 수 있게 된다.

![이미지 출처: https://github.com/bxcodec/dbresolver](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d6a7f3d3-d5b3-42a6-859c-c5a6636272ee/Untitled.png)

이미지 출처: https://github.com/bxcodec/dbresolver

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/34dc09d7-53ed-406a-8a52-c6cf3791dd72/Untitled.png)

```java
spring:
  datasource:
    master:
      hikari: 
        username: root
        password: password
        driver-class-name: com.mysql.cj.jdbc.Driver
        jdbc-url: jdbc:mysql://localhost:3306/examreplication
    slave:
      hikari:
        username: root
        password: password
        driver-class-name: com.mysql.cj.jdbc.Driver
        jdbc-url: jdbc:mysql://localhost:3307/examreplication
```

Transactional 애너테이션의 readOnly 옵션에 따라 분기하여 Master 또는 Slave의 DataSource를 호출하여 동작된다.

### 장애 극복 & 백업

만약 원본 서버에 장애가 발생했다면 장애 극복(fail over)을 위해 Slave DB를 Master DB로 승격(promotion)해 데이터의 복구를 빠르게 진행할 수 있다.

그러나 MySQL Replication은 기본이 비동기 방식으로 동작되기 때문에 Master DB에 반영된 데이터가 Slave DB로 복사되는 시간을 온전히 기다려주지 않는다. 따라서 데이터가 완벽하게 복사되지 않을 수도 있다.

### 장애 발생 케이스

**1) Slave DB의 일부가 죽었을 경우**

Replication Drvier에서 자동으로 감지하고 살아있는 다른 슬레이브로 데이터 요청을 보낸다.

**2) Slave DB가 모두 죽었을 경우**

Master와의 통신만 가능할 뿐 슬레이브와의 통신이 불가능해진다.

즉, 모든 데이터 요청(CUD, R)을 Master DB가 처리한다.

3**) Master가 죽었을 경우**

기존 Master DB가 다운되어 Slave DB가 Master DB로 승격된다.

단, 마스터 서버를 복구했을지라도 Master-Slave간에 완벽한 데이터 동기화가 이루어지지 않기 때문에 유실된 데이터가 존재할 수 있다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a86613cb-3da0-426f-a8b9-8c6251cca48a/Untitled.png)

**MHA(Master High Availability)**

MHA는 Master-Slave 구조의 복제(Replication) 환경에서 Master DB의 장애 발생 시 Master DB의 승격 작업을 자동화하는 기능이다.

즉, MHA에 의해 장애 발생이 확인되면 자동으로 Slave DB -> Master DB 승격하여 Master DB의 고가용성을 유지시키는 역할을 한다.

![이미지 출처: https://hoing.io/archives/9175](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/973a9550-7a47-41f4-a567-9e9981f82fcf/Untitled.png)

이미지 출처: https://hoing.io/archives/9175

### 데이터 동기화 방식

1. 동기와 비동기
2. 비동기 복제 방식
3. 반동기(Semi-Sync) 복제 방식

**데이터 처리 방식인 동기(Synchronous)와 비동기(Asynchronous)**

**동기 방식**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9046e413-8eb1-44b6-9673-f252ff5473fb/Untitled.png)

요청을 하면 시간이 얼마나 걸리던지 요청한 자리에서 결과가 주어지는 방식으로, 

요청한 작업에 대해 완료 여부를 따져 순차대로 처리한다.

**비동기 방식**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5d8419b2-350d-4206-afad-e6719512ca1a/Untitled.png)

하나의 요청에 따른 응답을 즉시 처리하지 않아도, 그 대기 시간동안 또 다른 요청에 대해 처리 가능한 방식으로, 시작과 종료 시기를 신경쓰지 않기 위해 사용한다.

---

### ****비동기 복제 방식****

원본 서버에서 트랜잭션 커밋이 수행되면 변경 사항이 바이너리 로그에 저장되고, 레플리카 서버는 주기적으로 바이너리 로그를 읽어와서 복제 서버에 적용하는 방식

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/00024ec2-fdfe-40d5-9417-98f108c9a884/Untitled.png)

1. 클라이언트가 트랜잭션 커밋을 실행시키면 가장 먼저 Master 서버에 존재하는 Binary log에 변경사항을 모두 기록
2. Master Thread는 비동기적으로(복사되는 시간을 기다려주지 않음) Binary log를 읽어 Slave 서버로 전송
3. Slave의 I/O Thread는 Master로 부터 받은 변경 데이터들을 Relay log에 기록
4. Slave의 SQL Thread는 Replay log의 기록들을 읽어 자신의 스토리지 엔진에 최종 적용

**특징**

비동기 복제 방식에서는 복제 서버로 바이너리 로그에 저장된 이벤트가 잘 전달 되었는지, 실제로 적용이 되었는지 알지 못하며 보장도 하지 않는다.

**단점**

- 원본 서버 장애시 페일오버(Failover)를 위해 레플리카를 승격(promotion) 시킬 경우 누락된 데이터를 찾고, 필요 시 승격된 레플리카에 누락분을 수동으로 반영해야한다.
- 변경 내역이 곧바로 적용되는 것이 아니기 때문에 레플리카에서 누락이 발생할 수 있다. 이러한 문제를 복제 지연이라고 한다.

**장점**

- 원본 서버는 레플리카에게 변경 사항 전달과 적용 여부를 신경쓰지 않아도 되기 때문에 이로 인한 오버헤드가 발생하지 않는다.
- 원본 서버는 레플리카 장애에 영향을 받지 않으므로 레플리카 서버에 무거운 쿼리가 돌아가 성능 저하가 발생해도 원본 서버는 영향을 받지 않는다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e4d4de99-274e-49c0-8ef7-70023eae4d0a/Untitled.png)

### 반동기 복제 방식(Semi-Sync Replication)

반동기 복제 방식은 레플리카 서버가 원본 서버로부터 전달받은 변경 사항을 릴레이 로그에 기록 하고 응답(ACK)을 보내면 그때서야 원본 서버의 트랜잭션을 커밋하는 방식

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b208bb33-7416-4b90-8d3e-f771ab91d882/Untitled.png)

**특징**

레플리카 서버는 원본 서버로부터 전달받은 변경 사항을 릴레이 로그에 기록하고 응답(ACK)을 보내고 커밋하기 때문에 비동기 복제 방식보다 좀 더 향상된 데이터 무결성을 제공한다.

그러나, 변경 사항을 “전달 받음”을 보장하는 것이지, “적용 했음”을 보장하지는 않는다.

따라서 완전한 동기가 아닌 반동기(Semi-Sync) 방식이다.

**단점**

반동기 복제 방식은 레플리카의 응답을 받아 트랜잭션의 처리가 이루어지기 때문에 비동기 방식에 비해 느려진다. 만약, 레플리카 서버에 부하가 걸려 ACK 응답이 더 늦는경우 트랜잭션 시간은 그만큼 더 길어진다.

**장점**

레플리카 서버로부터 응답(ACK)을 받은 뒤 트랜잭션을 커밋하므로 최소 하나 이상의 레플리카에는 복제가 성공했음을 보장할 수 있다. 

복제 과정에서 정합성이 중요하고, 성능은 다소 떨어져도 되는 상황에서 유리하다.

---

**추가로 알아볼만한 내용**

MySQL 반동기 복제 방식

- AFTER_COMMIT 모드
- AFTER_SYNC 모드

---

**궁금했던 점**

실무에서는 데이터 동기화를 어떻게 할까

>> 일정 주기로? 타임스탬프?
