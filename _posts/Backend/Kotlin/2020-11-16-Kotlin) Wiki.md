---
title: "kotlin) Wiki"
header:
  overlay_image: /assets/java.jpg
  overlay_filter: 0.2
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
categories:
  - Kotlin
---

Kotlin은 자바에 비해 생산성이 향상됩니다.

# 코틀린

## 코틀린에서 컨트롤러 테스트

```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
@AutoConfigureWebTestClient(timeout = "30000")
class GetApiControllerTest {

    @LocalServerPort
    private var port: Int = 0;

    @Autowired
    lateinit var webTestClient: WebTestClient

    // equivalent of final static
    companion object {
        private const val REQUEST_MAPPING: String = "/api/"
        private const val LOCALHOST: String = "http://localhost:";
    }

    @Test
    fun get() {

        // given
        val requestUrl: String = LOCALHOST + port + REQUEST_MAPPING + "hello"

        // when
        webTestClient.get()
                .uri(requestUrl)
                .accept(MediaType.APPLICATION_JSON)
                .exchange()
                .expectStatus().isOk
                .expectBody()
                .consumeWith {
                    assertThat(it.responseBody?.let { it1 -> String(it1) }).isEqualTo("hello kotlin")
                }
    }

}
```

- 여러개의 API 매핑을 가질 수 있게 설정하는 법

```java
@GetMapping(path = ["/hello", "/abcd"])
    fun hello(): String{
        return "hello kotlin"
    }
```

- @CsvSource 사용법

```java
@DisplayName("path-variable 이용한 GetMapping 테스트")
    @ParameterizedTest
    @CsvSource("jack,10,jack 10")
    fun getPathVariable(name: String, age: Int, expected: String) {
        // given
```

- 코틀린에서는 변수명을 스네이크 형식으로 선언할 수 없다. ex) phone-number (x) phoneNumber
- JsonProperty()이용해도 null로 들어온다. -> 왠지 쿼리파람으로 적용할 때 안 먹히는 것 같다. Post의 바디로 전달하면 먹힐 것 같다.

```java

Expecting:
 <null>
to be equal to:
 <"010-7720-7957">
but was not.
```

- Map<>을 통해서 쿼리파람을 받을 수 있다.
- 장점은 하이픈이 들어간 쿼리 파람을 받을 수 있다.

```java
@GetMapping("/get-mappling/query-param/map")
    fun queryParamMap(@RequestParam map: Map<String,Any>): Map<String, Any> {
        println(map)
        return map
    }
```

# 코틀린

## val, var
- val : 불변타입변수, 자바의 final과 유사하다. 해당 변수의 값은 변경할 수 없지만 해당 참조의 값은 변경이 가능하다고 한다.
- var : 가변타입변수

## apply, let

- let : 지정된 값이 null이 아닐 경우 실행되는 코드. if (result != null)와 같은 코드가 사라진다.

```java
getNullablePerson()?.let {
    // null 이 아닐때만 실행됩니다.
    promote(it)
}
```

- apply : 수신 객체 람다 내부에서 수신 객체의 함수를 사용하지 않고 수신 객체 자신을 다시 반환 하려는 경우에 사용

```java
val jack = Human().apply {
    // apply 의 블록 에서는 오직 프로퍼티 만 사용합니다!
    name = "Peter"
    age = 18
}
```

- also : 수신 객체 람다가 전달된 수신 객체를 전혀 사용 하지 않거나 수신 객체의 속성을 변경하지 않고 사용하는 경우에 사용
- run : 어떤 값을 계산할 필요가 있거나 여러개의 지역 변수의 범위를 제한할 때 사용

# 코틀린

## 정적팩터리 메서드 만들기

