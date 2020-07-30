## 깃허브 Oauth 적용법
아래 순서대로 차근차근 적용한다.

- jwt 의존성 추가

```java
compile group: 'io.jsonwebtoken', name: 'jjwt', version: '0.9.1'
```

- application.yml에 application-oauth 프로파일을 include한다

```java
spring:
  profiles:
    active: local
    include: aws, oauth

```

- jwtServie 구현

```java
@Service
public class JwtService {
    private final String GUEST = "guest";
    private final SignatureAlgorithm SIGNATUREALGORITHM = SignatureAlgorithm.HS256;
    private final long EXPIRATION_TIME = 1000 * 60 * 60 * 10;


    public String createJwt(String userId) {
        Map<String, Object> header = new HashMap<>();
        header.put(OauthEnum.TYP.getValue(), OauthEnum.TYP_VALUE.getValue());
        header.put(OauthEnum.ALG.getValue(), OauthEnum.ALG.getValue());

        Map<String, Object> payload = new HashMap<>();
        payload.put(OauthEnum.AUTHORIZATION.getValue(), userId);

        return Jwts.builder()
                .setHeader(header)
                .setClaims(payload)
                .setSubject(userId)
                .setIssuedAt(new Date(System.currentTimeMillis()))
                .setExpiration(new Date(System.currentTimeMillis() + EXPIRATION_TIME))
                .signWith(SIGNATUREALGORITHM, OauthEnum.SECRET_KEY.getValue())
                .compact();
    }

    public boolean isValidToken(String token) {
        try {
            Jwts.parser().setSigningKey(OauthEnum.SECRET_KEY.getValue()).parseClaimsJws(token);
            return true;
        } catch (Exception e) {
            throw new InvalidTokenException(e.getMessage());
        }
    }

    public String getUserId() {
        HttpServletRequest request = ((ServletRequestAttributes) RequestContextHolder.currentRequestAttributes()).getRequest();
        String token = request.getHeader(OauthEnum.AUTHORIZATION.getValue());
        if (token != null) {
            Jws<Claims> claims = Jwts.parser().setSigningKey(OauthEnum.SECRET_KEY.getValue()).parseClaimsJws(token);
            return claims.getBody().getSubject();
        }
        return GUEST;
    }
}
```

- InvalidTokenException 객체 구현

```java
@NoArgsConstructor
public class InvalidTokenException extends RuntimeException {
    public InvalidTokenException(String message) {
        super(message);
    }
}
```

- OauthEnum 객체에 oauth 관련 상수 넣기

```java
@Getter
public enum OauthEnum {
    USER_ID("userId"),  HEADER_LOCATION("Location"),
    MOBILE_REDIRECT_URL("issue04://?token="),
    HEADER_ACCEPT("Accept"), HEADER_MEDIA_TYPE("application/json"),
    AUTHORIZATION("Authorization"),
    OAUTH_URL_SERVER("https://github.com/login/oauth/authorize?client_id=bdd909bfff2137535182&redirect_uri=http://localhost:8080/callback&scope=user"),
    SECRET_KEY("issueTracker04"),
    TYP("typ"), TYP_VALUE("JWT"), ALG("HS256");

    private String value;

    OauthEnum(String value) {
        this.value = value;
    }
}
```

## LoginService 관련 객체 생성

- AccessTokenRequestBody 객체 생성

```java
@Getter
@NoArgsConstructor
@ToString
public class AccessTokenRequestBody {

    @JsonProperty("client_id")
    private String clientId;

    @JsonProperty("client_secret")
    private String clientSecret;

    @JsonProperty("code")
    private String code;

    @Builder
    public AccessTokenRequestBody(String clientId, String clientSecret, String code) {
        this.clientId = clientId;
        this.clientSecret = clientSecret;
        this.code = code;
    }
}
```


- GithubProperties 객체 생성

  - @ConfigurationProperties("github") 사용할 때 processor 관련 알람이 뜨면 아래 의존성을 추가한다.

  ```java
  annotationProcessor "org.springframework.boot:spring-boot-configuration-processor"
  ```

  ```java
  @Component
  @ConfigurationProperties("github")
  @Getter
  @Setter
  public class GithubProperties {

    public String accessTokenUrl;
    public String clientId;
    public String clientSecret;
    public String redirectUrl;
    public String userEmailRequestUrl;
  }

  ```



  - oauth 관련 설정을 관리할 application-oauth.yml 추가. clientSecret가 들어있으니 gitignore를 해야 하지만 배포된 이후에도 사용해야 하기에 하지 않음.
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

```java
@Getter
@NoArgsConstructor
@ToString
public class GithubResponseDto {

    @JsonProperty("access_token")
    private String accessToken;

    @JsonProperty("token_type")
    private String tokenType;

    @JsonProperty("scope")
    private String scope;

    public String getAccessToken() {
        return accessToken;
    }
}
```

- AccessToken으로 요청한 사용자 정보를 담는 GithubInformationOfUser 객체 생성

```java
@Getter
@ToString
@NoArgsConstructor
public class GithubInformationOfUser {

    @JsonProperty("login")
    private String userId;

    @JsonProperty("name")
    private String name;

    @JsonProperty("avatar_url")
    private String image;

    public void fixName() {
        this.name = this.userId;
    }

    public boolean nameIsNull() {
        return this.name == null;
    }
}
```

## UserService 관련 객체 생성

