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

---
# gRPC 특강
rpc란 원격에 존재하는 프로시저를 마치 로컬에 존재하는 것처럼 호출하는 기술을 의미한다. 원격과의 연결을 담당하는 stub과 원격과의 데이터 형식을 맞추는 proto가 있어야 원격과 호출할 수 있다. rpc는 과거에 이미 개발이 되었지만 러닝커브가 높고 복잡성이 있어서 restFul에게 자리를 내준 상황이었다. 구글이 프로토콜 버퍼를 추가한 gRPC를 선보이면서 마이크로서비스에 최적화된 서비스로 주목을 받고 있다.

## 장점
- 바이너리 데이터로 주고 받기 때문에 속도가 빠르고, 처리량이 많다. 프로토콜버퍼가 바이너리 데이터를 바꾸는 것.
- proto파일 하나로 idl를 정의하면 10가지 언어로 작성 가능하다.

## 스트리밍과 stub
gRPC는 4가지 스트리밍 방식을 제공하고 있으며 3가지 stub 구현체를 지원하고 있습니다. 3가지는 blockingStub, (async) Stub, FutureStub이며 Stub만 위 4가지 스트리밍 방식을 지원합니다. 대용량 처리량을 비동기로 구현을 해야 한다면 stub이 적합하고 적은 량의 단건을 처리한다면 bloockingstub도 사용 가능하다.

- [참고 강의](https://www.youtube.com/watch?v=sKWy7BJxIas&t=162s)

# StreamObserver<>
- OnNext : 실제 데이터를 전송하는 메서드, 여러번 호출될 수 있다.
- OnError : 에러가 발생할 때 호출되는 메서드
- OnComplete : 전송을 완료했을 때 호출되는 메서드 
