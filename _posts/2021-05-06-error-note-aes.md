---
layout: post
title: 에러노트 - Crypto AES 암호화
date: "2021-05-04"
categories: Node.js AES Crypto
---
React, Node를 독학하면서 기본적인 로그인 구현과 CRUD 게시판을 만들어봤다.<br>
기본적인 뼈대를 만들어봤으니, 깊이감이 있는 프로젝트를 위해 Crypto를 이용한 암호화를 구현하기로 했는데 에러 발생.<br>

<br>

[Crypto Documentation][1]<br>
공식문서에 나와있는 로직을 그대로 반영했는데,<br>
암호화, 복호화를 위해서는 동일한 key와 iv를 이용해야 한다고 해서 동일한 값을 부여했는데, 수정된 로직을 반영해도 복호화가 안된다.<br>
~~근데 문제는 최대한 심플하게 로직을 수정했는데도 안됨. 하하.~~<br>

**"에러 기록해서 꼭 해결해야지."** 라는 생각으로 기록한다.<br>

<br><br>

### 1. 암호화: cipher

```javascript
const algorithem = 'aes-192-cbc';
const keyword = 'I am password';
const key = scrypSync(keyword, 'salt', 24);
const iv = Buffer.alloc(16, 0);

const cipher = createCipheriv(algorithm, key, iv);
let password = cipher.update(password, 'utf8', 'base64');
password += cipher.final('base64');
```

<br><br><br>

### 2. 복호화: decipher

```javascript
const algorithem = 'aes-192-cbc';
const keyword = 'I am password';
const key = scrypSync(keyword, 'salt', 24);
const iv = Buffer.alloc(16, 0);

const decipher = createDecipheriv(algorithm, key, iv);
let solvePassword = decipher.update(encrypted, 'base64', 'utf8');
solvePassword += decipher.final('utf8'); 
```

<br><br><br>

### 3. Hello, Error!

```bash
Error: error:0606506D:digital envelope routines:EVP_DecryptFinal_ex:wrong final block length
    at Decipheriv.final (internal/crypto/cipher.js:174:29)
    at C:\development\javascript\reactjs\server\routes\user.js:73:25
    at processTicksAndRejections (internal/process/task_queues.js:93:5) {
  library: 'digital envelope routines',
  function: 'EVP_DecryptFinal_ex',
  reason: 'wrong final block length',
  code: 'ERR_OSSL_EVP_WRONG_FINAL_BLOCK_LENGTH'
}
```

+ base64, hex 사용해봤지만 에러코드 동일.
+ 128, 192, 256 사용해봤지만 에러코드 동일.
<br><br>

동일한 키 사용하면 된다면서...!<br>
_...넌 언제나 정답이 있으니 내가 열심히 공부해볼게..._<br><br><br>

***
<br><br><br>

### 에러해결!

우리가 비밀번호를 그냥 저장하지 않고, 위와 같이 복잡한 과정을 거치는 이유는 무엇보다도 보안을 위해서이지 않을까.<br>
수많은 유저들 중에 동일한 비밀번호를 사용한다고 했을 때,<br>
고정적인 키와 값들로 암호화를 진행하면 결국 암호화된 비밀번호도 동일해지겠지?<br><br>

위의 로직에서 keyword와 iv를 동일하게 설정해줬다고 하더라도,<br>
key가 적용된 함수에 의해서 랜덤값으로 설정되기 때문에 암호화가 된 이후에 복호화가 되지 않았다.<br>
~~너무 심플한 이유...~~<br><br>

에러를 해결해보려고 구글링을 하다보니 암호화 과정이 생각보다도 많이 복잡하다고 느꼈는데 **더 열심히 공부해야지!** 라고 다짐하며 해결한 로직을 남긴다.<br><br><br>

**▶ key와 iv를 회원가입하는 과정에서 데이터베이스에 함께 저장될 수 있도록 로직변경**

```javascript
const algorithm = 'aes-192-cbc';
const keyword = 'I am password';

// 암호화
scrypt(keyword, 'salt', 24, (err, key) => {
  if (err) throw err;
  randomFill(new Uint8Array(16), (err, iv) => {
    if (err) throw err;
    const cipher = createCipheriv(algorithm, key, iv);
    let encrypted = cipher.update(password, 'utf8', 'hex');
      encrypted += cipher.final('hex');
      const createUser = User.create({
        id: req.body.id,
        password: encrypted,
        keyBuf: key,
        ivBuf: Object.values(iv),
        username: req.body.username,
        phone: req.body.phone,
        email: req.body.email,
        address: req.body.address
      }).then(console.log('회원가입 완료'));


// 복호화
const selectUser = await User.findOne({ id: req.body.id });
const password = selectUser.password;
const key = selectUser.keyBuf;
const iv = new Uint8Array(selectUser.ivBuf);
const decipher = createDecipheriv(algorithm, key, iv);
let decrypted = decipher.update(password, 'hex', 'utf8');
decrypted += decipher.final('utf8');
if(req.body.password == decrypted){
  console.log('비밀번호 일치');
  res.cookie.id = selectUser.id;
  res.status(200).json(selectUser.id);
}
```
<br><br><br>

[1]: https://nodejs.org/api/crypto.html
