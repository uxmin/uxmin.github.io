---
layout: post
title: React와 Node.js 연동하기
date: "2021-01-30"
categories: React.js Node.js VSCode
---
Express로 프로젝트를 생성하게 되면 pug 템플릿을 이용하게 된다.<br>
pug를 이용하게 되면 코드가 굉장히 간결해지고 코드 중간에 javascript 코드를 삽입하기 쉬워 연산을 신속히 처리할 수 있다는 장점이 있다.<br>
혼자 개발을 한다고 하면 pug가 코드가 매우 간단해지기 때문에 향상성이 좋겠지만, 협업을 하면서 개발을 하게 되면 이 낯선 pug를 이용하기 어려울 수 있다.<br>
그래서 보통 협업을 할 때는 html 문법을 사용할 수 있으면서, 연산을 쉽게 처리할 수 있는 ejs 템플릿을 사용한다.<br>

<br>
그런데 개인적으로 ejs 템플릿은 javascript 코드를 사용하려면 html 문법 내 `<% %>` 혹은 `<%= %>`이 코드를 ~~그것도 한줄마다~~ 작성해야 해서 굉장히 가독성이 떨어지지 않나 생각을 한다.<br>
그래서 이번 포스트에서는 MVC 패턴에서 view를 pug가 아닌 요즘 **굉장히** 뜨고있는 React.js를 이용해 연동해 볼 예정이다.<br>

<br><br>

### 1. React 프로젝트 생성

React를 이용하려면 npm을 이용해 설치해야 하기 때문에, 우선적으로 Node.js가 설치되어 있어야 한다.<br>
[Node.js 설치하기][1]<br>

Node.js가 설치되어 있다면 root 경로에 create-react-app을 설치한다.<br>
```bash
$ npm i -g create-react-app
```
전역으로 설치를 해주고 나면, 이를 이용해 react 프로젝트를 생성할 수 있다.<br><br>

새로운 프로젝트를 생성한다.<br>
```bash
$ create-react-app 프로젝트명
```
새로운 프로젝트가 생성되면서 모듈 설치가 끝나면, `npm start` 명령어로 확인해보자.<br>
참고로 React의 기본 포트번호는 3000번이다.<br><br>

![React_Success_Terminal][2]<br>
![React_Success][3]<br>
_* 성공적으로 실행이 됐다는 콘솔창과 함께 실행된 모습._

<br><br><br>

### 2. Node.js Express 구축하기

react 기본 프로젝트 구조는 이렇게 구성되어 있다.<br>
![React_Folder][4]<br>
- **public**: 정적 파일. html, image 등.<br>
- **src**: Source. 개발의 대부분은 이 디렉토리에서 작업됨.<br>

우리는 이와 같은 레벨의 server 디렉토리를 생성해서, 서버를 구축할 예정이다.<br>
![React_Server][5]<br><br>

```javascript
// app.js
const express = require('express');
const app = express();
const port = process.env.PORT || 3001;

const indexRouter = require('./routes/index');

app.use(indexRouter);

app.listen(port, ()=> {
    console.log(`Server is running on ${port}`);
});
```
Express 기본 포트번호는 3000번인데 React가 3000번의 포트번호를 이미 사용하고 있기 때문에 3001번으로 설정해주었다.<br><br>

```javascript
// routes/index.js
const express = require('express');
const router = express.Router();

router.get('/', (req, res, next) => {
    res.end('index router');
});

module.exports = router;
```
<br>

코드를 완성하고 아래 명령어를 입력해서 app.js 서버를 실행시켜보면,<br>
```bash
$ node server/app.js
```
3001번으로 서버가 실행되면서 아래 창이 뜨는 것을 확인할 수 있다.<br>
![Index_Router][6]<br>
_* 기존의 localhost:3000은 React에서 사용중이어서 `npm start`를 실행하면 상단의 react 페이지가 뜬다._<br><br><br>

### 3. React와 Express 연동

React 서버는 3000번으로, Express 서버는 3001번으로 구축을 했다.
즉, 클라이언트 사이드인 React와 서버 사이드인 Express, 총 두 개의 서버가 존재한다는 것이다.<br><br>

포트번호가 다른 두 서버가 문제없이 실행되면 좋겠지만 그렇지 못하다.<br>
- CORS 이슈?<br>
클라이언트와 서버의 포트가 다른 상황에서 클라이언트 측에서 서버 측으로 요청을 했을 때, 브라우저가 보안상의 이유로 요청을 차단하는 문제.<br>
_ex. 3000번에서 3001번으로 요청을 보내면 차단됨._<br>

