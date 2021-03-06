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
- JDBC(Java Database connectivity) programming
- JDBC에 인터페이스를 활용하는 방법
## 핵심
- **인터페이스는 리모콘이다.**

## JDBC(Java Database connectivity) programming
JDBC programming은 자바를 이용해 데이터베이스를 조작하는 프로그래밍을 말한다. 데이터베이스 공급자는 여러 명이기 때문에 각 회사에서 만든 API는 다 다를 것이다. 기능은 유사해도 메서드의 이름이나 구현 방법은 다를 것이다. 그러면 자바에서 데이터베이스를 사용하려면 각 데이터베이스의 조작 방법을 전부 알아야 사용할 수 있다는 말이다. **근데 이 방법밖에 없을까?** 아니다. 인터페이스를 활용하면 조금 더 편리하게 사용할 수 있다. 그럼 어떻게 인터페이스를 활용할까?

## JDBC에 인터페이스를 활용하는 방법
자바 개발자가 각 공급사 별 데이터베이스 문법을 다 알고 쓸 수 없으니 ~~할 수는 있지만 상당히 힘들기 때문다. 개발자는 이런 걸 못 참는다.~~ 자바에서 데이터베이스와 관련된 인터페이스(리모콘)을 만들어 각 공급사에 제공하는 건 어떨까? 각 공급사는 전달 받은 인터페이스에 맞게 override를 하고 클래스 파일을 만들 수 있다. 이 클래스 파일을 Driver라고 한다. 우리가 컴퓨터 설치할 때 말하는 드라이버 파일이 여기서 비롯된 것 같다.<br>
이제 자바 개발자는 인터페이스로 모든 데이터베이스를 쉽게 관리할 수 있다. 비록 각 공급사 별 드라이버 클래스에서 구현되는 방식은 다르겠지만 개발자 입장에선 기능만 돌아가면 된다.

