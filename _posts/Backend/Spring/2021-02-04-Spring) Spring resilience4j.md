---
title: "Spring) Spring resilience4j"
header:
  teaser: /assets/spring.jpg
  overlay_image: /assets/spring.jpg
  overlay_filter: 0.2
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
categories:
  - Spring
---

# resilience4j

## Introduce
서킷브레이커의 상태는 CLOSED, OPEN, HALF OPEN으로 나눠진다. 기본은 CLOSED 상태인데 실패율이 기준을 넘으면 OPEN으로 상태가 변화한다. 이후 대기 시간이 경과하면 HALF OPEN 상태로 변한다. 이후 실패율이 기준을 넘지 않으면 Closed상태로 변하고, 기준을 다시 넘으면 OPEN상태로 갑니다.

서킷브레이커는 슬라이딩 윈도우를 사용하여 호출 결과를 저장하고 집계한다. 슬라이딩 윈도우는 원형 배열로 구성되어 있고 만약 크기가 10개라면 항상 10개의 측정값이 존재한다. 새 요청 결과가 들어오면 가장 오래된 측정을 제거하고 전체 결과에서 제거한다. 실패율과 느린 요청율을 계산한다.

실패율이나 느린 요청율이 임계값을 넘어가면 서킷브레이커는 OPEN상태가 된다. 실패율과 느린 요청의 비율은 최소 요청 개수가 저장된 경우만 계산할 수 있다. 최소한 10개의 요청이 있어야 실패율을 계산한다면 무조건 11개 이상의 요청이 와야 실패율을 계산할 수 있다. 서킷브레이커는 DISABLED(항상 엑세스 허용) 및 FORCED_OPEN(항상 엑세스 거부)의 특수 상태를 지원한다.

서킷브레이커는 원자성을 보장한다. 특정 시점에 하나의 스레드만 슬라이딩 윈도우를 업데이트할 수 있다. 하지만 함수 호출은 동기화하지 않는다. 슬라이딩 윈도우 크기가 15인 경우에도 슬라이딩 윈도우가 동시에 15개의 호출만 실행할 수 있는 건 아니다. 동시 스레드 수를 제한하려면 벌크 헤더를 사용해야 한다.

## config 설정
- slidingWindowSize는 서킷브레이커가 닫혔을 때? 발생하는 요청을 기록하는 사이즈이다. 타입은 숫자와 시간으로 나눠지고 가장 마지막 windowSize가 기록된다. 디폴트는 100이다.
- minimumNumberOfCalls는 서킷브레이커가 에러비율이나 느린 요청의 비율을 계산하기 시작하는 최소한의 요청이다. 서킷브레이커가 동작하는 실패 요청의 개수라고 이해된다.
- waitDurationInOpenState는 open 상태인 서킷브레이커가 대기하고 있는 시간이다. 이 시간이 지나면 Half open상태로 변화한다.
- automaticTransitionFromOpenToHalfOpenEnabled는 open에서 half open으로 자동으로 변화해도 되는지 설정이다. true / false로 나뉜다. false일 경우 대기시간이 지나도 half open으로 바뀌지 않고 특정 요청이 있어야 한다. false의 장점은 서킷브레이커를 모니터링하는 쓰레드 낭비가 없다는 점.
- slowCallRateThreshold는 half open 상태에서 허용된 요청의 개수를 의미

## 실습
- [getting started](https://resilience4j.readme.io/docs/getting-started-3)
