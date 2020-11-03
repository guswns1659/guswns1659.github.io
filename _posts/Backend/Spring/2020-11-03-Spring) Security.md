---
title: "Spring) Security"
header:
  overlay_image: /assets/spring.jpg
  overlay_filter: 0.2
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
categories:
  - Spring
---

# 스프링 시큐리티 이용한 비밀번호 암호화

암호화에 있어서 개발자들이 지켜야할 규칙들이 몇가지 있다.

1. 복호화(디코딩)가 불가능한 단방향 암호코드로 만들어져야 한다.
2. 암호는 개발자나 관리자도 알 수 없어야 하며 이용자 이외에는 누구도 접근할 수 없는 형태여야 한다.
3. 공격자가 예측할 수 없도록 솔트 처리를 해주어야 한다.

## 순서
- 스타터 시큐리티 의존성 추가, 스프링부트와 버전을 맞춘다. ex) 2.3.1

```java
// 비밀번호 암호화를 위한 security 의존성 추가, 스프링부트와 버전을 맞춘다
    implementation group: 'org.springframework.boot', name: 'spring-boot-starter-security', version: '2.3.1.RELEASE'
```

- BCryptPasswordEncoder()를 빈으로 등록

```java
@Configuration
public class SpringSecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    public void configure(WebSecurity web) {
        web.ignoring().antMatchers("/**");
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

- PasswordEncoder의 encode()를 사용한다.

```java
// 비밀번호 encode
        String encodePassword = passwordEncoder.encode(registerRequestDto.getPassword());
        registerRequestDto.setPassword(encodePassword);
```

## 시큐리티 disable 하는 법

```java
@Configuration
public class SpringSecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    public void configure(WebSecurity web) {
        web.ignoring().antMatchers("/**");
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

- [스프링 시큐리티 disable 하는 법](https://www.baeldung.com/spring-security-disable-profile)
