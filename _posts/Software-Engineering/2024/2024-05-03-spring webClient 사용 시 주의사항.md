---
title: Spring WebClient 사용 시 uri tag가 none으로 나올 때
date: 2024-05-03 11:49:08 +09:00
categories: [Software-Engineering]
tags: engineering
---

Spring boot를 3.x.x 버전이고 Spring webClient을 사용할 때 uri tag가 none으로 찍히는 이슈가 발생한다. 
사유는 버전업을 하면서 query param에 따라서 나눠지는 모든 uri이 찍히는 이슈를 해결했다고 한다. 
예를 들어 GET /api/post?id=${id} API를 호출하면 id에 따라 /api/post?id=1, /api/post?id=2, /api/post?id=3 등 모든 uri가 찍히게 이슈였다. 

그래서 위와 같은 케이스를 /api/post?id=${id} 같은 형식으로 한번만 찍힐 수 있게 하기 위함이다. 
아래와 같이 AS-IS에서 TO-BE로 작성해야한다.

AS-IS
```kotlin
WebClient.uri { it.uri("/api/post").param("id", 1) }.build(); 
```

TO-BE
```kotlin
WebClient.uri("/api/post) { it.param("id", 1) }.build(); 
```
