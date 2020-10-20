---
header:
  overlay_image: /assets/algorithm.jpg
  overlay_filter: 0.2
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
categories:
  - Algorithm
---

Algorithm은 자료구조를 기반으로 만들어진 다양한 문제해결법입니다.

### 문제 풀이 시 초기화하기

- 로컬에서 친구 네트워크 문제를 실행할 때는 정상동작하는데 제출하면 틀렸다고 나왔다. 이유는 테스트케이스 한 번 끝날 때 HashMap을 초기화하지 않았기 때문이다. 사소하지만 자주하는 실수니까 잊지 말자.

### char와 int를 계산할 때는 char에서 - '0'을 빼줘야 한다. char는 아스키 코드로 대응하기 때문이다.

```java
public static String test(String number) {
        for (int digit = 9; digit > -1; digit--) {
            for (int index = 0; index < number.length(); index++) {
                if (number.charAt(index) - '0' == digit) {
                    System.out.print(digit);
                }
            }
        }
        return "";
    }
```

### 계수정렬
- 수의 범위가 작고 (1~10000) nlogn보다 빠르게 정렬을 해야할 땐 계수정렬을 이용해야 한다.

```java
public class N10989 {

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int testCase = Integer.parseInt(br.readLine());
        int[] array = new int[10001];
        for (int loop = 0; loop < testCase; loop++) {
            array[Integer.parseInt(br.readLine())]++;
        }

        StringBuilder result = new StringBuilder();

        for (int index = 0; index < 10001; index++) {
            if (array[index] != 0) {
                for (int loop = 0; loop < array[index]; loop++) {
                    result.append(index).append("\n");
                }
            }
        }
        System.out.print(result);
    }
}
```

### 재귀 함수의 한계 : 피보나치 수열
- 입력 숫자가 클수록 반복되는 함수가 많아진다.
- 위 문제를 해결하기 위해 다이나믹 프로그래밍이 등장한다.
- 피보나치 수열의 경우 하나의 함수에 2개의 함수가 호출되니까 O(2의n승)이다.
- 자바로 반복문을 이용해 구현할 때는 temp값을 가지고 있어야 한다.

```java
private static int fibo(int number) {
        int a = 0;
        int b = 1;

        while (number > 0) {
            int temp = a;
            a = b;
            b = temp+b;
            number--;
        }
        return a;
    }
```
### 고급정렬
- 머지 정렬 사용해야 하는 문제인데 출력을 Stream으로 하니 시간초과가 발생한다..
- 퀵정렬은 상황에 따라 O(n제곱)이 발생할 수 있고, unstable하다.
- 고급 정렬을 사용해도 시간초과가 나오는 경우는 Stream을 없애고 for문을 쓰고 인풋처리를 StringTokenizer를 사용한다.

### 동적계획법
- 값을 저장하는 메모이제이션 기법이 적용되는데 재귀 함수의 원리를 이해하고 그걸 Collections에 저장한다 생각하면 된다.
- 아래는 피보나치 수열을 DP 방식으로 푼 문제.

```java
public class DynamicProgramming {

    public static int test(int number) {
        int[] memories = new int[number];
        memories[0] = 0;
        memories[1] = 1;

        for (int index = 2; index < memories.length; index++) {
            memories[index] = memories[index - 1] + memories[index - 2];
        }
        return memories[number-1];
    }
}
```

## 퀵정렬
- 예전에 자바 알고리즘 책보면서 할 땐 엄청 복잡해서 어려웠는데 지금 강의 들으면서 하니까 이해가 잘된다. 구현이 정말 간단하다. 성장이 느껴진다.

```java
public class QuickSort {
    public static List<Integer> quickSort(List<Integer> unsorted) {
        if (unsorted.size() <= 1) {
            return unsorted;
        }
        int pivot = unsorted.get(0);

        List<Integer> left = new ArrayList<>();
        List<Integer> right = new ArrayList<>();

        for (int index = 1; index < unsorted.size(); index++) {
            if (pivot > unsorted.get(index)) {
                left.add(unsorted.get(index));
            } else {
                right.add(unsorted.get(index));
            }
        }
        left = quickSort(left);
        left.add(pivot);
        right = quickSort(right);
        left.addAll(right);
        return left;
    }
}
```

