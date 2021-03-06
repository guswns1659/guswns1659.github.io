---
header:
  teaser: /assets/linux.jpg
  overlay_image: /assets/linux.jpg
  overlay_filter: 0.2
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
categories:
  - Linux
---

리눅스는 최고의 운영체제 중 하나입니다.

## 목차
- 리눅스란?
- 리눅스 공부 순서
- 리눅스 탄생
- 오픈 프로그램과 오픈 소스

## 핵심
- **리눅스는 서버 개발자라면 반드시 알아야할 언어이다.**

## 리눅스란?
운영 체제 중 하나이다. 개발자가 아닌 일반 사용자라면 원도우라는 운영 체제가 익숙할 것이다. 운영 체제는 컴퓨터가 운영될 수 있게 도와주는 프로그램이다. 세상에는 다양한 운영 체제가 있지만 원도우, 맥OS, 리눅스 등이 가장 유명하다.<br>
즉, 리눅스도 윈도우와 비슷한 친구이다. **그런데 왜 이렇게 낯설까?** 왜냐하면 리눅스는 일반 사용자가 사용하는 수준보단 조금 더 전문적인 영역에서 사용되기 때문이다. 다른 영역에서 리눅스의 영향력은 엄청나다. ~~추후에 알아보는 걸로~~<br>
그럼 리눅스를 반드시 배워야 할까? 아니다. 하지만 내가 웹사이트를 직접 만들어서 고객에게 제공하고 싶으면 서버를 운영해야 한다. 서버를 운영하기 위한 OS로 리눅스가 있다. 이런 상황이면 리눅스를 배우면 좋다. 한마디로 내가 백엔드 개발자가 되고 싶다면 리눅스는 배워야 한다. ~~그래서 나도 배우는 중입니다..~~<br>

## 리눅스 공부 순서
공부에 순서가 어디있겠느냐만 조금 더 효율적으로 공부할 수 있는 순서가 있다고 한다. 한번 보자.
### 1. 일반 사용자
처음엔 리눅스라는 OS를 사용하는 사용자의 입장에서 배우는 것이다. 우리가 윈도우를 쓰더라도 파일을 열고 읽고, 쓰고 하듯이 리눅스에도 모든 것이 가능하다. 다만, 원도우에 비해 터미널로 이루어지기 때문에 어려울 뿐이다.<br>
이 때 배우는 내용들이 파일을 다루는 법, VI라는 에디터를 쓰는 법, 쉘 사용하는 법 등이다.
### 2. 관리자(root)
일반 사용자를 넘어선다면 이제 관리자의 입장에서 공부를 해야 한다. 관리자는 보통 root를 말하며 리눅스 설치, 프로세스, 패키지 등 전문적으로 할 수 있는 기술들이다.  

### 3. 서비스
웹서비스를 만들고 사용자에게 서비스를 제공하는 수준이다. 웹이나, 메일, DNS, FTP 등을 여기서 배운다. DNS는 웹사이트 주소를 ip주소로 바꿔주는 기능을 말한다.

### 4. 보안
보안은 우리 집에 아무나 들어올 수 없게 잠구는 것을 말한다. 컴퓨터도 마찬가지다. 내 서버에 아무나 들어와서 망치면 안된다. 아무나 들어올 수 있게 잠구는 것도 필요하다.

### 5. 프로그래밍
자바, C언어, shell 등 다양한 언어를 배우는 단계이다. 이를 통해 프로그램을 만들어 응용할 수 있다.

## 리눅스 탄생
리눅스는 리누스 토발즈와 GNU프로젝트의 합작품이다. 이름이 리눅스이기 때문에 리누스 토발즈가 다 만들었다 싶지만, 토발즈는 커널을 만들었다. 커널은 OS의 핵심이라 부르는 기능이다.<br>
리누스의 커널과 GNU프로젝트가 함께 합작해 만든 OS가 리눅스이다. 다양한 리눅스 프로그램이 GNU프로젝트를 통해 만들어졌다.

## 오픈 프로그램과 오픈 소스
오픈 프로그램은 무료지만 소스코드는 공개되지 않는 프로그램을 말한다. 예를 들어, 카톡이다. 우리는 카톡 쓸 때 돈 내지 않는다. 하지만 그 안에 소스 코드까진 볼 순 없다.<br>
오픈 소스는 소스 코드도 공개가 되어 있는 경우를 말한다. 하지만 모든 오픈 소스가 무료는 아니고 값을 지불해야 하는 경우도 있다.

## root 이동
- root로 이동 : sudo -i
- 다시 복귀 : su -l hyunjun

## 자주 만나는 에러 3가지
- command not found : 잘못된 명령어를 입력했을 때, 당황하지말고 오타를 찾는다.
- Permission deined : 위와 마찬가지
- 그런 디렉터리나 파일 없음 : 위와 마찬가지!

## 리눅스 프로그램 설치
- 패키지 설치 목록 확인 Ubuntu 명령어 : dpkg -l | nl
- 결과를 파일로 저장할 때 : dpkg -l > dpkgList
- dpkg -l | grep ftp : ftp 문자열이 포함된 파일을 보여달라
- ping -c3 8.8.8.8 : 네트워크 연결 상태 확인

