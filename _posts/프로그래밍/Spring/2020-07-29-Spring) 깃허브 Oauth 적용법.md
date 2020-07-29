## 깃허브 Oauth 적용법
아래 순서대로 차근차근 적용한다.

- jwt 의존성 추가

```java
compile group: 'io.jsonwebtoken', name: 'jjwt', version: '0.9.1'
```

- jwtServie 구현

- InvalidTokenException 객체 구현

- OauthEnum 객체에 oauth 관련 상수 넣기

## LoginService 관련 객체 생성

- AccessTokenRequestBody 객체 생성

- GithubProperties 객체 생성

  - @ConfigurationProperties("github") 사용할 때 processor 관련 알람이 뜨면 아래 의존성을 추가한다.

  ```java
  annotationProcessor "org.springframework.boot:spring-boot-configuration-processor"
  ```

  - oauth 관련 설정을 관리할 application-oauth.yml 추가. clientSecret가 들어있으니 gitignore를 한다.
  - clientId, clientSecret, redirectUrl, userEmailRequestUrl은 각 프로젝트에 따라 바꾼다.

  ```java
  github:
    accessTokenUrl: https://github.com/login/oauth/access_token
    clientId : bdd909bfff2137535182
    clientSecret : 5de5688e0541838556d68954769ed7990ec54fac
    redirectUrl : http://localhost:8080/callback
    userEmailRequestUrl : https://api.github.com/user
  ```

- 깃허브에 accessToken을 요청한 뒤 받은 정보를 담는 GithubResponseDto 객체 생성

- AccessToken으로 요청한 사용자 정보를 담는 GithubInformationOfUser 객체 생성

## UserService 관련 객체 생성

- AccountRepository 생성

- LoginController 객체 생성
