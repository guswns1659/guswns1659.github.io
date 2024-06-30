---
title: k8s 오브젝트는 etcd에 저장되는 Json 객체와 프로세스로 실행되는 객체가 구분된다. 
date: 2024-06-30 11:49:08 +09:00
categories: [Software-Engineering]
tags: engineering
---

k8s를 공부하다보면 k8s의 오브젝트가 실제로 메모리 상에 떠 있는 프로세스로 이해하기 쉽습니다. 하지만, 어떤 객체는 etcd 내에 Json 객체로 저장되고, 
어떤 객체는 실제로 메모리 상에 프로세스로 실행되는 객체입니다. 

헷갈리는 객체를 구분하기 위해 질문을 구성했습니다. 아래 질문이 헷갈린다면 다음 내용을 이어서 읽으시면 됩니다.
- deployment는 Json 객체이다?
- deploymentController는 Json 객체이다?
- ReplicaSet은 Json 객체이다?
- Service는 Json 객체이다?
- kube-proxy는 Json 객체이다?

이해를 위해 Deployment와 DeploymentController를 구분하겠습니다. 

### Deployment 객체
- **역할**: Deployment는 애플리케이션 배포, 관리, 확장 및 업데이트를 선언적으로 정의합니다.
- **저장 위치**: Deployment 객체는 Kubernetes API 서버에 의해 etcd에 저장됩니다.
- **구성 요소**: Deployment 객체는 Pod 템플릿, 복제 수, 업데이트 전략 등을 포함합니다.

### Deployment 컨트롤러
- **역할**: Deployment 컨트롤러는 etcd에 저장된 Deployment 객체의 상태를 모니터링하고, 이를 기반으로 실제 상태를 조정합니다. 필요한 경우 새로운 ReplicaSet을 생성하거나 기존 ReplicaSet을 업데이트합니다.
- **실행 위치**: Deployment 컨트롤러는 Kubernetes의 컨트롤 플레인 내에서 실행되는 프로세스입니다. 이는 kube-controller-manager 내에서 실행됩니다.
- **작동 방식**: 컨트롤러는 etcd에서 Deployment 객체를 읽고, 정의된 스펙에 따라 Pod과 ReplicaSet을 생성하고 관리합니다.

### 요약
- **Deployment**: etcd에 저장된 선언적 구성 요소.
- **Deployment 컨트롤러**: 실제로 실행되어 Deployment 객체의 상태를 관리하고 조정하는 프로세스.

### 질문 답
- deployment는 Json 객체이다. O
- deploymentController는 Json 객체이다. X
- ReplicaSet은 Json 객체이다. O
- Service는 Json 객체이다. O
- kube-proxy는 Json 객체이다 X



### 참고 자료
- [Kubernetes 공식 문서: Deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)