그래서 두 서버를 연동을 하기 위해서는 Proxy를 사용해야 한다.<br>
![Proxy][7]<br><br>

아래 명령어를 이용해서 Proxy를 설치해주자.<br>
```bash
$ npm i http-proxy-middleware
```
_* 서버 사이드에서 JSON 형식으로 값을 넘길 예정이기 때문에 추가로 `npm i body-parser`를 이용해 추가로 설치해주자._<br><br>

그리고 src 폴더에 setupProxy.js 파일을 생성한다.<br>
```javascript
// src/setupProxy.js

const proxy = require('http-proxy-middleware');

module.exports = (app) => {
    app.use(
        proxy.createProxyMiddleware('/user', {
            target: 'http://localhost:3001'
        })
    )
}
```
<br>

이렇게 클라이언트 사이드와 서버 사이드를 Proxy로 연결해준 후,<br>
서버 사이드에서 JSON 형식의 데이터를 프록시를 통해 클라이언트 사이드로 보내보겠다.<br>

```javascript
// server/app.js

const express = require('express');
const app = express();
const port = process.env.PORT || 3001;

const cors = require('cors'); 
const bodyParser = require('body-parser');

const indexRouter = require('./routes/index');
const userRouter = require('./routes/user');

app.use(cors());
app.use(bodyParser.json());

app.use(indexRouter);
app.use('/user', userRouter);

app.listen(port, ()=> {
    console.log(`Server is running on ${port}`);
});
```
_* 위에 언급했던 CORS 이슈를 해결하기 위해서 추가로 `npm i cors` 명령어로 cors 설치 후 서버 사이드에 적용해주었다._<br><br>

클라이언트 사이드로 데이터를 보내기 위해서 서버 측에 user 라우터를 생성하고, 데이터를 전달하겠다.<br>
```javascript
// routes/user.js

const express = require('express');
const router = express.Router();

router.get('/', (req, res, next) => {
    res.send({username: 'Kim'});
});

module.exports = router;
```
<br>

서버에서 데이터를 보냈으니, 클라이언트 측에서 값을 받기 위해서 App.js를 수정해줄 것이다.<br>
```javascript
// src/App.js

import React from 'react';
import logo from './logo.svg';
import './App.css';

class App extends React.Component {
    constructor(props){
      super(props);
      this.state = {
        username: null
      };
    }
    
    componentDidMount() {
      fetch('http://localhost:3001/user')
        .then(res => res.json())
        .then(data => this.setState({username: data.username}));
    }

    render() {
      return (
        <div className="App">
          <header className="App-header">
            <img src={logo} className="App-logo" alt="logo" />
            <div>
              {this.state.username? `Hello ${this.state.username}`:'Hello World'}
            </div>
          </header>
        </div>
      );
    }
}

export default App;
```
<br>

모든 코드를 작성한 후 서버 측과 클라이언트 측 서버를 실행하면,<br>
![React_Json_Server][8]<br>
이렇게 **서버**에서 /user 설정해준 라우터 경로로 json 값이 출력이 되는 모습과,<br><br>

![React_Json_Success][9]<br>
기존경로의 **클라이언트**에서 Kim이라는 값을 서버에서 받아와 브라우저에 출력되는 모습을 확인할 수 있다.<br><br>

이렇게 클라이언트 사이드인 React.js와 서버 사이드인 Node.js를 성공적으로 연동해봤는데,<br>
지난 포스트를 토대로 MongoDB까지 연동하면 질적인 프로젝트를 만들 수 있겠다.<br><br><br>

#### npm start + node server/app.js

연동을 하며 서버 사이드를 실행하고, 클라이언트 사이드를 실행을 각각 진행하니 터미널 창도 복잡하고 힘들다면,<br>
약간의 설정으로 양측의 서버를 동시에 켤 수 있다.<br>
```json
// package.json

"scripts" {
    "start": "npm-run-all --parallel start:**",
    "start:client": "react-scripts start",
    "start:server": "node ./server/app.js",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
}
```
<br><br>

[1]: https://uxmin.github.io/vscode/node.js/express/mongodb/2021/01/19/nodejs-setting.html
[2]: /assets/img/Reactjs/react_success_terminal.png
[3]: /assets/img/Reactjs/react_success.png
[4]: /assets/img/Reactjs/react_folder.png
[5]: /assets/img/Reactjs/react_server.png
[6]: /assets/img/Reactjs/index_router.png
[7]: /assets/img/Reactjs/proxy.png
[8]: /assets/img/Reactjs/react_json_server.png
[9]: /assets/img/Reactjs/react_json_success.png