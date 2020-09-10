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

## 알고리즘

- 문제 제출할 때 Node 클래스를 만드는 위치는 public class Main 위에 만들어야 한다. 나동빈 강사 코드랑 큰 차이가 없는데 런타임 에러가 계속 발생하는데 이유를 모르겠다.
- 변수명에 i를 붙여서 반복문을 돌리고 싶지만 자바에서는 불가능하다.

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

# 알고리즘

- 백준 문자열 3문제 도전했는데 2문제 구현 어려워서 포기, 1문제 시도했는데 다른 사람 답 봄.... 넘 갈 길이 멀다.

## HashMap 사용 이유
- 전체 탐색할 때 이 수가 한번 사용된 수인지 체크를 해서 모든 조합에서 사용하면 넘어가는 로직 때 사용해도 좋다.

# 알고리즘

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

# 알고리즘
- 바킹독 유튜브를 통해 BFS 익히는 중인데 언어는 달라도 설명이 좋아서 이해가 잘된다. 어려웠던 DFS문제도 점점 익숙해지는 중

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

# 알고리즘
- [참고](https://www.mathfactory.net/10916)


## 등차수열
-  일반항 : An = A1 + (n+1)d

![image](https://user-images.githubusercontent.com/55608425/92671566-940cf880-f351-11ea-9b13-de4df7334453.png)

## 계차수열
- 일반항 : An+1 = An + Bn

![image](https://user-images.githubusercontent.com/55608425/92671656-cae30e80-f351-11ea-8264-244abd454f03.png)
