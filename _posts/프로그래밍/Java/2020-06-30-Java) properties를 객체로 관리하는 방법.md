## properties를 객체로 관리하는 방법

- 의존성 추가

```java
annotationProcessor "org.springframework.boot:spring-boot-configuration-processor"
```

- application.properties에 추가 .

```java
github.url = https://github.com/login/oauth/access_token;
github.client_id = 6adbd2fe704c49a614b7;
github.client_secret = 9130ab10336dcdec503fb7ec923f3e1193db3872;
github.redirect_url = http://15.164.34.6/api/github/oauth/callback;
```

- 클래스 생성

```java
package com.codesquad.baseball10.utils;

import lombok.Getter;
import lombok.Setter;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

@Component
@ConfigurationProperties("github")
@Getter
@Setter
public class GithubProperty {

    private String url;
    private String client_id;
    private String client_secret;
    private String redirect_url;
}

```

- Test 코드 작성

  ```java
  @RunWith(SpringRunner.class)
  @SpringBootTest
  public class PropertiesTest {
  
      @Autowired
      private GithubProperty githubProperty;
  
      @Test
      public void githubPropertiesTest() {
          assertThat(githubProperty.getUrl()).isNotNull();
          assertThat(githubProperty.getClient_id()).isNotNull();
          assertThat(githubProperty.getClient_secret()).isNotNull();
          assertThat(githubProperty.getRedirect_url()).isNotNull();
      }
  }
  ```

  