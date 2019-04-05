# express scaffolding

![image-20190405165743869](/Users/kyu/Library/Application Support/typora-user-images/image-20190405165743869.png)

- src 는 소스 폴더

  - 그 밑에 시작점인 app.js가 있다. 
  - app. js 의구조

  ```javascript
  const express = require('express')
  const app = express()
  const cors = require('cors')
  const api = require('./api')
  
  app.use(express.urlencoded({extended: false}))
  app.use(cors)
  app.use(api)
  
  //express default app => es 6 모듈화
  module.exports = app
  ```

  - require는 es6에서 import from으로 쓸수있다.
  - require에서 디렉토리를 파라미터로 주게 되면 entry point 인 index.js를 찾는다

- api

```javascript
  import user from 'api/routes/user';
  import book from 'api/routes/book';
  import tag from 'api/routes/tag';
  import auth from 'api/routes/auth';
  import express from 'express';
  import authCheck from '../lib/middleware/authCheck';
  
  const router = express.Router();
 
  router.use('/auth', auth);
  router.use('/auth2',authCheck(), auth);
  router.use('/user',user);
  router.use('/books', book);
  router.use('/tags', tag);

  export default router;
 
```

- index.js
  - 마찬가지로 디렉토리를 가져오게 되면 디렉토리안에 index.js가 존재해야된다.
  - api 안의 index.js 에서 router.use를 선언 ( 라우팅을 해준다.)
  - 미들웨어가 필요한 경우 **uri 경로 / 미들웨어 / 가져올 디렉토리** 이런순서로 파라미터를 적어주면 된다.
  - router를 위에 app.js에서 쓸 수 있도록 export해줌

- routes directory

  - 라우팅 모듈이 들어가 있는 디렉토리

    - 해당 모듈들이 디렉토리로 들어가져있다.
    - Index.js

    ```javascript
    import * as authCtrl from './auth.ctrl'
    import express from 'express'
    const router = express.Router()
    
    router.get('/test1',authCtrl.test1)
    router.post('/register', authCtrl.register)
    router.put('/update', authCtrl.put)
    router.delete('/delete', authCtrl.delete)
    ```

    - module.ctrl.js

    ```javascript
    import sql from 'db/model/smpl.sql.js'
    export const register = async ( req , res) => {
    	try{
        ~~~
    	}catch(err){
        res.json(err)
    	}
    }
    
    ~~~~~~
    /*
    그냥 nodejs에서
    module.exports = {
    	register : register,
    	~~ : ~~,
    	...
    }
    
    exports.register = function(){
     ~~~~
    }
    */
    ```

- database

  - src > db 

    - index.js

    ```javascript
      import mysql from 'mysql'
      import { TIMEOUT } from 'dns';
      let instance
      class Singleton {
          constructor(){
          if (instance) return instance;
      
          instance = mysql.createPool({
              connectionLimit : 1000,
              connectTimeout  : 60 * 60 * 1000,
              aquireTimeout   : 60 * 60 * 1000,
              timeout         : 60 * 60 * 1000,
              host : process.env.DB_HOST,
              user : process.env.DB_USER,
              password: process.env.DB_PW,
              database: process.env.DATABASE,
              port: process.env.PORT
          })
          return instance;
          }
      }
     
     
      export default Singleton
    ```

    - 환경변수? 
      - bash_profile에 등록해놓으면 됨
      - aws lambda 나 elastic beanstalk 같은경우에는 설정이 있을 뿐만 아니라 gui창에 들어가서 직접 설정가능
    - singleton pattern으로 mysql instance 받아옴
    - sql query

    - db>  model
    - smpl.sql.js

    ```javascript
      const escape = require('mysql').escape;
     
      exports.select = (model) => {
        return `
          select *
          from book
          where ${escape(model.where)}
          and column = ${escape(model.column)}
          ${cond1(model)}`
      }
     
      exports.select2 = (model) => {
        return `
          select * from book where isbn = ${escape(model.isbn)}`
      }
     
      exports.transaction = (model) => {
        return `
          insert into sample(
            id,col1,col2
          ) values(
            ${escape(model.id)}
            ,${escape(model.col1)}
            ,${escape(model.col2)}
          )`
      }
     
      exports.select3 = (model) => {
        return `
          select * from sample_rel where sample_id = ${escape(model.sample_id)}`
      }
    ```

- lib

  - middleware

    - 구조 - authCheck.js

    ```javascript
    export default() => (req, res, next) => {
      const token = req.headers.accesstoken
      if ( validate(token)){
        //validate
        return next()
      }else{
        return res.json(401)
      }
    }
    ```

- util 

  - 쓸만한걸 직접 만들었을 때 사용

- config

  - env

    - env.json

      - json형식의 정보

      ```
      {
        dev:"",
        prod:""
      }
      ```

      

  - Index.js

  ```javascript
  import json from 'config/env/env.json';
  const env = process.env.NODE_ENV || 'dev';
  export default json[env]; 
  ```

  

- Web pack 은 구글 검색~
  - 제로초 <https://www.zerocho.com/category/Webpack/post/58aa916d745ca90018e5301d>