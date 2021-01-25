---
title: "Spring) Spring cloud gateway"
header:
  teaser: /assets/spring.jpg
  overlay_image: /assets/spring.jpg
  overlay_filter: 0.2
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
categories:
  - Spring
---

# Spring cloud gateway

## 디버그

- Error creating bean with name 'configurationPropertiesBeans' defined in class path resource [org/springframework/cloud/autoconfigure/ConfigurationPropertiesRebinderAutoConfiguration.class]: Post-processing of merged bean definition failed; nested exception is java.lang.IllegalStateException: Failed to introspect Class [org.springframework.cloud.context.properties.ConfigurationPropertiesBeans] from ClassLoader [sun.misc.Launcher$AppClassLoader@18b4aac2]
- 위 에러는 spring cloud와 spring boot 버전이 맞지 않아서 발생하는 문제. Hoston은 2.2.x와 2.3.x에 맞는다. [참고](https://spring.io/projects/spring-cloud)

-----
- boot 버전과 cloud.Hoxton 버전이 맞지 않아 NettyClient를 못가져오는 문제, 2.2.4와 Hoxton.SR2까지 맞았다. [스택오버플로우](https://github.com/spring-cloud/spring-cloud-gateway/issues/1532)

```java
plugins {
    id 'org.springframework.boot' version '2.2.4.RELEASE'
    id 'io.spring.dependency-management' version '1.0.11.RELEASE'
    id 'java'
}

group = 'com.jack'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '1.8'

repositories {
    mavenCentral()
}

dependencyManagement {
    imports {
        mavenBom "org.springframework.cloud:spring-cloud-dependencies:Hoxton.SR2"
    }
}
```
