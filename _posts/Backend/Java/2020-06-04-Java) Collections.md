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

## List<객체> 를 특정 객체의 값을 기준으로 sort하고 싶을 때

```java
this.getReservations().sort(Comparator.comparing(UserReservation::getId).reversed());
```

## Collections.swap
- import static java.util.Collections.swap가 있어서 swap을 도와준다.
- 기존에는 직접 만들어서 사용했는데 제공하는 거 사용하면 훨씬 편해진다.

```java
public class BubbleSort {
    public void sort(List<Integer> unsortedList) {
        int size = unsortedList.size();

        for (int turn = 0; turn < size - 1; turn++) {
            boolean swap = false;
            for (int index = 0; index < size - turn - 1; index++) {
                if (unsortedList.get(index) > unsortedList.get(index + 1)) {
                    swap(unsortedList, index, index+1);
                    swap = true;
                }
            }
            if (!swap) {
                break;
            }
        }
    }
}
```

### 정렬
- 정렬할 때 2가지 기준이라면 Comparator 구현 시 조건문을 입력해야 한다.
- [참고](https://suite.tistory.com/entry/%EC%A0%95%EB%A0%AC-%EC%9E%90%EB%B0%94-%EB%8B%A4%EC%A4%91-%EC%A0%95%EB%A0%AC-multi-sort)

```java
package backjun;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.Comparator;
import java.util.List;

class Coordinate {
    public int x;
    public int y;

    public Coordinate(int x, int y) {
        this.x = x;
        this.y = y;
    }

    @Override
    public String toString() {
        return "Coordinate{" +
                "x=" + x +
                ", y=" + y +
                '}';
    }
}

public class N11650 {

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int testCase = Integer.parseInt(br.readLine());
        String[] inputs = new String[testCase];
        for (int index = 0; index < testCase; index++) {
            inputs[index] = br.readLine();
        }
        test(inputs);
    }

    public static void test(String[] inputs) {
        List<Coordinate> coordinates = new ArrayList<>();
        for (String input : inputs) {
            String[] splitInput = input.split(" ");
            coordinates.add(new Coordinate(Integer.parseInt(splitInput[0]), Integer.parseInt(splitInput[1])));
        }

        coordinates.sort((c1, c2) -> {
            if (c1.x > c2.x) {
                return 1;
            }
            if (c1.x == c2.x) {
                if (c1.y > c2.y) {
                    return 1;
                }
            }
            return -1;
        });

        coordinates.forEach(System.out::println);
    }
}

```
- Integer.compare를 활용한 훨씬 간결한 버전

```java
public static void test(String[] inputs) {
        List<Coordinate> coordinates = new ArrayList<>();
        for (String input : inputs) {
            String[] splitInput = input.split(" ");
            coordinates.add(new Coordinate(Integer.parseInt(splitInput[0]), Integer.parseInt(splitInput[1])));
        }

        coordinates.sort((c1, c2) -> {
            if (c1.x == c2.x) {
                return Integer.compare(c1.y, c2.y);
            }
            return Integer.compare(c1.x, c2.x);
        });

        for (Coordinate coordinate : coordinates) {
            System.out.println(coordinate.x + " " + coordinate.y);
        }

    }
```


- collections.sort()는 머지소트이고 stable 속성이 있다. Arrays.sort()는 퀵소트이고 unstable이다.

## Collections.binarySearch()
- 이진탐색 메서드 사용할 땐 반드시 오름차순 정렬은 한 후 사용해야 한다. 안 그러면 이상한 값이 결과로 나온다.
- binarySearch() 결과는 값이 있을 경우 해당 인덱스 값, 없을 경우 음수가 나온다.
