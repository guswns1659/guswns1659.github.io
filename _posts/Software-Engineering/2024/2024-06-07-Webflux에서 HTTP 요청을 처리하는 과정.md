---
title: Webflux에서 HTTP 요청을 처리하는 과정
date: 2024-06-06 11:49:08 +09:00
categories: [Software-Engineering]
tags: engineering
---

Spring WebFlux는 MVC와 다르게 비동기 논블락킹 방식으로 동작합니다. 이 때 자주 등장하는 단어는 netty의 이벤트루프입니다. 
HTTP 요청이 왔을 때 이벤트 루프가 요청을 어떻게 처리되는지 적어봤습니다.

### 동작 과정

1. **요청 수신**
  - 클라이언트가 HTTP 요청을 보냅니다.
  - Netty 서버가 요청을 수신합니다.

2. **DispatcherHandler 호출**
  - Netty의 이벤트 루프가 수신된 요청을 `DispatcherHandler`에 전달합니다.
  - `DispatcherHandler`는 요청을 적절한 컨트롤러 메서드에 라우팅합니다.

3. **리액티브 스트림 반환**
  - 컨트롤러 메서드는 Mono 또는 Flux를 반환합니다.
  - 예를 들어, `Mono.just("Hello, Mono")`와 같은 리액티브 스트림 객체가 생성됩니다.

4. **구독 시작**
  - `DispatcherHandler`는 반환된 Mono 또는 Flux를 구독합니다.
  - 구독이 시작되면 리액티브 스트림은 데이터를 방출하기 시작합니다.

5. **비동기 작업 처리**
  - 리액티브 스트림에서 정의된 비동기 작업이 실행됩니다.
  - 예를 들어, `delayElement(Duration.ofSeconds(1))`는 1초 지연 후 데이터를 방출합니다.

6. **데이터 방출 및 콜백 실행**
  - Mono 또는 Flux가 데이터를 방출할 때 `onNext` 콜백이 호출됩니다.
  - 모든 데이터가 방출되면 `onComplete` 콜백이 호출됩니다.

7. **완료 이벤트 처리**
  - 비동기 작업이 완료되면 Reactor 라이브러리가 완료 이벤트를 생성합니다.
  - 완료 이벤트에는 최종 데이터가 포함됩니다.

8. **이벤트 큐에 추가**
  - 완료 이벤트가 Netty의 이벤트 큐에 추가됩니다.

9. **응답 생성 및 전송**
  - Netty의 이벤트 루프가 이벤트 큐에서 완료 이벤트를 꺼내어 처리합니다.
  - 처리된 데이터는 HTTP 응답 바디에 포함되어 클라이언트로 전송됩니다.

### 예시 코드

```java
@RestController
public class ExampleController {

    @GetMapping("/mono")
    public Mono<String> getMono() {
        return Mono.just("Hello, Mono")
                   .delayElement(Duration.ofSeconds(1))
                   .doOnNext(data -> System.out.println("Processing data: " + data))
                   .doOnSuccess(data -> System.out.println("Completed with: " + data));
    }
}
```

### 예시 설명

- 클라이언트가 `/mono` 경로로 GET 요청을 보냅니다.
- Netty 서버가 요청을 수신하고 `DispatcherHandler`에 전달합니다.
- `DispatcherHandler`는 `getMono` 메서드를 호출합니다.
- `getMono` 메서드는 `Mono.just("Hello, Mono").delayElement(Duration.ofSeconds(1))`를 반환합니다.
- `DispatcherHandler`가 Mono를 구독하여 데이터 방출이 시작됩니다.
- 1초 지연 후 "Hello, Mono" 문자열이 방출됩니다.
- `onNext` 콜백이 호출되어 "Processing data: Hello, Mono"가 출력됩니다.
- 데이터 방출이 완료되면 `onComplete` 콜백이 호출되어 "Completed with: Hello, Mono"가 출력됩니다.
- 완료 이벤트가 Netty의 이벤트 큐에 추가됩니다.
- Netty 이벤트 루프가 완료 이벤트를 처리하여 "Hello, Mono"를 응답 바디에 포함시켜 클라이언트로 전송합니다.

이 과정을 통해 Spring WebFlux는 비동기 작업을 효과적으로 처리하고 응답을 생성하여 클라이언트에게 전송할 수 있습니다.
