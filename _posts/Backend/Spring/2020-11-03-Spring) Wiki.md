---
title: "Spring) Wiki"
header:
  overlay_image: /assets/spring.jpg
  overlay_filter: 0.2
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
categories:
  - Spring
---

스프링 프레임워크는 객체지향 프레임워크입니다.

## 과거
- 자바 프레임워크는 EJB가 존재했다. 어렵고 복잡하고 느렸다. 옛날로 돌아가자는 운동으로 POJO가 나온다. 이 때 스프링과 하이버네이트가 오픈소스로 시작한다. 하이버네이트를 만든 게빈킹을 데려와 자바 표준 JPA를 만든다.

# 스프링 프레임워크

![image](https://user-images.githubusercontent.com/55608425/96549287-41c4dd00-12ea-11eb-9654-0f56f677d8f1.png)

## 스프링부트 vs 스프링 (결국, 스프링 프레임워크를 사용하는 것)
- 톰캣같은 웹 서버를 내장해서 웹서버를 설치 않아도 된다.
- 손쉬운 빌드 구성을 위한 starter 종속성 제공
- 스프링과 외부 라이브러리 버전을 자동 구성 (이전에는 맞추는 게 힘들었다)
- 간결한 설정

## 스프링 단어?
- 스프링 DI 컨테이너 기술
- 스프링 프레임워크
- 스프링 전부를 포함한 스프링 생태계

## 스프링은 왜 만들어졌는가? (핵심 컨셉)
- 자바 언어 기반의 프레임워크
- **스프링은 좋은 객체 지향 애플리케이션을 개발할 수 있도록 도와주는 프레임워크**
- 과거엔 EJB에 의존적인 코드를 짜게 되고 객체지향 코드를 짤 수 없게 된다.

## 객체지향 특징
- 역할과 책임을 가진 객체 간의 협력 관계로 프로그래밍을 바라보는 관점
- 유연하고 변경에 강하다.
- 핵심은 다형성이다.

### 다형성의 본질
- 인터페이스를 구현한 객체 인스턴스를 실행 시점에 유연하게 변경할 수 있다.
- 클라이언트를 변경하지 않고 서버의 구현 기능을 유연하게 변경할 수 있다는 점이다.

### 역할과 구현을 분리하는 단점
- 인터페이스 자체가 변하면 클라이언트, 서버 모두에 큰 변경이 발생한다.
- 뻔하지만 인터페이스를 안정적으로 잘 설계하는 것이 중요하다.

### 스프링과 객체지향 (중요)
- 스프링은 다형성을 극대화해서 이용할 수 있게 도와준다.
- 제어의 역전, 의존관계 주입은 다형성을 활용해서 역할과 구현을 편리하게 다룰 수 있도록 지원한다.

## SOLID

## SRP
- 한 클래스는 하나의 책임만 가져야 한다.
- 중요한 기준은 변경이다. 변경이 있을 때 파급효과가 적으면 단일 책임 원칙을 잘 따른 것.

## OCP
- 확장에는 열려 있으나 변경에는 닫혀 있어야 한다.
- 기존 코드가 인터페이스에 의존하면 기능이 추가되고 변경 가능성이 줄어든다.
- 단순히 다형성만으로는 OCP를 구현하기는 어렵다. -> 나는 이걸 팩토리패턴으로 해결.

## LSP
- 컴파일 단계를 이야기 하는 것이 아니라, 상위 클래스의 메서드는 하위 클래스의 메서드로 바뀌어도 동작해야 한다.
- 예) 자동차 인터페이스의 엑셀은 앞으로 가라는 기능, 뒤로 가게 구현하면 LSP 위반, 느리더라도 앞으로 가야함.

## ISP
- 특정 클라이언트를 위한 인터페이스 여러개가 범용 인터페이스 하나보다 낫다.
- 예) 자동차 인터페이스 -> 운전 인터페이스, 정비 인터페이스로 분리
- 예) 사용자 클라이언트 -> 운전자 클라이언트, 정비사 클라이언트로 분리
- 분리하면 정비 인터페이스가 변해도 운전 인터페이스에 영향을 주지 않는다.

## DIP
- 고수준 모듈은 저수준 모듈의 구현에 의존하면 안된다.
- 구현 클래스에 의존하지 말고, 인터페이스에 의존하라는 뜻.

## 정리
- 다형성만으로는 OCP와 DIP를 지킬 수 없다. -> 어떻게 해야 할까

