---
title: "Network) Wiki"
header:
  overlay_image: /assets/spring.jpg
  overlay_filter: 0.2
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
categories:
  - Network
---

네트워크는 인터넷 세상을 가능하게 해준 기술입니다.

# HTTP 메서드 멱등성

## 멱등성이란?
- 연산을 여러 번 적용하더라도 결과가 달라지지 않는 성질을 의미합니다. 즉 메서드가 여러 번 실행되어도, 결과는 같으므로 안전하게 사용할 수 있는 성질이기도 합니다.
- HTTP 메서드 중에서 GET, PUT, DELETE가 멱등성을 유지되어야 한다.
- 삭제를 할 때 해당 id의 엔티티가 없더라도 삭제가 된 것이니 삭제 성공, 실제로 삭제가 이루어져도 삭제성공. 같은 결과를 유지해야 한다. 아래와 같이 네트워크와 통신할 때 발생할 수 있는 에러. 이를 방지하기 위해 멱등성을 유지해야 한다.

![image](https://user-images.githubusercontent.com/55608425/97660832-0a0e2000-1ab6-11eb-9309-c10f791f35ef.png)
- [그림 참고 : 지속 가능한 소프트웨어를 위한 코딩 방법 - 마지막 맺음말.](https://meetup.toast.com/posts/218)

- 아래는 todo를 삭제하는 메서드

```java
public TodoDeleteResponse delete(Long todoId) {
        try {
            Todo foundTodo = todoRepository.findById(todoId).orElseThrow(
                    () -> new NoSuchTodoException("해당 id의 Todo는 없습니다.")
            );
            todoRepository.delete(foundTodo.getId());
            return TodoDeleteResponse.from("삭제 성공");
        } catch (NoSuchTodoException e) {
            return TodoDeleteResponse.from("삭제 성공");
        }
    }
```

# 확장성 있는 웹 아키텍쳐와 분산 시스템

## 대규모 웹 시스템을 설계할 때 고려해야 할 주요 사항
- 가용성
- 성능
- 신뢰성(일관성)
- 확장성 : 더 많은 부하를 처리할 수 있도록 처리량을 증가시키기 위해 필요한 노력
- 관리성 : 쉽게 관리할 수 있는 시스템을 설계하는 것
- 비용 : 빌드 시간, 교육 비용 등 모든 비용.

## 서비스들

> 각 기능별로 서비스를 나눠서 역할을 나눈다.

### SOA(Service-Oriented Architecture)
- 명확하게 기능 별로 서비스를 구성한다. 각각의 서비스는 다른 서비스와 상호 작용을 위해 다른 서비스에서 공개하는 API 형태인 추상화된 인터페이스를 사용한다.

## 이중화 (Redundancy) - 여러개를 두어 문제 상황에 대처하는 백업의 의미
- DB의 이중화도 중요하지만 서비스의 이중화도 중요하다.

![image](https://user-images.githubusercontent.com/55608425/97938914-020bf400-1dc6-11eb-8e72-09ae5c174a7b.png)

## 파티션
- 데이터의 수가 많아질 때 수평적 확장이 가능해야 한다. 하지만 데이터 정합성과 데이터 지역성을 바탕으로 데이터간 위치가 멀어지니 성능이 저하될 수 있다.

## 빠른 데이터 엑세스를 위한 4가지 방법

### 캐시
- 캐시 미스가 발생할 때 전역 캐시와 분산 캐시 방법이 있다.

### 프록시
- 캐시와 다른 점은 프락시는 여러 클라이언트가 원하는 문서를 제공하기 위해 요청이나 콜을 최적화하는 방법이다.

### 인덱스
- 데이터를 논리적으로 정렬을 시킨 구조. 조회와 정렬할 때 빠르다.

### 로드 밸런서

## 빠른 데이터 쓰기를 위한 방법

### 큐
- 클라이언트의 요청을 비동기-논블락킹 방식으로 처리할 수 있는 자료구조
