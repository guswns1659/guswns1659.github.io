---
header:
  overlay_image: /assets/spring.jpg
  overlay_filter: 0.2
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
categories:
  - Network
---

네트워크는 인터넷 세상을 가능하게 해준 기술입니다.

### 클라이언트가 requset할 때 jwt 토큰을 Header에 넣었는데 cors에 안 걸리려면

- Header의 key를 SimpleCorsFilter.java에 추가하기
- 이번 경우에는 Authorization을 추가했다. 

```java
@Override
    public void doFilter(ServletRequest req, ServletResponse res, FilterChain chain) throws IOException, ServletException {

        HttpServletRequest request = (HttpServletRequest) req;
        HttpServletResponse response = (HttpServletResponse) res;
        response.setHeader("Access-Control-Allow-Origin", request.getHeader("Origin"));
        response.setHeader("Access-Control-Allow-Credentials", "true");
        response.setHeader("Access-Control-Allow-Methods", "POST, GET, OPTIONS, DELETE, PUT, PATCH");
        response.setHeader("Access-Control-Max-Age", "3600");
        response.setHeader("Access-Control-Allow-Headers", "Content-Type, Accept, X-Requested-With, remember-me, Authorization");
        chain.doFilter(req, res);
    }
```

