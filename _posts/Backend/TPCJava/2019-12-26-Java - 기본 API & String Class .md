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
- API(Application Programming Interface)란?
- 자바의 기본 API
- String class
## 핵심
- **API는 여러 클래스가 합쳐져 기능을 하는 도구이다**

## API(Application Programming Interface)란?
API란 프로그래밍 기초를 지나면 자주 만나게 되는 용어이다. 그럼 API는 무엇일까? 내 언어로 정리하자면 **여러 클래스가 합쳐져 기능을 하는 도구**이다. 프로그래밍은 절대 하나의 클래스, 프로그램으로만 이루어지지 않는다. 수십, 수백 개의 클래스들끼리 연결되어 하나의 프로그램이 만들어진다.<br>
이 때 여러 클래스들은 뭉쳐져서 하나의 기능을 수행할 수 있다. 마치 레고 조각이 뭉쳐져서 큰 성이 만들어지는 것처럼 말이다. 그런데 이 API는 내가 만들 수도 있지만 누군가 뛰어난 기능으로 만들었을 수도 있다. 다른 사람이 만든 걸 굳이 내가 만들 필요는 없다. 그 사람껄 쓰면 된다.<br>

## 자바의 기본 API
자바에도 물론 API가 있다. JDK 속에 설치되어 있다. 그래서 자바의 기본 API도 충분히 유용하고 잘 사용할 수 있어야 한다. 경로는 jrt-fs.jar에 있으며 2단계 구조로 패키지가 구성되어 있다. java.lang, java.util, java.sql, ... 등. 기본 API를 사용하려면 패키지 경로를 통해 접근해 사용하면 된다.

## String class
자바에서 문자열을 담는 자료형 String은 객체이다. 객체라는 의미는 String을 사용하려면 메모리에 String을 생성해야 한다. 객체를 생성할 땐 new 연산자와 생성자 메서드를 사용한다.<br>
**하지만 우리는 String을 선언할 때 new와 생성자 연산자를 사용하지 않는다. 무슨 일일까?**<br>
String을 생성하는 방법은 2가지가 있기 때문이다.<br>
 1. 일반적인 객체 생성처럼 new 연산자와 생성자 메서드를 사용하는 방법.
 2. 문자열 상수로 생성하는 방법이다. ** 이 둘의 차이는 무엇일까?**<br>
**이 둘의 차이는 객체의 재활용 여부이다.**<br>
첫 번째 방법은 heap area에 생성된다. 그리고 만약 같은 값의 문자열이 또 만들어지면 이전 객체의 주소를 재활용하지 않고 새롭게 heap area에 객체를 생성한다.<br>
두 번째 방법은 literal pool에 생성된다. 이곳은 문자열 상수가 생성되는 메모리 영역이다. 특징은 주소를 재활용한다. 그래서 만약 같은 값의 문자열이 또 만들어지면 객체를 따로 생성하는 것이 아니라 이전 객체 주소를 재활용한다. 즉, 객체 주소가 같다.

### 예제
```java
    String str1 = new String("hello");
        String str2 = new String("hello");

        if (str1.equals(str2)) {
            System.out.println("YES");
        } else {
            System.out.println("NO");
        }
```