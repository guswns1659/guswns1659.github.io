---
title: "Spring) Spring security"
header:
  teaser: /assets/spring.jpg
  overlay_image: /assets/spring.jpg
  overlay_filter: 0.2
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
categories:
  - Spring
---

# Spring security
- [참고 블로그](https://bravenamme.github.io/2019/08/01/spring-security-start/)

## 전체적인 흐름
- 사용자가 인증 요청을 한다
- filter에서 사용자 인증정보(id, password)를 바탕으로 Authentication Token을 생성한 다음 Manager에게 전달한다.
- Manager에서 provider의 anthenticate을 호출한다. (호출되는 과정은 WebSecurityConfigurerAdapter를 상속한 configure에서 정의한다.)
- provider에서 사용자 인증 정보가 추가된 Authentication Token을 반환한다.
- filter가 SecurityContext에 저장 후 인증 완료처리 한다.

## AuthenticationFilter
- doFilterInternal : doFilter와 기능이 똑같지만, 차이점은 하나의 요청 쓰레드에 있는 하나의 요청에 대해서만 동작한다.
- doFilter : 이 메서드의 구현체는 요청에 대해서 already filtered에 저장한다. 만약 요청이 이미 거기 있으면 다시 필터하지 않는다.

## WebMvcConfigurer
- 해당 인터페이스를 구현하면 로그인폼을 만들 수 있다.

## WebSecurityConfigurerAdapter
- 위 추상클래스를 구현하면 인증 로직을 구현하는 것
- WebSecurityConfigurerAdapter를 상속하고 configure의 설정은 순서대로 적용한다. 중요!
- configure에서 permitAll()로 특정 url을 exclude하려고 하는데 anonymous()에 대해선 적용이 안된다고 한다. 그래서 anonymous()를 추가했는데 반영이 안된다. -> doFilter에서 exception이 발생하기 때문에 적용이 안된다.

## 질문
- anonymous도 filter에 걸리는가?
