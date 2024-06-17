---
title: DDD START!
categories: [BOOK]
tags: book
---



## 기본 정보
- 지은이 : 최범균
- 옮긴이 :
- 출판사 : 지앤선
- 기간 : 2020.09.29 ~

> chap.x 제목 형식으로 정리. 중요 내용은 page를 기록하며 옮겨 적기

# chap6. 응용 서비스와 표현 영역

- p.170 표현 영역은 사용자의 요청을 해석한다. 컨트롤러의 역할이다.

<img src="https://user-images.githubusercontent.com/55608425/94524768-06e10380-026e-11eb-9888-eda7af384412.jpg" width=400>

- p.171 응용 서비스는 도메인 영역과 표현 영역을 연결해 주는 창구인 파사드 역할을 한다.

```java
public void changePhoneNumber(NewPhoneNumberRequest newPhoneNumberRequest, HttpServletRequest request) {
        Account foundAccount = findByEmail(request);
        foundAccount.changePhoneNumber(newPhoneNumberRequest);
    }
```

- p.172 응용 서비스가 이것보다 복잡하다면 응용 서비스에서 도메인 로직의 일부를 구현하고 있을 가능성이 높다.
- 도메인 객체 간의 실행 흐름을 제어하는 것과 더불어 응용 서비스의 주된 역할 중 하나는 트랜잭션 처리이다.

## 응용 서비스가 도메인 로직을 구하면 나타나는 문제점 : 응집도 떨어짐, 코드 중복
- p.174 1. 첫 번째 문제는 코드의 응집성이 떨어진다는 것이다. 도메인 데이터와 그 데이터를 조작하는 도메인 로직이 한 영역에 위치하지 않고 서로 다른 영역에 위치한다는 것은 도메인 로직을 파악하기 위해 여러 영역을 분석해야 한다는 것을 뜻한다.
- p.175 두 번째 문제는 여러 응용 서비스에서 동일한 도메인 로직을 구현할 가능성이 높아진다는 것이다.(코드 중복)
