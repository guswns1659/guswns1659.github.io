---
title: Namespace와 ResourceQuota
date: 2024-07-08 11:49:08 +09:00
categories: [Kubernetes]
tags: engineering
---

# namespace와 resourceQuota

Kubernetes의 네임스페이스는 클러스터 내에서 **논리적인 격리 단위를 제공하여** 리소스를 그룹화하고 관리하는 데 사용됩니다. 네임스페이스는 여러 팀이나 프로젝트가 동일한 클러스터를 공유하면서도 리소스를 분리하고 관리할 수 있게 합니다.

ResourceQuota는 **특정 네임스페이스 내에서 사용할 수 있는 리소스의 총량을 제한**하고 관리하는 데 사용됩니다.

### 네임스페이스의 주요 역할

1. **리소스 격리**
2. **리소스 할당**
3. **액세스 제어**

### 예시

#### 네임스페이스 생성

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: mynamespace
```


#### ResourceQuota 설정

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: example-quota
  namespace: mynamespace
spec:
  hard:
    pods: "10"
    requests.cpu: "4"
    requests.memory: "8Gi"
    limits.cpu: "10"
    limits.memory: "16Gi"
```

### 그러면 파드의 Requests 및 Limits와의 차이는 뭘까?

#### 파드의 Requests 및 Limits

- **Requests**:
  - **목적**: 파드의 컨테이너가 정상적으로 실행되기 위해 필요한 최소한의 자원을 정의합니다.
  - **스케줄링 기준**: 스케줄러는 파드를 노드에 배치할 때, 요청된 자원을 기준으로 적절한 노드를 선택합니다.

- **Limits**:
  - **목적**: 파드의 컨테이너가 사용할 수 있는 자원의 최대치를 정의합니다.
  - **자원 사용 제어**: 파드가 제한된 자원을 초과하려고 하면, CPU의 경우 자원 사용이 제한되거나, 메모리의 경우 OOMKiller에 의해 컨테이너가 종료될 수 있습니다.

#### ResourceQuota의 Requests 및 Limits

- **Requests**:
  - **목적**: 특정 네임스페이스 내에서 requests의 총량을 제한합니다. **팟과는 달리 약간 limit의 개념이 포함되어 있습니다.**
  - **자원 요청 관리**: 네임스페이스 내 모든 파드가 요청할 수 있는 자원의 총량을 제한함으로써, 네임스페이스 간의 자원 사용을 공정하게 관리합니다.

- **Limits**:
  - **목적**: 특정 네임스페이스 내에서 limit의 총량을 제한합니다.
  - **자원 사용 관리**: 네임스페이스 내 모든 파드가 사용할 수 있는 자원의 총량을 제한하여, 특정 네임스페이스가 과도한 자원을 사용하지 않도록 합니다.

### 예시

1. **파드 생성 및 자원 요청**:
   - 네임스페이스 내에서 파드를 생성할 때, 파드의 자원 요청과 제한이 ResourceQuota의 제한을 초과하지 않는지 확인합니다.
   - 예를 들어, 네임스페이스의 ResourceQuota가 `requests.memory: 8Gi`로 설정된 경우, 네임스페이스 내 모든 파드의 자원 요청(memory)이 8Gi를 초과할 수 없습니다.

2. **팟 생성 시 ResourceQuota의 requests 초과 시**:
   - 만약 네임스페이스 내에서 새로운 파드가 생성될 때, 해당 파드의 자원 요청이 ResourceQuota의 제한을 초과하면 파드 생성이 실패합니다.

3. **실행 중인 팟들이 ResourceQuota의 limit을 초과 시**:
   - 파드가 실행 중일 때, limit을 초과하면 CPU 사용이 제한되거나, 메모리 사용이 초과될 경우 OOMKiller에 의해 컨테이너가 종료됩니다. **Qos 우선순위에 따라서 제거됩니다.**