### Map에서 value의 최댓값을 찾고 그 값의 키를 찾아야 할 때

- values()에서 최댓값 찾고 다시 keySet() 반복문 돌면서 최댓값을 가진 key를 찾는다.

```java
public static List<String> test(List<String> books) {

        Map<String, Integer> soldBooks = new LinkedHashMap<>(books.size());
        int max = 0;
        for (String book : books) {
            if (soldBooks.containsKey(book)) {
                soldBooks.put(book, soldBooks.get(book) + 1);
            } else {
                soldBooks.put(book, 1);
            }
            max = Math.max(soldBooks.get(book), max);
        }

        List<String> bestSeller = new ArrayList<>();

        for (String title : soldBooks.keySet()) {
            if (soldBooks.get(title) == max) {
                bestSeller.add(title);
            }
        }
        Collections.sort(bestSeller);
        return bestSeller;
    }
```

## HashMap 사용 이유
- 전체 탐색할 때 이 수가 한번 사용된 수인지 체크를 해서 모든 조합에서 사용하면 넘어가는 로직 때 사용해도 좋다.

# 그래프 이해

- 그래프는 실제 세계의 현상이나 사물을 정점(Vertex) 또는 노드(Node) 와 간선(Edge)로 표현하기 위해 사용
- 노드 (Node): 위치를 말함, 정점(Vertex)라고도 함
- 간선 (Edge): 위치 간의 관계를 표시한 선으로 노드를 연결한 선이라고 보면 됨 (link 또는 branch 라고도 함)
- 인접 정점 (Adjacent Vertex) : 간선으로 직접 연결된 정점(또는 노드)

## 무방향 그래프 (Undirected Graph)
- 방향이 없는 그래프
- 간선을 통해, 노드는 양방향으로 갈 수 있음
- 보통 노드 A, B가 연결되어 있을 경우, (A, B) 또는 (B, A) 로 표기

## 방향 그래프 (Directed Graph)
- 간선에 방향이 있는 그래프
- 보통 노드 A, B가 A -> B 로 가는 간선으로 연결되어 있을 경우, <A, B> 로 표기 (<B, A> 는 B -> A 로 가는 간선이 있는 경우이므로 <A, B> 와 다름)

## 가중치 그래프 (Weighted Graph) 또는 네트워크 (Network)
- 간선에 비용 또는 가중치가 할당된 그래프

