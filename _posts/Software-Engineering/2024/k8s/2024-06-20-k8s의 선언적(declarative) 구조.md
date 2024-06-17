---
title: k8s의 선언적(declarative) 구조
date: 2024-06-16 11:49:08 +09:00
categories: [Software-Engineering]
tags: engineering
---

k8s는 기본적인 동작방식을 선언적 구조를 채택했습니다. 선언적 구조는 무엇이며, 이를 구현하기 위해 어떤 매커니즘을 사용했는지 살펴보겠습니다.

## 선언적 방식과 명령적 방식의 차이

### 선언적 방식 (Declarative Approach)

사용자는 시스템의 최종 상태(desired state)를 정의합니다. Kubernetes는 이 정의된 상태를 자동으로 유지합니다. 즉, 사용자는 무엇이 되어야 하는지를 명시하고, 시스템은 지속적으로 현재 상태를 모니터링하여 최종 상태와 일치하도록 조정합니다. 이 방식은 자동 복구(self-healing)와 상태 일관성(consistency)을 보장합니다.

### 명령적 방식 (Imperative Approach)

사용자는 시스템이 수행할 명령을 순차적으로 정의하고 실행합니다. 즉, 사용자는 구체적인 작업을 하나씩 명령하고, 시스템은 이를 순차적으로 실행합니다. 명령적 방식은 사용자가 각 단계를 명확히 제어할 수 있게 하며, 특정 작업을 즉시 실행할 수 있는 장점이 있습니다.

### k8s 아키텍쳐

![Kubernetes Architecture](/assets/img/full-kubernetes-model-architecture.png)

### 마스터 노드의 선언적 방식: 
마스터 노드는 선언적 구성 파일을 통해 Kubernetes 객체를 관리합니다. 구성 파일은 YAML 형식으로 작성되며, kubectl apply 명령을 통해 클러스터에 적용됩니다. 이 방식은 객체의 원하는 상태를 선언하고, 컨트롤러나 스케쥴러가 자동으로 현재 상태를 조정하여 선언된 상태로 일치시키도록 합니다​. 이 때 객체의 현재 상태를 확인하기 위해 ping API를 호출하는데, k8s는 watch 매커니즘이라 부릅니다.

### 워커 노드의 명령적 방식: 
워커 노드는 Kubelet과 컨테이너 런타임을 통해 실제 작업을 수행합니다. Kubelet은 API 서버로부터 명령을 받아 Pod을 실행하고, 컨테이너 런타임은 이 명령을 받아 컨테이너를 실행합니다. 이는 명령적 방식으로 볼 수 있으며, 각 작업을 순차적으로 처리합니다.

## [watch 매커니즘](https://kubernetes.io/docs/reference/using-api/api-concepts/#efficient-detection-of-changes) 

k8s의 마스터 노드에서는 선언적 방식을 구현하기 위해 watch 매커니즘을 이용합니다. 
Kubernetes의 watch 메커니즘은 클러스터 리소스의 변경 사항을 실시간으로 감지하고 처리하기 위한 효율적인 방법입니다. 이를 통해 클라이언트는 리소스의 상태 변경을 즉시 감지하고 필요한 조치를 취할 수 있습니다. 아래에서는 watch 메커니즘이 어떻게 동작하는지, 요청과 응답의 예시를 포함하여 정리하겠습니다.

![Kubernetes Architecture](/assets/img/master-node-network.png)


### Watch 메커니즘 동작 방식

1. **Watch 요청**:
   클라이언트는 Kubernetes API 서버에 HTTP GET 요청을 보내 리소스를 감시합니다. 이 요청에는 `watch` 매개변수와 `resourceVersion` 매개변수가 포함됩니다.

   ```plaintext
   GET /api/v1/namespaces/test/pods?watch=1&resourceVersion=10245
   ```

   - **/namespaces/test**: 이 부분은 요청이 특정 네임스페이스에 범위가 지정된다는 것을 의미합니다. 여기서 test는 네임스페이스 이름입니다.
   - **/pods**: 네임스페이스 내의 Pod 리소스를 대상으로 한다는 것을 나타냅니다.
   - **watch=1**: 이 매개변수는 watch 모드를 활성화하여 리소스의 변경 사항을 실시간으로 감시합니다.
   - **resourceVersion=10245**: 이 매개변수는 지정된 리소스 버전 이후의 변경 사항만을 감지합니다.

2. **API 서버의 응답**:
   API 서버는 클라이언트의 watch 요청을 처리하고 연결을 유지하면서, 리소스의 변경 사항이 발생할 때마다 실시간으로 데이터를 스트리밍합니다. 응답은 HTTP/1.1의 청크 전송 인코딩(chunked transfer encoding)을 사용하여 부분적으로 전송됩니다.

   ```plaintext
   200 OK
   Transfer-Encoding: chunked
   Content-Type: application/json
   ```

