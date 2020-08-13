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
