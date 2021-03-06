---
header:
  overlay_image: /assets/java.jpg
  overlay_filter: 0.2
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
categories:
  - TPCJava
---

TPC는 Thinking, Presentation, Coding의 약자입니다. 

## 목차
- Class를 보는 관점 2가지
- 우리가 만드는 Class의 종류 3가지
- 우리가 사용하게될 Class의 종류
## 핵심
**Class는 무궁무진하다. 내가 어떻게 쓰느냐에 따라 활용 범위가 달라진다**

## Class를 보는 관점 2가지
지금까지 클래스에 대해서 배웠다. 2가지 관점에서 클래스를 바라봤다.

1. Data type 측면 : 새로운 자료형(객체)를 설계하는 도구 -> 모델링
2. OPP(객체 지향) 측면 : 객체의 상태 정보와 행위정보를 추출하여 캡슐화 시키는 도구

그래서 클래스를 모델이라고도 한다. 왜냐하면 역할이 정해져있기 때문이다.

## 우리가 만드는 Class의 종류 3가지
지금까지는 DTO기능을 하는 클래스에 대해서 주로 배웠다. DTO는 객체를 바구니 역할로 사용하는 것이다. 다양한 데이터를 담아서 이동하는 역할이다. 하지만 이외에도 클래스의 기능은 더 있다.
1. DTO(Data Transfer Object) : 데이터를 이동하는 역할, 담는 역할
2. DAO(Data Access Object) : 데이터를 처리하는 역할, 데이터베이스와 연결해 CRUD를 하기도 한다.
3. Utility(Helper Object) : 다양한 기능을 가지고 있어서 돕는 역할(날짜, 시간, ...)

## 우리가 사용하게될 Class의 종류
1. 자바에서 기본으로 제공하는 클래스 : Array, Hash, System. ...
2. 직접 설계해서 사용하는 클래스 : DTO, DAO, Utility
3. 외부 회사나 사람이 만들어서 배포하는 클래스 : Gson, Jsoup, ...