---
title: "Spring) WebFlux"
header:
  overlay_image: /assets/spring.jpg
  overlay_filter: 0.2
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
categories:
  - Spring
---

# Spring web flux

## 주의할점
- 무조건 webflux가 좋은 것이 아니라 스레드 풀이 처리못할 정도의 대규모 트래픽이 아니라면 동기방식이 나을 수도 있다. 동기방식이 코드 작성 및 이해, 디버깅이 더 쉽다는 장점이 있기 때문이다. 무조건 바꿀 필요는 없다.

## 왜 등장
- 적은 하드웨어 자원과 쓰레드 이용해서 논블록킹 방식으로 동시성을 해결하기 위해 등장했다.

### 스프링 Servlet Stack의 한계를 극복하기 위해 등장
- 스프링은 Reactive Stack과 Servlet Stack 두 가지 형태를 제공한다. Reactive Stack은 논 블로킹 방식으로 동시성을 처리한다고 한다. 그럼 기존 Servlet Stack의 문제점은 무엇일까.
- 스레드 풀 내에 있는 쓰레드가 처리할 수 있는 요청(thread pool size)에 대해서만 동시적으로 처리되고 나머지는 큐에 대기한다. 만약 대규모 트래픽이 몰리면 큐에는 계속해서 사용자 요청이 쌓인다. 이 현상을 Thread poll hell이라고 한다. 보통 DB나 네트워크과 통신할 때 시간이 많이 소요된다.
- 이러한 단점을 극복하기 위해 Webflux를 제공한다.

## 장단점

## 장점
- 장점은 적은 자원으로 동시성을 효과적으로 처리할 수 있다는 점이다.

## 단점
- 모든 작업이 논 블로킹 기반으로 동작해야 성능을 최대치로 사용할 수 있다. 1~2개 작업이 블로킹으로 처리되는데 이 작업에 요청이 몰린다면 딱히 의미가 없을 수 있다.
- 트래킹(디버깅)이 어렵다는 단점이 있다. 이유는 소수의 쓰레드가 많은 요청을 처리하고 순서대로 작업이 처리되지 않고 Event에 기반하여 이루어지기 때문이다.
- 자바 진영에서는 DB connection을 논블로킹으로 지원하는 라이브러리가 널리 보급되지는 않은 상황이라고 한다.


## 동작원리

### 블로킹과 논블로킹

- 블로킹은 애플리케이션에서 운영체제에 요청을 하면 해당 작업이 완료되기 전까지 애플리케이션이 블락되어 사용할 수 없는 상태.
- 위 문제를 해결하기 위해 멀티쓰레딩 방식이 고안된다. 빠르게 컨텍스트 스위칭이 일어나면서 작업을 변환하는 것이다. 하지만 스위칭의 비용이 존재한다.
- 동기 논블로킹 방식 : 애플리케이션에서 운영체제에 요청 후 바로 return되어 다른 작업을 수행하다가 특정 시간에 결과가 준비 되었는지 상태를 확인하는 방식. 주기적으로 체크하는 방식을 폴링이라고 한다. 이 방식은 불필요한 자원을 낭비한다.
- 비동기 논블로킹 방식 : 애플리케이션에서 운영체제에 요청 후 바로 return된다. 동기와 차이점은 결과가 준비되면 이벤트가 발생하여 알려주거나 callback 함수가 동작해서 이후 작업이 진행된다. 이 방식이 블로킹과 폴링이 없기 때문에 자원을 보다 효율적으로 사용 가능하다.

### webflux 구조
- 사용자의 요청이 들어오면 Event loop를 통해서 작업이 처리된다. 해당 요청의 콜백 함수를 등록하고 다른 요청을 받는다. 만약 이전 작업이 마무리되면 콜백함수가 발동해 해당 동작이 이루어진다.

