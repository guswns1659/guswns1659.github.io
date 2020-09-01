---
title: "Spring) Wiki"
header:
  teaser: /assets/spring.jpg
  overlay_image: /assets/spring.jpg
  overlay_filter: 0.2
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
categories:
  - Spring
---

스프링 프레임워크는 객체지향 프레임워크입니다.

## 스프링부트

### 현재 스프링부트에서 사용하는 하이버네이트 버전을 확인하는 방법
- spring.io -> project -> spring boot

![image](https://user-images.githubusercontent.com/55608425/90307647-37b1e700-df13-11ea-9555-9b3f8e181433.png)

![image](https://user-images.githubusercontent.com/55608425/90307658-51ebc500-df13-11ea-92f4-3725b3d23c9c.png)

- org.hibernate 검색 후 버전 확인

![image](https://user-images.githubusercontent.com/55608425/90307675-747dde00-df13-11ea-804b-e05f852e7a3b.png)

# 스프링과 스프링부트의 차이

![image](https://user-images.githubusercontent.com/55608425/91444228-6636a700-e8af-11ea-906e-90c849a17929.png)

# @ConfigurationProperties("github") 설정
- 이 어노테이션을 사용하려면 application-yml에 해당 값(github)을 추가해야한다.

```java
github:
  client-id: c2e25fe082feb25b3a02
  client-secret: 849283d9227c9d35a9a6a6f82475bcb0d42eb549
  redirect-url: http://15.164.35.235/api/github/oauth/callback2
  access-token-request-url: https://github.com/login/oauth/access_token
```

- 위 설정과 매핑되는 클래스를 아래와 같이 설정한다. 그런데 매핑되는 이름이 카멜케이스여도 스프링에서 자동으로 변환해주는 것 같다.

```java
package com.titanic.airbnbclone.utils;

import com.fasterxml.jackson.annotation.JsonProperty;
import lombok.Getter;
import lombok.Setter;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

@Component
@ConfigurationProperties("github")
@Getter
@Setter
public class GithubProperties {

    @JsonProperty(value = "url")
    private String accessTokenRequestUrl;

    @JsonProperty(value = "client_id")
    private String clientId;

    @JsonProperty(value = "client_secret")
    private String clientSecret;

    @JsonProperty(value = "redirect_url")
    private String redirectUrl;

    private String redirectCode;

    public void addRedirectCode(String redirectCode) {
        this.redirectCode = redirectCode;
    }
}

```

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

# Spring

- Dto 생성 시 DB에서는 데이터가 잘 넘어왔는데 Dto of 메서드로 생성 시에 값이 안들어 가는 경우
- 원인 : 엔티티를 dto에 바로 사용하는 건 금지한다. Getter가 없기 때문에 직렬화할 때 문제가 발생하는 것 같다.
- dto로 들어가는 엔티티(Picture)에 `@Getter`를 붙여준다.

```java
@Entity
@Getter
public class Picture {

    @Id
    @GeneratedValue
    @Column(name = "picture_id")
    private Long id;

    @Lob
    @Column(name = "picture_url")
    private String url;

    @ManyToOne
    @JoinColumn(name = "accommodation_id")
    @JsonIgnore
    private Accommodation accommodation;
}

```