- [참고 블로그](https://kotlin-code.com/design-patterns/creational-patterns/factory-method/)

- 생성

```java
class Complex private constructor(private val re: Double, private val im: Double) {

    companion object Factory {
        fun valueOf(re: Double, im: Double): Complex {
            return Complex(re, im)
        }
    }
}
```

- main에서 사용, 계속 실수한게 val valueOf: Complex로 구하려고 했음.

```java
fun main(args: Array<String>) {
    val valueOf = Complex.valueOf(3.1, 3.2)
    print(valueOf.toString())
}
```

## open 키워드
- 상속을 위한 부모 클래스에 붙여야 하는 키워드이다. 해당 키워드가 없으면 상속을 구현할 수 없다고 한다.

# 코틀린

## assertAll 테스트
- import org.junit.jupiter.api.assertAll

```java
assertAll("convert",
            { assertThat(convertToEntityAttribute).isEqualTo(decryptedField) },
            { assertThat(convertToDatabaseColumn).isEqualTo(encryptedField) }
        )
```

## apply 사용법
- apply 적용 후에 run을 실행하면 된다.

```java
@Service
class TodoRepositoryImpl: TodoRepository {

   @Autowired
   lateinit var todoDataBase: TodoDataBase

   override fun save(todo: Todo): Todo {

       return todo.apply {
           this.index = todoDataBase.index++
       }.run {
           todoDataBase.todoList.add(todo)
           this
       }
   }
```

- let 사용법
- null이 아닐 경우, null일 경우에 대한 코드를 아래처럼 처리

```java
override fun delete(index: Int): Boolean {
       val todo = findOne(index)

       return todo?.let {
           todoDataBase.todoList.remove(it)
           true
       }?: kotlin.run {
           false
       }
   }
```

## List, Map

- [참고글](https://brunch.co.kr/@mystoryg/28)

- listOf()는 읽기 전용 모드, mutableListOf<String>()가 추가, 삭제가 가능한 list
- mapOf()는 읽기 전용, mutableListOf<String>()는 추가, 삭제가 가능한 map

## 에러 : Lint error > [import-ordering] Imports must be ordered in lexicographic order without any empty lines in-between
- 원인 : lint 설정에 맞지 않는 포매팅이 있기 때문이다. import가 사전식으로 돼야한다는 에러
- 해결 : gradle -> formatting -> formatKotlin

## 에러 : wildcard-import 문제
- 원인 : 몇개 이상이면 *로 바꾸는 설정 문제
- 해결 : 아래와 같이 설정 변경

![image](https://user-images.githubusercontent.com/74221090/102974944-ad256800-4542-11eb-967c-ccc590761115.png)

# Kotlin

## ResponseEntity에서 리다이렉트 하는 법

```java
@GetMapping(path = [""])
    fun read(@RequestParam(required = false) index: Int?): ResponseEntity<Any> {
        return index?.let {
            todoService.read(it)
        }?.let {
            ResponseEntity.ok(it)
        }?: kotlin.run {
            return ResponseEntity.status(HttpStatus.MOVED_PERMANENTLY).header(HttpHeaders.LOCATION, "/api/todo/all").build()
        }
    }
```

## Swagger2 설정

```java
@Configuration
@EnableSwagger2
class SwaggerConfig {

    @Bean
    fun docket(): Docket {
        return Docket(DocumentationType.SWAGGER_2)
            .select()
            .apis(RequestHandlerSelectors.any())
            .paths(PathSelectors.any())
            .build()
    }
}
```

## Swagger Dto 예시 변경하기
- Dto에 들어가서 @ApiModelProperty를 추가한다.

```java
data class TodoDto(
    @field:ApiModelProperty(
        value = "DB INDEX",
        example = "1",
        required = false
    )
    var index:Int?=null,
    @field:ApiModelProperty(
        value = "일정명",
        example = "일정관리",
        required = true
    )
    @field:NotBlank
    var title:String?=null,
    var description:String?=null,
    @field:NotBlank
    // yyyy-MM-dd HH:mm:ss // TODO : 이전에 학습한 custom annotation으로 변경
    var schedule: String?=null,
    var createdAt:LocalDateTime?=null,
    var updatedAt:LocalDateTime?=null


)
```

- swagger 어노테이션 설명

![image](https://user-images.githubusercontent.com/74221090/103165392-90e33d00-485a-11eb-9479-ed77ebea0dd1.png)
