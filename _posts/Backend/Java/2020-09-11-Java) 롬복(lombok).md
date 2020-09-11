---
title: 'Java) 롬복(lombok)'
header:
  teaser: /assets/java.jpg
  overlay_image: /assets/java.jpg
  overlay_filter: 0.2
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
categories:
  - Java
---

자바는 단언컨대 훌륭한 객체 지향 언어 중 하나입니다.


## 롬복

### 동작원리
- lombok annotation이 있는 실행코드를 컴파일할 때 annotation processrot로 등록된 lombokprocessor가 어노테이션을 확인하고 그에 맞는 메소드를 자동으로 생성하여 자바 바이트 코드로 변환을 시킨다.

### `@AllArgsConstructor`, `@RequiredArgsConstructor` 사용금지 대신 생성자에 `@Builder`를 붙여서 사용한다.
- 만약 객체 필드의 순서가 바뀌면 자동으로 위 어노테이션들이 생성자의 위치를 바꾼다. 그러면 기존에 존재하는 코드들은 이전의 순서를 유지해서 매개변수가 들어가기 때문에 심각한 버그를 발생할 수  있다.

### 무분별한 `@EqualsAndHashCode` 사용 자제
