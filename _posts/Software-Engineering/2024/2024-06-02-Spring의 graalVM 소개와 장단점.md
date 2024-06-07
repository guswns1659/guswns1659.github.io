---
title: Spring의 graalVM 소개와 장단점
date: 2024-06-02 11:49:08 +09:00
categories: [Software-Engineering]
tags: engineering
---

Spring6가 릴리즈될 때 [GraalVM](https://docs.spring.io/spring-boot/reference/native-image/introducing-graalvm-native-images.html)에 대한 관심이 높았던 걸로 기억합니다. 
지금은 살짝 시들해진 것 같고, 생각보다 현업에서 자주 사용되지 않는 것 같습니다. 시간이 지나 한번 개념과 장단점을 적어봤습니다.  

### GraalVM을 사용한 Spring 프레임워크 네이티브 이미지 빌드

#### GraalVM 소개

**GraalVM**은 고성능의 다중 언어 런타임으로, Java뿐만 아니라 JavaScript, Ruby, Python, R 등 다양한 언어를 지원합니다. GraalVM의 주요 특징 중 하나는 **네이티브 이미지(Native Image)** 기능으로, 이는 Java 애플리케이션을 기계어 수준으로 컴파일하여 네이티브 실행 파일로 변환할 수 있게 해줍니다. 이를 통해 메모리 사용량을 줄이고 애플리케이션의 시작 시간을 크게 향상시킬 수 있습니다.

#### GraalVM과 Spring 프레임워크

Spring 프레임워크는 GraalVM의 네이티브 이미지 기능을 지원하여, Spring 애플리케이션을 네이티브 실행 파일로 컴파일할 수 있습니다. 이 과정은 Spring Native 프로젝트를 통해 가능하며, Spring Boot와도 통합되어 있습니다.

##### 주요 특징 및 구현 방법

1. **Spring Native 프로젝트**:
  - **Spring Native**는 GraalVM을 활용하여 Spring 애플리케이션을 네이티브 이미지로 변환하는 도구를 제공합니다. 이를 통해 메모리 사용량 감소 및 빠른 시작 속도를 실현할 수 있습니다.
  - **지원**: Spring Boot 2.4.0 이상부터 GraalVM Native Image를 공식적으로 지원합니다. 이는 Spring Boot 애플리케이션을 네이티브 이미지로 쉽게 빌드할 수 있게 해줍니다.

2. **빌드 및 실행**:
  - **Maven/Gradle 플러그인**: 네이티브 이미지를 빌드하기 위해 Maven과 Gradle 플러그인을 사용할 수 있습니다. 이를 통해 `mvn clean package -Pnative` 또는 `gradle nativeBuild` 명령어로 네이티브 이미지를 생성할 수 있습니다.
  - **Buildpacks**: Spring Boot는 Buildpacks를 통해 네이티브 이미지를 생성하는 기능도 제공합니다. 이는 OCI(OCI: Open Container Initiative) 이미지 형식으로 애플리케이션을 패키징하여 컨테이너 환경에서 쉽게 배포할 수 있게 합니다 .

3. **제약 사항**:
  - **동적 요소 제한**: GraalVM 네이티브 이미지 생성 시 정적 분석을 통해 애플리케이션 코드를 최적화하므로, 런타임에 동적으로 생성되는 요소들(예: 리플렉션, 동적 프록시)은 제한됩니다. 이를 해결하기 위해 GraalVM 구성 파일을 사용하여 이러한 동적 요소들을 미리 정의해야 합니다.
  - **클래스패스 고정**: 네이티브 이미지로 빌드된 애플리케이션의 클래스패스는 빌드 시점에 고정되며, 런타임에 변경될 수 없습니다. 이는 네이티브 이미지의 특징 중 하나로, 빠른 시작 시간과 메모리 최적화를 가능하게 합니다  .

#### 운영체제 특화 네이티브 이미지

네이티브 이미지로의 변환은 바이트코드나 소스 코드를 기계어로 변환하여 직접 실행할 수 있게 하는 것을 의미합니다. 이는 각 운영체제(OS)와 하드웨어 아키텍처에 맞춰 컴파일되기 때문에, 해당 환경에서 최적의 성능을 발휘할 수 있습니다.

- **운영체제 특화**:
  - 네이티브 이미지는 특정 운영체제와 하드웨어 아키텍처에 맞게 컴파일됩니다. 예를 들어, Linux 환경에서 생성된 네이티브 이미지는 Linux 운영체제에서 실행될 수 있으며, Windows 환경에서 생성된 네이티브 이미지는 Windows 운영체제에서 실행됩니다.
  - 이는 각 운영체제의 시스템 호출, 라이브러리, 하드웨어 명령어 집합 등을 직접 활용할 수 있기 때문에, JVM에서 실행하는 것보다 더 빠르고 효율적입니다.

- **빌드 과정**:
  - 네이티브 이미지를 생성하는 과정에서는 해당 운영체제의 빌드 도구와 라이브러리가 사용됩니다. 예를 들어, GraalVM에서 네이티브 이미지를 생성할 때 Linux에서는 `glibc`와 같은 라이브러리가 필요하고, Windows에서는 Visual Studio의 빌드 도구가 필요할 수 있습니다.
  - 네이티브 이미지 생성 시 GraalVM은 애플리케이션 코드를 분석하고, 필요한 모든 의존성을 포함하여 최적의 실행 파일을 생성합니다 .

#### 결론
GraalVM을 사용하여 Spring 프레임워크 애플리케이션을 네이티브 이미지로 변환함으로써, 메모리 사용량을 줄이고 시작 속도를 개선할 수 있습니다. 이를 위해 Spring Native 프로젝트와 Spring Boot의 지원을 활용할 수 있습니다. 다만, 네이티브 이미지 생성 시 몇 가지 제약 사항이 있으므로, 이를 고려하여 애플리케이션을 설계해야 합니다.

### 참고 자료
- [Spring Boot GraalVM Native Image Support](https://docs.spring.io/spring-boot/docs/current/reference/html/native-image.html)
- [Spring Native for GraalVM](https://spring.io/blog/2020/11/23/spring-native-for-graalvm-0-8-3-available-now)
- [Baeldung: Native Images with Spring Boot and GraalVM](https://www.baeldung.com/spring-native-graalvm)
- [GraalVM 공식 문서](https://www.graalvm.org/reference-manual/native-image/)


