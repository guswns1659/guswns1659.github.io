---
header:
  overlay_image: /assets/devops.jpg
  overlay_filter: 0.2
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
categories:
  - DevOps
---

DevOps는 기술뿐만 아니라 문화, 철학, 도구의 조합입니다.

## 개요
AWS 계정을 만든 후 VPC, EC2, RDS, CodeDeploy까지 셋팅하는 법을 설명하는 글

## VPC
가장 먼저 생성할 서비스. VPC의 서브넷을 통해 EC2와 RDS의 네트워크를 나눌 수 있다. RDS 접근은 EC2에서만 가능하게 만든 뒤 보안을 더 높인다.

### VPC 생성
- 이름은 해당 프로젝트-vpc로 한다.
- vpc는 0.0.0.0/16로 설정.  
- 아래 사진 참고

![vpc이름 설정](/assets/aws/vpc이름%20설정.png)

### 서브넷 생성
- public, private 2개를 만든다.
- 이름은 public-프로젝트이름, private-프로젝트이름
- IPv4 CIDR 블록은 0.0.0.0/24로 셋팅한다.
- 아래 사진 참고

![public서브넷생성](/assets/aws/public서브넷생성.png)

### 라우팅테이블 생성
외부에서 생성한 서브넷에 접근하거나 해당 서브넷에서 외부로 나가기 위해선 라우팅 주소가 있는 테이블이 있어야 한다.

- 이름은 publicRT-프로젝트이름, privateRT-프로젝트이름

![라우팅테이블생성](/assets/aws/라우팅테이블생성.png)

### 라우팅테이블에 연결할 인터넷 게이트웨이를 생성한다.
- 이름 : IGW-anglehack

![인터넷게이트웨이생성](/assets/aws/인터넷게이트웨이생성.png)

- 생성한 인터넷게이트웨이를 내가 만든 VPC에 연결한다.

![인터넷게이트웨이와vpc연결](/assets/aws/인터넷게이트웨이와vpc연결.png)

### 퍼블릭RT에 IGW 연결한다.

![퍼블릭서브넷에퍼블릭RT연결](/assets/aws/퍼블릭서브넷에퍼블릭RT연결.png)

### 퍼플릭 서브넷에 퍼플릭RT를 연결한다.

![퍼플릭서브넷에퍼플릭RT연결](/assets/aws/퍼플릭서브넷에퍼플릭RT연결.png)

### 프라이빗 서브넷에 프라이빗RT를 연결한다.

- 위 사진과 동일. 프라이빗RT 선택하고 서브넷 연결을 누른다.


## EC2
서버 역할을 할 EC2를 생성한다. 기존에 생성한 퍼플릭서브넷를 EC2에 붙여줘서 인터넷이 가능하게 만든다.

### 인스턴스 세부 정보 구성
- 리눅스 18.04 선택 후 인스턴스 세부 정보 구성은 아래와 같이.
- 생성한 퍼플릭 서브넷을 ec2에 붙인다.

![ec2_인스턴스세부정보구성](/assets/aws/ec2_인스턴스세부정보구성.png)


### 보안그룹 생성
- 이름 : <프로젝트명>SG
- 인바운드 규칙에 추가한다. 아래 사진처럼 HTTP, SSH, 사용자지정 TCP를 추가한다. 사진에는 없지만 소스도 0.0.0.0/0으로 추가한다. 해당 프로토콜로 접근할 수 있는 설정이다.

![ec2_보안그룹설정](/assets/aws/ec2_보안그룹설정.png)

### ec2 생성 후 탄력적IP 연결
ec2에 고정된 ip를 부여하기 위해선 탄력적ip를 사용해야 한다. 주의할 점은 켜져있는 ec2에서는 탄력적ip의 요금이 발생하지 않는다. 하지만 ec2를 중지한 상태라면 탄력적ip 요금이 발생할 수 있다.

![탄력적IP생성](/assets/aws/탄력적IP생성.png)

- 탄력적 ip와 ec2를 연결한다.

![탄력적ip와ec2연결](/assets/aws/탄력적ip와ec2연결.png)


### ec2 인터넷 연결 테스트
- pem키 이용해 ec2에 접근한 뒤 아래 명령어를 입력한다

```
ping ietf.org
```

- 결과로 ping이 계속 나오면 성공.


## RDS 생성
DB 역할을 할 RDS 서비스를 생성한다. RDS는 프라이빗 서브넷에 두고 보안을 강화한다.

### 주의사항
- vpc 서브넷 az가 둘다 a면(같은 영역) rds에 적용이 안됨. private 서브넷의 az를 c로 변경하기.
- DNS server, DNS hostname이 문제라면 vpc 콘솔 들어간 뒤 `작업` 버튼 클릭 후 DNS 확인 편집, DNS hostname 편집에 활성화한다.

