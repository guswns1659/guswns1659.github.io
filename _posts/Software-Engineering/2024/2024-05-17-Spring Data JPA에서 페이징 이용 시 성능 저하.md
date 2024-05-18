---
title: Spring Data JPA 페이징 시 OFFSET으로 인한 성능 저하와 해결법
date: 2024-05-17 11:49:08 +09:00
categories: [Software-Engineering]
tags: engineering
---

Spring Data JPA를 사용할 때 페이징은 매우 유용한 기술입니다. 
하지만 대규모 데이터셋에서는 쿼리 내 OFFSET으로 인해 성능 저하를 유발할 수 있습니다. 
OFFSET으로 인한 성능 저하의 원인과 이를 해결하는 방법에 대해 알아보겠습니다.

## 문제 발생 원인

페이징을 구현하기 위해 Spring Data JPA는 보통 `findAll(Pageable pageable)` 메서드를 사용합니다. 이 메서드는 내부적으로 OFFSET과 LIMIT을 사용하는 SQL 쿼리를 생성합니다. 예를 들어, 다음과 같은 쿼리가 생성될 수 있습니다:

```sql
SELECT * FROM users ORDER BY id LIMIT 10 OFFSET 9990;
```

이 쿼리는 10,000개의 레코드 중 마지막 10개를 가져오기 위해 OFFSET 9990을 사용합니다. 문제는 데이터베이스가 OFFSET에 도달하기 위해 9990개의 레코드를 모두 읽어야 한다는 점입니다. 이는 큰 데이터셋에서 성능 저하를 일으킬 수 있습니다.

## 해결 방법
Keyset Pagination은 OFFSET 대신 특정 키를 사용하여 다음 페이지를 가져오는 방식입니다. 이 방법은 성능 저하를 줄일 수 있습니다. 예를 들어, id 필드를 사용하여 페이징을 구현할 수 있습니다.

```kotlin
interface UserRepository : JpaRepository<User, Long> {
    @Query("SELECT u FROM User u WHERE u.id > :lastId ORDER BY u.id ASC")
    fun findNextPage(@Param("lastId") lastId: Long, pageable: Pageable): List<User>
}
```

## Keyset Pagination 주의사항
1. 인덱스 필요: Keyset Pagination을 사용하려면 페이징에 사용되는 필드에 인덱스가 있어야 합니다. 예를 들어, id 필드에 인덱스가 있어야 성능이 최적화됩니다. 인덱스가 없으면 성능 저하가 발생할 수 있습니다.
2. 단일 컬럼 기준: Keyset Pagination은 단일 컬럼을 기준으로 페이징을 수행하기 때문에, 다중 컬럼을 기준으로 페이징을 해야 하는 경우에는 적합하지 않을 수 있습니다.
3. 데이터 일관성: 페이징 기준이 되는 컬럼의 데이터가 자주 변경될 경우, 페이징 결과가 일관되지 않을 수 있습니다. 예를 들어, id가 아니라 createdDate와 같은 값이 자주 변경되는 필드를 기준으로 페이징을 할 경우 문제가 발생할 수 있습니다.

# 결론
Spring Data JPA에서 페이징을 구현할 때 대규모 데이터셋에서는 성능 저하가 발생할 수 있습니다. 
이를 해결하기 위해 Keyset Pagination을 사용할 수 있으며, 이 방법은 대규모 데이터셋에서도 성능 저하 없이 효율적으로 페이징을 처리할 수 있습니다. 
다만, Keyset Pagination을 사용할 때는 인덱스를 적절히 설정하고 데이터 일관성 문제를 고려해야 합니다.




