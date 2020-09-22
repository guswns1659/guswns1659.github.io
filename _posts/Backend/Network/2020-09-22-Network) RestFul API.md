---
header:
  overlay_image: /assets/spring.jpg
  overlay_filter: 0.2
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
categories:
  - Network
---

네트워크는 인터넷 세상을 가능하게 해준 기술입니다.

# RestFul API 설계 고민해보기
- 1. URL는 정보의 자원을 표현해야 한다.
- 2. 자원에 대한 행위는 HTTP Method (GET, POST, PUT, DELETE)로 표현한다.

![image](https://user-images.githubusercontent.com/55608425/93752246-56ee1380-fc39-11ea-832a-d1acb2dc41e3.png)

## URI 설계 시 주의할 점
- [ ] 슬래시 구분자는 계층관계를 나타내는데 사용
- [ ] 하이픈(-)은 URI 가독성을 높이는데 사용
- [ ] 밑줄(_)은 URI에 사용하지 않는다.
- [ ] URI 경로에는 소문자가 적합하다.
- [ ] 파일 확장자는 URI에 포함시키지 않는다. 헤더의 Accept를 사용한다.

## HTTP 응답 상태 코드

### 성공응답
- 200 : OK, 요청을 정상적으로 수행
- 201 : Created, 해당 리소스가 정상적으로 생성됨
- 202 : Accepted, 요청을 수신했지만 그에 응하여 행동할 수 없습니다. 결과를 바로 응답하지 않는 것.
- 204 : No Content, 요청에 대해서 보내줄 콘텐츠가 없지만 헤더는 의미있을 수 있다.

### 리다이렉션 메세지
- 301 : Moved Permanently, 요청한 리소스의 URI가 변경되었음을 의미한다. 새로운 URI가 응답 헤더의 Location에 담긴다.
- 302 : Found, 요청한 리소스의 URI가 일시적으로 변경되었다. 그래서 이후 요청에도 지금과 같은 URI로 요쳥해야 한다.

### 클라이언트 에러 응답
- 400 : Bad Request, 잘못된 문법으로 서버가 요청을 이해할 수 없음을 의미
- 401 : UnAuthorized, 리소스에 접근할 권한이 없음을 의미
- 403 : Forbidden, 클라이언트는 콘텐츠에 접근할 권리를 가지고 있지 않다. 401과 다른 점은 서버는 클라이언트가 누구인지 알고 있다.

### 서버 에러 응답
- 500 : Internal Server Error, 서버가 처리 방법을 모르는 상황. 처리 방법을 알 수 없다.
- 503 : Service Unavailable, 서버가 요청을 처리할 준비가 되지 않았습니다. 일반적인 원인은 유지보수를 위해 작동이 중단되거나 과부하가 걸렸을 때 입니다.
