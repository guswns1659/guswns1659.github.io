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