![image](https://user-images.githubusercontent.com/55608425/96536301-b68a1e00-12ce-11eb-9f16-ad4efb75da92.png)

### Java, Spring 코드 실습

- [실습 저장소](https://github.com/guswns1659/javatest/blob/master/src/test/java/com/titanic/javatest/webflux/BlockingTest.java)

- 블로킹

```java
@DisplayName("Blocking 테스트")
    @Test
    void blocking() {
        final RestTemplate restTemplate = new RestTemplate();

        final StopWatch jackWatch = new StopWatch();
        jackWatch.start();

        for (int loop = 0; loop < 3; loop++) {
            final ResponseEntity<String> response =
                    restTemplate.exchange(THREE_SECOND_URL, HttpMethod.GET, HttpEntity.EMPTY, String.class);
            assertThat(response.getBody()).contains("success");
        }

        jackWatch.stop();

        log.info("TotalTime : {}", jackWatch.getTotalTimeSeconds());
    }
```

- 결과
![image](https://user-images.githubusercontent.com/55608425/96534513-d91a3800-12ca-11eb-9708-af02b7cf11b3.png)

--------

- 논블로킹

```java
@DisplayName("Non-Blocking 테스트")
    @Test
    void nonBlocking() throws InterruptedException {

        final StopWatch jackWatch = new StopWatch();
        jackWatch.start();

        for (int loop = 0; loop < LOOP_COUNT; loop++) {
            webClient.get()
                    .uri(THREE_SECOND_URL)
                    .retrieve()
                    .bodyToMono(String.class)
                    .subscribe(it -> {
                        count.countDown();
                        System.out.println(it);
                    });
        }

        // count가 zero가 될 때까지 기다린다. 여기선 10초
        count.await(10, TimeUnit.SECONDS);
        jackWatch.stop();
        log.info("Total Time : {}", jackWatch.getTotalTimeSeconds());
    }
```


- 결과 : 논블로킹 방식이기 때문에 3초 API를 100번 돌려도 오래걸려야 4초 걸린다. 동시에 처리하기 때문이다.
- success - 숫자는 해당 요청을 처리한 순서를 의미한다. 동시성을 효과적을 처리하는 모습이다.

![image](https://user-images.githubusercontent.com/55608425/96535974-fef50c00-12cd-11eb-96d7-e5d64ac69271.png)

### 용어 정리
- retrieve() : HTTP 리퀘스트를 실행하고 리퀘스트 바디를 리턴한다.

```java
		   Mono<Person> bodyMono = client.get()
		       .uri("/persons/1")
		       .accept(MediaType.APPLICATION_JSON)
		       .retrieve()
		       .bodyToMono(Person.class);
```

- exchange() : HTTP 리퀘스트를 실행하고 ClientResponse를 리턴한다. 주의할 점은 exchange은 어떤 결과든 상관없이 response content를 소비하기 때문에 메모리 누수가 발생할 수 있다. 그래서 특별한 이유가 없다면 retrieve()를 사용해라.

- BodyToMono :  리스폰스 바디를 Mono 타입으로 추출한다. 만약 리스폰스 코드가 400 또는 500일 경우 Mono는 WebClientException을 갖게 된다.
- subcribe : Subscribe to this Mono and request unbounded demand. Mono를 구독한다(계속 지켜본다. 결과가 나왔는지?) 그리고 제한없는 demand를 요청한다.

# webflux

## webclient 이용해서 post 요청하는 법
- [참고 : 밸덩](https://www.baeldung.com/spring-5-webclient)
- webclient은 static 메서드 create()로 생성한다. 처음부터 url를 지정한 webclient를 생성할 수 있는 것 같다

```java
@Slf4j
@Service
public class LoginService {

    private final GithubProperties githubProperties;
    private WebClient webClient;

    public LoginService(GithubProperties githubProperties) {
        this.githubProperties = githubProperties;
        this.webClient = WebClient.create();
    }

    public ResponseEntity<Void> login(String redirectCode,
                                      HttpServletResponse response) {

        githubProperties.addRedirectCode(redirectCode);

        AccessTokenRequestDto accessTokenRequestDto
                = AccessTokenRequestDto.of(githubProperties);

        String accessToken = webClient.post()
                .uri(githubProperties.getAccessTokenRequestUrl())
                .body(Mono.just(accessTokenRequestDto), AccessTokenRequestDto.class)
                .exchange()
                .block()
                .bodyToMono(String.class)
                .block();

        log.info("accessToken : {}", accessToken);

        return new ResponseEntity<>(HttpStatus.FOUND);
    }
}
```

## webflus.get() 요청에서 응답이 배열로 올 때 타입 지정하는 법

- 처음엔 응답타입을 List로 가지는 객체를 생성하고 매핑하려 했는데 아래 에러가 뜬다. 이는 래퍼 클래스의 변수명을 응답결과에서 없기 때문에 json 키가 맞지 않다고 말하는 것.

```java
Cannot deserialize instance of object out of START_ARRAY token in Spring 3 REST Webservice
```

- exchange() 대신 retrieve() 사용하고 log()를 사용한다.

```java
            GithubEmailResponseDto[] githubEmail = webClient.get()
                    .uri(githubProperties.getEmailRequestUrl())
                    .accept(MediaType.APPLICATION_JSON)
                    .header("Authorization", "token " + accessToken)
                    .retrieve()
                    .bodyToMono(GithubEmailResponseDto[].class)
                    .log()
                    .block();

            log.info("userEmailInfo2 : {}", githubEmail[0].getEmail());

```
