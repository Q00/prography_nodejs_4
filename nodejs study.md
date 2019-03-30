````js
var _this = this
$('.btn').click(function(event){
  _this.sendData()
})
````

~~~js
$('.btn').click((event) => {
  this.sendData()
})
~~~

```javascript
const people ={

Birthdate : '0327'

age : '19'

sex: 'man'

}

const { birthdate,, sex} = people
const {username, password} = req.body
const [line1, line2, line3, , line5] = file.split('\n')
```

```js
function q (){
  let a = 2
  {
    let a = 5
  }
  console.log(a)
}
q() => // result is 2
```



```javascript
let p1 = new Promise(function(resolve, reject) {
    resolve(42);
});

let p2 = new Promise(function(resolve, reject) {
    resolve(43);
});

let p3 = p1.then(function(value) {
    // first fulfillment handler
    console.log(value);     // 42
    return p2;
});

p3.then(function(value) {
    // second fulfillment handler
    console.log(value);     // 43
});
```

- p3랑 p2랑 같다고 볼 수 있을끼ㅣㅏ
- 프로미스에서 성공결과를 보장한다면!

~~~javascript
let p2 = p1.then(() => {
  throw new Error('lol')
})
// p2 was rejected with Error('lol')

let p3 = p1.then(() => {
  return 42
})
// p3 was fulfilled with 42
~~~

- 사실은 실제로 성공할지 안할지는 몰라
- 같다는 생각은!
- **“I want p3 to resolve to whatever p2 resolves, whether it succeeds or fails”** => X
- p3(resolved promise) 와 p2(resolved value) 
  - `return`-ing an another promise
- p3 !==p2

```js
return checkCache().then(cachedValue => {
  if (cachedValue) {
    return cachedValue
  }

  fetchData().then(fetchedValue => {
    // Doesn’t make sense: it’s too late to return from outer function by now.
    // What do we do?

    // return fetchedValue
  })
})
```

캐시된 밸류가 있을 경우 프로미스를 생성후 비동기 작업대신 바로 반환하게 된다.
프로미스가 유용하지 않다는 말이다.

- pseudo code - then

```js
then(callback) {
  // Save these so we can manipulate
  // the returned Promise when we are ready
  let resolve, reject

  // Imagine this._onFulfilled is an internal
  // queue of code to run after current Promise resolves.
  this._onFulfilled.push(() => {
    let result, error, succeeded
    try {
      // Call your callback!
      result = callback(this._result)
      succeeded = true
    } catch (err) {
      error = err
      succeeded = false
    }

    if (succeeded) {
      // If your callback returned a value,
      // fulfill the returned Promise to it
      resolve(result)
    } else {
      // If your callback threw an error,
      // reject the returned Promise with it
      reject(error)
    }
  })

  // then() returns a Promise
  return new Promise((_resolve, _reject) => {
    resolve = _resolve
    reject = _reject
  })
}
```

- promise를 리턴하는경우

````js
    if (succeeded) {
      // If your callback returned a value,
      // resolve the returned Promise to it...
      if (typeof result.then === 'function') {
        // ...unless it is a Promise itself,
        // in which case we just pass our internal
        // resolve and reject to then() of that Promise
        result.then(resolve, reject)
      } else {
        resolve(result)
      }
    } else {
      // If your callback threw an error,
      // reject the returned Promise with it
      reject(error)
    }
  })
````

- async await

```js

function wait(time) {
  return new Promise(function(res, rej) {
    setTimeout(() => {
      // 파라미터로 넘어온 시간 만큼 이후에 'ok'라는 값을 응답합니다.
      res('ok');
    }, time);
  });
}
 
async function series() {
  const wait1 = await wait(500);
  const wait2 = await wait(500);
  return "done!";
}
 
async function parallel() {
  const wait1 = wait(500);
  const wait2 = wait(500);
  const responseWait1 = await wait1;
  const responseWait2 = await wait2;
  return "done!";
}
```

- series와 parallel 의 차이점?

```js
function getData() {
    return promise1()
        .then(response => {
            return response;
        })
        .then(response2 => {
            return response2;
        })
        .catch(err => {
            //TODO: error handling
            // 에러가 어디서 발생했는지 알기 어렵다.
        });
}

// async / await 연속 호출
async function getData() {
  try{
    const response = await promise1();
    const response2 = await promise2(response);
    return response2;
  }catch(err){
    console.log(err)
  }
```

