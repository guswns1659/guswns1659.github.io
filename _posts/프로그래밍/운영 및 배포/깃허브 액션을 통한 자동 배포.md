## 깃허브 액션을 통한 자동 배포

## 깃허브 액션 참고 강의
- [참고: 생활코딩 강의](https://www.youtube.com/watch?v=uBOdEEzjxzE)

### 깃허브 액션이란?
- 코드 배포나 운영을 도와주는 가상머신을 깃허브에서 대여해주는 서비스
- 깃허브 액션은 YML파일로 구동되는 서비스이다.

## 이슈트래커 프로젝트에 깃허브 액션 적용하기.

### 순서
- 깃허브 액션에 workflow (.yml)을 추가한다.
### AWS EC2, codedeploy, S3 생성 및 권한 부여
- 배포를 할 때 AWS의 서비스를 사용한다면 각 서비스 인스턴스를 생성하고 IAM 권한을 부여해야 한다. 관련 작업을 잘 정리된 블로그가 있어서 아래 첨부했다. 차근차근 따라하면 된다.

- [참고 블로그 : https://isntyet.github.io/deploy/github-action%EA%B3%BC-aws-code-deploy%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%98%EC%97%AC-spring-boot-%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0(4)/](https://isntyet.github.io/deploy/github-action%EA%B3%BC-aws-code-deploy%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%98%EC%97%AC-spring-boot-%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0(4)/)

### 참고 블로그 따라할 때 주의할 점
- ec2에서 code deploy를 설치할 때 ec2가 설치된 리전에 맞게 바꿔준다.

```java
wget https://aws-codedeploy-ap-northeast-2.ap-northeast-2.amazonaws.com/latest/install
```

[참고: aws 사이트](https://docs.aws.amazon.com/ko_kr/codedeploy/latest/userguide/resource-kit.html#resource-kit-bucket-names)

- codedeploy-agent가 제대로 설치되면 아래 1번째 명령어로 확인한 뒤 아직 inactive면 2번째 명령어를 입력한다.
```
sudo service codedeploy-agent status
sudo service codedeploy-agent start
```

- codedeploy의 iam을 생성하려면 역할 만들기 누른 뒤에 codedeploy를 선택해야 한다. 예시 블로그에서 따라하는데 이 부분이 헷갈림.

### AWS 인스턴스 생성 후 깃허브 액션에서 설정하는 법

- 깃허브 액션을 활용할 브랜치로 체크아웃한 뒤 action에 들어간다.
- 저장소 action에 들어간 뒤 새 workflow를 생성한다.

![new workflow 생성](https://i.imgur.com/YCiGGDK.png)

<details markdown="1">
<summary>workflow yml 파일</summary>

### workflow yml 파일
- 아래 workflow에 각 단계 별 설명을 추가했지만 다음 챕터에 각 단계별로 자세히 알아보자.  

```shell script
# github action workflow yml file
# 액션의 이름이다.
name: CI
# 아래처럼 변수를 지정할 수 있다. 나중에 S3 디렉토리명을 지정할 수 있다.
env:
  PROJECT_NAME: Jack_codedeploy
# 언제 액션이 이루어질 지 정할 수 있다.
# 우리는 deploy 브랜치에 Push되면 자동 배포하도록 정의할 것이다.
on:
  push:
    branches: deploy

# 아래의 Job들이 깃헙 액션에서 진행된다.
jobs:
  # 하나의 Job을 정의한다. 여기서 Job의 이름은 DEPLOY이다.
  deploy:
    name: DEPLOY
      # 빌드가 어느 운영체제에서 돌아가느냐?
    runs-on: ubuntu-18.04
    # 아래는 주석처리 됐지만 깃허브 액션에서 제공하는 인스턴스의 어떤 경로에서 작업할지 지정할 수 있다.
    # 디렉토리 경로에 따라 성공, 실패가 좌지우지 되기 때문에 중요하다.  
    #env:
      #working-directory: .

      # step은 job의 하위 집합이다. step에서 정의한 작업을 순차적으로 진행한다.
    steps:
      - name: Debug Action
        uses: hmarr/debug-action@v1.0.0

        # 맨 처음 정의한 브랜치로 체크아웃한다.
      - name: Checkout
        uses: actions/checkout@v2

        # 메시지를 출력한다.
      - name: Run a one-line script
        run: echo Start Deploy.

        # 여러 가지의 메시지를 출력할 수 있다.
      - name: Run a multi-line script
        run: |
          echo We love Honux,
          echo CodeSquad 2020 Java Backend.

        # 자바 버전을 설정해준다.
      - name: Set up JDK 1.8
        uses: actions/setup-java@v1
        with:
          java-version: 1.8

        # Gradle에 실행 권한을 부여한다.
      - name: Grant execute permission for gradlew
        run: |
          echo $(pwd)
          chmod +x gradlew
        shell: bash
        # 아래처럼 해당 run이 동작하는 디렉토리를 지정할 수 있다.
        # working-directory: ./BE
#
        # Gradle을 활용해 배포한다.
      - name: Build with Gradle
        run: |
          ./gradlew build -x test
        shell: bash
#        working-directory: /BE

        # 버전마다 이름을 다르게 하기 위해서 GITHUB_SHA라고 하는 해시이름을 활용하여 zip 파일을 만든다.
        # CodeDeploy를 사용하기 위해서는 S3를 거쳐야 한다. 따라서 압축된 파일을 만들어주고 이를 옮긴다.
      - name: Make zip file
        run: |
          echo $(pwd)
          zip -qq -r ./$GITHUB_SHA.zip .
        shell: bash
#        working-directory: ./build/libs

        # AWS 서비스를 사용하기 위한 인증 과정이다.
      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v1
        with:
          # secret은 settings -> Secret에서 지정할 수 있다.
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ${{ secrets.AWS_REGION }}

      #- name: Setup tmate session
       # uses: mxschmitt/action-tmate@v2

        # 배포 zip 파일을 S3에 업로드한다.
      - name: Upload to S3
        run: aws s3 cp --region ap-northeast-2 ./$GITHUB_SHA.zip s3://issuetracker-deploy/$PROJECT_NAME/$GITHUB_SHA.zip
        #working-directory: ./build/libs

        # 실제로 코드를 배포한다.
      - name: Code Deploy
        run: aws deploy create-deployment --application-name issueTracker-codedeploy --deployment-config-name CodeDeployDefault.OneAtATime --deployment-group-name dev --s3-location bucket=issuetracker-deploy,bundleType=zip,key=$PROJECT_NAME/$GITHUB_SHA.zip

```

</details>

#### make zip file 단계 설명

![make zip file 단계 설명 사진](https://i.imgur.com/jLKbqOx.png)

- 위 단계를 통해 깃허브 가상머신에게 현재 디렉토리를 zip파일로 만들라는 명령이다. 만들어진 zip 파일은 s3로 옮겨진다.

#### AWS 서비스를 이용할 수 있는 권한을 가상머신에게 부여하는 단계 설명

![AWS 서비스를 사용하기 위한 인증 설정하는 단계 사진](https://i.imgur.com/5ejNQ4o.png)

- 네모칸에 있는 정보는 민감정보이기 때문에 공개하지 않고 깃허브의 Secret 기능으로 감춘 상태로 사용할 수 있다.

##### 깃허브 Secret를 하는 법

![깃허브 secret를 설정하는 법](https://i.imgur.com/mdeOZBp.png)

- 위 사진 순서대로 secret를 등록하면 된다. 이전에 AWS를 설정하면서 부여받은 정보를 입력하면 된다.

##### zip파일을 s3에 업로드하는 과정

![ZIP 파일을 S3에 업로드하는 과정 설명 ](https://i.imgur.com/c62WOA9.png)

- 위 사진처럼 내 s3 버켓 이름을 넣어야 한다. 안넣으면 에러 발생.

#### code deploy가 s3에 담긴 zip파일을 ec2로 넘기는 작업.

![code deploy가 s3에 담긴 zip파일을 ec2로 넘기는 작업](https://i.imgur.com/MFydnHO.png)

- 위 사진처럼 어플리케이션 이름과 s3 버켓 이름을 정확히 사용한다.