## 다시 스프링으로
- 스프링은 DI로 다형성 + OCP, DIP를 가능하게 지원해준다.
- DI 컨테이너 제공
- 클라이언트 코드의 변경없이 확장 가능

## 실무 고민
- 인터페이스를 도입하면 추상화라는 비용이 발생한다. -> 개발자가 코드를 한번 더 뜯어봐야 하는 문제가 발생

## Ioc, DI, 그리고 컨테이너

### 제어의 역전
- 기존 프로그램은 구현 객체가 프로그램의 제어 흐름을 스스로 조종했다. 하지만 AppConfig가 등장한 이후에는 프로그램의 제어 흐름은 이제 AppConfig가 가져간다. 이렇게 프로그램의 제어흐름을 직접 제어하는 것이 아니라 외부에서 관리하는 것을 제어의 역전이라 한다.

#### 프레임워크 vs 라이브러리
- 프레임워크가 내가 작성한 코드를 제어하고, 대신 실행하면 그것은 프레임워크가 맞다. (JUnit)
- 반면에 내가 작성한 코드가 직접 제어의 흐름을 담당한다면 그것은 프레임워크가 아니라 라이브러리다

### DI
- 의존관계는 정적인 클래스 의존 관계와 실행 시점에 결정되는 동적인 객체(인스턴스) 의존 관계, 둘을 분리해서 생각해야 한다.

#### 정적인 클래스 의존 관계
- 클래스가 사용하는 import 코드만 보고 의존관계를 쉽게 판단할 수 있다. 정적인 의존관계는 애플리케이션
을 실행하지 않아도 분석할 수 있다. 클래스 다이어그램을 보자. OrderServiceImpl 은 MemberRepository , DiscountPolicy 에의존한다는 것을 알 수 있다.그런데 이러한 클래스 의존관계 만으로는 실제 어떤 객체가 OrderServiceImpl 에 주입 될지 알 수 없다.

