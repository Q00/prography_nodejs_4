# Express Scaffolding

글쓴이: 정예은

---

#### Express 디렉터리 만들기까지의 과정

1. `npm init`

   해당 폴더에 package.json 파일을 생성한다.

2. `npm install`

   의존 패키지들을 설치

3. `npm install express-generator`

   express 명령어를 쓰기 위해 설치

4. `express example(폴더 이름)`

   express 디렉터리 생성 완료!



#### package.json 파일

```javascript
{
  "name": "express-test",
  "version": "0.0.0",
  "private": true,
  "scripts": {
    "start": "node ./bin/www"
  },
  "dependencies": {
    "cookie-parser": "~1.4.3",
    "debug": "~2.6.9",
    "express": "~4.16.0",
    "http-errors": "~1.6.2",
    "jade": "~1.11.0",
    "morgan": "~1.9.0"
  }
}
```

프로젝트 이름, 버전, 의존패키지 리스트에 대한 정보를 담고 있음



#### 디렉터리 구조

```
express_tutorial/
├── package.json
├── public
│   └── css
│   └── style.css
├── router
│   └── main.js
├── server.js
└── views
 ├── about.html
 └── index.html
```

* /bin/www : 코드를 실행할 때 연결될 port 번호를 담고 있다.
* public : css, html과 같은 정적인 코드
* routes : 라우팅과 관련된 코드
* views : 템플릿을 담고 있다. 기본 템플릿은 jade
* app.js : 미들웨어들을 담고 있는 코드



#### 내부 환경 변수 보관 파일 .env

사용자의 작업 환경 변수를 .env 파일에 저장하여 불러오는 식으로 사용한다.

이 때 .env 파일을 .gitignore에 포함시켜 git에 올리지 않도록 주의한다.

~~프로그라피 홈페이지 DB 비밀번호를 이걸로 숨겼어야 했구나ㅠㅠ~~



`npm install --save dotenv`

우선 dotenv 모듈을 설치해야 한다.

그 후 .env 파일을 생성하여 아래와 같이 DB 접속 정보를 입력한다.

```
DB_NAME=DotENV
DB_USER=root
DB_PASS=1234
DB_HOST=localhost
```

```
require('dotenv').config()

const pool = mysql.createPool({
  host: process.env.DB_HOST,
  user: process.env.DB_USER,
  password: process.env.DB_PASS,
  database: process.env.DB_NAME
})
```

`process.env.example`  방식으로 환경 변수를 불러와서 사용할 수 있다.



#### Singleton

singleton pattern은 인스턴스가 사용될 때에 똑같은 인스턴스를 만들어 내는 것이 아니라, 동일 인스턴스를 사용하게끔 하는 것이다.

주로 공통된 객체를 여러 개 생성해서 사용하는 DBCP(Database Connecion Pool)와 같은 상황에서 많이 사용한다.

```javascript

const mysql2 = require('mysql2') // 정확히 어떤 거였는지 생각이 안 남
let pool

module.exports = {
    getPool: () => {
      if (pool) return pool
      pool = mysql.createPool({
          host: process.env.DB_HOST,
          user: process.env.DB_USER,
          password: process.env.DB_PASS,
          database: process.env.DB_NAME
      })
      return pool;
    }
};

```

불러오기는 아래와 같다.

```javascript
const db = require('./app/util/database.js')
var pool = db.getPool(); // re-uses existing if already created or creates new one
pool.getConnection(function(err, connection) {
   // don't forget to check error
   connection.query('select 1+1', function(err, rows) {
     // don't forget to check error
     // ...
     // use your data - response from mysql is in rows    
   });
});
```

~~꼭 실제로 해볼 것~~



#### exports: 모듈 추출하기

* **모듈이란 무엇인가?**

  관련된 코드들을 하나의 코드 단위로 캡슐화 하는 것

  EX. `greeting.js`

  ```javascript
  // greeting.js
  sayHelloInEnglish = () => {
      return "Hello"
  }
  
  sayHelloInSpanish = () => {
      return "Hola"
  }
  ```

  

* **모듈 추출하기 (exporting)**

  `greeting.js`의 코드가 다른 파일에서 사용될 때 그 효용성이 증가할 것이다. 이를 위해 추출을 해보자.

  1. `greeting.js` 첫 부분에 다음과 같은 코드를 넣는다.

     ```javascript
     // greeting.js
     const exports = module.exports = {}
     ```

  2. 다른 파일에서 exports 객체를 사용하기를 원한다면 `greeting.js` 파일에서 다음과 같이 작성해야 한다.

     ```javascript
     // greetings.js
     exports.sayHelloInEnglish = () => {
         return "Hello"
     }
     
     exports.sayHelloInSpanish = () => {
         return "Hola"
     }
     ```

     * 1번과 2번을 합쳐서

       `module.exports.sayHelloInEnglish = () => {` 

       이런 식으로 많이 쓰는 것 같다.

  3. `module.exports`의 현재 값은 다음과 같다.

     ```javascript
     module.exports = {
         sayHelloInEnglish : () => {
             return "Hello"
         },
         
         sayHelloInSpanish: () => {
             return "Hola"
         }
     }
     ```

  

* **모듈 사용하기 (importing)**

  `main,js`라는 새로운 파일에서 `greeting.js`의 메소드를 사용할 수 있도록 import 해보자.

  모듈을 import 할 때 쓰이는 require 키워드를 사용한다. require 키워드는 object를 반환한다

  ```javascript
  // main.js
  const greeting = require('./greeting.js')
  
  // "Hello"
  greetings.sayHelloInEnglish()
  
  // "Hola"
  greetings.sayHelloInSpanish()
  ```



* **중요한 포인트**

  module.exports와 exports는 동일한 객체를 바라보고 있고, 리턴되는 값은 항상 <u>module.exports</u>이다.

  EX)

  ```javascript
  const express = require('express')
  let router = express.Router()
  
  router.get('/', (req, res) => {
      res.render('index', {title: 'Express'})
  })
  
  module.exports = router
  ```

  express.Router()가 리턴한 객체에 일부 프로퍼티를 수정한 뒤, 이 객체 자체를 모듈로 리턴한 셈이다.

  

* 출처: <https://uroa.tistory.com/57> [Node.js]  module.exports 와 exports 이해하기

