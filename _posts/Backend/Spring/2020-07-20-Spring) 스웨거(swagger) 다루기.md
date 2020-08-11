## 스웨거(Swagger) 문서 다루기
스웨거도 다양한 설정이 가능하다. 아래 문서를 참고한다.

- [참고1](https://mrsence.tistory.com/56)
- [참고2](https://www.codota.com/code/java/classes/io.swagger.annotations.ApiImplicitParams)


### 의존성 추가한다.

```java
    implementation group: 'io.springfox', name: 'springfox-swagger2', version: '2.9.2'
    implementation group: 'io.springfox', name: 'springfox-swagger-ui', version: '2.9.2'
```

## 각 컨트롤러 설명 붙이는 메서드

- @ApiOperation : 컨트롤러에 설명을 붙일 수 있다.
  - value : 상단의 표시
  - note : 컨트롤러 클릭하면 본문에 표시

```java
@ApiOperation(value = "300M 이내 식당 요청",
        notes = "기준 좌표 300M 이내 식당 리스트 요청, 고정 latitude = 33.24859, longitude = 126.5648")
    @GetMapping("nearRestaurant")
    public NearRestaurantResponseDtos nearRestaurant(
        @RequestParam(value = "latitude") Double latitude,
        @RequestParam(value = "longitude") Double longitude) {
        return restaurantService.nearRestaurant(latitude, longitude);
    }
```

### 에러 : TypeError : fail to fetch 발생 시 swagger 설정의 host를 본다. 로컬인데 배포 호스트로 했는지 아닌지 
