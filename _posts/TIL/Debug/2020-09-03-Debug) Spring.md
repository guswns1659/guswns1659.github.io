---
title: "Debug) Spring"
header:
  overlay_image: /assets/write.jpg
  overlay_filter: 0.2
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
categories:
  - Debug
---

프로젝트에서 만난 문제를 해결한 이야기

# Spring
- 가끔 프로젝하다가 `git rm --cached -r`을 하다가 프로젝트가 사라지는(?) 현상이 발생하는데 신기한점은 파일은 그대로 있다.
- 이때 그냥 커밋, 푸시를 하고 프로젝트를 지운 뒤 다시 설치한다.

![image](https://user-images.githubusercontent.com/55608425/92064655-df1a8f00-edd8-11ea-8cfa-717f4e81decd.png)


## Exceeded limit on max bytes to buffer webflux 에러 발생 시
- 문제상황 : webflux를 이용해 테스트 하던 중 응답받는 값이 buffer byte를 넘었다는 에러
- 원인 : max-in-memory-size가 작기 때문.
- 해결 : application.yml에서 위 옵션을 늘린다. 아래처럼 [참고](https://stackoverflow.com/questions/59735951/databufferlimitexception-exceeded-limit-on-max-bytes-to-buffer-webflux-eroor)

```java
spring:
  codec:
    max-in-memory-size: 10MB
```
