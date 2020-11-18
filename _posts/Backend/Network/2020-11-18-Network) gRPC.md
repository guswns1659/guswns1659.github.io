---
title: "Network) gRPC"
header:
  overlay_image: /assets/spring.jpg
  overlay_filter: 0.2
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
categories:
  - Network
---

네트워크는 인터넷 세상을 가능하게 해준 기술입니다.

# gRPC

## gRPC(Remote Procedure Calls)란
구글에서 개발한 RPC 프레임워크이다. RPC라는 개념은 과거에 존재했지만 구글이 프로토콜 버퍼라는 기능을 추가해 성능을 최적화한 프레임워크이다.

## 왜 쓰는가?
API 명세가 통일된다는 점이다. gRPC는 해당 API에 대해서 IDL(Interface Definition Language)를 정의한다. 그럼 protoc 컴파일러가 해당 IDL를 각 언어에 맞게 변환한다. 이 때 변환된 추상 클래스를 실제 서비스가 구현한다. API 명세(IDL)이 만들어져야 개발을 할 수 있기 때문에 개발자 전부가 통일된 API 양식을 이해할 수 있다.  

위 장점은 마이크로서비스에서 극대화된다. 작은 서비스들끼리 소통을 하는 마이크로서비스는 언어와 프레임워크가 다를 수 있다. 그 때 통일된 API명세를 정의하면 쉽게 다른 프레임워크 간 소통도 쉽게 가능하기 때문이다.

## 어떤 상황에는 아쉬울까?
간단한 CRUD를 제공하는 서비스라면 RestFul 명세가 나을 수 있다. RestFul 명세로 구현된 서비스를 gRPC와 연동하기 위해선 gRPC 게이트웨이가 필요하다. 클라이언트가 RestFul 방식으로 요청을 보내면 gRPC 게이트웨이가 해당 요청을 gRPC로 변경하고 gRPC 서비스에 전달한다. gRPC 서비스는 해당 요청을 처리한 후 응답한다.