![image](https://user-images.githubusercontent.com/55608425/96599375-d0f1e500-132a-11eb-9a5e-e344c71b87b9.png)

#### 동적인 객체 인스턴스 의존 관계
- 애플리케이션 실행 시점에 실제 생성된 객체 인스턴스의 참조가 연결된 의존 관계다.
- 애플리케이션 실행 시점(런타임)에 외부에서 실제 구현 객체를 생성하고 클라이언트에 전달해서 클라이언
트와 서버의 실제 의존관계가 연결 되는 것을 의존관계 주입이라 한다.
- 의존관계 주입을 사용하면 정적인 클래스 의존관계를 변경하지 않고, 동적인 객체 인스턴스 의존관계를 쉽
게 변경할 수 있다.

### DI 컨테이너
- 객체를 생성하고 관리하면서 의존관계를 연결해주는 것을 DI 컨테이너라 한다.

## @Component, @Bean의 차이

- [복사 : 기억보단 기록을, 내용을 그대로 가져왔습니다.](https://jojoldu.tistory.com/27#:~:text=%40Bean%EC%9D%80%20setter%EB%82%98%20builder,%EB%A7%A1%EA%B8%B0%EB%8A%94%20%EA%B2%83%20%EC%B0%A8%EC%9D%B4%EA%B0%80%20%EC%9E%88%EC%A7%80%EC%9A%94.)

@Bean의 경우 개발자가 컨트롤이 불가능한 외부 라이브러리들을 Bean으로 등록하고 싶은 경우에 사용된다.

반대로 개발자가 직접 컨트롤이 가능한 Class들의 경우엔 @Component를 사용한다.

그럼 개발자가 생성한 Class에 @Bean은 선언이 가능할까?

@Bean과 @Component는 각자 선언할 수 있는 타입이 정해져있어 해당 용도외에는 컴파일 에러를 발생시킨다.


## 스프링부트

### 현재 스프링부트에서 사용하는 하이버네이트 버전을 확인하는 방법
- spring.io -> project -> spring boot

![image](https://user-images.githubusercontent.com/55608425/90307647-37b1e700-df13-11ea-9555-9b3f8e181433.png)

![image](https://user-images.githubusercontent.com/55608425/90307658-51ebc500-df13-11ea-92f4-3725b3d23c9c.png)

- org.hibernate 검색 후 버전 확인

![image](https://user-images.githubusercontent.com/55608425/90307675-747dde00-df13-11ea-804b-e05f852e7a3b.png)

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

## spring.datasource.initialization-mode
- embedded DB외에 schema.sql로 스키마를 생성할 때 필요한 옵션
- 외부 데이터를 사용할 때 필요

- [인프런 백기선님 답변](https://www.inflearn.com/questions/5761)

## swagger url을 interceptor에서 지우고 싶을 때

```java
@Override
    public void addInterceptors(InterceptorRegistry registry) {

        String[] excludePathPatterns = new String[]{"/account/**", "/goal/**"};
        String[] swaggerPaths = new String[]{"/v2/api-docs", "/swagger-resources/**", "/swagger-ui.html", "/webjars/**"};

        registry.addInterceptor(loginInterceptor())
                .addPathPatterns(ALL_PATH)
                .excludePathPatterns(excludePathPatterns)
                .excludePathPatterns(swaggerPaths);

    }
```

## Swagger에서 요청 시 header 넘기는 기능 구현

- [참고 : https://stackoverflow.com/questions/40801442/add-a-header-parameter-in-swagger-ui-documentation-with-springfox/40801443](https://stackoverflow.com/questions/40801442/add-a-header-parameter-in-swagger-ui-documentation-with-springfox/40801443)

# 스프링

## TDD 적용 방식 변경 해보기
- 기존엔 컨트롤러 테스트 작성 -> 서비스 테스트 -> 레포지토리 테스트 작성으로 진행했다. 컨트롤러 통합 테스트를 진행하니 직접 port로 api를 요청하는 테스트를 진행하니 테스트의 단위가 커지고 무엇보다 서비스의 테스트가 사라진다.
- 남은 포크 프로젝트에서는 영한님처럼 서비스 -> 레포지토리 순서로 테스트를 하고 마지막에 컨트롤러 통합테스트를 진행하자. 아래 예시처럼.

```java
@RunWith(SpringRunner.class)
@SpringBootTest
@Transactional
public class MemberServiceTest {

    @Autowired
    private MemberService memberService;

    @Autowired
    private MemberRepository memberRepository;

    @Autowired
    private EntityManager entityManager;

    @Test
    public void 회원가입() throws Exception {
        // given
        Member member = new Member();
        member.setName("kim");

        // when
        Long savedId = memberService.join(member);

        // then
        entityManager.flush();
        assertThat(memberRepository.findOne(savedId)).isEqualTo(member);
    }

    @Test(expected = IllegalStateException.class)
    public void 중복_회원_예외() throws Exception {
        // given
        Member member1 = new Member();
        member1.setName("kim1");

        Member member2 = new Member();
        member2.setName("kim1");

        // when
        memberService.join(member1);
        memberService.join(member2); // 예외가 발생 해야 한다.

        // then
        fail("예외가 발생해야 한다.");
    }

}
```

# Spring

## DTO의 필드를 getter로  가져오지 말고 바로 DTO를 넘겨서 내부에서 구현한다.

```java
public void register(RegisterRequest registerRequest, HttpServletResponse response) {
        validateDuplicateEmail(registerRequest);

        // 비밀번호 encode
        String encodePassword = passwordEncoder.encode(registerRequest.getPassword());
        Member account = Member.of(registerRequest, encodePassword);
        accountRepository.save(account);
        jwtService.loadJwtToHeader(response, registerRequest);
    }
```

## AccountDto들의 공통적인 속성을 상속할 수 있도록 AccountRequestDto를 생성한다.
- 콘크리트 DTO 클래스들이 상속받을 수 있게 한다.
- 이유 : 추상화를 통해 변경의 비용을 줄이기 위해

- 추상DTO와 콘크리트 DTO 클래스

```java
@Getter
@SuperBuilder
@NoArgsConstructor
public abstract class AccountRequestDto {

    private String email;
    private String password;

    public AccountRequestDto(String email, String password) {
        this.email = email;
        this.password = password;
    }

    public void changeEncodedPassword(String encodePassword) {
        this.password = encodePassword;
    }
}

@Getter
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@SuperBuilder
public class RegisterRequest extends AccountRequestDto {

    private String name;
    private String phoneNumber;

    public RegisterRequest(String email, String password, String name, String phoneNumber) {
        super(email, password);
        this.name = name;
        this.phoneNumber = phoneNumber;
    }

    public static RegisterRequest of(String email, String password, String name, String phoneNumber) {
        return RegisterRequest.builder()
                .email(email)
                .password(password)
                .name(name)
                .phoneNumber(phoneNumber)
                .build();
    }
}

```

- 위 상속이 활용되는 메서드 : JwtProvider 속 loadJwtToHeader
- 파라미터를 상위 클래스인 AccountRequestDto로 받는다.

```java
public void loadJwtToHeader(HttpServletResponse response, AccountRequestDto accountRequestDto) {
        response.setHeader(LoginEnum.AUTHORIZATION.getValue(), createJwtTokenWithEmail(accountRequestDto.getEmail()));
        log.info("jwtToken, {}", createJwtTokenWithEmail(accountRequestDto.getEmail()));
    }

```

## Interceptor에서 excludePath 지정할 때 *, **, ?의 의미

![image](https://user-images.githubusercontent.com/55608425/94395089-cfa12280-0199-11eb-9c5f-868d0e0fdad4.png)

## Interceptor
- api가 같은 단계로 나눠진다면 함께 인터셉터에서 제외된다. (무슨의미냐면..)
- 아래에서 "/account/{email}/\***"로 excludePath에 추가하면 /account/\*** 이렇게 제외되는 것이랑 마찬가지
- 해결책 : 앞에 구분자를 더 둔다.  "/account/{email}/\***" -> /account/email/{email}/\***

```java
@Override
    public void addInterceptors(InterceptorRegistry registry) {

        /*
         * 1. 회원가입하는 url (~/account) 제외
         * 2. 중복 이메일 확인하는 url (~/account/email/{email}) 제외
         */
        String[] excludePathPatterns = new String[]{"/account", "/account/{email}/**"};
        String[] swaggerPaths = new String[]{"/v2/api-docs", "/swagger-resources/**", "/swagger-ui.html", "/webjars/**"};

        registry.addInterceptor(loginInterceptor())
                .addPathPatterns(ALL_PATH)
                .excludePathPatterns(excludePathPatterns)
                .excludePathPatterns(swaggerPaths);
    }
```

## Devtools
- 타임리프로 개발 시 매번 어플리케이션을 다시 시작하지 않고 재컴파일만 해도 변경 사항이 반영되게 도와준다.
- devtools 의존성을 추가하고 재컴파일(Ctrl + Shift + F9) 를 한다.

## bootStrap(부트스트랩)
- CSS와 JS를 쉽게 적용할 수 있는 라이브러리
- [부트스트랩 사이트](https://getbootstrap.com/)에 들어간 뒤 Download를 클릭한다. (아래 사진 참고)

![image](https://user-images.githubusercontent.com/55608425/94646239-c8f4e580-0328-11eb-8cca-3b745967a157.png)

- CSS/JS를 다운받고 압축 푼 CSS, JS 폴더를 스프링부트 프로젝트 main/resources/static 폴더에 넣는다.

![image](https://user-images.githubusercontent.com/55608425/94646318-f772c080-0328-11eb-8920-956aac60e766.png)

![image](https://user-images.githubusercontent.com/55608425/94646343-03f71900-0329-11eb-88e1-9db6a3f5e4a6.png)

- 위 방법했는데 CSS가 적용이 안되면 resources 폴더 마우스 오른쪽 클릭해 'reload from disk'로 업데이트를 하고 build를 다시한다(Ctrl + F9)

![image](https://user-images.githubusercontent.com/55608425/94646487-4ae50e80-0329-11eb-8154-bafe0c0a44f7.png)

## 위치를 올바르게 정렬하고 싶을 때
- jombotron-narrow.css를 만들어서 적용시킨다.

![image](https://user-images.githubusercontent.com/55608425/94646679-b4fdb380-0329-11eb-9c58-8b1ff8107ae5.png)

- jombotron 코드

```java
/* Space out content a bit */
body {
    padding-top: 20px;
    padding-bottom: 20px;
}
/* Everything but the jumbotron gets side spacing for mobile first views */
.header,
.marketing,
.footer {
    padding-left: 15px;
    padding-right: 15px;
}
/* Custom page header */
.header {
    border-bottom: 1px solid #e5e5e5;
}
/* Make the masthead heading the same height as the navigation */
.header h3 {
    margin-top: 0;
    margin-bottom: 0;
    line-height: 40px;
    padding-bottom: 19px;
}
/* Custom page footer */
.footer {
    padding-top: 19px;color: #777;
    border-top: 1px solid #e5e5e5;
}
/* Customize container */
@media (min-width: 768px) {
    .container {
        max-width: 730px;
    }
}
.container-narrow > hr {
    margin: 30px 0;
}
/* Main marketing message and sign up button */
.jumbotron {
    text-align: center;
    border-bottom: 1px solid #e5e5e5;
}
.jumbotron .btn {
    font-size: 21px;
    padding: 14px 24px;
}
/* Supporting marketing content */
.marketing {
    margin: 40px 0;
}
.marketing p + h4 {
    margin-top: 28px;
}
/* Responsive: Portrait tablets and up */
@media screen and (min-width: 768px) {
    /* Remove the padding we set earlier */
    .header,
    .marketing,
    .footer {
        padding-left: 0;padding-right: 0;
    }
    /* Space out the masthead */
    .header {
        margin-bottom: 30px;
    }
    /* Remove the bottom border on the jumbotron for visual effect */
    .jumbotron {
        border-bottom: 0;
    }
}
```

- header.html에서 부트스트랩 설정을 적용했기 때문에 적용된 것

![image](https://user-images.githubusercontent.com/55608425/94646783-fdb56c80-0329-11eb-8944-c086daafb396.png)

## Validation
- 컨트롤러에서 입력받는 값에 대한 validation을 어노테이션 기반으로 할 수 있다.
- BindingResult result는 만약 DTO가 올바르지 않은 값이 있다면 에러가 담기는 객체이다. result.hasErrors()를 이용해 에러가 발생했다면 해당하는 결과값을 리턴할 수 있다.
- 만약 RestFul API라면 ResponseEntity<>()를 응답하면 될 듯?

```java
// 회원가입 컨트롤러
@PostMapping("/members/new")
    public String create(@Valid MemberForm form, BindingResult result) {

        if (result.hasErrors()) {
            return "members/createMemberForm";
        }

        Address address = new Address(form.getCity(), form.getStreet(), form.getZipcode());

        Member member = new Member();
        member.setName(form.getName());
        member.setAddress(address);

        memberService.join(member);
        return "redirect:/";
    }

// 회원 가입 폼(DTO)
@Getter
@Setter
public class MemberForm {

    @NotEmpty(message = "회원 이름은 필수입니다")
    private String name;

    private String city;
    private String street;
    private String zipcode;
}
```

# 타임리프
- th:replace라는 태그는 공통적으로 사용되는 템플릿에 대한 코드를 모은 것이다. 주로 header, body, footer 등이 있다.
- 아래 코드에서 `<head th:replace="fragments/header :: header">` 이부분의 의미는 fragements 폴더 속 header.html로 대체하겠다는 의미이다. 그러니 해당 폴더가 저 위치에 존재해야 한다.

```java
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<head th:replace="fragments/header :: header">
    <title>Hello</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8"/>
</head>
<body>
<div class="container">
    <div th:replace="fragments/bodyHeader :: bodyHeader"/>
    <div class="jumbotron">
        <h1>HELLO SHOP</h1>
        <p class="lead">회원 기능</p>
        <p>
            <a class="btn btn-lg btn-secondary" href="/members/new">회원 가입</a>
            <a class="btn btn-lg btn-secondary" href="/members">회원 목록</a>
        </p>
        <p class="lead">상품 기능</p>
        <p>
            <a class="btn btn-lg btn-dark" href="/items/new">상품 등록</a>
            <a class="btn btn-lg btn-dark" href="/items">상품 목록</a>
        </p>
        <p class="lead">주문 기능</p>
        <p>
            <a class="btn btn-lg btn-info" href="/order">상품 주문</a>
            <a class="btn btn-lg btn-info" href="/orders">주문 내역</a>
        </p>
    </div>
    <div th:replace="fragments/footer :: footer"/>
</div> <!-- /container --></body>
</html>
```

# 외부 API

- 외부 API 요청 후 JSON 데이터를 내가 원하는 필드만 받고 싶을 때는 DTO를 원하는 필드만 지정하면 된다.
- 원래 10개 이상 넘어오는 데이터인데 아래 원하는 필드만 추려냄

```java
package com.banksalad.jackcalculator.web.dto;

import lombok.Getter;
import lombok.Setter;
import lombok.ToString;

@Getter
@ToString
public class StockHistoryDto {

    private String date;
    private double open;
    private double close;
    private double high;
    private double low;
}

```