3. **이벤트 수신 및 처리**:
   클라이언트는 API 서버로부터 실시간으로 이벤트를 수신합니다. 각 이벤트에는 리소스의 변경 유형(예: 추가, 수정, 삭제)과 변경된 리소스의 세부 정보가 포함됩니다.

   ```json
   {
     "type": "ADDED",
     "object": {"kind": "Pod", "apiVersion": "v1", "metadata": {"resourceVersion": "10596", ...}, ...}
   }
   {
     "type": "MODIFIED",
     "object": {"kind": "Pod", "apiVersion": "v1", "metadata": {"resourceVersion": "11020", ...}, ...}
   }
   ```

  - **type**: 이벤트 유형을 나타내며, `ADDED`, `MODIFIED`, `DELETED` 등의 값이 올 수 있습니다.
  - **object**: 변경된 리소스의 세부 정보가 포함됩니다.

### Watch 메커니즘의 장점

- **실시간 업데이트**: 클라이언트는 API 서버로부터 실시간으로 리소스 변경 사항을 수신하여 즉각적으로 대응할 수 있습니다.
- **연결 유지**: watch 요청은 연결을 유지하면서 변경 사항이 발생할 때마다 데이터를 스트리밍 방식으로 전달받습니다.
- **효율성**: 클라이언트는 필요할 때마다 전체 리소스를 재조회하지 않고, 변경된 부분만을 실시간으로 받아 처리할 수 있어 효율적입니다.

### Watch 요청이 끊어질 수 있는 상황

- **네트워크 문제**: 클라이언트와 API 서버 간의 네트워크 연결이 불안정하거나 끊어질 때.
- **타임아웃**: 클라이언트와 API 서버 간의 연결이 일정 시간 동안 유지되지 않으면 타임아웃 발생.
- **API 서버의 재시작 또는 장애**: API 서버가 재시작되거나 장애가 발생하면 기존의 watch 연결이 끊어질 수 있음.
- **리소스 버전 불일치**: 클라이언트가 watch 요청을 보낼 때 사용하는 리소스 버전이 오래되었거나 유효하지 않은 경우.
- **클라이언트의 재시작**: 클라이언트 애플리케이션이 재시작되면 기존의 watch 연결이 끊어짐.
- **리밸런싱 및 재조정**: Kubernetes 클러스터 내에서 리소스가 리밸런싱되거나 재조정되면 관련된 watch 연결이 일시적으로 끊어질 수 있음.

이러한 watch 메커니즘을 통해 Kubernetes는 클러스터 내 리소스의 상태를 실시간으로 관리하고 조정할 수 있습니다.

## [상태 업데이트 매커니즘](https://kubernetes.io/docs/reference/using-api/api-concepts/#patch-and-apply)

Watch 매커니즘을 통해 리소스의 상태를 파악할 수 있고, 관련 작업을 수행한 후에는 API 서버에 상태를 보고할 필요가 있다. 관련해 공유합니다.

Kubernetes는 객체를 업데이트하기 위해 `PUT`과 `PATCH` 메커니즘을 제공합니다.

### HTTP PUT
- **전체 덮어쓰기**: 객체 전체를 덮어씁니다.
- **resourceVersion 사용**: 객체의 현재 `resourceVersion`을 지정하여 업데이트 시 충돌을 방지합니다.
- **409 Conflict**: 제공한 `resourceVersion`이 최신이 아닌 경우, API 서버가 `409 Conflict` 오류를 반환합니다.

### HTTP PATCH
- **부분 업데이트**: 객체의 특정 부분만을 업데이트합니다.
- **타입**: JSON Patch, JSON Merge Patch, Strategic Merge Patch 지원.
- **resourceVersion 사용**: `resourceVersion`을 지정하여 변경이 최신 상태인지 확인합니다.


### (참고자료) HTTP/1.1의 청크 인코딩과 HTTP/2와 비교 

### HTTP/1.1의 기능

- **청크 전송 인코딩**: 데이터가 준비되는 대로 여러 청크로 나누어 전송합니다.
- **지속 연결 (Keep-Alive)**: 한 번의 연결로 여러 요청과 응답을 처리하여 성능을 개선합니다.
- **파이프라이닝**: 여러 요청을 순차적으로 보내고, 서버로부터 응답을 기다리지 않고 다음 요청을 보낼 수 있습니다. 그러나 이는 잘 지원되지 않는 경우가 많습니다.

### HTTP/2의 서버 푸시 (Server Push)

HTTP/2에서는 서버가 클라이언트의 요청에 응답하면서, 클라이언트가 필요할 것으로 예상되는 리소스를 미리 푸시할 수 있습니다. 이는 웹 페이지 로딩 속도를 크게 향상시킵니다.

- **서버 푸시**: 클라이언트의 요청 없이도 서버가 리소스를 미리 전송할 수 있는 기능입니다.
- **멀티플렉싱**: 하나의 연결에서 여러 요청과 응답을 병렬로 처리할 수 있습니다.
- **헤더 압축**: HPACK을 사용하여 헤더를 압축함으로써 대역폭을 절약합니다.

