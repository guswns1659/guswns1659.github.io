## 목차

## 핵심
- **도커는 컨테이너 기반의 오픈소스 가상화 플랫폼이다**
## 도커의 필요성
클라우드의 발전과 짧아진 배포 주기는 관리할 서버가 수백, 수천대에 이르는 상황에 도달한다. 하나의 서버를 관리하는 것도 쉽지 않은데 수 많은 서버를 관리하는 것은 더 어려운 상황이다. 이 때 도커가 등장하고 서버 관리 방식이 바뀐다.

## 도커란?
**도커는 컨테이너 기반의 오픈소스 가상화 플랫폼이다** 컨테이너가 정해진 규격으로 물품의 운반을 쉽게 도와준다. 도커의 의미는 어떤 프로그램이든 컨테이너처럼 포장(?)을 할 수 있고 이는 어떤 환경에서는 실행할 수 있다.   

## 참고할 영상
[동빈나도커](https://www.youtube.com/watch?v=2-w679FFMrc)

# 에러메세지
## 도커 설치 중 해시합이 맞지 않습니다. 라는 에러

apt-get update 실행 중 발생하는 오류.

소프트웨어 & 업데이트 서버가 한국 서버라서 나는 오류라고 한다.간단하게 서버만 바꿔주면 됨

- 해결방법
시스템 설정 - 소프트웨어 & 업데이트에 들어가서 서버를 한국서버에서 '주 서버'로 변경

[출처] [우분투 error] 해시 합이 맞지 않습니다.|작성자 Veronica

---

## psql: error: could not connect to server: FATAL:  role "postgres" does not exist
- postgres 사용 시 psql 명령어 사용할 때 나타날 수 있는 에러이다.
- 도커 접속 명령
```
docker run -p 5432:5432 -e POSTGRES_PASSWORD=pass -e POSTGRES_USER=jack -e POSTGRES_DB=springdata --name postgres_boot -d postgres
```

- 1번 : 도커 접속할 때 입력한 user와 db를 지정해서 접근한다.
```
postgres@766d6461a448:~$ psql --username jack --dbname springdata
```
- 2번 : docker로 postgres를 접근할 때 user를 postgres로 변경한다.
```
docker run -p 5432:5432 -e POSTGRES_PASSWORD=pass -e POSTGRES_USER=postgres -e POSTGRES_DB=springdata --name postgres_boot -d postgres

```
- [참고 : 백기선님 spring data jpa 강의](https://www.inflearn.com/questions/5647)

### psql이란?
- 쿼리문을 작성할 수 있는 터미널이라 볼 수 있다.

---

## “Error:java: Compilation failed: internal java compiler error idea”

[참고 : 스택오버플로우](https://stackoverflow.com/questions/42966889/intellij-idea-tells-me-errorjava-compilation-failed-internal-java-compiler-e)

### 답변
- On Intellij IDEA Ctrl + Alt + S to open settings.
- Build, Execution, Deployment -> Compiler -> Java Compiler
- choose your java version from Project bytecode version
- Uncheck Use compiler from module target JDK when possible
- click apply and ok.

## docker postgres DB 셋팅
- docker ps로 살아있는 지 확인한다.
- 없다면 docker ps -a로 죽은 컨테이너를 확인한다.
- docker start postgres_boot 실행
- docker exec -i -t postgres_boot bash
- su - postgres
- psql --username jack --dbname springdata (콘솔에 접근하는 명령어)
