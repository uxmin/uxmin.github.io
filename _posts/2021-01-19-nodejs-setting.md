---
layout: post
title: VS Code에서 Node.js 사용하기 1
date: "2021-01-19"
categories: VSCode Node.js Express MongoDB
---
이번 포스트에서는 VS Code를 이용해 Node.js를 작업할 수 있도록 환경을 구축하는 내용을 담을 예정이다.<br>
이 포스트에서는 Express를 기반으로 기본적인 Node.js 환경을 구축하는 방법에 대해서 설명한다.<br>

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

<!-- 작업할 폴더를 새롭게 생성을 한 후, VS Code로 폴더를 불러오면 새로운 프로젝트를 진행할 준비가 된다.<br>
폴더를 새롭게 생성하는 방법은 두 가지가 있다.<br>
1. 직접 경로에 들어가서 새 폴더를 생성하는 방법
2. 명령창을 이용해서 새 폴더를 생성하는 방법
``` bash
$ mkdir nodejs
```


![Nodejs_New][11]<br>
_* 나는 C: > development > nodejs 경로에 폴더를 새롭게 생성했다._<br><br> -->

기존에는 Node.js로 개발을 할 때,<br>
1. 새로운 폴더를 생성하고
1. app.js 서버를 생성하고
1. views 폴더를 생성하고 …<br>

등등 번거로운 과정을 거쳐야 했다.<br>
_* ~~새로운 것을 배우기도 벅찬데 디렉토리 구조를 파악하는건 너무 어려워!~~_<br><br>
하지만 Express.js 프레임워크를 이용해서 Node.js를 구축할 예정이기 때문에,<br>
입문자가 디렉토리 구조를 어떻게 설계해야 하는 지 고민을 덜어줄 수 있는 명령어가 있다.<br>

```bash
$ npm i -g express-generator
```
[express-generator][11]를 전역으로 설치해주면, 프로젝트 생성의 절반은 끝났다.<br><br>
새로 프로젝트를 만들 경로에 아래 명령어를 입력해주면 새 프로젝트가 설치되는 모습을 볼 수 있다.<br>
```bash
$ express nodejs
```
![Nodejs_Express][12]<br>
- **bin/www**: 코드를 실행할 때 연결될 port 번호.
- **public**: css, html과 같은 정적인 코드.
- **routes**: 라우팅과 관련된 코드.
- **views**: 템플릿. 기본 템플릿은 jade.
- **app.js**: 미들웨어 코드.

<br>
자, 이제 필요한 모듈을 위해 아래 명령어를 입력해보면!<br>
```bash
$ npm i
```
![Nodejs_Npm_Err][15]<br>
_* ~~응. 안돼. 돌아가.~~_<br><br>

순탄하게 될 리가 없지.<br>
오류를 분석해보면,<br>
1. jade 확장자가 pug로 변경되었으니 최신 버전을 설치해라.
1. constantinople을 업데이트해라.
1. transformers 패키지를 더 이상 사용되지 않으니 jstransformer 패키지를 사용해라.<br><br>

#### 4-1. jade를 pug로 변경하기
```bash
$ npm uninstall jade
$ npm install pug
```
명령어를 실행하고, views 파일에 있는 .jade 확장자를 .pug로 변경해준다.<br>
그리고 app.js를 열어서 아래와 같이 변경해준다.<br>
```js
app.set('view engine', 'pug');
```
<br><br>

#### 4-2. constantinople 업데이트
```bash
$ npm update constantinople
```
<br><br>

#### 4-3. transformers 패키지를 jstransformer 패키지로 변경하기
```bash
$ npm uninstall transformers
$ npm install jstransformer
```
<br><br>

모든 설정을 완료하고 `npm i` 명령어를 다시 입력하면 무리없이 설치된다.<br><br>

아래 명령어를 입력하고 서버가 실행되면 **bin/www**에 설정된 포트인 **localhost:3000**로 실행되는 모습을 볼 수 있다.<br>
```bash
$ npm start
```
![Express_Success2][17]<br>
![Express_Success][16]<br>
_* 성공적으로 서버가 실행된 모습이다._

<!-- #### 4_1. npm init

**Ctrl + Shift + `** 단축키를 사용해 터미널을 열고 아래 명령어를 입력한다.<br>
```bash
$ npm init 
```
이것은 Node.js를 실행하기 위한 초기화 과정으로, package.json 파일이 생성된다.<br>
_* 이미 앞의 과정으로 생성이 되어있기 때문에 명령어를 실행하면 기존 package.json에 추가적으로 설정된다._

![Nodejs_Init][13]<br>
_* 여러가지 설정 항목들이 나오는데 ~~귀찮으니까~~ enter 눌러주자._<br><br>

#### 4_2. npm Dependency(의존 패키지) 설치

터미널에 아래 명령어를 입력해서 생성된 프로젝트 내 패키지를 설치한다.<br>
```bash
$ npm i --save express mongoose body-parser
```
- **express**: 웹프레임워크<br>
- **mongoose**: MongoDB 연동 라이브러리<br>
- **body-parser**: 데이터 처리 미들웨어<br>

![Nodejs_Express2][14]<br>
_* 추가적으로 설치된 모습을 볼 수 있다._<br><br><br> -->


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
[11]: https://expressjs.com/en/starter/generator.html
[12]: /assets/img/Nodejs/nodejs_express.png
<!-- [13]: /assets/img/Nodejs/nodejs_init.png -->
<!-- [14]: /assets/img/Nodejs/nodejs_express2.png -->
[15]: /assets/img/Nodejs/nodejs_npm_err.png
[16]: /assets/img/Nodejs/express_success.png
[17]: /assets/img/Nodejs/express_success2.png