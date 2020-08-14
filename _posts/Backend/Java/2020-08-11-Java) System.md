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

## System.nanoTime() 이용한 프로그램 측정시간 파악

- 테스트코드 실행 전에 startTime, 실행 후에 endTime을 측정한 뒤 그 둘을 빼서 계산한다.
- currentTimeMillis()보다 nanoTime()이 정확하기 때문이다.

```java
public class BubbleSortTest {

    private BubbleSort bubbleSort;
    private List<Integer> unsortedList;
    private long startTime;
    private long endTime;

    @BeforeEach
    void 버블정렬을_생성한다() {
        this.bubbleSort = new BubbleSort();

        Set<Integer> randomSet = new HashSet<>(30);
        while(randomSet.size() != 30) {
            randomSet.add(new Random().nextInt(100));
        }
        this.unsortedList = new ArrayList<>(randomSet);
        this.startTime = System.nanoTime();
    }

    @Test
    void 버블정렬을_테스트한다() {

        System.out.println(unsortedList);
        this.bubbleSort.sort(unsortedList);
        System.out.println(unsortedList);
    }

    @AfterEach
    void tearDown() {
        this.endTime = System.nanoTime();
        double elapsedTime = (this.endTime - this.startTime) / 1000000.0;
        System.out.println("elapsedTime : " + elapsedTime);
    }
}
```

### nanoTime()으로 시간 계산할 땐 초인지 밀리초인지에 따라 나누는 값이 달라진다.
- 1초 = 1,000,000,000 nanoTime()
- 1밀리초 = 1,000,000 nanoTime()
