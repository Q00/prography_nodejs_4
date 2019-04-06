

### Node.js 스터디 2주차 - Express & Scaffold



- Express - Node js 웹 서버 개발할 때 가장 많이 사용하는 모듈

  ```javascript
  npm install -g express
  express myapp
  ```

  ```
  /myapp
    ⌊ /bin
        ⌊ www
    ⌊ /public
        ⌊ /images
        ⌊ /javascripts
        ⌊ / stylesheets
    ⌊ /routes
        ⌊ index.js //라우팅 설정 
    ⌊ /views
        ⌊ index.jade
    ⌊ app.js    //익스프레스 객체 생성, 환경 설정
    ⌊ package.json
  ```

  기본 골격 생성 



  <routes/index.js>

  ```javascript
  const express = require('express');
  const router = express.Router();
  
  /* GET home page. */
  router.get('/',(req, res, next) => {
    res.render('index', { title: 'Express' });
  });
  
  module.exports = router;
  ```



  <app.js>

```javascript
const express = require('express'); //모듈 추출

const app = express(); //객체를 통해서 서버 생성
const port = 3000;

app.get('/', (req, res, next) => {
    res.send('hello world!');
});
//router 미들웨어, 따로 만들어서도 사용 가능 

app.listen(port, () => {
    console.log(`Server is running at ${port}`);
}); //웹 서버 설정 및 실행 
```

​     request & response 객체 사용 가능 



- 미들웨어 

  expresss 모듈은 request 이벤트 리스너를 연결하는 데 use () 메소드를 사용한다. 

  요청의 응답을 완료하기 전까지 요청 중간중간에 여러 가지 일을 처리할 수 있다. 따라서 use() 메서드의 매개변수에 입력하는 함수



  - router 미들웨어 

    get, post, put,delete,all

  - static 미들웨어 

    지정한 폴더에 있는 내용을 모두 웹 서버 루트 폴더에 올림

  - body parser 미들웨어 

    POST 요청 데이터를 추출하는 미들웨어 

    request 객체에 body 속성이 부여된다 .


- Scaffold - 데이터 베이스를 이용한 프로그램에서 MVC 구조의 CRUD 프로그램의 뼈대를 만들어준다. 



참고 사진 ! 

![express](C:\Users\심정민\Desktop\express.PNG)