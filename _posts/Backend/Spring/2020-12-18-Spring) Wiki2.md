---
title: "Spring) Wiki2"
header:
  overlay_image: /assets/spring.jpg
  overlay_filter: 0.2
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
categories:
  - Spring
---

## 스케쥴링

- 스프링부트 버전에서 지원하는 버전에 맞게 의존성 추가한다. 해당 프로젝트가 부트 2.3.1이라서 한참 아래 버전인 3.1.6 추가

```java
// https://mvnrepository.com/artifact/org.awaitility/awaitility
    implementation group: 'org.awaitility', name: 'awaitility', version: '3.1.6'

```

- 스케쥴링 객체 생성

```java
@Component
@Slf4j
public class ScheduledTasks {

    private static final SimpleDateFormat dateFormat = new SimpleDateFormat("HH:mm:ss");

    @Scheduled(fixedRate = 5000)
    public void reportCurrentTime() {
        log.info("The time is now {}", dateFormat.format(new Date()));
    }
}
```

- Application에 EnableScheduling 어노테이션 추가

```java
@SpringBootApplication
@EnableScheduling
public class JavaTestApplication {

    public static void main(String[] args) {
        SpringApplication.run(JavaTestApplication.class);
    }
}
```

- 결과

![image](https://user-images.githubusercontent.com/55608425/95415042-37115c00-096a-11eb-9d45-3d04f1891a33.png)

## Spring cron
- UNIX cron과 문법이 조금 다르다.
- 항목이 6개가 있다. 초, 분, 시, 일, 달, 요일
- 아래 예시

```java
"0 0 18 * * MON-FRI" means every weekday at 6:00 PM.

"0 0 */1 * * *" means every hour on the hour.

"0 0 */8 * * *" means every 8 hours on the hour.

"0 0 12 1 * *" means 12:00 PM on the first day of every month.
```

- zone 설정

```java
@Component
@Slf4j
@Transactional
public class Scheduler {

    @PersistenceContext
    private EntityManager entityManager;

    /**
     * 매일 자정에 todayElapsedTime 0으로 초기화
     */
    @Scheduled(cron = "0 0 0 */1 * *", zone = "Asia/Seoul")
    public void resetTodayElapsedTime() {
        entityManager.createNativeQuery("update account_goal set today_elapsed_time = :value")
                .setParameter("value", 0)
                .executeUpdate();
    }

    /**
     * 매주 일요일 자정에 weeklyElapsedTime 0으로 초기화
     */
    @Scheduled(cron = "0 0 0 * * SUN", zone = "Asia/Seoul")
    public void resetWeeklyElapsedTime() {
        entityManager.createNativeQuery("update account_goal set weekly_elapsed_time = :value")
                .setParameter("value", 0)
                .executeUpdate();
    }
```

## 참고
- [밸덩](https://www.baeldung.com/spring-scheduled-tasks)


## @Validated 사용법
- 쿼리 파람 validation할 때 필요하다

- [참고 블로그](https://reflectoring.io/bean-validation-with-spring-boot/)

## ResponseEntity<> 사용법
- 하나씩 처음부터 해보니 사용법에 대해서 익숙해진다.
- 원인 : new ResponseEntity<Object, HttpStatus>() 생성자의 순서가 틀림
- 해결 : Object, HttpStatus 순서로 가야함

```java
@ExceptionHandler(NoSuchTodoException.class)
    public ResponseEntity<ExceptionResponse> noSuchEntity(NoSuchTodoException e) {
        return new ResponseEntity<>(ExceptionResponse.of(HttpStatus.NOT_FOUND.value(), e.getMessage()),
                HttpStatus.NOT_FOUND);
    }
```

# Spring

## 회원 도메인 협력 관계
- 기획자도 함께 보는 도메인 모델

![image](https://user-images.githubusercontent.com/55608425/97852914-b8c29280-1d3a-11eb-933b-a02f98090282.png)


## 회원 클래스 다이어그램
- 개발자가 사용하는 특정 언어, 프레임워크에 기반해서 작성하는 다이어그램. 아래같은 경우는 자바를 기반으로 객체 기반 다이어그램이다.

![image](https://user-images.githubusercontent.com/55608425/97852928-bf510a00-1d3a-11eb-9ea9-eba4e45b28ff.png)


## 회원 객체 다이어그램
- 실제 서비스가 운영될 때 사용되는 인스턴스를 의미한다. 다형성이 구현되어 있으면 구체적인 인스턴스는 프로그램이 실행될 때 정해지기 때문이다.

![image](https://user-images.githubusercontent.com/55608425/97852944-c710ae80-1d3a-11eb-8437-b120d2af2220.png)

## 서비스 추상화
- 지금까지 개발할 땐 콘트리트 서비스 클래스를 사용했다. 하지만 스프링 예제를 보니 서비스도 추상화 계층을 두고 콘크리트 클래스를 따로 사용하는 것 같다.

- 인터페이스

```java
package com.titanic.springstudy.member;

public interface MemberService {

    void join(Member member);

    Member findMember(Long memberId);
}

```


- 콘크리트 클래스

```java
public class MemberServiceImpl implements MemberService {

    private final MemberRepository memberRepository = new MemoryMemberRepository();

    @Override
    public void join(Member member) {
        memberRepository.save(member);
    }

    @Override
    public Member findMember(Long memberId) {
        return memberRepository.findById(memberId);
    }
}
```

## OCP 및 DIP가 적용이 안된 코드

- 아래 코드는 콘크리트 클래스(MemberServiceImpl)에 직접 구현하고 있기 때문에 DIP가 깨진 상황
- 만약 다른 Service로 변경해야 한다면 변해야 하는 코드가 많기 때문에 OCP도 지켜지지 않고 있다.

```java
public class MemberApp {

    public static void main(String[] args) {
        MemberService memberService = new MemberServiceImpl();
        Member member = new Member(1L, "memberA", Grade.VIP);
        memberService.join(member);

        Member findMember = memberService.findMember(1L);
        System.out.println("new member = " + member.getName());
        System.out.println("find Member = " + findMember.getName());
    }
}
```

## 역할과 구현 분리
- 역할이란 인터페이스를 의미하고 구현이란 해당 인터페이스를 구현한 콘크리트 클래스를 의미한다. 역할과 구현을 분리했다는 의미는 추상화를 했다는 말이다.

![image](https://user-images.githubusercontent.com/55608425/97868863-c9cbcd80-1d53-11eb-9229-5c66aca6707e.png)

- 구체화된 클래스 다이어그램

![image](https://user-images.githubusercontent.com/55608425/97868917-e36d1500-1d53-11eb-870d-255bae88d0ec.png)

# Spring

## DIP 위반
- 인터페이스에 의존하고 있다고 해서 DIP, OCP가 항상 적용되지 않다. 클라이언트에서 인터페이스도 의존하지만 구체 클래스도 의존하고 있다면 DIP 위반. 아래 코드를 보자

- 클라이언트(ServiceImpl)이 DiscountPolicy(인터페이스)와 RadeDiscountPolicy(구체클래스)에 둘 다 의존하고 있다 .

```java
public class OrderServiceImpl implements OrderService {

    private final MemberRepository memberRepository = new MemoryMemberRepository();
    private final DiscountPolicy discountPolicy = new RateDiscountPolicy();

    @Override
    public Order createOrder(Long memberId, String itemName, int itemPrice) {
        Member member = memberRepository.findById(memberId);
        int discountPrice = discountPolicy.discount(member, itemPrice);

        return new Order(memberId, itemName, itemPrice, discountPrice);
    }
}
```

## 그럼 어떻게 해야할까?
- 구체 클래스를 클라이언트에서 생성하지 않고 외부에서 주입해주는 객체가 필요하다. 이게 스프링의 역할이다. DI 컨테이너를 통해서 빈으로 등록된 객체는 스프링이 주입을 해준다.

- AppConfig (구체 클래스를 주입해주는 DI 컨테이너 역할)

```java
public class AppConfig {

    public MemberService memberService() {
        return new MemberServiceImpl(new MemoryMemberRepository());
    }

    public OrderService orderService() {
        return new OrderServiceImpl(new MemoryMemberRepository(), new FixDiscountPolicy());
    }
}
```

- DIP가 적용된 클라이언트 클래스 (생성자를 통해 주입된다)

```java
public class OrderServiceImpl implements OrderService {

    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;

    public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }
```

- main 코드 (이제 의존성 주입은 AppConfig가 담당한다.)

```java
public class MemberApp {

    public static void main(String[] args) {
        AppConfig appConfig = new AppConfig();
        MemberService memberService = appConfig.memberService();
        Member member = new Member(1L, "memberA", Grade.VIP);
        memberService.join(member);

        Member findMember = memberService.findMember(1L);
        System.out.println("new member = " + member.getName());
        System.out.println("find Member = " + findMember.getName());
    }
}
```

## 순수 자바 코드를 스프링 코드로 변경하기
- AppConfig에 @Bean과 @Configuration을 붙인다.

```java
@Configuration
public class AppConfig {

    @Bean
    public MemberService memberService() {
        return new MemberServiceImpl(memberRepository());
    }

    @Bean
    public MemberRepository memberRepository() {
        return new MemoryMemberRepository();
    }

    @Bean
    public OrderService orderService() {
        return new OrderServiceImpl(memberRepository(), discountPolicy());
    }

    @Bean
    public DiscountPolicy discountPolicy() {
//        return new FixDiscountPolicy();
        return new RateDiscountPolicy();
    }
}
```

### ApplicationContext를 사용한 코드

```java
public class MemberApp {

    public static void main(String[] args) {
//        AppConfig appConfig = new AppConfig();
//        MemberService memberService = appConfig.memberService();

        ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
        MemberService memberService = applicationContext.getBean("memberService", MemberService.class);

        Member member = new Member(1L, "memberA", Grade.VIP);
        memberService.join(member);

        Member findMember = memberService.findMember(1L);
        System.out.println("new member = " + member.getName());
        System.out.println("find Member = " + findMember.getName());
    }
}
```

## 스프링 컨테이너
- ApplicationContext를 스프링 컨테이너라고 한다.
- 스프링 컨테이너는 @Configuration 이 붙은 AppConfig 를 설정(구성) 정보로 사용한다. 여기서 @Bean이라 적힌 메서드를 모두 호출해서 반환된 객체를 스프링 컨테이너에 등록한다. 이렇게 스프링 컨테이너에 등록된 객체를 스프링 빈이라 한다.
- 스프링 빈은 @Bean 이 붙은 메서드의 명을 스프링 빈의 이름으로 사용한다(memberService ,orderService )

- 스프링에서는 ObjectMapper가 있어 json을 object로 변환해주는 역할을 한다.
- @JsonNaming(PropertyNamingStrategy.SnakeCaseStrategy::class)을 사용하면 전체 클래스의 네이밍 전략을 지정할 수 있다.

## @PostConstruct

- @PostConstruct는 의존성 주입이 이루어진 후 초기화를 수행하는 메서드이다. @PostConstruct가 붙은 메서드는 클래스가 service(로직을 탈 때? 로 생각 됨)를 수행하기 전에 발생한다. 이 메서드는 다른 리소스에서 호출되지 않는다해도 수행된다

# Spring

## ApplicationContextAware
- 용도 : 빈이 아닌 객체에 빈 객체를 주입하고 싶을 때 사용
- 방법 : ApplicationContextAware를 구현하는 클래스를 만든다음 아래처럼 사용한다.

```java
@Component
class ApplicationContextProvider : ApplicationContextAware {

    companion object {
        private lateinit var applicationContext: ApplicationContext

        fun getApplicationContext() : ApplicationContext{
            return applicationContext
        }
    }

    override fun setApplicationContext(appContext: ApplicationContext) {
        applicationContext = appContext
    }
}

class BeanUtils {
    companion object {
        fun getBean(beanName:String) : Any{
            val applicationContext = ApplicationContextProvider.getApplicationContext();
            return applicationContext.getBean(beanName)
        }
    }
}
```

# Spring

## Validation
- [공식문서](https://beanvalidation.org/2.0-jsr380/spec/#builtinconstraints-null)

- validation을 사용하기 위해선 자바빈으로 등록되어야 하는데 @Validated가 도와준다.

```java
@RestController
@RequestMapping("/api")
@Validated
class DeleteController {

    @DeleteMapping(path = ["/delete-mapping"])
    fun deleteMapping(
        @RequestParam(value = "name") _name : String,

        @NotNull(message = "age 값이 누락되었습니다.")
        @Min(value = 20, message = "ages는 20보다 커야 합니다.")
        @RequestParam(name = "age") _age : Int
    ) : String {
        println(_name)
        println(_age)
        return "$_name $_age"
    }

```

- requestDTO에 대해서 검증할 때 @Valid는 하나의 객체에 적용할 수 있다.

```java
@RestController
@RequestMapping("/api")
class PutApiController {

    @PutMapping(path = ["/put-mapping/object"])
    fun putMappingObject(@Valid @RequestBody userRequest: UserRequest) : UserResponse {
        return UserResponse().apply {

        }
    }
}

data class UserResponse(

    @field:NotEmpty
    @field:Pattern(regexp = "")
    private var result: Result?=null,
    private var description: String?=null

)
```

- validation할 때 에러가 발생할 때 처리하는 법

```java
@RestController
@RequestMapping("/api")
class PutApiController {

    @PutMapping(path = ["/put-mapping/object"])
    fun putMappingObject(@Valid @RequestBody userRequest: UserRequest, bindingResult: BindingResult): ResponseEntity<String> {

        if (bindingResult.hasErrors()) {
            // 500 error
            val msg = StringBuilder()
            bindingResult.allErrors.forEach {
                // as는 형변환
                val filed = it as FieldError
                val message = it.defaultMessage
                msg.append("${filed.field} : $message\n")
            }
            return ResponseEntity.badRequest().body(msg.toString())
        }
        return ResponseEntity.ok("")
    }
}
```

- 내가 정의한 validation사용하는 법 : @AssertTrue를 사용한다. 메서드명은 isValid필드명으로 해야 하는 것 같다

```java
data class UserRequest (

        var name: String?=null,
        var phoneNumber: String?=null,

        var createdAt:String?=null // yyyy-MM-dd HH:mm:ss
) {
    @AssertTrue(message = "생성일자의 패턴은 yyyy-MM-dd HH:mm:ss")
    private fun isValidCreatedAt():Boolean {

        try {
            LocalDateTime.parse(this.createdAt, DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss"))
            return true

        } catch (e:Exception) {
            return false
        }
    }
}
```

# Spring

## Custom Validation 어노테이션
- 직접 어노테이션을 만들어서 해당 필드에 대한 validation을 할 수 있다.

- 내가 필요한 어노테이션을 만든다.

```java
@Constraint(validatedBy = [StringFormatDateTimeValidator::class])
@Target(
    AnnotationTarget.FIELD,
    AnnotationTarget.PROPERTY_GETTER,
    AnnotationTarget.PROPERTY_SETTER,
)
@Retention(AnnotationRetention.RUNTIME)
@MustBeDocumented
annotation class StringFormatDateTime(
    val pattern: String = "yyyy-MM-dd HH:mm:ss",
    val message: String = "시간 형식이 유효하지 않습니다.",
    val groups: Array<KClass<*>> = [],
    val payload: Array<KClass<out Payload>> = []
)
```

- 위 어노테이션의 검증 역할을 담당하는 Validattor를 만든다. ConstraintValidator라는 인터페이스가 isValid라는 명세를 정의하고 있다. 그래서 isValid에 내가 필요한 검증로직을 넣는다.


```java
class StringFormatDateTimeValidator : ConstraintValidator<StringFormatDateTime, String> {

    private var pattern : String? = null

    override fun initialize(constraintAnnotation: StringFormatDateTime?) {
        this.pattern = constraintAnnotation?.pattern
    }

    // 정상이면 true, 비정상이면 false
    override fun isValid(value: String?, context: ConstraintValidatorContext?): Boolean {
        try {
            LocalDateTime.parse(value, DateTimeFormatter.ofPattern(pattern))
            return true

        } catch (e:Exception) {
            return false
        }
    }
}
```

- 실제 필드에서 사용하는 법

```java
data class UserRequest(

    var name: String? = null,
    var phoneNumber: String? = null,

    @field:StringFormatDateTime(pattern = "yyyy-MM-dd HH:mm:ss", message = "패턴이 올바르지 않습니다.")
    var createdAt: String? = null // yyyy-MM-dd HH:mm:ss
)
```

# ControllerAdvice

## ControllerAdvice의 범위를 지정하는 법
- 어노테이션 내부에 다양한 기능이 존재한다. 그 중 basePackageClasses이다.

```java
@RestControllerAdvice(basePackageClasses = [ExceptionApiController::class])
class GlobalControllerAdvice {

    @ExceptionHandler(value = [RuntimeException::class])
    fun exception(e : RuntimeException) : String {
        return "Server Error"
    }

    @ExceptionHandler(value = [IndexOutOfBoundsException::class])
    fun indexOutOfBoundsException(e : IndexOutOfBoundsException) : ResponseEntity<String> {
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body("Index Error")
    }
}

```

# Spring

## constraintViolationException 사용해서 디테일하게 에러 출력
- requestParam을 validation할 때 에러 발생 시 constraintViolationException에 에러 상세 정보가 들어간다. 아래처럼 상세하게 에러를 핸들링 할 수 있다.

```java
@RestController
class ExceptionApiController {

    @ExceptionHandler(value = [ConstraintViolationException::class])
    fun constraintViolationException(e: ConstraintViolationException, request: HttpServletRequest): ResponseEntity<ErrorResponse> {
        // 1. 에러분석
        val errors = mutableListOf<Error>()

        e.constraintViolations.forEach {
            val error = Error().apply {
                this.field = it.propertyPath.last().name
                this.message = it.message
                this.value = it.invalidValue
            }
            errors.add(error)
        }

        // 2. ErrorResponse
        val errorResponse = ErrorResponse().apply {
            this.resultCode = "FAIL"
            this.httpStatus = HttpStatus.BAD_REQUEST.value().toString()
            this.httpMethod = request.method
            this.message = "요청에 에러가 발생했습니다."
            this.path = request.requestURI.toString()
            this.timestamp = LocalDateTime.now()
            this.errors = errors
        }

        // 3. ResponseEntity
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(errorResponse)
    }
}
```

- requestBody를 @Valid로 검증할 때 에러를 핸들링하는 법

```java
    @ExceptionHandler(value = [MethodArgumentNotValidException::class])
    fun methodArgumentNotValidException(e: MethodArgumentNotValidException, request: HttpServletRequest): ResponseEntity<ErrorResponse> {

        val errors = mutableListOf<Error>()

        e.bindingResult.allErrors.forEach { errorObject ->
            val error = Error().apply {

                val field = errorObject as FieldError
                this.field = field.field
                this.message = errorObject.defaultMessage
                this.value = errorObject.rejectedValue
            }
            errors.add(error)
        }

        // 2. ErrorResponse
        val errorResponse = ErrorResponse().apply {
            this.resultCode = "FAIL"
            this.httpStatus = HttpStatus.BAD_REQUEST.value().toString()
            this.httpMethod = request.method
            this.message = "요청에 에러가 발생했습니다."
            this.path = request.requestURI.toString()
            this.timestamp = LocalDateTime.now()
            this.errors = errors
        }

        // 3. ResponseEntity
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(errorResponse)

    }
```
