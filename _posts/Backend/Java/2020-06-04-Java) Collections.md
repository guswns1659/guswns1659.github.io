## List<객체> 를 특정 객체의 값을 기준으로 sort하고 싶을 때

```java
this.getReservations().sort(Comparator.comparing(UserReservation::getId).reversed());
```

## Collection.sort()에서 Comparator를 사용하고 싶을 때

```Java
listOfStudent.sort((s1, s2) -> s1.age - s2.age);
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
