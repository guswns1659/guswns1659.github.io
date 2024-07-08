---
title: k8s 오브젝트는 etcd에 저장되는 Json 오브젝트와 프로세스로 실행되는 오브젝트로 구분된다. 
date: 2024-06-30 11:49:08 +09:00
categories: [Kubernetes]
tags: engineering
---

k8s를 공부하다보면 k8s의 모든 오브젝트가 실제 메모리 상에 떠 있는 프로세스로 이해하기 쉽습니다. 하지만, 어떤 오브젝트는 etcd 내에 Json 오브젝트로 저장되고, 
어떤 오브젝트는 실제로 메모리 상에 프로세스로 실행되는 오브젝트입니다. 

헷갈리는 오브젝트를 구분하기 위해 질문을 구성했습니다. 아래 질문이 헷갈린다면 다음 내용을 이어서 읽으시면 됩니다.
- deployment는 etcd에만 저장된 Json 오브젝트이다?
- deploymentController는 etcd에만 저장된 Json 오브젝트이다?
- ReplicaSet은 etcd에만 저장된 Json 오브젝트이다?
- ReplicaSetController은 etcd에만 저장된 Json 오브젝트이다?  
- Service는 etcd에만 저장된 Json 오브젝트이다?
- kube-proxy는 etcd에만 저장된 Json 오브젝트이다?

이해를 위해 Deployment와 DeploymentController를 예시로 들어보겠습니다.

### Deployment
- **역할**: Deployment는 애플리케이션 배포, 관리, 확장 및 업데이트를 선언적으로 정의합니다.
- **저장 위치**: Deployment 오브젝트는 Kubernetes API 서버에 의해 etcd에 저장됩니다.
- **구성 요소**: Deployment 오브젝트는 Pod 템플릿, 복제 수, 업데이트 전략 등을 포함합니다.

**Deployment가 노드가 할당되지 않고 status가 없음을 보여주는 정보. 팟과는 다름**

![image](https://github.com/guswns1659/guswns1659.github.io/assets/55608425/8e900428-753a-4fd2-a186-1208a9ddf713)

**팟 정보**

![image](https://github.com/guswns1659/guswns1659.github.io/assets/55608425/d389bd8e-fe62-4f1d-83a3-79252719a146)


### DeploymentController
- **역할**: Deployment 컨트롤러는 etcd에 저장된 Deployment 오브젝트의 상태를 모니터링하고, 이를 기반으로 실제 상태를 조정합니다. 필요한 경우 새로운 ReplicaSet을 생성하거나 기존 ReplicaSet을 업데이트합니다.
- **실행 위치**: Deployment 컨트롤러는 Kubernetes의 컨트롤 플레인 내에서 실행되는 프로세스입니다. 이는 kube-controller-manager 내에서 실행됩니다.
- **작동 방식**: 컨트롤러는 etcd에서 Deployment 오브젝트를 읽고, 정의된 스펙에 따라 Pod과 ReplicaSet을 생성하고 관리합니다.

**kube-controller-manager 팟에서 DeploymentController가 시작하는 내용이 담긴 로그**

![image](https://github.com/guswns1659/guswns1659.github.io/assets/55608425/691c79ef-8819-4976-90ef-9e3f01f52bc1)

DeploymentController가 팟 내에서 실행되는 로그를 확인할 수 있습니다.

### 질문 답
- deployment는 etcd에만 저장된 Json 오브젝트이다? O
- deploymentController는 etcd에만 저장된 Json 오브젝트이다? X
- ReplicaSet은 etcd에만 저장된 Json 오브젝트이다? O
- ReplicaSetController은 etcd에만 저장된 Json 오브젝트이다? X
- Service는 etcd에만 저장된 Json 오브젝트이다? O
- kube-proxy는 etcd에만 저장된 Json 오브젝트이다? X


