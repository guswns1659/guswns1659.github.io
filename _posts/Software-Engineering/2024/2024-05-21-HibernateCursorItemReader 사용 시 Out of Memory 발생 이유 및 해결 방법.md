---
title: HibernateCursorItemReader 사용 시 Out of Memory 발생 이유 및 해결 방법 
date: 2024-05-21 11:49:08 +09:00
categories: [Software-Engineering]
tags: engineering
---

### HibernateCursorItemReader 사용 시 Out of Memory 발생 이유 및 해결 방법

Spring Batch에서 대용량 데이터를 처리할 때, `HibernateCursorItemReader`는 메모리 효율성을 높이기 위한 강력한 도구입니다. 그러나 올바르게 설정되지 않으면 `OutOfMemoryError`가 발생할 수 있습니다. 이번 글에서는 이 오류가 발생하는 원인과 해결 방법에 대해 자세히 설명하겠습니다.

#### Out of Memory 발생 이유

`HibernateCursorItemReader`를 사용할 때 Out of Memory 오류가 발생하는 주된 이유는 대용량 데이터를 메모리에 로드하기 때문입니다. 기본적으로, `HibernateCursorItemReader`는 `Query.scroll()` 메서드를 사용하여 결과를 가져오지만, 이를 사용할 때 적절한 설정이 없으면 메모리에 모든 결과를 로드할 수 있습니다.

`useStatelessSession(true)`를 설정하여 `StatelessSession`을 사용하더라도, JDBC 드라이버가 커서 기반으로 데이터를 가져오지 않으면 결국 모든 데이터를 메모리에 로드하게 되어 메모리 부족 문제가 발생할 수 있습니다.

#### 해결 방법

Out of Memory 오류를 방지하기 위해 다음과 같은 방법을 사용할 수 있습니다:

1. **JDBC 드라이버의 커서 사용 설정**:
   JDBC 드라이버가 커서 기반으로 데이터를 가져올 수 있도록 설정해야 합니다. MySQL의 경우 `useCursorFetch` 속성을 활성화할 수 있습니다. 예를 들어, HikariCP를 사용하는 경우 다음과 같이 설정할 수 있습니다:

    ```java
    @Bean
    public DataSource dataSource() {
        HikariDataSource dataSource = new HikariDataSource();
        dataSource.setDriverClassName("com.mysql.cj.jdbc.Driver");
        dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/mydb");
        dataSource.setUsername("username");
        dataSource.setPassword("password");
        dataSource.addDataSourceProperty("useCursorFetch", "true");
        return dataSource;
    }
    ```

2. **Fetch Size 조정**:
   `fetchSize`를 설정하여 한 번에 가져오는 데이터의 양을 조절할 수 있습니다. 너무 작게 설정하면 성능이 떨어질 수 있고, 너무 크게 설정하면 다시 메모리 문제가 발생할 수 있습니다.

    ```java
    HibernateCursorItemReader<MyEntity> reader = new HibernateCursorItemReader<>();
    reader.setSessionFactory(sessionFactory);
    reader.setQueryString("FROM MyEntity");
    reader.setFetchSize(100);  // 적절한 fetchSize 설정
    reader.setUseStatelessSession(true);  // 메모리 효율성을 위해 StatelessSession 사용
    ```

3. **StatelessSession 사용**:
   `StatelessSession`은 캐시를 사용하지 않기 때문에 메모리 사용을 줄일 수 있습니다. `setUseStatelessSession(true)`를 설정하여 `StatelessSession`을 사용하도록 합니다.

#### 예제 코드

다음은 `HibernateCursorItemReader`를 적절하게 설정하여 대용량 데이터를 메모리 효율적으로 처리하는 예제입니다:

```java
@Bean
public HibernateCursorItemReader<MyEntity> reader() {
    HibernateCursorItemReader<MyEntity> reader = new HibernateCursorItemReader<>();
    reader.setSessionFactory(sessionFactory);
    reader.setQueryString("FROM MyEntity");
    reader.setFetchSize(100);
    reader.setUseStatelessSession(true);
    return reader;
}

@Bean
public DataSource dataSource() {
    HikariDataSource dataSource = new HikariDataSource();
    dataSource.setDriverClassName("com.mysql.cj.jdbc.Driver");
    dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/mydb");
    dataSource.setUsername("username");
    dataSource.setPassword("password");
    dataSource.addDataSourceProperty("useCursorFetch", "true");
    return dataSource;
}
```

이 설정을 통해 데이터베이스 커서를 사용하여 한 번에 하나의 행씩 데이터를 가져오므로 메모리 사용량을 크게 줄일 수 있습니다.

#### 결론

`HibernateCursorItemReader`를 사용할 때 Out of Memory 오류를 방지하려면 JDBC 드라이버의 커서 사용을 활성화하고, `fetchSize`를 적절하게 설정하며, `StatelessSession`을 사용하는 것이 중요합니다. 이를 통해 대용량 데이터를 처리할 때 메모리 효율성을 극대화할 수 있습니다.

---

**참고 자료:**

1. [Spring Batch Documentation](https://docs.spring.io/spring-batch/docs/current/reference/html/index.html)
2. [Hibernate Documentation](https://docs.jboss.org/hibernate/orm/5.5/userguide/html_single/Hibernate_User_Guide.html#batch)
3. [Spring HibernateCursorItemReader Examples](https://www.demo2s.com/java/spring-hibernatecursoritemreader-tutorial-with-examples.html)
4. [GitHub Discussion on HibernateCursorItemReader Memory Issues](https://github.com/spring-projects/spring-batch/issues/2678)