### rds를 생성하기 전에 내가 생성한 ec2에서 rds에 연결할 수 있게 설정을 해준다.
이부분은 헷갈려서 아래 블로그글을 참고했다.
- [참고 : 안경잽이개발자 블로그](https://ndb796.tistory.com/226)

- rds에 연결할 ec2의 보안그룹명과 vpc id를 살펴본다.
  - 보안그룹명 : sg-02fbec7c5aea4082d
  - vpc : vpc-04cbf78071b7ccbc1

- DB 보안그룹을 만들고 ec2의 보안그룹을 등록한다.

![rds_보안그룹에ec2보안그룹추가](/assets/aws/rds_보안그룹에ec2보안그룹추가.png)


- DB 서브넷 그룹 생성
참고한 블로그에 따르면 서브넷 그룹에 vpc에 포함된 모든 서브넷을 추가하라고 하는데 private 서브넷만 추가하면되지 않을까? 라는 의문이 든다. 우선 2개다 추가해보자.

![rds_서브넷그룹에서vpc설정](/assets/aws/rds_서브넷그룹에서vpc설정.png)

![rds_서브넷그룹에서서브넷선택](/assets/aws/rds_서브넷그룹에서서브넷선택.png)

### 파라미터 그룹 설정
RDS에서 한글이 깨지지 않게 파라미터를 설정해야 한다.

- 파라미터 설정, 총 10개
	- time_zone : Asia/Seoul
	- character set ~ 설정 : utf8mb4
		- character_set_client
		- character_set_connection
		- character_set_database
		- character_set_filesystem
		- character_set_results
		- character_set_server
	- collation~~ :utf8mb4-general-ci
		- collation_server
		- collation_connection
	- max_connection : 150
- rds랑 연결된 서브넷에 인터넷 게이트웨이를 설정안하면 intellij에서 연결이 안된다.

### character_set이랑 collation utf8 설정하기
- 파라미터 설정 안되면 파라미터 그룹 확인해보고 재부팅해본다.
- aws에서 설정한 파라미터 그룹이 설정안되면 DB 연결한 뒤에 아래처럼 확인한 후에 변경을 해도 된다.

```
show variables like 'c%';  

alter database baseball_test
character set = 'utf8mb4'  
collate = 'utf8mb4_general_ci';  

select @@time_zone, now();
```

## RDS 설치

### 엔진옵션 선택
주의할 점은 무료로 사용하기 위해선 MySQL를 선택해야 한다. 실수로 aurora를 1일 정도 켜놨다가 5달러 정도 요금이 부과된 기억이 있다.

![rds_엔진옵션선택](/assets/aws/rds_엔진옵션선택.png)

### 마스터 사용자 설정
rds 생성할 때 유저 하나를 생성할 수 있다. 나는 개발 단계에서는 보통 마스터 사용자이름과 비밀번호를 동일하게 유지한다.

![rds_마스터사용자설정](/assets/aws/rds_마스터사용자설정.png)

### 스토리지 자동 조정
기본 스토리지를 초과하면 자동 조정해주는 옵션이다. 요금이 발생할 수 있으니 해제한다.

![rds_스토리지자동조정비활성화](/assets/aws/rds_스토리지자동조정비활성화.png)

### VPC와 서브넷 그룹 연결
기존에 생성한 VPC와 서브넷 그룹을 RDS와 연동한다.

![rds_vpc및서브넷그룹연결](/assets/aws/rds_vpc및서브넷그룹연결.png)

- 가용영역은 프라이빗서브넷 가용영역으로 선택한다. 무조건 해야하는 지는 모르겠지만.

### DB 파라미터 그룹 연결한다.
기존에 생성한 파라미터 그룹을 연결한다. 자동백업은 비활성화한다.


![rds_파라미터그룹설정](/assets/aws/rds_파라미터그룹설정.png)

- 마이너버전 자동 업그레이드, 삭제 방지 옵션은 선택하지 않는다. 요금이 나올 수 있는 옵션은 모두 차단.

### ec2에서 rds 접근 확인
rds가 제대로 생성되고 보안이 잘 유지되는 지 ec2에서 접근을 해본다.

- ec2에서 mysql을 설치 한다.

```
sudo apt-get update
sudo apt install mysql-client-core-5.7
```


- 기본 터미널과 ec2에서 아래 명령어를 입력한다.

```
mysql -u <rds마스터사용자이름> -p -h <rds 엔드포인트>
```

- 기본 터미널에서는 못 들어가지고 ec2에서만 접근이 가능해야 한다.

## ec2 Nginx 설치
ec2에 Nginx를 설치 후 리버스 프록시를 하면 서버에 대한 요청에 :8080 포트를 안 붙여도 된다.

### 자바8 설치

- 패키지 업데이트

```
sudo apt update
```

- 자바 버전 확인 -> 7이하일 경우 삭제 후 8설치
  - 아래 코드를 참고하되 자바7 이하가 설치 안되어 있는 경우에는 자바8설치하면 된다.
```
sudo apt remove java-1.7.0-openjdk
sudo apt-get install openjdk-8-jdk
sudo /usr/sbin/alternatives --config java
그리고 2를 엔터한다.
```

### Nginx 설치

```
sudo apt install nginx
sudo service nginx start
```

- 실행이 잘 되는 지 확인하기

```
ps -ef | grep nginx
```

### 스프링부트 프로젝트와 Nginx 연결하기

- 서버 설정 파일 수정하기
```
sudo vim /etc/nginx/sites-available/default
```

- 리버스 프록시 설정 추가
```
# include snippets/snakeoil.conf;

# 프론트 경로
    root /var/www/html;

    # Add index.php to the list if you are using PHP
    index index.html index.htm index.nginx-debian.html;

    server_name _;

    location / {
            # First attempt to serve request as file, then
            # as directory, then fall back to displaying a 404.
            try_files $uri $uri/ =404;
    }


    # 백엔드 영역
    # url에서 other 뒤에 있는 URL을 전부 그대로 사용.
    location /api/ {
            rewrite ^/api(/.*)$ $1 break;
            proxy_pass http://localhost:8080;
    }
```

- 재시작

```
sudo service nginx restart
```
