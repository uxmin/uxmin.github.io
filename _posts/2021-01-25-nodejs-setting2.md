---
layout: post
title: VS Code에서 Node.js 사용하기 2
date: "2021-01-25"
categories: VSCode Node.js Express MongoDB
---
이전 포스트에 이어서 VS Code에서 Node.js를 위한 환경을 구축하기 위한 과정을 담을 것이다.<br>
이 포스트에서는 이전 포스트에서 구축한 프로젝트를 기반으로 MongoDB를 연결하는 과정을 다룬다.<br>

<br><br>

### 1. MongoDB 기초 설정 및 계정 생성

#### 1-1. 기초 설정

지난 포스트에서 MongoDB를 설치하는 과정을 담았다.<br>
MongoDB 서버가 제대로 작동하기 위해서는 기초적인 설정이 필요한데, 다음 과정을 거치면 서버를 실행할 수 있다.<br>
기본적으로 서버를 실행하기 위해서 아래와 같은 명령어를 입력해야 하는데,<br>
```bash
$ mongod
```
입력하면 서버가 실행되지 않는 문제가 발생한다.<br>
![Mongod_Error][2]<br>
_* Windows 환경에서 MongoDB는 기본적으로 path를 C:\data\db로 설정해두는데 대충 폴더를 찾을 수 없다는 내용._<br><br>

`mkdir` 명령어를 사용하여 폴더를 생성한 후, 다시 위 명령어를 입력하면 MongoDB 서버가 실행되는 모습을 볼 수 있다.<br>
주소창에 **localhost:27017**을 입력하면 _It looks like you are trying to access MongoDB over HTTP on the native driver port._ 문장이 출력되어 있는 것을 확인할 수 있다.<br><br><br>


#### 1-2. 계정 생성

[MongoDB Built in Roles][1]<br>
우리가 알고 있는 Oracle, MySQL 등 데이터베이스를 관리하는 프로그램들은 외부 공격에 노출되는 것을 방지하고자 기초적인 보안 기능을 가지고 있다.<br><br>
MongoDB도 마찬가지로 보안 기능을 제공하고 있는데, MySQL의 경우 설치를 하면 초기에 root 계정을 생성하는 과정이 있는 것에 비해 MongoDB는 별다른 계정 과정이 없어서 직접 계정을 등록해주어야 한다.<br><br>
Mongo를 실행해서 root 계정을 생성해보자.<br>
```bash
use admin
db.createUser({
    user: '이름',
    pwd: '비밀번호',
    roles: ['root']
})
```
![MongoDB_Root][3]<br>
_* 성공적으로 root 계정이 만들어진 모습._<br><br>

아래 명령어를 입력해서 로그인을 할 수 있으니 참고하자.<br>
```bash
$ mongo admin -u 이름 -p 비밀번호
```
<br><br><br>

### 2. Mongoose를 이용한 Node.js와 MongoDB 연결

#### 2-1. Mongoose란?

[Sequelize ORM][4]<br>
Sequelize는 자바스크립트 객체와 데이터베이스 릴레이션을 연결해주는 ORM(Object-relational Mapping)이다.<br>
Sequelize를 사용하게 되면 직접적으로 SQL를 작성할 필요없이 Javascript 구문을 자동적으로 변경해주기 때문에 MySQL을 다룰때 쉽게 조작할 수 있다.<br><br>

[Mongoose ODM][5]<br>
Mongoose도 이와 유사하다.<br>
하지만 MongoDB는 릴레이션이 아니라 Document를 사용하기 때문에 ODM(Object Document Mapping)이라고 한다.<br>
MongoDB는 Javascript 기반인데 Mongoose를 사용하는 이유는,<br>

1. **MongoDB는 Schema가 없음.** <br>
기존 SQL에는 정해진 테이블이 있기 때문에 규칙에 어긋나는 데이터가 Insert되려고 하면 에러를 반환하는데,<br>
MongoDB는 테이블이 없기 때문에 어떤 데이터를 Insert를 해도 에러를 반환하지 않고 Insert가 된다.<br>
자유롭다는 장점과 함께, 잘못된 데이터가 들어갈 수 있다는 단점이 있다.<br>
이걸 막고자 Mongoose에서는 사용자가 작성한 스키마를 기준으로 데이터를 DB에 Insert되기 전에 검사할 수 있도록 스키마를 부활시켰다.<br>

1. **MongoDB는 Join을 지원하지 않음.**<br>
MongoDB를 포함한 NoSQL은 기본적으로 join 기능을 지원하지 않는다.<br>
그래서 관계된 데이터를 추출하기 위해서는 최소 두 개의 쿼리가 필요하다.<br>
대규모 DB 작업환경에서 join 없이 데이터를 추출하게 되면 쿼리가 늘어나면서 비효율적일 수 있다.<br>
Mongoose는 이를 보완하기 위해서 populate라는 메서드로 join 기능을 대신한다.<br>

정도가 되겠다.<br><br>

Mongoose는 아래 명령어를 입력하면 설치된다.<br>
```bash
$ npm i mongoose
```
<br><br>

#### 2-2. MongoDB 연결

