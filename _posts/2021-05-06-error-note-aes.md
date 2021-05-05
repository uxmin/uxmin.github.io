---
layout: post
title: [에러노트] Crypto AES 암호화
date: "2021-05-04"
categories: Node.js AES Crypto
---
React, Node를 독학하면서 기본적인 로그인 구현과 CRUD 게시판을 만들어봤다.<br>
기본적인 뼈대를 만들어봤으니, 깊이감이 있는 프로젝트를 위해 Crypto를 이용한 암호화를 구현하기로 했는데 난관에 봉착했다...<br>

<br>

[Crypto Documentation][1]<br>
공식문서에 나와있는 로직을 그대로 반영했는데,<br>
암호화, 복호화를 위해서는 동일한 key와 iv를 이용해야 한다고 해서 로직반영을 해도 복호화가 안됐나보다.<br>
~~근데 문제는 최대한 심플하게 로직을 수정했는데도 안됨.~~<br>

**에러 기록해서 꼭 해결해야지.** 라는 생각으로 기록한다.<br>

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

### 3. 에러

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
<br>
+ base64, hex 사용해봤지만 에러코드 동일.
+ 128, 192, 256 사용해봤지만 에러코드 동일.
<br>

동일한 키 사용하면 된다면서...!<br>
...넌 언제나 정답이 있으니 내가 열심히 공부해볼게...<br><br><br>

[1]: https://nodejs.org/api/crypto.html
