---
title: Spring WebClient 사용 시 uri tag가 none으로 나올 때
date: 2024-05-03 11:49:08 +09:00
categories: [Software-Engineering]
tags: engineering
---

Spring boot를 3.x.x 버전이고 Spring WebClient을 사용할 때 이슈이다. uri을 람다식 내 uriBuilder에서 셋팅해서 사용할 경우 uri tag가 none으로 찍히는 이슈가 발생한다. 
이렇게 변경된 사유는 버전업을 하면서 query param에 따라서 나눠지는 모든 uri이 찍히는 이슈를 해결하기 위함이라고 한다. 

버전업 전에 이슈에 대해서 이야기 해보자. 예를 들어, WebClient로 외부 서버를 GET /api/post?id=${id}로 호출하면 id에 따라 /api/post?id=1, /api/post?id=2, /api/post?id=3 등 모든 uri가 찍히게 된다. 

그래서 위와 같이 같은 uri에 대해서 여러번 찍히는 이슈를 /api/post?id=${id} 같은 형식으로 한번만 찍히도록 변경했다. 
그래서 만약 한번만 uri가 찍히기 위해서는 아래와 같이 AS-IS에서 TO-BE로 작성해야한다.

AS-IS
```kotlin
WebClient.uri { it.uri("/api/post").param("id", 1) }.build(); 
```

TO-BE
```kotlin
WebClient.uri("/api/post") { it.param("id", 1) }.build(); 
```
