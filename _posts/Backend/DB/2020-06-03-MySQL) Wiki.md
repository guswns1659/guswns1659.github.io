---
header:
  teaser: /assets/database2.jpg
  overlay_image: /assets/database2.jpg
  overlay_filter: 0.2
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
categories:
  - DB
---

Database는 애플리케이션의 필수 요소입니다. 

# MySQL 설치 
- 버전 확인 
- 설치 : sudo apt-get install mysql-server-5.7
- root 비밀번호 설정 : 
```mysql 
dan@dan_ubuntu:~$ sudo mysql
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 4
Server version: 5.7.29-0ubuntu0.18.04.1 (Ubuntu)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> update mysql.user set plugin='mysql_native_password' where user='root';
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> update mysql.user set authentication_string=PASSWORD('root') where user='root';
Query OK, 1 row affected, 1 warning (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 1

mysql> flush privileges;
Query OK, 0 rows affected (0.01 sec)

mysql> quit;
Bye
```
## characterset 설정 
[[참고1 : https://webdir.tistory.com/217](https://webdir.tistory.com/217)]
[[참고2 : https://zzznara2.tistory.com/765](https://zzznara2.tistory.com/765)]

- 아래 설정을 추가해야 하는데, 이걸 mysql.cnf 파일 하나에 추가해도 된다고 하기도 하고, mysql.conf.d 디렉토리 안으로 들어가 각 설정별 파일을 만들어야 된다고도 한다. 나는 각 설정 파일을 만들었다. 
- `sudo vim my.cnf` 와 같이 sudo를 붙여야 한다. 안 붙이면 readOnly 형식만 된다. 

```shell script
sudo vim /etc/mysql/my.cnf
```

```mysql
[client] 
default-character-set = utf8 
[mysqld] 
character-set-client-handshake=FALSE 
init_connect="SET collation_connection = utf8_general_ci" 
init_connect="SET NAMES utf8" 
character-set-server = utf8 
collation-server = utf8_general_ci 

[mysqldump] 
default-character-set = utf8 

[mysql] 
default-character-set = utf8  
```

- 설정 완료하면 `sudo service mysql restart` 해줘야 한다. 
- 설정 완료하면 mysql들어가서 확인한다. 

```mysql
show variables like '%char%';

+--------------------------+----------------------------+
| Variable_name            | Value                      |
+--------------------------+----------------------------+
| character_set_client     | utf8                       |
| character_set_connection | utf8                       |
| character_set_database   | latin1                     |
| character_set_filesystem | binary                     |
| character_set_results    | utf8                       |
| character_set_server     | latin1                     |
| character_set_system     | utf8                       |
| character_sets_dir       | /usr/share/mysql/charsets/ |
+--------------------------+----------------------------+
```

- 추가적으로 max_allowed_packet = 16M을 256M으로 수정하면 쿼리양이 많을 때 도움이 된다고 한다. 

```shell script
sudo vim /etc/mysql/my.cnf
sudo service mysql restart
```

## root외 사용자 추가하기. 
- user 만들기 

```mysql
CREATE DATABASE mydb CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
create user 'sunny'@'%' identified by '비밀번호';

```

- 권한 주기 

```mysql
GRANT ALL ON mydb.* TO 'sunny'@'%';
# 모든권한 갖기 
grant all privileges on *.* to 'sunny'@'%';
```

# ERD
요구사항 파악할 때 ERD 다이어그램을 작성하는 경우가 많다. 

## 미슈 에러
sevice mysql start 접속 후 mysql -u root로 접속한다. 

## Join 
- cross Join : 각 모든 행의 조합으로 보여준다. 중복되는 컬럼이 등장한다. 

```mysql
SELECT * FROM COURSE CROSS JOIN PROF;
```

- inner join : 교집합의 의미. 각 테이블 사이의 공통적인 컬럼만 남긴다. 

```mysql
SELECT C.CNAME, P.NAME FROM COURSE C JOIN PROF P ON C.PROFID = P.PID;
```

- DB에서 null이란 값이 없다의 의미가 아니라 모른다는 의미.
- 외래키 제약 조건 : primary key가 없는 값은 넣을 수 없고, null값은 넣을 수 있다. 

## 정규화 
정규화 : 이상 현상을 막기 위해 테이블을 분리하는 작업을 정규화라고 한다. 

## 모델링 
- 데이터 모델링할 때는 필요한 최소한의 정보만 가져와야 한다. <br>
[lucas ERD 참고 : https://lucas.codesquad.kr/course/%EB%A7%88%EC%8A%A4%ED%84%B0%EC%A6%88-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8/%ED%95%99%EC%8A%B5%EC%9E%90%EB%A3%8C-BE/ERD](https://lucas.codesquad.kr/course/%EB%A7%88%EC%8A%A4%ED%84%B0%EC%A6%88-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8/%ED%95%99%EC%8A%B5%EC%9E%90%EB%A3%8C-BE/ERD)
### 개념적 설계
- 동그라미 2개 : 나중에 컬럼이 나눠질 수 있는 경우 
- 선이 1개 : null 값이 가능하다. 값을 모른다.  
- 선이 2개 : 반드시 값이 존재한다. 
- 다이아몬드 선 2개 : 식별관계라 생각! 

### 논리적 설계 
- N : 1 -> 1로 매칭되는 외래키를 N 테이블에 붙인다. 
- N : M -> 외래키를 붙이기가 애매하기에 테이블을 하나 만든다.  

## 쿼리 최적화 

- 프로파일링을 언제 하는가?
    - 성능(1건 당 응답 속도)이 느려졌을 때 원인을 파악하기 위해 한다. 
    - 1건 당 오래 걸리는 메서드를 튜닝하는 것이 아니라 전체 시간이 오래 걸리는 메서드를 튜닝해야 한다. 

- [암달의 법칙]([https://ko.wikipedia.org/wiki/%EC%95%94%EB%8B%AC%EC%9D%98_%EB%B2%95%EC%B9%99](https://ko.wikipedia.org/wiki/암달의_법칙))이란
    - 시스템의 일부를 개선했을 때 전체 시스템의 성능이 얼마나 향상되는지 계산하는 수식 
    - 하지만 아무리 코어를 늘려도 성능이 20배 이상 향상되지 못한다.
- 쿼리 튜닝은 안하는 게 좋다. 그럼 그 전에 성능을 개선할 수 있는 사항은?
    - 커넥션 수 조정 
    - 하드웨어 수직 확장 (기존 인프라의 성능 향상)
    - DB 수평 확장은? (어렵다. 샤딩이나 리드 레플리카를 만들어야 한다.)
- 클러스터드 인덱스
    - MySQL의 저장구조이고 p.k에 대해서 인덱스를 생성하고 p.k의 인덱스(B-tree 구조)를 통해 데이터에 접근하는 방식.
- MySQL의 explain 명령은 DB의 실행계획을 알려준다. 
    - id는 하나의 실행 단위를 말한다.  
    
### MySQL에 on delete cascade 적용하기  (외래키에서만 가능?)

- optional인 테이블 속 참조키에 on delete cascade로 지정한다. 
- foreign key가 아닌 references로 참조하는 key일 경우 on delete cascade가 먹히지 않는 것 같다? 
- accommodation이 삭제되면 참조하고 있는 List<AcommodationReservation>도 삭제되기 때문에 자바 코드로 해결하면 될 거 같다. 

## Spring과 사용할 때 유용한 MySQL 명령

### foreign Key 제약을 무시하고 테이블을 삭제하는 명령어

```mysql
SET foreign_key_checks = 0;
```