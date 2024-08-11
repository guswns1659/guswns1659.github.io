---
title: Mysql에서 update문이 실행될 때 로그가 기록되는 방식
date: 2024-06-06 11:49:08 +09:00
categories: [Software-Engineering]
tags: engineering
---

Real Mysql 책을 읽으면서 InnoDB 스토리지 엔진의 아키텍쳐를 공부하고 있습니다. 실제로 쿼리문이 실행될 때 각 로그가 어떻게 기록되는지 궁금해서 찾아봤습니다.


MySQL에서 `UPDATE` 문이 실행될 때, 변경 사항이 버퍼 풀, 언두 로그 버퍼, 리두 로그 버퍼, 리두 로그 파일, 그리고 바이너리 로그에 어떻게 기록되는지 단계별로 설명하겠습니다.

### 전체 과정 요약
1. **언두 로그 생성**
2. **버퍼 풀 업데이트**
3. **리두 로그 버퍼 기록**
4. **리두 로그 파일 플러시**
5. **바이너리 로그 기록**
6. **더티 페이지 플러시**

### 1. 언두 로그 생성
언두 로그는 트랜잭션이 롤백될 때 변경 사항을 원래 상태로 되돌리기 위해 사용됩니다. 또한, 일관된 읽기를 위해 필요합니다.
- **언두 로그 기록**: `UPDATE` 문이 실행되면, 변경될 데이터의 현재 상태(변경 전 상태)가 언두 로그에 기록됩니다. 이는 이후 트랜잭션이 롤백될 경우 데이터를 복구하기 위해 사용됩니다.
- 예시: `employee_id = 1`인 레코드의 `salary`가 50000이었다면, 이 값이 언두 로그에 기록됩니다.

### 2. 버퍼 풀 업데이트
- **버퍼 풀**: InnoDB는 변경될 데이터를 메모리 내 버퍼 풀로 로드합니다. 이미 버퍼 풀에 존재하는 경우, 그 페이지가 사용됩니다.
- **데이터 변경**: `UPDATE` 문에 따라 버퍼 풀 내의 해당 데이터 페이지가 변경됩니다.
- **더티 페이지**: 변경된 페이지는 더티 페이지(Dirty Page)로 표시됩니다. 이는 해당 페이지가 메모리에서 수정되었지만, 아직 디스크에 쓰여지지 않았음을 의미합니다.

### 3. 리두 로그 버퍼 기록
- **리두 로그 버퍼**: 변경 사항이 리두 로그 버퍼에 기록됩니다. 리두 로그는 데이터 변경 작업의 지속성을 보장하는 데 사용됩니다.
- 예시: `UPDATE employees SET salary = 60000 WHERE employee_id = 1`와 같은 로그 항목이 리두 로그 버퍼에 기록됩니다.

### 4. 리두 로그 파일 플러시
- **커밋 시 리두 로그 플러시**: 트랜잭션이 커밋될 때, 리두 로그 버퍼에 있는 데이터가 디스크의 리두 로그 파일로 플러시됩니다. 이는 데이터베이스가 시스템 충돌 후에도 데이터 무결성을 유지할 수 있게 합니다.
- **주기적 플러시**: InnoDB는 설정에 따라 주기적으로 리두 로그 버퍼를 디스크로 플러시합니다.

### 5. 바이너리 로그 기록
- **역할**: 바이너리 로그는 데이터베이스의 변경 사항을 기록하며, 복제(replication) 및 데이터 복구에 사용됩니다.
- **동작**:
  - 트랜잭션이 커밋될 때, 해당 변경 사항이 바이너리 로그에 기록됩니다.
  - 바이너리 로그는 변경 사항을 순차적으로 기록하여, 데이터베이스 상태를 복구하거나 복제할 때 사용됩니다.

### 6. 더티 페이지 플러시
- **체크포인트**: InnoDB는 주기적으로 체크포인트를 설정합니다. 체크포인트 시점에 더티 페이지가 디스크로 플러시됩니다.
- **백그라운드 플러시**: InnoDB의 백그라운드 쓰레드가 주기적으로 더티 페이지를 디스크로 플러시합니다.
- **버퍼 풀 공간 확보**: 버퍼 풀이 가득 차면, 더티 페이지를 디스크로 플러시하여 공간을 확보합니다.

### 동작 순서 예시
1. **UPDATE 쿼리 실행**:
   ```sql
   UPDATE employees SET salary = 60000 WHERE employee_id = 1;
   ```
  - `employee_id = 1`인 레코드의 기존 `salary` 값(예: 50000)이 언두 로그 버퍼에 기록됩니다.
  - 데이터 페이지가 버퍼 풀에 로드되어 `salary`가 60000으로 변경됩니다.
  - 이 변경 사항이 리두 로그 버퍼에 기록됩니다.

2. **트랜잭션 커밋**:
   ```sql
   COMMIT;
   ```
  - 커밋 시 리두 로그 버퍼의 내용이 리두 로그 파일로 플러시됩니다.
  - 언두 로그도 주기적으로 플러시됩니다.
  - 커밋된 변경 사항이 바이너리 로그에 기록됩니다.

3. **체크포인트 또는 백그라운드 플러시**:
  - 주기적으로 또는 특정 조건에서 더티 페이지가 디스크로 플러시됩니다. 이로 인해 변경된 데이터가 데이터 파일에 영구적으로 저장됩니다.

### 참고 자료
- [MySQL 8.0 Reference Manual - Redo Log](https://dev.mysql.com/doc/refman/8.0/en/innodb-redo-log.html)
- [MySQL 8.0 Reference Manual - Undo Logs](https://dev.mysql.com/doc/refman/8.0/en/innodb-undo-logs.html)
- [MySQL 8.0 Reference Manual - Buffer Pool](https://dev.mysql.com/doc/refman/8.0/en/innodb-buffer-pool.html)
- [MySQL 8.0 Reference Manual - Binary Log](https://dev.mysql.com/doc/refman/8.0/en/binary-log.html)
- [Percona - Understanding the Differences Between InnoDB Undo Log and Redo Log](https://www.percona.com/blog/2018/03/22/understanding-the-differences-between-innodb-undo-log-and-redo-log/)
