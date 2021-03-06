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
- Object Casting(객체 형 변환)이란?
- 그럼 상속이 중요한 이유
## 핵심
- **upCasting을 사용하면 부모 객체로 자식 객체를 자유자재로 활용할 수 있다.**

## Object Casting(객체 형 변환)이란?
객체 형 변환이란 상속 관계에 있는 클래스끼리 자료형을 바꾸는 기능을 말한다. 객체 형 변환에는 upCasting과 downCasting이 있다.<br>
upCasting은 자식 객체를 부모 객체로 바꾸는 것을 말한다. 왜 바꿀까? 일전에 말한대로 만약 자식 객체의 기능(메서드)를 사용할 수 없는 상황일 때 부모 객체를 통해 자식 객체의 기능에 접근할 수 있다. (다만, Override가 된 전제 안에서)<br>
그럼 downCasting은 언제 사용할까? 자식 객체의 메서드 중 override가 안된 메서드를 사용할 때 필요하다. override가 안 된 메서드라면 부모 객체에서 접근할 수 없다. 그래서 upCasting한 부모 객체를 다시 자식 객체로 형 변환을 해서 사용해야 한다.<br>
```java
Animal ani = new Dog(); // upCasting
ani.eat() //컴파일 시점에서는 Animal의 eat()이지만 실행하면 Dog의 eat()으로 실행

Dog d = (Dog) ani; // downCasting
```<br>

## 그럼 상속이 중요한 이유
상속이 중요한 이유는 무엇일까? 부모 객체로 자식 객체를 핸들링할 수 있다는 점은 알겠다. 그럼 이게 실생활에서 어떻게 사용될까? 바로 API를 쓸 때 중요하다. 대부분의 API는 상속으로 이루어져있기 때문에 이 원리를 안다면 똑똑하게 사용할 수 있다.