MongoDB는 주소를 이용해서 연결한다.<br>
_* 기본 형식: **mongodb://이름:비밀번호@host:포트번호/DB**_<br><br>
나는 admin DB에 저장되어 있는 mongo라는 root계정을 이용해서 기본 localhost:27017 포트를 사용할 것이기 때문에,<br>
**mongodb://mongo:mongo@localhost:27017/admin**<br>
주소를 이용해서 연결을 할 예정이다.<br><br>

root 경로에 schemas 폴더를 생성해준 뒤, index.js를 작성하자.<br>
```javascript
// schemas/index.js

const mongoose = require('mongoose');

const connect = () => {
    // 개발 환경일 때 콘솔창에 Mongoose의 쿼리 내용을 확인할 수 있음.
    if (process.env.NODE_ENV !== 'production') {
        mongoose.set('debug', true);
    }

    // MongoDB 연결. DB 접속을 시도하는 것은 계정 DB인 admin, 실제 사용할 DB는 nodejs!
    mongoose.connect('mongodb://mongo:mongo@localhost:27017/admin', {
        dbName: 'nodejs',
        useNewUrlParser: true,
        useCreateIndex: true
    }, (err) => {
        if(err){
            console.log('MongoDB Connected: Error', err);
        }else {
            console.log('MongoDB Connected: Success');
        }
    });
};


// 이벤트 리스너. 에러 발생 시 에러 기록하거나 연결 종료 시 다시 재연결.
mongoose.connection.on('error', (err) => {
    console.log('MongoDB Connected: Error', err);
});
mongoose.connection.on('disconnected', () => {
    console.log('MongoDB connected lost. Will be reconnected MongoDB.');
    connect();
});

module.exports = connect;
```
<br><br>

app.js에서 위에서 작성한 module을 require해준다.<br>
```javascript
// app.js

// MongoDB 연결
const connect = require('./schemas/index');

// view engine setup
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'pug');
connect();
```
<br><br>

그리고 user라는 스키마를 생성하는 파일을 만든다.<br>
```javascript
// schemas/user.js

const mongoose = require('mongoose');

const { Schema } = mongoose;
const userSchema = new Schema({
    email: {
        type: String,
        required: true,
        unique: true
    },
    name: {
        type: String,
        required: true
    },
    password: {
        type: String,
        required: true
    },
    birth: String,
    join: {
        type: Date,
        default: Date.now
    }
});

// model 메서드로 userSchema와 'User' 컬렉션 연결
module.exports = mongoose.model('User', userSchema);
```
- `required`: 필수값
- `unique`: 고유값
- `Date.now`: 데이터가 생성될 때 시간

_* 나의 경우 이메일, 이름, 비밀번호, 생년월일, 가입일 로 설정했다._<br><br>

MongoDB가 연결이 되었는지 Insert를 통해 확인하려면 기본적인 view가 필요하다.<br><br>

**views/index.pug**<br>
```html
extends layout

block content
  h1= title
  p Welcome to #{title}
  a(href='/signup') Go to Sign Up
```
<br>

**veiws/signup.pug**<br>
```html
doctype html
html(lang='en')
    head
        title SignUp
    body
        h4 Sign Up
        form(name='sign' action='/signup' method='post')
            | 아이디: 
            input(type='text' name='email' placeholder='email')
            br
            | 비밀번호: 
            input(type='password' name='password' placeholder='password')
            br
            | 이름: 
            input(type='text' name='name' placeholder='name')
            br
            | 생일: 
            input(type='date' name='birth')
            br
            input(type='submit')
```
<br><br>

app.js 미들웨어를 거쳐 routes 폴더 내 index.js 라우터에 Insert 과정이 수행되도록 아래 코드를 작성한다.<br>
```javascript
// routes/index.js

const cookieParser = require('cookie-parser');
var express = require('express');
var router = express.Router();
var User = require('../schemas/user.js');

// index.pug
router.get('/', (req, res, next) => {
  res.render('index');
});

router.route('/signup')
  // signup.pug
  .get(async (req, res, next) => {
    res.render('signup');
  })
  // signup.pug 에서 전달받은 post
  .post(async (req, res, next) => {
    try {
      const user = await User.create({
        email: req.body.email,
        name: req.body.name,
        password: req.body.password,
        birth: req.body.birth
      });
      console.log(user);
      res.status(201).json(user);
    } catch (error) {
      console.error(error);
      next(error);
    }
  });

module.exports = router;
```
<br><br>

`npm start`로 서버를 실행한 후, view 페이지에서 과정을 수행하면,<br>
![Mongoose_Success][6]<br>
MongoDB가 연결되었다는 메세지와 함께, 성공적으로 데이터가 생성되며 json형태로 리턴된 모습을 확인할 수 있다.<br>
![Mongoose_Json][7]<br><br>

![Mongoose_Check][8]<br>
_* nodejs Database 밑 users collection이 생성되고 데이터가 성공적으로 insert된 모습._


[1]: https://docs.mongodb.com/manual/reference/built-in-roles/
[2]: /assets/img/Nodejs/mongod_error.png
[3]: /assets/img/Nodejs/mongodb_root.png
[4]: https://sequelize.org/
[5]: https://mongoosejs.com/
[6]: /assets/img/Nodejs/mongoose_create_success.png
[7]: /assets/img/Nodejs/mongoose_json.png
[8]: /assets/img/Nodejs/mongoose_final.png