---
header:
  overlay_image: /assets/algorithm.jpg
  overlay_filter: 0.2
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
categories:
  - Algorithm
---

Algorithm은 자료구조를 기반으로 만들어진 다양한 문제해결법입니다.

## 합집합-찾기 알고리즘 (Union-find)

- 각 노드 별 집합을 찾아내는 알고리즘
- 그래프를 이용하는 문제에 사용할 수 있다. 자바로 그래프를 구현하려니 익숙하지 않아서 그런가 상당히 어렵다.

- 코드

```java
public static void main(String[] args) throws IOException {
        for (int i = 1; i < 5; i++) {
            parent.put(i, i);
        }

        union(1,4);
        union(2,4);

        for (int i = 1; i < 5; i++) {
            System.out.println(find(i));
        }
    }

    public static void union(int x, int y) {
        int x2 = find(x);
        int y2 = find(y);

        if (x2 != y2) {
            parent.put(y2, x2);
        }
    }

    private static int find(int x) {
        if (x == parent.get(x)) {
            return x;
        }
        int p = find(parent.get(x));
        parent.put(x, p);
        return parent.get(x);
    }
```
