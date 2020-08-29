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

## 우분투 초기 셋팅
- 구글 크롬 + 동기화
	- 소프트웨어 설치가 안 될 때가 있다. 그 땐 아래 페이지 참고
	- [could not get lock /var/lib/dpkg/lock-frontend - open 에러 참고 페이지](https://www.notion.so/guswns2012/Could-not-get-lock-var-lib-dpkg-lock-frontend-4ea9635fc5a94f298b075cbbcd80e67b)
	- 크롬을 터미널에서 설치해야 할 수도 있다. 그럼 아래 페이지 참고
	- [크롬 설치 안될 때 아래 페이지 참고](https://www.notion.so/guswns2012/How-to-Install-Google-Chrome-Web-Browser-on-Ubuntu-18-04-Linuxize-6540ef034bac45fc83d1e811faff1613)
- 한글 설정
	- UIM 설정이 적용안될 때가 있다. 그 땐 IBUS로 변경해서 사용한다. 슬랙에서 한글 입력이 안된다.

----
## 한글 설정
- language support에 들어가서 자동으로 필요한 파일을 다운 받는다.
- region & language에 들어가서 input sources에 korean(Hangul)을 추가한다.
- keyboard에 들어가서 switch to next input source에서 한/영 키로 변경한다.

### tweak 사용 (오른쪽 alt를 한영변환키로 바꾸는 작업)
- Then open GNOME Tweaks (gnome-tweaks).
- Select Keyboard & Mouse tab
- Click Additional Layout Options button
- Expand Switching to another layout
- Select Ctrl + Shift here
[출처 : https://askubuntu.com/questions/1029588/18-04-ctrlshift-to-change-language](https://askubuntu.com/questions/1029588/18-04-ctrlshift-to-change-language)

### Uim 설정 하는 법
한글 밀리는 거 해결하려고 이것저것 설치하다가 갑자기 한글 자소 구분이 안되서 결국 UIM으로 설치 완료..
나중에 정리할 예정 ! 아래 출처
[http://progtrend.blogspot.com/2018/06/ubuntu-1804-uim.html](http://progtrend.blogspot.com/2018/06/ubuntu-1804-uim.html)

----

## 마우스 오른쪽 클릭 안될 때
- 우분투의 기본 설정은 터치 패드에 두 손가락으로 클릭하면 된다.
- 오른쪽 클릭이 작동하기 위해선 software center에서 GNOME Tweaks를 설치한다.
- Keyboard & Mouse -> Mouse Click Emulation -> Area 클릭

## java 설치 및 JAVA_HOME, PATH 지정
[영상 참고: 5분 짜리 영상](https://www.theserverside.com/video/5-steps-for-an-easy-JDK-13-install-on-Ubuntu)

- 운영체제에 맞게 jdk8 tar.gz파일을 다운받는다. [여기서 다운](https://www.oracle.com/java/technologies/javase-jdk8-downloads.html)
- 압축 파일을 푼 후 압축을 푼 파일을 /opt로 이동한다. sudo mv jdk8~~ /opt
- 아래처럼 자바 환경변수를 설정할 shell 파일을 만들고 환경변수 명령을 입력한다

```java
jdk13@ubuntu:$ sudo tee /etc/profile.d/jdk8env.sh <<EOF
export JAVA_HOME=/opt/jdk-1.8.0_241
export PATH=$PATH:$JAVA_HOME/bin
EOF
```

- source 명령으로 sh파일을 실행한다.

```java
$ source /etc/profile.d/jdk8env.sh
```

- 잘 지정됐는지 확인한다.

```java
echo $JAVA_HOME
echo $PATH
```

- java -version 입력 후 제대로 PATH가 지정됐는지 확인한다.

## file 찾는 방법
- sudo apt-get install locate
- sudo updatedb
- locate [fileName
### whereis 파일명


## file 확장자 찾는 법
- file [fileName]

## 단축키
- Ctrl + Alt + T : 바탕화면에서 터미널을 실행
- Ctrl + Shift + T : 터미널내에서 새탭으로 터미널을 실행
- Ctrl + Shift + N : 터미널내에서 새로운 터미널을 실행
- Ctrl + Shift + W : 탭으로 실행된 터미널을 종료
- Ctrl + Shift + Q : 현재 터미널을 종료(탭포함)
- Ctrl + Shift + F : 터미널내에 문자열검색
- Alt + 숫자 : 탭간에 이동
- Ctrl + PageUp/PageDown : 다음/이전 탭으로 이동

## Ubuntu에 git 설치하기
- sudo apt update
- sudo apt install git
- git --version // 버전확인

## 기본 터미널 설정 바꾸기
### 솔라리스 어두움 - 녹색 글씨
- 단점 : autoSuggestion 글씨 색깔이랑 배경이랑 같아서 안보임.. -> 글씨 못 바꾸나?  
- 최초 터미널 크기 100열 / 24행
- 사용자 지정 글꼴 : DejaVu Sans Mono Book 15
- 배경의 내장 팔레트 : 솔라리스 어두움을 기본으로 한 뒤 글씨 색깔은 #30E688(녹색계열)
- 투명한 배경 사용 : 없음 (완전 불투명)
- 내장 팔레트 : 솔라리스
- 프로파일 저장도 가능.

## 블루투스이 on이 안될 때
- 갑자기 블루투스가 off 된 뒤 on으로 안된다.
- bluez라는 프로그램을 설치하고 다시 껐다가 켠다.
- 참고 : [https://askubuntu.com/questions/1036195/bluetooth-doesnt-work-after-resuming-from-sleep-ubuntu-18-04-lts](https://askubuntu.com/questions/1036195/bluetooth-doesnt-work-after-resuming-from-sleep-ubuntu-18-04-lts)

## 사진 편집 프로그램, Shutter 설치법
- 참고 : [https://webnautes.tistory.com/513](https://webnautes.tistory.com/513)

## 우분투 18.04에서 듀얼모니터 설정
- setting -> 장치 -> 디스플레이(display)로 들어간다.

### 메인 화면 정하기
- 주요 디스플레이를 설정

### 모니터 위치 변경하기.
- 모니터 하나를 클릭한 뒤 이동시킨다.

## 화면 잠금 시간 설정
- 2가지 방법이 있는데 뭐가 정확한지는 헷갈린다.
- setting -> 개인정보 -> 화면잠금 클릭 후 시간 설정
- setting -> 전원 -> 절전 -> 빈화면 시간 설정

## logitech K380의 F1 ~ F12 키를 기본키로 바꾸는 법
- [이 사이트](https://github.com/jergusg/k380-function-keys-conf)의 README를 따라서 차근차근 한다.
- 중간에 `make install` 명령이 안 먹히면 `sudo make install`로 시도
- `sudo k380_conf -d /dev/hidrawX -f on` X is 1 or 2 or 3 or 4

## zsh

## Terminal에 oh-my-zsh 추가하기
- 글 참고 : [https://dev.to/mskian/install-z-shell-oh-my-zsh-on-ubuntu-1804-lts-4cm4](https://dev.to/mskian/install-z-shell-oh-my-zsh-on-ubuntu-1804-lts-4cm4)
- 위 글대로 하되 테마 설정 완료 한 후 [이 글 참고해서 실행하기](https://github.com/ohmyzsh/ohmyzsh/issues/8038)

## oh.my.zsh 테마 설정
- cd ~
- cd .oh-my-zsh/themes
- vim agnoster.zsh-theme 들어간 뒤 필요한 테마 설정

### agnoster new line 설정하기

- 원래 코드를 작성했는데 github.io에 올릴 때 에러가 나서 아래 사이트로 대체.
[참고 사이트](https://wayhome25.github.io/etc/2017/03/12/zsh-alias/)

## 터미널 프롬프트 설정
- .bashrc:16: command not found: shopt
	- zsh가 기본 설정인데 예전 bashrc를 설정하려고 할 때 나는 에러라고 함.
	- 참고 : [https://stackoverflow.com/questions/26616003/shopt-command-not-found-in-bashrc-after-shell-updation](https://stackoverflow.com/questions/26616003/shopt-command-not-found-in-bashrc-after-shell-updation)

## 터미널 프롬프트 이름 지우는 방법
참고 : [https://the-illusionist.me/49](https://the-illusionist.me/49)

- 아래 코드를 vim ~/.zshrc 맨 마지막에 넣고 wq!로 저장한다.
- 원래 코드가 있었으나 github.io 문제로 위 사이트로 대체. 그대로 따라하면 된다.


- 그 뒤 source ~/.zshrc 실행하면 바로 적용.

## auto-suggestions 적용하는 법
- 참고 : [https://nachwon.github.io/how-to-install-zsh-plugins/](https://nachwon.github.io/how-to-install-zsh-plugins/)

## FzF 적용하는 법
- 참고 : [https://medium.com/harrythegreat/fzf%EB%A1%9C-zsh-%ED%84%B0%EB%AF%B8%EB%84%90-%EB%8D%94-%EA%B0%95%EB%A0%A5%ED%95%98%EA%B2%8C-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0-730c20eb496b](https://medium.com/harrythegreat/fzf%EB%A1%9C-zsh-%ED%84%B0%EB%AF%B8%EB%84%90-%EB%8D%94-%EA%B0%95%EB%A0%A5%ED%95%98%EA%B2%8C-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0-730c20eb496b)
- 좋은 기능으로 보이는데 초보인데 벌써 사용하고 싶진 않다.

## 유용한 zsh 플러그인 6가지.
- 주소 : [https://medium.com/harrythegreat/zsh%EC%99%80-%ED%95%A8%EA%BB%98-%EC%82%AC%EC%9A%A9%ED%95%A0-%ED%94%8C%EB%9F%AC%EA%B7%B8%EC%9D%B8-%EC%B6%94%EC%B2%9C-6%EA%B0%80%EC%A7%80-8f9b8b7f3c24](https://medium.com/harrythegreat/zsh%EC%99%80-%ED%95%A8%EA%BB%98-%EC%82%AC%EC%9A%A9%ED%95%A0-%ED%94%8C%EB%9F%AC%EA%B7%B8%EC%9D%B8-%EC%B6%94%EC%B2%9C-6%EA%B0%80%EC%A7%80-8f9b8b7f3c24)


## zsh에서 java 명령이 없다고 뜰 때

- h2를 다운받은 후 실행하려니 java가 없다고 함.
- 원인 : 자바 PATH를 제대로 설정하지 않아서 못 읽어오는 문제.

### 1 : ~/.zshrc 속  export PATH=를 변경한다

- 기존것에서 /sbin을 추가한 문법을 넣는다.

```java
# If you come from bash you might have to change your $PATH.
export PATH=$HOME/bin:/usr/local/bin:/sbin:/usr/sbin:$PATH
# export PATH=$HOME/bin:/usr/local/bin:$PATH
```

- [export PATH를 변경한 후 리부트한다.](https://unix.stackexchange.com/questions/328542/zsh-command-not-found-services)

### 2 : 자바 PATH를 $JAVA_HOME가 있는 위치로 정확하게 지정한다.

- 아래 코드의 차이를 보면 알지만 $JAVA_HOME은 최상단 루트 디렉토리에 있었는데 계속 $HOME으로 지정하니 리눅스가 bin 폴더를 제대로 못 찾는 문제가 발생한다.

**Before**

```java
export JAVA_HOME=/opt/jdk1.8.0_261
export PATH=$HOME/$JAVA_HOME/bin:$PATH
```

**After**

```java
export JAVA_HOME=/opt/jdk1.8.0_261
export PATH=$JAVA_HOME/bin:$PATH
```


### ~/.zshrc의 Path를 잘못 건드려서 모든 명령을 못찾을 때

- [참고 스택오버 플로우](https://stackoverflow.com/questions/18428374/commands-not-found-on-zsh)

- 아래 명령어를 zsh에서 입력한다.

    ```shell
    PATH=/bin:/usr/bin:/usr/local/bin:${PATH}
    export PATH
    ```


### 아나콘다 설치하기
- [이 영상을 참고한다](https://www.youtube.com/watch?v=9Uy00NhbG6w)
- 위 영상대로 참고하되 마지막에 아래 명령어로 .bashrc를 실행하고 anaconda-navigation 입력
```java
source /home/jack/.bashrc

anaconda-navigator
```

### 우분투에서 인텔리J 버전 선택해서 다운받는 법
- 우분투 소프트웨어에서 intellij를 검색한다.
- 아래로 스크롤하면 버전을 선택할 수 있다 (아래 사진 참고)

![우분투_인텔리j_다운그레이드방법](/assets/linux/우분투_인텔리j_다운그레이드방법.png)

## sudo apt update 에러 발생할 때

- 에러 메세지

```java
The repository 'http://ppa.launchpad.net/armagetronad-dev/ppa/ubuntu bionic Release' does not have a Release file.
```

- 해결책
해당 ppa 패키지를 지운다.

```java
sudo add-apt-repository -r ppa:<ppa to remove>
```

## $PATH 추가하는 문법
- [참고 : 밸덩 리눅스](https://www.baeldung.com/linux/path-variable)
- export PATH 명령어를 이용해 PATH를 추가할 수 있다.
- 만약 아래 코드처럼 :$PATH를 뒤에 붙이면 기존에 존재하는 PATH들 앞으로 추가한 PATH가 붙는다.
- 만약 아래 코드와 다르게 기존에 존재하는 PATH 뒤로 넣고 싶으면 `export PATH=$PATH:/anothor/path`처럼 기존 PATH 앞에서 붙인다.

```java
export PATH=$HOME/bin:/usr/local/bin:/sbin:/usr/sbin:$PATH
```

# 우분투에서 동영상 촬영하는 법

- 에어비앤비 리팩토링 하기 전에 기존에 존재하는 프로젝트를 영상으로 찍어서 남기려고 함.
- [내장 프로그램인데 불편함](https://tipsonubuntu.com/2018/06/09/record-gnome-desktop-ubuntu-18-04/)
- [Kazam 프로그램, 편리하다](https://itsfoss.com/best-linux-screen-recorders/)
