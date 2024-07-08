---
title: Deployment의 RollingUpdate
date: 2024-07-08 11:49:08 +09:00
categories: [Software-Engineering]
tags: engineering
---

# Kubernetes RollingUpdate 전략: 동작 방식

### 단계별 동작 과정

#### 설정
- **replicas**: 4
- **maxSurge**: 40% (반올림하여 최대 2개의 추가 Pod)
- **maxUnavailable**: 40% (내림하여 최대 1개의 비활성 Pod)

#### 예제 동작

1. **초기 상태**:
   - 4개의 기존 Pod가 실행 중입니다.

2. **첫 번째 단계**:
   - `maxSurge`에 따라 2개의 새로운 Pod가 생성됩니다.
   - 총 6개의 Pod가 실행 중입니다 (4개의 기존 Pod + 2개의 새로운 Pod).

3. **두 번째 단계**:
   - 1개의 기존 Pod가 `maxUnavailable`에 따라 종료됩니다.
   - 총 5개의 Pod가 실행 중입니다 (3개의 기존 Pod + 2개의 새로운 Pod).

4. **세 번째 단계**:
   - 새로운 Pod 2개 중 하나가 실행 가능해지면, 새로운 Pod 1개가 추가로 생성됩니다 (`maxSurge`가 2이기 때문에, 동시에 최대 2개의 추가 Pod가 존재할 수 있습니다).
   - 1개의 기존 Pod가 종료됩니다 (`maxUnavailable`에 따라).
   - 총 5개의 Pod가 실행 중입니다 (2개의 기존 Pod + 3개의 새로운 Pod).

5. **네 번째 단계**:
   - 새로운 Pod 2개 중 하나가 실행 가능해지면, 새로운 Pod 1개가 추가로 생성됩니다.
   - 1개의 기존 Pod가 종료됩니다.
   - 총 5개의 Pod가 실행 중입니다 (1개의 기존 Pod + 4개의 새로운 Pod).

6. **다섯 번째 단계**:
   - 마지막 기존 Pod가 종료됩니다.
   - 총 4개의 Pod가 실행 중입니다 (모두 새로운 Pod).

### 실행 가능 여부 판단: Readiness Probe

Kubernetes의 `Deployment`에서 컨테이너가 트래픽을 받을 준비가 되었는지를 판단하는 기준은 **Readiness Probe**입니다.

#### Readiness Probe
- **목적**: 컨테이너가 요청을 받을 준비가 되었는지를 확인합니다.
- **동작**: 주기적으로 설정된 경로에 대해 HTTP GET 요청, TCP 소켓 연결, 또는 특정 명령어 실행을 통해 상태를 확인합니다.
- **결과**:
  - **성공**: 컨테이너가 트래픽을 받을 준비가 되었다고 판단되어 서비스의 엔드포인트에 추가됩니다.
  - **실패**: 컨테이너가 준비되지 않았다고 판단되어 서비스의 엔드포인트에서 제거됩니다.

### 예제 설정

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: example-pod
spec:
  containers:
  - name: example-container
    image: example-image:latest
    readinessProbe:
      httpGet:
        path: /ready
        port: 8080
      initialDelaySeconds: 5
      periodSeconds: 5
    livenessProbe:
      httpGet:
        path: /healthz
        port: 8080
      initialDelaySeconds: 10
      periodSeconds: 10
```
