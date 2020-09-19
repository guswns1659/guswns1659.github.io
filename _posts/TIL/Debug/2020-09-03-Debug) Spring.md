---
title: "Debug) TDD"
header:
  overlay_image: /assets/write.jpg
  overlay_filter: 0.2
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
categories:
  - Debug
---

프로젝트에서 만난 문제를 해결한 이야기

## 에러 : `@WebMvcTest` 시 NoSuchBean~~ 에러가 발생할 때
- 해당 Controller에 있는 '@RequiredConstructor`를 제거하고 기본 생성자 추가하고 생성자로 빈을 주입받는다.

```java
@RestController
@NoArgsConstructor
public class RegisterController {

    private RegisterService registerService;

    public RegisterController(RegisterService registerService) {
        this.registerService = registerService;
    }

```

## 에러 : java.lang.IllegalArgumentException: Header value must not be null
- 문제상황 : CORS를 위한 filter를 적용할 때 발생하는 에러
- 해결 : 테스트 시 header를 추가한다.

```java
// when
        final ResultActions actions = mockMvc.perform(post("/account")
                .header("Origin", "*")
                .contentType(MediaType.APPLICATION_JSON)
                .content(asJsonString(registerWantDto))
                .accept(MediaType.APPLICATION_JSON))
                .andDo(print());
```
