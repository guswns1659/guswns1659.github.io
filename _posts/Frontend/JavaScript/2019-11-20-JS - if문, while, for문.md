---
header:
  overlay_image: /assets/javascript.jpg
  overlay_filter: 0.2
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
categories:
  - JavaScript
---

자바스크립트 객체 기반의 스크립트 프로그래밍 언어입니다.


# 목차
* if문
* while, for문
	* while, for문 속 break, continue

## If문 
타 언어와 비슷하다. 
```javascript
if(조건) {
	// code 들어가는 위치 
} else if {

} else {

}
```

## while,  for문 
타 언어와 비슷하다. 써본 결과 나는 for문이 while문 보다 편하다. 한줄로 입력된다.

1. 변수 초기화
2. 반복할 조건 입력
3. 코드 입력
4. 변수++

```javascript
var i = 0;
while (조건) {
	// code 
	i++;
}

for(var i = 0; i < n; i++){
	// code
}
```

### 반복문 속 break, continue
`break`는 반복문을 빠져나가는 명령이다. 이제 그만해! 라는 느낌. 주의할 점은 가장 가까운 반복문 1개를 빠져나간다. 만약, 반복문이 2개였다면 1개만 빠져나가고 다른 하나는 그대로 실행된다. 

`continue`는 뒤에 코드는 실행하지 않고 다시 반복문으로 돌아가는 명령이다. "여기서 다시 반복문으로 돌아가"라는 느낌. 만약, 3번째 반복 중이었다면 4번째부터 시작한다. 

## 배열
배열은 객체이다. 객체이니까 속성과 메소드가 존재한다. 배열은 문자열과 비슷하다. 하지만 가장 큰 차이점은 문자열은 `immutable(바꾸지 못한다)`이다. 배열은 매소드를 통해 수정이 가능한데, 문자열은 수정이 불가능하다. 

그래서 배열의 메소드 중 원래 값을 바꾸는 메소드는 문자열에는 없다. 나머지는 거의 다 있다. 
