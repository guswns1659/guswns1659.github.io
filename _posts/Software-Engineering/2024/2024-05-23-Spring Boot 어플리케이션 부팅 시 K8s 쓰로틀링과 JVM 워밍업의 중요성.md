---
title: Spring Boot 어플리케이션 부팅 시 K8s 쓰로틀링과 JVM 워밍업의 중요성
date: 2024-05-22 11:49:08 +09:00
categories: [Software-Engineering]
tags: engineering
---

# Spring Boot 어플리케이션 부팅 시 K8s 쓰로틀링과 JVM 워밍업의 중요성

Spring Boot 어플리케이션을 Kubernetes(K8s) 환경에서 운영할 때, 특히 어플리케이션이 크고 복잡할 경우 초기 부팅 시 CPU 성능 저하 문제를 경험할 수 있습니다. 이는 K8s의 리소스 제한 및 JVM의 워밍업(warm-up) 과정과 관련이 있습니다. 이 글에서는 이러한 문제의 원인과 JVM 워밍업의 필요성에 대해 상세히 설명하겠습니다.

## CPU 성능 저하의 원인: K8s 쓰로틀링

### Kubernetes의 리소스 제한

K8s는 컨테이너 오케스트레이션 도구로, 각 컨테이너에 대해 CPU와 메모리 사용량을 제한하는 기능을 제공합니다. 이는 `requests`와 `limits` 설정을 통해 이루어집니다.

- **requests**: 컨테이너가 최소한으로 필요한 리소스 양을 정의합니다.
- **limits**: 컨테이너가 사용할 수 있는 최대 리소스 양을 정의합니다.

이 설정은 K8s 클러스터 내에서 리소스를 효율적으로 분배하고, 한 컨테이너가 과도하게 리소스를 사용하여 다른 컨테이너의 성능에 영향을 미치는 것을 방지합니다.

### 쓰로틀링(Throttling)

만약 Spring Boot 어플리케이션이 부팅 시 많은 리소스를 요구하게 되면, K8s는 설정된 `limits`를 초과하는 리소스 사용을 제한하기 위해 쓰로틀링을 적용합니다. 이는 어플리케이션의 성능 저하를 유발합니다.

예시:

```yaml
resources:
  requests:
    memory: "512Mi"
    cpu: "500m"
  limits:
    memory: "1024Mi"
    cpu: "1000m"
```

위 설정에서, 컨테이너는 최대 1000m(1 CPU)까지 사용할 수 있지만, 이 값을 초과하려고 하면 쓰로틀링이 발생합니다. Spring Boot 어플리케이션의 초기 부팅 시 많은 리소스를 필요로 하기 때문에 이 시점에서 성능 저하를 경험할 수 있습니다.

## JVM 워밍업이 필요한 이유

### JVM의 동작 원리

Java Virtual Machine(JVM)은 Java 어플리케이션을 실행하는 데 중요한 역할을 합니다. JVM의 성능은 어플리케이션의 초기 부팅 속도와 런타임 성능에 직접적인 영향을 미칩니다. JVM은 Just-In-Time(JIT) 컴파일러를 사용하여 바이트코드를 머신 코드로 변환합니다. 이 과정은 시간이 걸리며, JVM은 어플리케이션의 성능을 최적화하기 위해 워밍업 단계를 필요로 합니다.

### JIT 컴파일과 워밍업

JIT 컴파일러는 실행 중인 코드의 일부를 컴파일하여 성능을 향상시킵니다. 이 과정에서 JVM은 자주 사용되는 메서드를 최적화합니다. 하지만 JIT 컴파일링 과정은 초기 부팅 시 오버헤드를 발생시킬 수 있습니다. JVM이 최적화된 머신 코드를 생성하기 전에, 초기 실행 단계에서는 인터프리터 모드로 실행되기 때문입니다.

### 워밍업의 중요성

워밍업 없이 JVM이 바로 최적화된 상태로 실행되기는 어렵습니다. 따라서 초기 부팅 시 JVM이 충분히 워밍업되지 않으면 성능 저하가 발생할 수 있습니다. 이는 특히 Spring Boot와 같은 무거운 어플리케이션에서 두드러집니다. JVM이 최적화 작업을 마치기 전까지는 어플리케이션의 응답 시간이 느려질 수 있습니다.

## 예시 코드와 설정

Spring Boot 어플리케이션의 초기 부팅 성능을 개선하기 위한 몇 가지 전략을 소개합니다.

### 1. JVM 옵션 조정

JVM 옵션을 조정하여 JIT 컴파일러의 동작을 최적화할 수 있습니다.

```bash
java -XX:+TieredCompilation -XX:TieredStopAtLevel=1 -Xms512m -Xmx1024m -jar app.jar
```

위 명령은 JVM이 초기 컴파일 단계에서 더 빠르게 워밍업하도록 설정합니다.

### 2. K8s 리소스 요청과 제한 조정

Spring Boot 어플리케이션이 부팅 시 충분한 리소스를 사용할 수 있도록 K8s 설정을 조정합니다.

```yaml
resources:
  requests:
    memory: "1024Mi"
    cpu: "1000m"
  limits:
    memory: "2048Mi"
    cpu: "2000m"
```

### 3. 어플리케이션 코드 최적화

어플리케이션의 초기화 로직을 최적화하여 부팅 시간을 줄입니다. 불필요한 초기화 작업을 지연시키거나, 필요할 때만 초기화하도록 수정합니다.

```java
@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

    @Bean
    public CommandLineRunner commandLineRunner(ApplicationContext ctx) {
        return args -> {
            // 초기화 작업을 지연시킴
            CompletableFuture.runAsync(() -> {
                // 초기화 작업
            });
        };
    }
}
```

## 결론

Spring Boot 어플리케이션의 초기 부팅 시 CPU 성능 저하는 주로 K8s의 쓰로틀링과 JVM 워밍업 부족으로 발생합니다. K8s의 리소스 제한을 적절히 설정하고, JVM 옵션을 최적화하며, 어플리케이션의 초기화 로직을 개선함으로써 이러한 문제를 해결할 수 있습니다. 이를 통해 Spring Boot 어플리케이션의 부팅 성능을 향상시키고, 안정적인 운영 환경을 구축할 수 있습니다.

## 참고 문헌

1. [Kubernetes Documentation - Managing Resources for Containers](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/)
2. [Oracle Documentation - Java HotSpot VM Options](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/java.html)
3. [Spring Boot Documentation](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/)
4. [JVM Performance Optimization - JIT Compilation](https://www.oracle.com/technical-resources/articles/javase/jit.html)
