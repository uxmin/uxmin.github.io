---
layout: post
title: VS Code에서 Node.js 사용하기 1
date: "2021-01-19"
categories: VSCode Node.js Express MongoDB
---
이번 포스트에서는 VS Code를 이용해 Node.js를 위한 환경을 구축하는 내용을 담을 예정이다.

<br><br>

### 1. VS Code 설치

[Visual Studio Code Download][1]<br>
상단의 링크를 따라 들어가서 VS Code를 설치한다. <br>
자신의 운영체제에 맞게 설치하면 된다.<br>
_* 설치에 어려움은 없을테니 자세한 설명은 생략한다._<br><br><br>

### 2. Node.js 설치

[Node.js Download][2]<br>
상단의 링크를 타고 들어가면 Node.js 다운로드 창이 열린다.<br>
각 운영체제에 맞게 설치하면 되는데, 최신 버전보다는 안정적인 LTS 버전을 설치한다.<br><br>
![Node_Install][3]<br><br><br>

### 3. MongoDB 설치

[MongoDB Download][4]<br>
NoSQL 중 가장 대표적이고 유명한 MongoDB는 강력하고 유연하며 확장성이 높은 Document Base DB(문서지향 데이터베이스)이다.<br>
_* 참고: [MongoDB란 무엇인가?][5]_<br><br>

![MongoDB_Install][6]<br>
MongoDB 사용을 위해서는 우선적으로 Server를 설치해야 한다.<br>
상단의 링크를 따라 환경에 맞게 설정 후, Download 버튼을 클릭해 설치를 시작한다.<br><br>

![MongoDB_Install2][7]<br>
설치를 진행하다가 보면 Complete, Custom 두 갈래로 나뉘는데 모든 기능이 탑재되어 있는 Complete 버전으로 설치한다.<br><br>

![MongoDB_Install3][8]<br>
체크박스를 체크하면 MongoDB의 GUI인 Compass가 함께 설치된다.<br><br>

성공적으로 MongoDB가 설치되면, 환경변수를 설정해준다.<br>
_* MongoDB 경로: **C: > Program Files > MongoDB > Server > bin**_<br>
![MongoDB_Path][9]<br><br>

모든 설치가 완료된 후, 아래 명령어를 입력하면 로컬의 기본 포트 27017로 연결되는 모습을 볼 수 있다.<br>
```bash
$ mongo
```
![MongoDB_Success][10]<br><br><br>

### 4. Node.js를 위한 프로젝트 생성 및 환경설정

작업할 폴더를 새롭게 생성을 한 후, VS Code로 폴더를 불러오면 새로운 프로젝트를 진행할 준비가 된다.<br>
![Nodejs_New][11]<br>
_* 나는 C: > development > nodejs 경로에 폴더를 새롭게 생성했다._<br><br>

#### 4_1. npm init

**Ctrl + Shift + `** 단축키를 사용해 터미널을 열고 아래 명령어를 입력한다.<br>
```bash
$ npm init 
```
이것은 Node.js를 실행하기 위한 초기화 과정으로, package.json 파일이 생성된다.<br>

![nodejs_init][12]<br>
_* 여러가지 설정 항목들이 나오는데 ~~귀찮으니까~~ enter 눌러주자._<br><br>

#### 4_2. npm install express, mongoose, body-parser

터미널에 아래 명령어를 입력해서 생성된 프로젝트 내 패키지를 설치한다.<br>
```bash
$ npm i --save express mongoose body-parser
```
1. express: 웹프레임워크<br>
1. mongoose: MongoDB 연동 라이브러리<br>
1. body-parser: 데이터 처리 미들웨어<br>


[1]: https://code.visualstudio.com/download
[2]: https://nodejs.org/en/download/
[3]: /assets/img/Nodejs/nodejs_installer.png
[4]: https://www.mongodb.com/try/download/community
[5]: http://dev.youngkyu.kr/22
[6]: /assets/img/Nodejs/mongodb_installer.png
[7]: /assets/img/Nodejs/mongodb_installer_complete.png
[8]: /assets/img/Nodejs/mongodb_installer_compass.png
[9]: /assets/img/Nodejs/mongodb_path.png
[10]: /assets/img/Nodejs/mongodb_success.png
[11]: /assets/img/Nodejs/nodejs_new.png
[12]: /assets/img/Nodejs/nodejs_init.png