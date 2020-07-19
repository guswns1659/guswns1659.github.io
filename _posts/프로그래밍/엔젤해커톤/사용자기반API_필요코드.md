## 사용자 기반 API 기능 구현 위한 클래스 및 코드

## utils 패키지 생성

## enum CardinalDirection

```Java
package me.titatic.hackatonpractice.utils;

import lombok.Getter;

@Getter
public enum CardinalDirection {
    NORTH(0.0), WEST(270.0), SOUTH(180.0), EAST(90.0),
    NORTHWEST(315.0), SOUTHWEST(225.0), SOUTHEAST(135.0), NORTHEAST(45.0);

    private final Double bearing;

    CardinalDirection(Double bearing) {
        this.bearing = bearing;
    }
}
```

## GeometryUtils

```java
package me.titatic.hackatonpractice.utils;

public class GeometryUtils {

    public static Location calculateByDirection(Double baseLatitude, Double baseLongitude, int distance,
        Double bearing) {

        Double radianLatitude = toRadian(baseLatitude);
        Double radianLongitude = toRadian(baseLongitude);
        Double radianAngle = toRadian(bearing);
        Double distanceRadius = distance / 6371.01;

        Double latitude = Math.asin(sin(radianLatitude) * cos(distanceRadius) +
            cos(radianLatitude) * sin(distanceRadius) * cos(radianAngle));
        Double longitude = radianLongitude + Math.atan2(sin(radianAngle) * sin(distanceRadius) * cos(radianLatitude),
            cos(distanceRadius) - sin(radianLatitude) * sin(latitude));

        longitude = (longitude + 540) % 360 - 180;

        return new Location(toDegree(latitude), toDegree(longitude));
    }

    private static Double toRadian(Double coordinate) {
        return coordinate * Math.PI / 180.0;
    }

    private static Double toDegree(Double coordinate) {
        return coordinate * 180.0 / Math.PI;
    }

    private static Double sin(Double coordinate) {
        return Math.sin(coordinate);
    }

    private static Double cos(Double coordinate) {
        return Math.cos(coordinate);
    }
}

```

## Location 객체

```java
package me.titatic.hackatonpractice.utils;

import lombok.AllArgsConstructor;
import lombok.Getter;
import lombok.RequiredArgsConstructor;

@Getter
@RequiredArgsConstructor
@AllArgsConstructor
public class Location {

    private Double latitude;
    private Double longitude;
}

```


## 조회를 위한 쿼리

```java
@DisplayName("nKm 이내의 식당을 구하는 테스트")
    @Test
    void 범위_내_식당을_구한다() {
        Double baseLatitude = 37.51573;
        Double baseLongitude = 127.11835;
        int distance = 1;

        // 북동쪽 좌표 구하기
        Location northEast = GeometryUtils.calculateByDirection(baseLatitude, baseLongitude, distance, CardinalDirection.NORTHEAST
            .getBearing());
        Location southWest = GeometryUtils.calculateByDirection(baseLatitude, baseLongitude, distance, CardinalDirection.SOUTHWEST
            .getBearing());

        double x1 = northEast.getLongitude();
        double y1 = northEast.getLatitude();
        double x2 = southWest.getLongitude();
        double y2 = southWest.getLatitude();

        // native query 활용
        Query query = entityManager.createNativeQuery("" +
            "SELECT r.id, r.name, r.description, r.point \n" +
            "FROM restaurant AS r \n" +
            "WHERE MBRContains(ST_LINESTRINGFROMTEXT(" + String.format("'LINESTRING(%f %f, %f %f)')", x1, y1, x2, y2) + ", r.point)"
        , Restaurant.class);

        // query.setParameter(1, baseLongitude);
        // query.setParameter(2, baseLatitude);
        // query.setParameter(3, distance * 1000);

        List<Restaurant> result = query.getResultList();
        assertThat(result.size()).isEqualTo(2);
        assertThat(result.get(0).getId()).isEqualTo(BigInteger.valueOf(5));
    }
```
