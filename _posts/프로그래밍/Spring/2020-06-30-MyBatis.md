## MyBatis

> 인출 학습법을 참고해 작성한 글입니다. 정보 나열보단 질문에 답하는 형식으로 작성했습니다.

### MyBatis에 대해 간략히 설명하라

MyBatis는 데이터 처리용 프레임워크이다. 기존에 JDBC API를 이용할 때는 문자열 조합으로 쿼리문을 작성했지만 MyBatis를 이용하면 XML로 설정할 수 있다. 이 때문에 MyBatis를 쿼리 매퍼라고도 부른다. 

### MyBatis 사용 이유는? 

JDBC 구현체는 Spring Data JDBC, Spring Data JPA 등 다양하지만 이번 프로젝트에선 MyBatis를 사용했다. 이유는 한번도 사용해보지 못한 기술이고, SQL문 작성 능력을 키우고 싶기 때문이다.  

가장 사용하고 싶은 프레임워크는 JPA지만 러닝커브가 있다고 들어서 먼저 MyBatis를 적용하고 다음 프로젝트에는 JPA를 적용할 예정이다. 

### 기술 스택

- SpringBoot 2.3.0
- MySql 5.7
- Gradle 6.3 

### 설정 

스프링에서 MyBatis를 사용하기 위해선 MyBatis.jar나 MyBatis-Spring.jar를 사용해야 하지만 스프링부트에서는 아래 starter 의존성을 통해 편리하게 사용할 수 있다.  

- 의존성 추가 

    ```java
    // https://mvnrepository.com/artifact/org.mybatis.spring.boot/mybatis-spring-boot-starter
    compile group: 'org.mybatis.spring.boot', name: 'mybatis-spring-boot-starter', version: '1.3.2'
    
    ```

### SqlSessionTemplate 설정

SqlSession은 마이바티스에서 쿼리 매핑 구문을 실행하기 위한 객체이다. 스프링에서는 SqlSesstionTemplate으로 사용한다. 그 이유는 예외처리를 스프링이 직접할 수 있기 때문이다.

- SqlSessionTemplate 설정 

    - @MapperScan의 basePacages는 레포지토리가 만들어지는 패키지를 설정한다. 

    ```java
    @Configuration
    @MapperScan(basePackages = {"com.codesquad.demo.repository"})
    public class MyBatisConfig {
    
        @Bean
        public SqlSessionTemplate sqlSessionTemplate(SqlSessionFactory sqlSessionFactory) {
            return new SqlSessionTemplate(sqlSessionFactory);
        }
    
        @Bean
        public SqlSessionFactory sqlSessionFactory(DataSource dataSource) throws Exception {
            SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
            sqlSessionFactoryBean.setDataSource(dataSource);
    //        sqlSessionFactoryBean.setConfigLocation((new PathMatchingResourcePatternResolver().
    //                getResource("classpath:mybatis-config.xml")));
            sqlSessionFactoryBean.setMapperLocations(new PathMatchingResourcePatternResolver().
                    getResources("classpath:sample/mapper/*.xml"));
            return sqlSessionFactoryBean.getObject();
        }
    }
    ```

- Application.properties에 데이터베이스 설정 추가 

    ```java
    spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
    spring.datasource.url=jdbc:mysql://127.0.0.1:3306/test?serverTimezone=Asia/Seoul&characterEncoding=UTF-8&useLegacyDatetimeCode=false
    spring.datasource.username=sunny
    spring.datasource.password=1234
    spring.datasource.platform=mysql
    spring.datasource.initialization-mode=always
    spring.datasource.schema=classpath:schema-mysql.sql
    spring.datasource.data=classpath:data-mysql.sql
    spring.datasource.separator=;
    spring.datasource.sql-script-encoding=UTF-8
    ```

- schema-mysql.sql을 통해 테이블 생성 문법을 작성한다. 

    ```mysql
    create table accommodation (
        id INT not null auto_increment primary key,
        hotel_name TEXT,
        description TEXT,
        location TEXT,
        street TEXT,
        latitude DOUBLE ,
        longitude DOUBLE,
        available_guest INT,
        current_price INT,
        previous_price INT,
        discount_price INT,
        hotel_rating DOUBLE ,
        airbnb INT references airbnb(id),
        airbnb_key INT
    );
    ```

    

