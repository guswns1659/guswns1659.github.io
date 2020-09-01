---
header:
  teaser: /assets/java.jpg
  overlay_image: /assets/java.jpg
  overlay_filter: 0.2
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
categories:
  - Java
---

자바는 단언컨대 훌륭한 객체 지향 언어 중 하나입니다.

# 자바프로젝트에 Junit5와 assertJ 적용하는 법

## AssertJ 적용법
- 아래 사진처럼 프로젝트 모듈에서 검색한 뒤 다운받는다.

- org.assertj:assertj-core:3.12.2

![자바_assertj적용법](/assets/java/자바_assertj적용법.png)

- assertj import문

```java
import static org.assertj.core.api.Assertions.*;
```

## Junit5 적용법

- 인텔리j 켠 뒤 테스트 클래스에서 @BeforeEach를 입력하면 자동완성으로 classpath에 등록할건지 물어본다. 그 때 자동으로 의존성 추가하면 된다.


## assertJ에서 Exception 테스트하기

- [참고 : 밸덩](https://www.baeldung.com/assertj-exception-assertion)

```java
assertThatThrownBy(() -> doublyLinkedList.add(15, new LinkedListNode(value2)))
                .isInstanceOf(IndexOutOfBoundsException.class);
```

## Junit5에서 @MethodSource로 배열 여러개를 인자로 한번에 받는 법

```java
public class N2920Test {

    private N2920 n2920;

    @BeforeEach
    void setUp() {
        this.n2920 = new N2920();
    }

    private static Stream<Arguments> setUpInputs() {
        return Stream.of(
                Arguments.of(new int[]{1, 2, 3, 4}, "ascending"),
                Arguments.of(new int[]{8, 7, 6, 5}, "descending"),
                Arguments.of(new int[]{1, 4, 3, 2}, "mixed")
        );
    }

    @ParameterizedTest
    @MethodSource("setUpInputs")
    void 알고리즘테스트한다(int[] inputs, String expected) {
        assertThat(n2920.test(inputs)).isEqualTo(expected);
    }
}
```

## Junit5
- `@MethodSource` 로 String[]을 넘길 때는 (Object)로 캐스팅해줘야 한다.

```java
private static Stream<Arguments> setUpInputs() {
        return Stream.of(
                Arguments.of((Object) new String[]{"3 4", "1 1", "1 -1"})
        );
    }
```

- `@CsvSource`의 delimeter를 다르게 설정해서 주입하는 법

```java
@ParameterizedTest
    @CsvSource(value = {"ababababa:aba:2", "a a a a a:a a:2"}, delimiter = ':')
    void N1543알고리즘을_테스트한다(String input, String word, int expected) {
        assertThat(N1543.test(input, word)).isEqualTo(expected);
    }
```
