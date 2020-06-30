## Line 이야기
- 메신저 데이터를 처리하기 위해 Redis와 Hbase를 사용한다. Redis는 인메모리라서 영속성이 없다. 그래서 Hbase를 사용한다. 하둡기반 데이터베이스가 HBase이다.

## 실습
- csv파일을 생성한다.
- mysql에 csv 파일을 넣는다.
- 인덱스를 생성한다. 조회한다.

### csv 데이터를 만드는 코드

```java
import java.time.LocalDate;
import java.util.Random;
- public class User {
    int id;
    String name;
    int rank;
    int money;
    LocalDate startDate;
    LocalDate lastLogin;
    static Random random = new Random();
    public User(String name) {
        this.id = (int)(Math.random() * 10) + 1;
        this.name = name;
        this.rank = (int)(Math.random() * 9) + 1;
        this.money = (int)(Math.random() * 99999998) + 1;
        LocalDate now = LocalDate.now();
        LocalDate start = LocalDate.of(2019, 1, 1);
        long number = random.nextInt(Math.toIntExact(now.toEpochDay()) - Math.toIntExact(start.toEpochDay()) + 1) + Math.toIntExact(start.toEpochDay());
        this.startDate = LocalDate.ofEpochDay(number);
        LocalDate login = LocalDate.of(2020, 4, 1);
        number = random.nextInt(Math.toIntExact(now.toEpochDay()) - Math.toIntExact(login.toEpochDay()) + 1) + Math.toIntExact(login.toEpochDay());
        this.lastLogin = LocalDate.ofEpochDay(number);
    }
    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", rank=" + rank +
                ", money=" + money +
                ", startDate=" + startDate +
                ", lastLogin=" + lastLogin +
                '}';
    }
}
```

- csv 파일 db에 넣는법

```
LOAD DATA LOCAL INFILE '/home/hyunjun/Downloads/csvFile.csv' INTO TABLE csvdb.user FIELDS TERMINATED BY ',' LINES TERMINATED BY '\n';
```

- ec2에서 LOAD DATA 명령어 사용하는 법.
	- 우선 ec2에 csv 파일이 존재해야 한다.
	```
	mysql -u jack -p -h airbnb-07.csjhbz8c8oyh.ap-northeast-2.rds.amazonaws.com --local-infile airbnb -e "LOAD DATA LOCAL INFILE 'test_seattle_accommodation.csv' INTO TABLE accommodation FIELDS TERMINATED BY ',' LINES TERMINATED BY '\n'"
	```
