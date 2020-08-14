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
