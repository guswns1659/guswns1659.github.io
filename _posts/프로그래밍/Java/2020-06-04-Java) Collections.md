## List<객체> 를 특정 객체의 값을 기준으로 sort하고 싶을 때

```java
this.getReservations().sort(Comparator.comparing(UserReservation::getId).reversed());
```

## Collection.sort()에서 Comparator를 사용하고 싶을 때

```Java
listOfStudent.sort((s1, s2) -> s1.age - s2.age);
```