![image](https://user-images.githubusercontent.com/55608425/91522888-41394700-e936-11ea-9020-9318b1308603.png)


# 너비 우선 탐색과 깊이 우선 탐색

대표적인 그래프 탐색 알고리즘 -> 트리 탐색 알고리즘이 아니다.
- 너비 우선 탐색 (Breadth First Search): 정점들과 같은 레벨에 있는 노드들 (형제 노드들)을 먼저 탐색하는 방식
- 깊이 우선 탐색 (Depth First Search): 정점의 자식들을 먼저 탐색하는 방식

## 너비우선탐색

![image](https://user-images.githubusercontent.com/55608425/91522510-624d6800-e935-11ea-86c7-d9f7740bb022.png)

### 너비우선탐색의 시간 복잡도
- 일반적인 BFS 시간 복잡도
- 노드 수: V
- 간선 수: E
- 위 코드에서 while need_visit 은 V + E 번 만큼 수행함
- 시간 복잡도: O(V + E)

## 너비우선탐색 로직

- 루트 노드를 needVisit에 넣는다.
- while (needVisit.size() != 0) 이면
  - needVisit의 맨 앞 노드를 꺼낸다. (List.remove(0) 하면 해당 노드 꺼내짐)
  - visited에 꺼낸 노드가 없다면
    - visited에 추가한다.
    - 해당 노드의 자식 노드를 needVisit에 추가한다.
- visited를 리턴한다.

### 구현 코드

- graph 초기화

```java
public class BfsTest {

    private Bfs bfs;
    private Map<String, List<String>> graph;

    @BeforeEach
    void setUp() {
        this.bfs = new Bfs();
        this.graph = new LinkedHashMap<>();
        this.graph.put("A", Arrays.asList("B", "C"));
        this.graph.put("B", Arrays.asList("A", "D"));
        this.graph.put("C", Arrays.asList("A", "G", "H"));
        this.graph.put("D", Arrays.asList("B"));
        this.graph.put("G", Arrays.asList("C"));
        this.graph.put("H", Arrays.asList("C"));
    }
```

- bfs 메서드

```java
public class Bfs {

    public List<String> search(Map<String, List<String>> graph, String startNode) {
        List<String> needVisit = new ArrayList<>();
        List<String> visited = new ArrayList<>();

        needVisit.add(startNode);

        while (needVisit.size() != 0) {

            String needVisitNode = needVisit.remove(0);
            if (!visited.contains(needVisitNode)) {
                visited.add(needVisitNode);
                needVisit.addAll(graph.get(needVisitNode));
            }
        }
        return visited;
    }
}
```

## 깊이우선탐색

- 정점의 자식노드를 우선적으로 탐색하는 방법

![image](https://user-images.githubusercontent.com/55608425/91526374-721d7a00-e93e-11ea-858a-5e0de1467795.png)

## 깊이우선탐색 로직

- 루트 노드를 needVisit에 넣는다.
- while (needVisit.size() != 0) 이면
  - needVisit의 맨 뒤 노드를 꺼낸다. (List.remove(needVisit.size() - 1) 하면 해당 노드 꺼내짐)
  - visited에 꺼낸 노드가 없다면
    - visited에 추가한다.
    - 해당 노드의 자식 노드를 needVisit에 추가한다.
- visited를 리턴한다.

### 구현코드

```java
public class Dfs {

    public static void main(String[] args) {
        System.out.println("hello");
    }

    public List<String> search(Map<String, List<String>> graph, String startNode) {

        List<String> visited = new ArrayList<>(graph.size());
        List<String> needVisit = new ArrayList<>();

        needVisit.add(startNode);

        while (needVisit.size() != 0) {
            String popNode = needVisit.remove(needVisit.size() - 1);
            if (!visited.contains(popNode)) {
                visited.add(popNode);
                needVisit.addAll(graph.get(popNode));
            }
        }
        return visited;
    }
}
```

- 시간 복잡도는 너비우선탐색과 동일하다. : O(간선 + 노드)

## 깊이 우선 탐색과 너비 우선 탐색 회고
- 반복문으로 구현하며 원리를 익힐 수 있었던 시간. 이 두가지 탐색을 코딩테스트를 위한 기본 탐색이라고 하는지 아직까진 모르겠다. 주로 알고리즘에서 푸려면 개념을 가지고 응용에 적용해야할 듯 하다.

## BFS

![image](https://user-images.githubusercontent.com/55608425/92348890-689acb80-f10f-11ea-8739-040ea3eae09e.png)

## 시간복잡도
- 모든 칸이 큐에 한번씩 들어가므로 시간복잡도는 칸이 N개일 때 O(N)

## 자주 하는 실수

![image](https://user-images.githubusercontent.com/55608425/92349435-0ba01500-f111-11ea-97f4-5b46ecd06de9.png)

## 응용 유형
- 시작점이 여러곳인 경우 -> 이중 반복문으로 시작점을 정해야하거나, 모든 시작점을 큐에 넣고 시작하는 방법이 있다. 둘 다 똑같나??
- 시작점으로부터 거리를 측정하는 경우

## BFS
- BFS를 돌 때 큐에 쌓이는 순서는 시작점에서부터 거리순으로 쌓인다.
- 유형 : 시작점이 다른 유형일 경우.
- 시작점이 서로 영향을 안 주는 경우 2개를 따로 dfs 돌면서 풀면 되는데 서로 영향을 주는 경우엔 백트래킹을 사용해야 한다.

## 등차수열
-  일반항 : An = A1 + (n+1)d
- [참고](https://www.mathfactory.net/10916)

![image](https://user-images.githubusercontent.com/55608425/92671566-940cf880-f351-11ea-9b13-de4df7334453.png)

## 계차수열
- 일반항 : An+1 = An + Bn

![image](https://user-images.githubusercontent.com/55608425/92671656-cae30e80-f351-11ea-8264-244abd454f03.png)

## 바킹독 재귀
- 재귀함수의 조건 : 1. 특정 입력에 대해서는 자기 자신을 호출하지 않고 종료되어야 함(Base Condition). 2. 모든 입력은 base Condition으로 수렴해야 한다.

![image](https://user-images.githubusercontent.com/55608425/93062487-f2b6d700-f6af-11ea-934b-28d209a11665.png)

![image](https://user-images.githubusercontent.com/55608425/93062574-1843e080-f6b0-11ea-8916-0ae2263d8bba.png)

# 바킹독 알고리즘

## 시간, 공간복잡도
- 컴퓨터는 1초에 3~4억 연산이 가능하다.
- 입력의 크기와 문제를 해결하는데 걸리는 시간의 상관관계
- 빅오표기법
  - 주어진 식을 값이 가장 큰 대표항만 남겨서 나타내는 방법 ex) 5N + 3 -> O(N)

![image](https://user-images.githubusercontent.com/55608425/94400748-91f5c700-01a4-11eb-9725-e3938222497e.png)

- 512MB면 1.2억개 int를 선언할 수 있다.

## 실수 자료형

![image](https://user-images.githubusercontent.com/55608425/94540441-6eed1500-0281-11eb-8b8b-ad4b6c56b2e0.png)

- exponent를 기록할 때 지수에 (위에선 2)에 127을 더한다. 음수지수도 올바르게 입력하기 위함이라고 한다.
- 실수의 저장/연산 과정에서 반드시 오차가 발생할 수 밖에 없다. -> 왠만하면 double을 사용하자.
  - float는 유효숫자 6자리
  - double는 유효숫자 15자리까지 정확하다.
- 실수를 비교할 때는 등호를 사용하면 안된다.

## 수학적 귀납법
- 수학적 귀납법은 연역적 증명에 해당한다. 연역적 증명은 어떤 명제가 참인가를 증명하고자 할 때 그 증거 자료로서 이미 증명된 사실들을 이용하는 방식이다.

### 도미노 예시
- 도미노 예시를 들어보면 무수히 많은 도미노가 있을 때 모든 도미노가 넘어갈 것이라고 어떻게 증명할까? 연역적은 1,2,3.... 모든 번째 도미노를 확인한 후 결론을 내린다. 하지만 시간 상 불가능하다.
- 수학적 귀납적으로 생각해보면
  - 첫번째 도미노가 넘어간다.
  - n번째 도미노 조각이 넘어가면 n+1 조각도 넘어갈 것이다.

## 다시 수학적 귀납법으로
- 수학적 귀납법의 원리가 위 도미노 예시와 유사하다.
- 수학적 귀납법은 자연수 n과 관련된 명제 p(n)이 모든 자연수에 대해 성립한다는 것을 증명하는 방법으로 아래 두가지가 성립함을 보이면 된다.
  - p(1)이 성립한다.
  - p(n)이 성립하면, p(n+1)도 성립한다.
- 위 조건을 다시 정리하면 다음과 같다.
  - n = 1일 때 성립
  - n = k일 때 성립한다고 가정하고 n = K+1도 성립함을 보인다.

- [참고 : 수학적 귀납법을 이용해 식 증명하기](https://m.blog.naver.com/PostView.nhn?blogId=jamogenius&logNo=221198859160&proxyReferer=https:%2F%2Fwww.google.com%2F)

# 트리
- 무방향이면서 사이클이 없는 연결 그래프

![image](https://user-images.githubusercontent.com/55608425/95159276-a1929280-07d8-11eb-8470-614d69b93bec.png)

- V 개의 정점을 가진 트리는 V - 1개의 간선을 가지고 있다는 성질
- 임의의 두 점은 연결하는 simple path가 유일하다는 성질
- 트리는 임의의 노드를 루트로 만들 수 있다. 단, 루트가 달라지면 각 노드의 부모 또한 달라집니다.

### 트리 BFS, 부모 배열을 이용하면 노드의 부모를 알 수 있다.

```java
package com.titanic.javatest.barkingdog.tree;

import java.util.*;

public class Tree {

    private static int[] parent = new int[6];
    private static Map<Integer, List<Integer>> tree = new HashMap<>();

    public static void main(String[] args) {
        // 트리
        // 1 = [2, 3], 2 = [1, 4, 5], 3 = [1], 4 = [2], 5 = [2]
        tree.put(1, Arrays.asList(2,3));
        tree.put(2, Arrays.asList(1,4,5));
        tree.put(3, Collections.singletonList(1));
        tree.put(4, Collections.singletonList(2));
        tree.put(5, Collections.singletonList(2));

        bfs(1);
        System.out.println(Arrays.toString(parent));
    }

    public static void bfs(int root) {
        Queue<Integer> queue = new LinkedList<>();
        queue.offer(root);

        while (!queue.isEmpty()) {
            int current = queue.poll();

            // 연결된 노드를 순회하고 자식 노드면 queue에 넣는다.
            for (int next : tree.get(current)) {
                // 부모 노드일 경우
                if (parent[current] == next) continue;
                queue.offer(next);
                parent[next] = current;
            }
        }
    }
}

```

### depth를 구할 수 있는 bfs

```java
private static void depthBfs(int root) {
        Queue<Integer> queue = new LinkedList<>();
        queue.offer(root);

        while(!queue.isEmpty()) {
            int current = queue.poll();

            for (int next : tree.get(current)) {
                if (parent[current] == next) continue;
                parent[next] = current;
                queue.offer(next);
                depth[next] = depth[current] + 1;
            }
        }
    }
```

## DFS

### 부모 배열과 depth를 구하는 dfs 메서드

```java
private static void dfs(int root) {
        Stack<Integer> stack = new Stack<>();
        stack.push(root);

        while (!stack.isEmpty()) {
            int current = stack.pop();

            for (int next : tree.get(current)) {
                if (parent[current] == next) continue;
                parent[next] = current;
                stack.push(next);
                depth[next] = depth[current] + 1;
            }
        }
    }
```

### dfs : 부모와 depth 배열 채우기, 재귀

```java
private static void recursiveDfs(int node) {
        System.out.println(node);
        for (int next : tree.get(node)) {
            if (parent[node] == next) continue;
            parent[next] = node;
            depth[next] = depth[node] + 1;
            recursiveDfs(next);
        }
    }
```

### dfs : 단순 순회 목적이라면 parent배열을 두지 않고 인자로 parent를 넘겨서 더 간단하게 구현할 수 있다.

```java
private static void recursiveDfs(int current, int parent) {
        System.out.println(current);
        for (int next : tree.get(current)) {
            if (parent == next) continue;
            recursiveDfs(next, current);
        }
    }
```


## 이진탐색트리 4가지 순회
- 이진트리를 구현하는 방법은 구조체를 만들어서 할수도 있지만 코딩테스트에서는 거창한 자료구조 대신 왼쪽, 오른쪽 배열로 이진트리를 구현할 수 있다.
- 배열 속 0이라는 값은 해당 자리가 비어있음을 의미한다.

![image](https://user-images.githubusercontent.com/55608425/95167312-68631e00-07ea-11eb-97ae-104f324f438c.png)


### 레벨 순회, 중위, 후위, 전위 순회

![image](https://user-images.githubusercontent.com/55608425/95167487-b9731200-07ea-11eb-8527-9f3d71a0bfcc.png)


```java
package com.titanic.javatest.barkingdog.tree;

import java.util.LinkedList;
import java.util.Queue;

public class BinaryTree {
    private static int[] lc = new int[]{0,2,4,6,0,0,0,0,0};
    private static int[] rc = new int[]{0,3,5,7,0,8,0,0,0};

    public static void main(String[] args) {
        int root = 1;
//        levelTraversal(root);
//        preorderTraversal(root);
//        inorderTraversal(root);
        postTraversal(root);
    }

    private static void postTraversal(int current) {
        if (lc[current] != 0) postTraversal(lc[current]);
        if (rc[current] != 0) postTraversal(rc[current]);
        System.out.println(current);
    }

    private static void inorderTraversal(int current) {
        if (lc[current] != 0) inorderTraversal(lc[current]);
        System.out.println(current);
        if (rc[current] != 0) inorderTraversal(rc[current]);
    }

    private static void preorderTraversal(int current) {
        System.out.println(current);
        if (lc[current] != 0) preorderTraversal(lc[current]);
        if (rc[current] != 0) preorderTraversal(rc[current]);
    }

    /** 레벨 순회
     *  bfs 방식으로 순회하면된다. 다만, lc, rc에 맞게 구현해야 한다
     *  부모 배열이 필요하면 레벨 순회할 때 코드를 추가하면 된다.
     */
    private static void levelTraversal(int root) {
        Queue<Integer> queue = new LinkedList<>();
        queue.offer(root);

        while (!queue.isEmpty()) {
            int current = queue.poll();
            System.out.println(current);
            if (lc[current] != 0) {
                parents[lc[current]] = current;
                queue.offer(lc[current]);
            }
            if (rc[current] != 0) {
                parents[rc[current]] = current;
                queue.offer(rc[current]);
            }
        }
    }
}

```

# 그래프

- 인접리스트로 트리 구조를 표현하고 DFS, BFS로 문제에서 필요한 값을 구하면 된다.
- 보통 BFS나 DFS로 접근하면 된다.

## BFS에서 연결 그래프가 아닌 경우 (그래프가 끊어진 경우)
- 1번을 시작 정점으로 잡지 않고 아직 방문하지 않는 정점을 시작 정점으로 잡게끔 코드를 변형하면 된다.
- 아래 코드는 C++ 코드지만 처음 시작 노드를 잡을 때 if(vis[i]) continue;로 방문했는지 확인한다.

![image](https://user-images.githubusercontent.com/55608425/96413049-03fe8080-1226-11eb-9377-d549ca88d824.png)

## DFS에서 탐색 시 재귀로 구현 가능 (dfs가 스택을 사용하기 때문에 비슷한 원리)
- 연결 그래프에서의 순회, 재귀는 아래 코드처럼 구현할 수 있다.
- 다만, 재귀를 들어가기 전에 시작 노드를 방문했다는(dist[i] = 0 or visite[i] = true)와 같은 작업을 한 뒤 들어가야 한다.
- 주의할점) 스택 영역 메모리 제한이 256. 512일 경우 dfs를 재귀로 구현하면 스택오버플로우가 발생할 수 있다.

![image](https://user-images.githubusercontent.com/55608425/96413501-b1719400-1226-11eb-8061-fbabb478a56d.png)

## 재귀 DFS와 비재귀 DFS의 동작 차이
- 재귀적인 방식과 비재귀적인 방식은 방문했다는 기록을 언제 남기냐에 대한 차이가 있다.
- 우리가 관념적으로 생각하는 dfs는 하나의 노드에 들어가면 그 노드의 자식노드로 바로 들어가는 구조이다.
- 비재귀 DFS는 순회를 잘 수행하지만 우리가 관념적으로 생각하는 DFS와 세부 동작이 다르다. 그래서 단순히 Flood fill 내지는 순회를 하는 것이라면 비재귀 DFS로 해도 된다. 하지만 DFS의 고유한 성질을 사용해 문제를 해결해야 하는 상황일 경우 비재귀 코드를 이용하면 안된다. 재귀  DFS 이용하기.
- **하지만 DFS의 고유한 성질을 이용하는 문제는 코테에서 많이 나오지 않는다고 하니까 비재귀 방식 하나만 알아둬도 좋을 것 같다.**  

### DFS에서 연결 그래프가 아닌 경우
- 위에 BFS에서 처럼 방문하지 않은 노드를 시작 노드로 설정해서 순회하면 된다.

![image](https://user-images.githubusercontent.com/55608425/96414729-783a2380-1228-11eb-9124-b664406840e4.png)