- resources 패키지에 sample/mapper 디렉토리를 만들고 userMapper.xml을 추가한다. 

    - SqlSesstionTemplate를 설정할 때 mapperLocations를 sample.mapper/userMapper.xml로 설정했기 때문에 스프링은 이 경로에서 찾는다. 

    - ![image-20200604190558322](/home/hyunjun/사진/Mybatis/image-20200604190558322.png)

    - userMapper.xml 파일 
    
        - 이 파일에 직접 쿼리를 작성한다. 기존 JDBC API와 차이점은 쿼리문을 XML로 따로 관리한다는 점이다. 이는 개발자에게 편리함을 제공한다. 
    
        ```java
        <?xml version="1.0" encoding="UTF-8" ?>
        <!DOCTYPE mapper
                PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
                "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
        
        <mapper namespace="sample.mapper.accommodationMapper">
        
            <resultMap id="accommodationResultMap" type="com.codesquad.demo.domain.Accommodation">
                <result property="id" column="id"/>
                <result property="hotelName" column="hotel_name"/>
                <result property="description" column="description"/>
                <result property="location" column="location"/>
                <result property="street" column="street"/>
                <result property="latitude" column="latitude"/>
                <result property="longitude" column="longitude"/>
                <result property="availableGuest" column="available_guest"/>
                <result property="current_price" column="current_price"/>
                <result property="previous_price" column="previous_price"/>
                <result property="discount_price" column="discount_price"/>
                <result property="hotelRating" column="hotel_rating"/>
                <collection property="pictures" javaType="java.util.ArrayList" resultMap="picturesResultMap"/>
                <collection property="reservations" javaType="java.util.ArrayList" resultMap="accommodationReservationResultMap"/>
            </resultMap>
        
        <resultMap id="picturesResultMap" type="com.codesquad.demo.domain.Picture">
                <result property="id" column="id"/>
            <result property="url" column="url"/>
            </resultMap>
        
            <resultMap id="accommodationReservationResultMap" type="com.codesquad.demo.domain.AccommodationReservation">
                <result property="id" column="id"/>
                <result property="startDate" column="start_date"/>
                <result property="endDate" column="end_date"/>
                <result property="people" column="people"/>
                <result property="totalPrice" column="total_price"/>
            </resultMap>
        
            <select id="initAccommodations" resultMap="accommodationResultMap">
            <![CDATA[
                select a.id, hotel_name, description, location, street, latitude, longitude, available_guest, current_price,
                       previous_price, discount_price, hotel_rating, p.id, url
                from accommodation a
                         left join picture p
                                   on a.id = p.accommodation limit 30;
            ]]>
            </select>
        ```
    
    - UserRepository에서 Mapper에 등록한 sql 메서드 사용하기
    
        ```java
        @Repository
        public class AccommodationRepository {
        
            private static final String MAPPER_NAME_SPACE = "sample.mapper.accommodationMapper.";
        
            private final SqlSessionTemplate sqlSessionTemplate;
        
            public AccommodationRepository(SqlSessionTemplate sqlSessionTemplate) {
                this.sqlSessionTemplate = sqlSessionTemplate;
            }
        
            public List<Accommodation> getInitAccommodations() {
                return sqlSessionTemplate.selectList(MAPPER_NAME_SPACE + "initAccommodations");
            }
        
            public List<Accommodation> getFiltering(FilterRequestDto filterRequestDto) {
                String location = filterRequestDto.getLocation();
                Integer people = filterRequestDto.getPeople();
        
                LocationPriceRequestDto locationPriceRequestDto = LocationPriceRequestDto.builder()
                        .location(location)
                        .people(people)
                        .build();
        
                return sqlSessionTemplate.selectList(MAPPER_NAME_SPACE + "accommodationsByFiltering", locationPriceRequestDto);
            }
        ```
    
        

### 다양한 에러 

- class path resource [mybatis-config.xml] cannot be opened because it does not exist

    - SqlSessionTemplate 설정할 때 주석처리된 부분이다. 책에서는 설정만 하고 파일은 없어도 된다고 했는데 직접 구동하니 없다고 에러가 발생했다. 
    - 주석 처리하니까 정상적으로 설정이 완료했다. 

- 쿼리문이 작성된 mapper.xml의 경로를 sqlSessionfactroyBean가 못찾는 상황.

    - 디렉토리 경로를 못찾는 문제였음. 패키지 간 주소는 "."이 아니라 "/"이렇게 표현해야 리소스에 등록된다. 

        ```java
        sqlSessionFactoryBean.setMapperLocations(new PathMatchingResourcePatternResolver().
                        getResources("classpath:sample/mapper/*.xml"));
        ```

    - Config에서 mapper.xml을 등록할 땐 패키지 경로는 /로 구분되니까 "/"로 적고 mapper를 사용할 땐 에선 "."으로 적는다.

        - ex)  private static final String MAPPER_NAME_SPACE = "sample.mapper.accommodationMapper."

- Mapped Statements collection does not contain value for sample.mapper.userMapper.selectUserInfoAll

    - 이런 에러가 발생할 땐 5가지 이유가 있는데 [위 글](https://yunyoung1819.tistory.com/25)을 참고하자.
    
- Cannot find class: accommodationResultMap
    
    - Mapper.xml의 select 쿼리의 resultType을 resultMap으로 변경하기.

### airbnb 프로젝트에 Mybatis 적용하기 

- accommodation은 객체에선 List를 가지고 있고 DB의 column엔 부모 테이블의 FK가 있다. mapper에서는 List의 resultMap을 어떻게 지정하지? 
    - 우선 accommodation의 List는 제외해보자. -> 제외하니 빈 List가 들어간다. 
- 1 : N 관계 매핑은 2중 ResultMap을 통해 구현한다. 

#### 에러 