## 리눅스 파일 복사, 삭제
- cp /etc/b* . : etc경로 내 b로 시작하는 파일 복사
- cp -r /etc/b* . : etc경로 내 b로 시작하는 파일 + 디렉토리 복사

- rm b* : 현재 경로에서 b로 시작하는 파일 삭제
- rm -r b* : 디렉토리까지 삭제
- cp /etc/bashr . : .은 현재 디렉토리를 말한다. 경로를 꼭 지정해야 복사 가능하다.
- cp /etc/bashr b2 : 이름 바꿔서 저장할 때 뒤에 새로운 파일 명을 붙인다.

------

## root 계정 변경 및 사용자 추가
SSH Server를 설치하기 위해선 root 계정으로 변경해야 한다.
- su - root : 루트 계정으로 변경
- root 계정이 막힌 경우 :
    - sudo passwd root 입력 후 root password 설정
    - sudo passwd -u root
### 사용자 추가하는 명령어    
- 사용자 추가 : sudo useradd -m jun
- 추가한 사용자에게 sudo 권한 주기 : 먼저 sudo 권한 있는 사용자로 이동한다. 그 뒤 sudo usermod -a -G sudo jun

### 외에 사용자 관련 명령어
- id : 현재 사용자의 정보를 알려준다.
- who : 현재 접속한 사용자들을 알려준다.
- exit : 계정 빠져 나오기.

## Shell script

### 디렉토리 탐색, 압축, 백업
- shell script 실행이 안될 때 bash ./< script file > 으로 해보기. 에러 : "(" unexpected

### 2. Shell Script
script의 의미는 연극의 대본이다. 배우들의 대사 순서가 적혀있다. 그래서 대본대로 연극은 진행된다. 이처럼 프로그램도 명령이 일련의 순서로 이루어지기 때문에 script와 비슷하다. shell도 script가 있어서 자동화된 업무를 쉽게 처리할 수 있다.<br>

백업할 때 #!/bin/bash를 가장 먼저 입력한다. 컴퓨터가 bash를 통해 이 문법이 해석된다는 것을 이해하게 도와준다.
nano backup 한 뒤 문법 작성,
<br>
```shell script
if ! [ -d bak ]; then
    mkdir bak
fi
cp *.cs bak
```


### 변수
- 변수를 미리 선언하지 않고 초기화하면 바로 변수가 만들어진다.
- 모든 변수는 String 취급한다. 연산을 하려면 다른 함수를 써야 한다.
- 변수 대입할 때는 '=' 좌우에 공백이 없어야 한다.
- 출력할 때는 '$'를 붙여야 한다.
```shell script
testval="Hello"
echo $testval
```
#### 변수의 입력과 출력
- 값을 입력받을 땐 read myvar를 사용한다. 자바의 Scanner 기능.
- $을 출력하고 싶으면 '\' 또는 "$myvar"처럼 전체를 감싼다.

#### 숫자 계산
- expr과 ``을 사용한다.
```shell script
#!/bin/bash
num3=`expr 100 + 200`
echo $num3
```

### 파라미터 계산
- script 파일에서 $1, $2, $3으로 매개변수를 표현하고 호출할 때 파일 명 옆에 매개변수를 넣는다.
```shell script
#!/bin/sh
echo "첫 번째 매개변수는 <$1>이다."
echo "두 번째 매개변수는 <$2>이다."
echo "전체 매개변수는 <$*>이다."
```

### 코드스쿼드 미션
- 디렉토리 반복적으로 만드는 문법 :  
- 디렉토리 안에 파일에 조건을 넣는 문법 : if 문
- zip으로 압축해 백업하는 문법
- 압축한 파일을 /backup에 복사 문법 : zip 새로생성할 파일이름.zip 압축할 파일 이름
- scp 명령

- 1~3 반복문
```shell script
for i in `seq 1 3`
do
        touch test$i.cs
done
```

- 파일 다른 디렉토리로 복사하기
```shell script
cp abc.txt xyz
```

### 압축 해제 시 한글 깨질 때 적용 방법
- 윈도우에서 압축할 때 사용하는 인코딩방식과 리눅스에서 해제할 때 사용하는 인코딩 방식이 다르기 때문에 발생
- vim ~/.profile에 들어간 뒤 아래 설정을 추가한 뒤 재시작한다.

```java
export UNZIP="-O cp949"
export ZIPINFO="-O cp949"
```

## tee
tee 는 표준 입력(standard input)에서 읽어서 표준 출력(standard output) 과 파일에 쓰는 명령어이다. 입력값을 출력하거나 파일에 쓸 때 사용된다.

```java
echo "jack" | tee OUT // "jack"을 OUT에 쓴다.

cat OUT | tee OUT2 // OUT파일을 OUT2에 복사한다.

echo "jack" | tee -a OUT // "jack"을 OUT에 추가로 쓴다.
```
