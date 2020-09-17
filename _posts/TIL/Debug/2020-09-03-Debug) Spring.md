---
title: "Debug) Spring"
header:
  overlay_image: /assets/write.jpg
  overlay_filter: 0.2
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
categories:
  - Debug
---

프로젝트에서 만난 문제를 해결한 이야기

# Spring
- 가끔 프로젝하다가 `git rm --cached -r`을 하다가 프로젝트가 사라지는(?) 현상이 발생하는데 신기한점은 파일은 그대로 있다.
- 이때 그냥 커밋, 푸시를 하고 프로젝트를 지운 뒤 다시 설치한다.

![image](https://user-images.githubusercontent.com/55608425/92064655-df1a8f00-edd8-11ea-8cfa-717f4e81decd.png)


## Exceeded limit on max bytes to buffer webflux 에러 발생 시
- 문제상황 : webflux를 이용해 테스트 하던 중 응답받는 값이 buffer byte를 넘었다는 에러
- 원인 : max-in-memory-size가 작기 때문.
- 해결 : application.yml에서 위 옵션을 늘린다. 아래처럼 [참고](https://stackoverflow.com/questions/59735951/databufferlimitexception-exceeded-limit-on-max-bytes-to-buffer-webflux-eroor)

```java
spring:
  codec:
    max-in-memory-size: 10MB
```

# sql파일이 스프링 시작할 때 주입이 안될 때
- 문제상황 : sql파일이 스프링 시작할 때 주입이 안되서 테이블과 LOAD INFILE이 실행이 안된다.
- 원인 : spring 옵션을 제대로 모르고 사용하기 때문이다.

## 해결책
- `initialization-mode: always` 와 `ddl-auto: update`로 바꾼다.
- schema.sql에 처음 create나 update 옵션으로 jpa가 만들어주는 쿼리문을 복사해서 넣는다.
- alter table 관련해서는 필요없고 drop table과 create table만 넣는다. drop table 순서를 자식 테이블을 먼저 넣는다.(외래키가 있는 테이블)  
- 한번 생성되면 ddl-auto : vaildate로 변경해준다.

```java
drop table if exists accommodation;
drop table if exists account;
drop table if exists picture;
drop table if exists reservation;

create table accommodation (
       accommodation_id bigint not null auto_increment,
        available_guest_count integer not null,
        current_price integer not null,
        description longtext,
        discount_price integer not null,
        hotel_rating double precision not null,
        latitude double precision,
        location longtext,
        longitude double precision,
        accommodation_name varchar(255),
        previous_price integer not null,
        street longtext,
        primary key (accommodation_id)
);

create table account (
       dtype varchar(31) not null,
        account_id bigint not null auto_increment,
        account_email varchar(255) unique,
        primary key (account_id)
);

create table picture (
       picture_id bigint not null auto_increment,
        picture_url longtext,
        accommodation_id bigint,
        primary key (picture_id)
);

create table reservation (
       reservation_id bigint not null auto_increment,
        client_count integer,
        end_date date,
        price integer,
        start_date date,
        accommodation_id bigint,
        account_id bigint,
        primary key (reservation_id)
);



```

- [참고](https://stackoverflow.com/questions/45082574/spring-boot-doesnt-load-data-to-initialize-database-using-data-sql)
- [ddl-auto : update 추천](https://galid1.tistory.com/610)

# Spring

## Swagger가 안 뜰 때
- 원인 : 인터셉터가 적용되서 토큰이 없는 경우라 화면이 보이지 않음.
- 해결 : 인터셉터 excludePatterns에 swagger를 추가한다. 우선 전부 추가함

```java
@Override
    public void addInterceptors(InterceptorRegistry registry) {

        String[] excludePathPatterns = new String[]{"/account/**", "/**"};

        registry.addInterceptor(loginInterceptor())
                .addPathPatterns(ALL_PATH)
                .excludePathPatterns(excludePathPatterns);
    }
```