- AccountRepository 생성

```java
public interface AccountRepository extends JpaRepository<Account, Long> {

    Optional<Account> findByUserId(String userId);
}
```

- UserService 객체 생성

```java
@Service
@RequiredArgsConstructor
public class UserService {

    private final AccountRepository accountRepository;

    public void save(GithubInformationOfUser githubInformationOfUser) {
        Account account = accountRepository
                .findByUserId(githubInformationOfUser.getUserId()).orElse(new Account());
        accountRepository.save(account);
    }
}

```

- loginService 생성

```java
@Slf4j
@RequiredArgsConstructor
@Service
public class LoginService {

    private final GithubProperties githubProperties;
    private final JwtService jwtService;
    private final UserService userService;
    private final Integer EXPIRE_TIME = 60 * 60 * 6;

    public ResponseEntity<Void> loginProcess(String code, HttpServletResponse response) {
        GithubResponseDto githubResponseDto = requestAccessToken(code);
        log.info("Github AccessToken, TokenType, Scope Data : {}", githubResponseDto);
        GithubInformationOfUser githubInformationOfUser = requestUserInfo(githubResponseDto.getAccessToken());
        log.info("Github UserId : {}", githubInformationOfUser);

        userService.save(githubInformationOfUser);
        String jwt = jwtService.createJwt(githubInformationOfUser.getUserId());

        Cookie cookie = new Cookie(OauthEnum.USER_ID.getValue(), githubInformationOfUser.getUserId());
        cookie.setMaxAge(EXPIRE_TIME);
        response.addCookie(cookie);
        response.setHeader(OauthEnum.HEADER_LOCATION.getValue(), OauthEnum.MOBILE_REDIRECT_URL.getValue() + jwt);
        return new ResponseEntity<>(HttpStatus.FOUND);
    }

    private GithubResponseDto requestAccessToken(String code) {
        AccessTokenRequestBody accessTokenRequestBody = AccessTokenRequestBody.builder()
                .clientId(githubProperties.clientId)
                .clientSecret(githubProperties.clientSecret)
                .code(code)
                .build();
        HttpHeaders headers = new HttpHeaders();
        headers.set(OauthEnum.HEADER_ACCEPT.getValue(), OauthEnum.HEADER_MEDIA_TYPE.getValue());
        HttpEntity<?> httpEntity = new HttpEntity<>(accessTokenRequestBody, headers);
        ResponseEntity<GithubResponseDto> responseEntity = new RestTemplate()
                .postForEntity(githubProperties.accessTokenUrl, httpEntity, GithubResponseDto.class);
        return responseEntity.getBody();
    }

    private GithubInformationOfUser requestUserInfo(String accessToken) {
        HttpHeaders headers = new HttpHeaders();
        headers.setBearerAuth(accessToken);
        HttpEntity<?> httpEntity = new HttpEntity<>(headers);
        ResponseEntity<GithubInformationOfUser> responseEntity = new RestTemplate()
                .exchange(githubProperties.userEmailRequestUrl, HttpMethod.GET, httpEntity, GithubInformationOfUser.class);
        return githubUserValidation(responseEntity.getBody());
    }

    private GithubInformationOfUser githubUserValidation(GithubInformationOfUser githubInformationOfUser) {
        if (githubInformationOfUser.nameIsNull()) {
            githubInformationOfUser.fixName();
        }
        return githubInformationOfUser;
    }
}
```

- LoginController 객체 생성

```java
@Slf4j
@RequiredArgsConstructor
@RestController
public class LoginController {

    private final LoginService loginService;

    @GetMapping("/callback")
    public ResponseEntity<Void> oauthCallback(@Param("code") String code, HttpServletResponse response) {
        return loginService.loginProcess(code, response);
    }
}
```

- jwt 토큰을 검증하는 LoingInterceptro 객체 구현

```java
@Slf4j
@RequiredArgsConstructor
public class LoginInterceptor extends HandlerInterceptorAdapter {

    private final JwtService jwtService;

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) {
        if (request.getMethod().equals("OPTIONS")) {
            return true;
        }
        final String jwtToken = request.getHeader(OauthEnum.AUTHORIZATION.getValue());
        log.info("token >> {}", jwtToken);
        String userId = jwtService.getUserId(jwtToken);

        if (!jwtService.isValidToken(jwtToken)) {
            response.setStatus(HttpStatus.SC_UNAUTHORIZED);
            return false;
        }
        request.setAttribute("userId", userId);
        return true;
    }
}
```

- WebConfig에 LoginInterceptor를 빈으로 등록해 동작하도록 시킨다.

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addCorsMappings(CorsRegistry registry) {
        long MAX_AGE_SECS = 3600;
        registry.addMapping("/**")
                .allowedOrigins("*")
                .allowedMethods("GET", "POST", "PUT", "PATCH", "DELETE", "OPTIONS")
                .allowedHeaders("*")
                .allowCredentials(true)
                .maxAge(MAX_AGE_SECS);
    }

//    @Bean
//    public LoginInterceptor loginInterceptor() {
//        return new LoginInterceptor();
//    }
//
//    @Override
//    public void addInterceptors(InterceptorRegistry registry) {
//        registry.addInterceptor(loginInterceptor())
//                .addPathPatterns("/**")
//                .excludePathPatterns("/api/github/**")
//                .excludePathPatterns("/github/**") ;
//    }
}
```
