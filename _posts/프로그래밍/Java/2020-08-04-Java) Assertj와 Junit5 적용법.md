# 자바프로젝트에 Junit5와 assertJ 적용하는 법

## AssertJ 적용법
- 아래 사진처럼 프로젝트 모듈에서 검색한 뒤 다운받는다.

- org.assertj:assertj-core:3.12.2

![자바_assertj적용법](/assets/java/자바_assertj적용법.png)

## Junit5 적용법

- 인텔리j 켠 뒤 테스트 클래스에서 @BeforeEach를 입력하면 자동완성으로 classpath에 등록할건지 물어본다. 그 때 자동으로 의존성 추가하면 된다.


## assertJ에서 Exception 테스트하기

- [참고 : 밸덩](https://www.baeldung.com/assertj-exception-assertion)

```java
assertThatThrownBy(() -> doublyLinkedList.add(15, new LinkedListNode(value2)))
                .isInstanceOf(IndexOutOfBoundsException.class);
```
