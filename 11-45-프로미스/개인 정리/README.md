# 45장 프로미스 (Promise)

- 콜백 헬로 인해 가독성이 나쁘고 에러 처리가 곤란한 기곤 코드를 대체하기 위하여 ES6에서는 비동기 처리를 위한 또 다른 패턴으로 프로미스를 도입했다.
- 프로미스는 전통적인 콜백 패턴이 가진 단점을 보완하여 비동기 처리 시점을 명확하게 표현할 수 있다는 장점이 있다.

## 01. 비동기 처리를 위한 콜백 패턴의 단점

### 01-01. 콜백 헬

- 비동기 함수를 호출하면 함수 내부의 비동기로 동작하는 코드가 완료되지 않았다 해도 기다리지 않고 즉시 종료된다.
  - 즉, 비동기 함수 내부의 비동기로 동작하는 코드는 비동기 함수가 종료된 이후에 완료된다.
- 따라서 비동기 함수 내부의 비동기로 동작하는 코드에서 처리 결과를 외부로 반환하거나 상위 스코프의 변수에 할당하면 기대한 대로 동작하지 않는다.

##### 1. get 함수 작성

```js
// GET 요청을 위한 비동기 함수
const get = (url) => {
  const xhr = new XMLHttpRequest();
  xhr.open("GET", url);
  xhr.send();

  xhr.onload = () => {
    if (xhr.status === 200) {
      // 서버의 응답을 콘솔에 출력한다.
      console.log(JSON.parse(xhr.response));
    } else {
      console.error(`${xhr.status} ${xhr.statusText}`);
    }
  };
};

// id가 1인 post를 취득
get("https://jsonplaceholder.tyricode.com/posts/1");
/*
{
  "userId": 1,
  "id": 1,
  "title": "sunt aut facere ... ",
  "body": "quia et suscipit ... "
}
*/
```

- 위 예제에서 get 함수는 비동기 함수다.
- 비동기 함수를 호출하면 함수 내부의 비동기로 동작하는 코드가 완료되지 않았다 해도 기다리지 않고 즉시 종료된다.
- 즉, 비동기 함수 내부의 비동기로 동작하는 코드는 비동기 함수가 종료된 이후에 완료된다.
- 따라서 비동기 함수 내부의 비동기로 동작하는 코드에서 처리 결과를 외부러 반환하거나 상위 스코프의 변수에 할당하면 기대한 대로 동작하지 않는다.
- get 함수가 비동기 함수인 이유는 get 함수 내부의 `onload`이벤트 핸들러가 비동기로 동작하기 때문이다.
  - get 함수를 호출하면 GET 요청을 전송하고 `onload`이벤트 핸들러를 등록한 다음 `undefined`를 반환하고 즉시 종료된다.
  - 그리고 비동기 함수인 get함수가 종료된 이후에 `onload`이벤트 핸들러가 실행된다.
  - 따라서 get 함수의 `onload`이벤트 핸들러에서 서버의 응답 결과를 반환하거나 상위 스코프의 변수에 할당하면 기대한 대로 동작하지 않는다.

##### 2. get 함수 서버의 응답 결과를 반환하도록 수정

```js
// GET 요청을 위한 비동기 함수
const get = (url) => {
  const xhr = new XMLHttprequest();
  xhr.open("GET", url);
  xhr.send();

  xhr.onload = () => {
    if (xhr.status === 200) {
      // 1. 서버의 응답을 반환한다.
      return JSON.parse(xhr.response);
    }
    console.error(`${xhr.status} ${xhr.statusText}`);
  };
};

// 2. id가 1만 post를 취득
const response = get("https://jsonplaceholder.tyricode.com/posts/1");
console.log(response); // undefined
```

- `xhr.onload`이벤트 핸들러 프로퍼티에 바인딩한 이벤트 핸들어의 반환문(1)은 get 함수의 반환문이 아니다.
  - get 함수는 반환문이 생략되었으므로 암묵적으로 `undefined`를 반환한다.
- 함수의 반환값은 명시적으로 호출한 다음에 캐치할 수 있으므로 `onload`이벤트 핸들러를 get 함수가 호출할 수 있다면 이벤트 핸들러의 반환값을 get함수가 캐치하여 다시 환환할 수 있겠지만 `onload`이벤트 핸들러는 get 함수가 호출하지 않기 때문에 그럴 수도 없다.
- 따라서 `onload`이벤트 핸들러의 반환값은 캐치할 수 없다.

##### 3. 서버의 응답을 상위 스코프의 변수에 할당

```js
let todos;

// GET 요청을 위한 비동기 함수
const get = (url) => {
  const xhr = new XMLHttprequest();
  xhr.open("GET", url);
  xhr.send();

  xhr.onload = () => {
    if (xhr.status === 200) {
      // 1. 서버의 응답을 반환한다.
      todos = JSON.parse(xhr.response);
    }
    console.error(`${xhr.status} ${xhr.statusText}`);
  };
};

// 2. id가 1만 post를 취득
const response = get("https://jsonplaceholder.tyricode.com/posts/1");
console.log(response); // undefined
```

- 이 또한 의도한 대로 동작하지 않는다.
- `xhr.onload` 이벤트 핸들러 프로퍼티에 바인딩한 이벤트 핸들러는 언제나 (2)의 `console.log`가 종료한 이후에 호출된다.
  - (2)의 시점에는 아직 전역 변수 todos에 서버의 응답 결과가 할당되기 이전이다.
  - 다시말해 서버의 응답을 상위 스코프의 변수에 할당해도 처리 순서가 보장되지 않는다.
- 의도한 대로 동작하지 않는 이유를 살펴보자
  - 비동기 함수 get이 호출되면 함수 코드를 평가하는 과정에서 get 함수의 실행 컨텍스트가 생성되고 실행 컨택스트 스택(콜 스택)에 push된다.
  - 이후 함수 코드 실행 과정에서 `xhr.onload`이벤트 핸들러 프로퍼티에 이벤트 핸들러가 바인딩된다.
  - get 함수가 종료하면 get 함수의 실행 컨텍스트가 콜 스택에서 pop되고, 곧바로 `console.log`가 호출된다.
    - 이때 `console.log`의 실행 컨텍스트가 생성되어 콜 스택에 push된다.
    - 만약 `console.log`가 호출되기 직전에 `load`이벤트가 발생했더라도 `xhr.onload`이벤트 핸들러 프로퍼티에 바인딩한 이벤트 핸들러는 결코 `console.log`보다 먼저 실행되지 않는다.
  - 서버로부터 응답이 도착하면 xhr 객체에서 `load`이벤트가 발생한다.
    - 이때 `xhr.load` 핸들러 프로퍼티에 바인딩한 이벤트 핸들러가 즉시 실행되는 것이 아니다.
    - `xhr.onload`이벤트 핸들러는 `load`이벤트가 발생하면 일단 태스크 큐에 저장되어 대기하다가, 콜 스택이 비면 이벤트 루프에 의해 콜 스택으로 push되어 실행된다.
    - 이벤트 핸들러도 함수이므로 다음 과정을 거친다.
      1.  이벤트 핸들러의 평가
      2.  이벤트 핸들러의 실행 컨텍스트 생성
      3.  콜 스택에 push
      4.  이벤트 핸들러 실행 과정
  - 이벤트 핸들러가 실행되는 시점에는 콜 스택이 빈 생태여야 하므로 (2)의 `console.log`는 이미 종료된 이후다.
  - 즉, `xhr.onload`이벤트 핸들러에서 상위 스코프의 변수에 서버의 응답 결과를 할당하기 이전에 `console.log`가 먼저 호출되어 `undefined`가 출력된다.

> - 이처럼 비동기 함수는 비동기 처리 결과를 외부에 반환할 수 없고, 상위 스코프의 변수에 할당할 수도 없다.
> - 따라서 비동기 함수의 처리 결과(서버의 응답 등)에 대한 후속 처리는 비동기 함수 내부에서 수행해야 한다.
> - 이때 비동기 함수를 범용적으로 사용하기 위해 비동기 함수에 비동기 처리 결과에 대한 후속 처리를 수행하는 콜백 함수를 전달하는 것이 일반적이다.
> - 필요에 따라 비동기 처리가 성공하면 호출될 콜백 함수와 비동기 처리가 실패하면 호출될 콜백 함수를 전달할 수 있다.

##### 4. 콜백 함수를 이용한 비동기 처리 결과 후속 처리

```js
// GET 요청을 위한 비동기 함수
const get = (url, successCallback, failureCallback) => {
  const xhr = new XMLHttpRequest();
  xhr.open("GET", url);
  xhr.send();

  xhr.onload = () => {
    if (xhr.status === 200) {
      // 서버의 응답을 콜백 함수에 인수로 전달하면서 호출하여 응답에 대한 후속 처리를 한다.
      successCallback(JSON.parse(xhr.response));
    } else {
      // 에러 정보를 콜백 함수에 인수로 전달하면서 호출하여 에러 처리를 한다.
      failureCallback(xhr.status);
    }
  };
};

// id가 1인 post를 취득
// 서버의 응답에 대한 후속 처리를 위한 콜백 함수를 비동기 함수인 get에 전달해야 한다.
get("https://jsonplaceholder.tyricode.com/posts/1", console.log, console.error);
/*
{
  "userId": 1,
  "id": 1,
  "title": "sunt aut facere ... ",
  "body": "quia et suscipit ... "
}
*/

// 콜백 헬 패턴 예시
// id가 1인 post의 userId를 취득
const url = "https://jsonplaceholder.tyricode.com"
get(`${url}/post/1`, ({ userId })) => {
  console.log(userId) // 1
  // post의 userId를 사용하여 user 정보를 취득
  get(`${url}/users/${userId}`, userInfo => {
    console.log(userInfo) // {id: 1, name: "Leanne Graham", username: "Bret", ...}
  })
}
```

- 이처럼 콜백 함수를 통해 비동기 처리 결과에 대한 후속 처리를 수행하는 비동기 함수가 비동기 처리 결과를 가지고 또다시 비동기 함수를 호출해야 한다면 콜백 함수 호출이 중첩되어 복잡도가 높아지는 현상이 발생하는데, 이를 `콜백 헬`이라 한다.

### 01-02. 에러 처리의 한계

- 비동기 처리를 위한 콜백 패턴의 문제점 중에서 가장 심각한 것은 에러 처리가 곤란하다는 것이다.
  - 이를 극복하기 위해 ES6에서 프로미스가 도입되었다.
- 에러는 호출자 방향으로 전파된다.

## 02. 프로미스의 생성

- ES6에서 도입된 Promise는 호스트 객체가 아닌 ECMAScript 사양에 정의된 표준 빌트인 객체다.
- Promise 생성자 함수를 new 연산자와 함께 호출하면 프로미스 객체를 생성한다.
- Promise 생성자 함수는 비동기 처리를 수행할 콜백 함수(executor 함수)를 인수로 전달받는데 이 콜백 함수는 resolve와 reject 함수를 인수로 전달받는다.
- Promise 생성자 함수가 인수로 전달받은 콜백 함수 내부에서 비동기 처리를 수행한다.
  - 이때 비동기 처리가 성공하면 콜백 함수의 인수로 전달받은 resolve 함수를 호출하고, 비동기 처리가 실패하면 reject 함수를 호출한다.

```js
// GET 요청을 위한 비동기 함수
const promiseGet = (url) => {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest();
    xhr.open("GET", url);
    xhr.send();

    xhr.onload = () => {
      if (xhr.status === 200) {
        // 성공적으로 응답을 전달받으면 resolve 함수를 호출한다.
        resolve(JSON.parse(xhr.response));
      } else {
        // 에러 처리를 위해 reject 함수를 호출한다.
        reject(new Error(xhr.status));
      }
    };
  });
};

// promiseGet 함수는 프로미스를 반환한다.
promiseGet("https://jsonplaceholder.tyricode.com/post/1");
```

- 프로미스는 현대 비동기 처리가 어떻게 진행되고 있는지를 나타내는 상태 정보를 갖는다.
  | 프로미스의 상태 정보 | 의미 | 상태 변경 조건 |
  |----------------------|--------------------------|---------------------------------|
  | `pending` | 비동기 처리가 아직 수행되지 않은 상태 | 프로미스가 생성된 직후 기본 상태 |
  | `fulfilled` | 비동기 처리가 수행된 상태(성공) | `resolve` 함수 호출 |
  | `rejected` | 비동기 처리가 수행된 상태(실패) | `reject` 함수 호출 |
- 생성된 직후의 프로미스는 기본적으로 pending 상태다. 이후 비동기 처리가 수행되면 비동기 처리 결과에 따라 다음과 같이 프로미스의 상태가 변경된다.
  - 비동기 처리 성공: resolve 함수를 호출해 프로미스를 fulfilled 상태로 변경한다. 그리고 비동기 처리 결과를 값으로 갖는다.
  - 비동기 처리 실패: reject 함수를 호출해 프로미스를 rejected 상태로 변경한다. 그리고 비동기 처리 결과인 Error 객체를 값으로 갖는다.
    이처럼 프로미스의 상태는 resolve 또는 reject 함수를 호출하는 것으로 결정된다.
- 즉, 프로미스는 비동기 처리 상태와 처리 결과를 관리하는 객체다.

## 03. 프로미스의 후속 처리 메서드

- 프로미스는 후속 메서드 `then, catch, finally`를 제공한다.
  - 모든 후속처리 메서드는 프로미스를 반환하며, 비동기로 동작한다.
- 프로미스의 비동기 처리 상태가 변화하면 후속 처리 메서드에 인수로 전달한 콜백 함수가 선택적으로 호출된다.

### 03-01. Promise.prototype.then

- `then`메서드는 두 개의 콜백 함수를 인수로 전달받는다.
  - 첫 번째 콜백 함수는 프로미스가 fulfilled 상태(resolve 함수가 호출된 상태)가 되면 호출된다. 이때 콜백 함수는 프로미스의 비동기 처리 결과를 인수로 전달받는다.
  - 두 번째 콜백 함수는 프로미스가 rejected 상태(reject 함수가 호출된 상태)가 되면 호출된다. 이때 콜백 함수는 프로미스의 에러를 인수로 전달받는다.
- `then`메서드는 언제나 프로미스를 반환한다. 만약 `then`메서드의 콜백 함수가 프로미스를 반환하면 그 프로미스를 그대로 반환하고, 콜백 함수가 프로미스가 아닌 값을 반환하면 그 값을 암묵적으로 resolve 또는 reject하여 프로미스를 생성해 반환한다.

```js
// fulfilled
new Promise((resolve) => resolve("fulfilled")).then(
  (v) => console.log(v),
  (e) => console.error(e)
); // fulfilled

// rejected
new Promise((_, reject) => reject(new Error("rejected"))).then(
  (v) => console.log(v),
  (e) => console.error(e)
); // Error: rejected
```

### 03-02. Promise.prototype.catch

- `catch`메서드는 한 개의 콜백 함수를 인수로 전달받는다.
- `catch`메서드의 콜백 함수는 프로미스가 `rejected`상태인 경우만 호출된다.
- `catch`메서드는 `then(undefined, onRejected)`과 동일하게 동작한다. 따라서 `then`메서드와 마찬가지로 언제나 프로미스를 반환한다.

### 03-03. Promise.prototype.finally

- `finally`메서드는 한 개의 콜백 함수를 인수로 전달받는다.
  - 콜백 함수는 프로미스의 성공(fulfilled) 또는 실패(rejected)와 상관없이 무조건 한 번 호출된다.
- `finally`메서드는 프로미스의 상태와 상관없이 공통적으로 수행해야 할 처리 내용이 있을 때 유용하다.
- `finally`메서드도 `then/catch`메서드와 마찬가지로 언제나 프로미스를 반환한다.

## 04. 프로미스의 에러 처리

- 프로미스는 에러를 문제없이 처리할 수 있다.
- 비동기 처리 결과에 대한 후속 처리는 프로미스가 제공하는 후속 처리 메서드 `then, catch, finally`를 사용하여 수행한다.
- `catch`메서드를 모든 `then`메서드를 호출한 이후에 호출하면 비동기 처에서 발생한 에러(rejected 상태)뿐 아니라 `then`메서드 내부에서 발생한 에러까지 모두 캐치할 수 있다.
- 가독성도 좋아지니까 에러처리는 `then`에서 하지말고 `catch`메서드에서 하자.

#### 비동기 처리에서 발생한 에러는 `then`메서드의 두 번째 콜백 함수로 처리할 수 있다.

```js
const wrongUrl = "잘못된 Url";

// 부적절한 URL이 지정되었기 때문에 에러가 발생한다.
promiseGet(wrongUrl).then(
  (res) => console.log(res),
  (err) => console.error(err)
); // Error: 404
```

#### 비동기 처리에서 발생한 에러는 프로미스의 후속 처리 메서드 `catch`를 사용해 처리할 수도 있다.

```js
const wrongUrl = "잘못된 Url";

// 부적절한 URL이 지정되었기 때문에 에러가 발생한다.
promiseGet(wrongUrl)
  .then((res) => console.log(res))
  .catch((err) => console.errro(err)); // Error: 404
```

## 05. 프로미스 체이닝(promise chaining)

- 프로미스는 `then, catch, finally`후속 처리 메서드를 통해 콜백 헬을 해결한다.
  - 후속 처리 메서드는 언제나 프로미스를 반환하므로 연속적으로 호출할 수 있다.
  - 이를 프로미스 체이닝이라 한다.
- 후속 처리 메서드의 콜백 함수는 프로미스의 비동기 처리 상태가 변경되면 선택적으로 호출된다.
  | 후속 처리 메서드 | 콜백 함수의 인수 | 후속 처리 메서드의 반환값 |
  |----------------|-----------------|---------------------------|
  | then | promiseGet 함수가 반환한 프로미스가 resolve한 값(id가 1인 post) | 콜백 함수가 반환한 프로미스 |
  | then | 첫 번째 then 메서드가 반환한 프로미스가 resolve한 값(post의 userId로 취득한 user 정보) | 콜백 함수가 반환한 값(undefined)을 resolve한 프로미스 |
  | catch<br />※ 에러가 발생하지 않으면 호출되지 않는다. | promiseGet 함수 또는 앞선 후속 처리 메서드가 반환한 프로미스가 reject한 값 | 콜백 함수가 반환한 값(undefined)을 resolve한 프로미스 |
- 후속 처리 메서드는 콜백 함수가 반환한 프로미스를 반환한다.
  - 만약 후속 처리 메서드의 콜백 함수가 프로미스가 아닌 값을 반환하더라도 그 값을 암묵적으로 resolve 또는 reject하여 프로미스를 생성해 반환한다.
- 프로미스는 프로미스 체이닝을 통해 비동기 처리 결과를 전달받아 후속 처리를 하므로 비동기 처리를 위한 콜백 패턴에서 발생하던 콜백 헬이 발생하지 않는다.
  - 다만, 프로미스도 콜백 패턴을 사용하므로 콜백 함수를 하용하지 않는 것은 아니다.
- 콜백 패턴을 사용하여 가독성이 나빠지는 문제는 ES8에서 도입된 async/await을 통해 프로미스의 후속 처리 메서드 없이 마치 동기처럼 프로미스가 처리 결과를 반환하도록 구현할 수 있다.

## 06. 프로미스의 정적 메서드

- Promise는 주로 생성자 함수로 사용되지만 함수도 객체이므로 메서드를 가질 수 있다.
- Promise는 5가지 정적 메서드를 제공한다.

### 06-01. Promise.resolve / Promise.reject

- `Promise.resolve`와 `Promise.reject`메서드는 이미 존재하는 값을 래핑하여 프로미스를 생성하기 위해 사용한다.
  - `Promise.resolve`메서드는 인수로 전달받은 값을 resolve하는 프로미스를 생성한다.
  - `Promise.reject`메서드는 인수로 전달받은 값을 reject하는 프로미스를 생성한다.

### 06-02. Promise.all

- `Promise.all`메서드는 여러 개의 비동기 처리를 모두 병렬 처리할 때 사용한다.

```js
const requestData1 = () =>
  new Promise((resolve) => setTimeout(() => resolve(1), 3000));
const requestData2 = () =>
  new Promise((resolve) => setTimeout(() => resolve(2), 2000));
const requestData3 = () =>
  new Promise((resolve) => setTimeout(() => resolve(3), 1000));

// 세 개의 비동기 처리를 순차적으로 처리
const res = [];
requestData1()
  .then((data) => {
    res.push(data);
    return requestData2();
  })
  .then((data) => {
    res.push(data);
    return requestData3();
  })
  .then((data) => {
    res.push(data);
    console.log(res); // [1, 2, 3] => 약 6초 소요
  })
  .catch(console.error);

// 세 개의 비동기 처리를 병렬로 처리
Promise.all([requestData(), requestData2(), requestData3()])
  .then(console.log) // [1, 2, 3] => 약 3초 소요
  .catch(console.error);
```

- `Promise.all`메서드는 프로미스를 요소로 갖는 배열 등의 이터러블을 인수로 전달받는다.
- 전달받은 모든 프로미스가 모두 fulfilled 상태가 되면 모든 처리 결과를 배열에 저장해 새로운 프로미스를 반환한다.
- `Promise.all`메서드는 인수로 전달받은 배열의 모든 프로미스가 모두 fulfilled 상태가 되면 종료한다.
  - 따라서 가장 늦게 fulfilled 상태가 되는 프로미스의 처리 시간보다 조금 더 소요된다.
  - 이때 배열에 저장한 처리 순서가 보장된다.
- 인수로 전달받은 배열의 프로미스가 하나라도 rejected 상태가 되면 나머지 프로미스가 fulfilled 상태가 되는 것을 기다리지 않고 즉시 종료한다
- `Promise.all`메서드는 인수로 전달받은 이터러블의 요소가 프로미스가 아닌 경우 `Promise.resolve`메서드를 통해 프로미스로 래핑한다.
  ```js
  Promise.all([
    1, // => Promise.resolve(1)
    2, // => Promise.resolve(2)
    3, // => Promise.resolve(3)
  ])
    .then(console.log) // [1, 2, 3]
    .catch(console.log);
  ```

### 06-03. Promise.race

- `Promise.race`메서드는 `Promise.all`메서드와 동일하게 프로미스를 요소로 갖는 배열 등의 이터러블을 인수로 전달받는다.
- `Promise.race`메서드는 `Promise.all`메서드처럼 모든 프로미스가 fulfilled 상태가 되는 것을 기다리는 것이 아니라 가장 먼저 fulfilled 상태가 된 프로미스의 처리 결과를 resolve하는 새로운 프로미스를 반환한다.
- 프로미스가 rejected 상태가 되면 `Promise.all`메서드와 동일하게 전달된 프로미스가 하나다로 rejected 상태가 되면 에러를 reject하는 새로운 프로미스를 즉시 반환한다.

### 06-04. Promise.allSettled

- `Promise.allSettled`메서드는 프로미스를 요소로 갖는 배열 등의 이터러블을 인수로 전달받는다.
- 그리고 전달받은 프로미스가 모두 settled 상태(비동기 처리가 수행된 상태, fulfilled or rejected 상태)가 되면 처리 결과를 배열로 반환한다.

```js
Promise.allSettled([
  new Promise((resolve) => setTimeout(() => resolve(1), 2000)),
  new Promise((_, reject) =>
    setTimeout(() => reject(new Error("에러발생 끼얏호우~")), 1000)
  ),
]);

/*
[
  // 프로미스가 fulfilled 상태인 경우
  {status: "fulfilled", value: 1},
  // 프로미스가 rejected 상태인 경우
  {status: "rejected", reason: Error: 에러발생 끼얏호우~ at ~~~...}
]
*/
```

- `Promise.allSetted`메서드가 반환한 배열에는 fulfilled 또는 rejected 상태와는 상관없이 `Promise.allSettled`메서드가 인수로 전달받은 모든 프로미스들의 처리 결과가 모두 담겨 있다.

## 07. 마이크로태스크 큐 (microtask queue / job queue)

- 프로미스의 후속 처리 메서드의 콜백 함수는 태스크 큐가 아니라 마이크로태스크 큐에 저장된다.
- 마이크로태스크 큐에는 프로미스의 후속 처리 메서드의 콜백 함수가 일시 저장된다.
  - 그 외의 비동기 함수의 콜백 함수나 이벤트 핸들러는 태스크 큐에 일시 저장된다.
  - 마이크로태스크 큐는 태스크 큐와는 별도의 큐다.
- 콜백 함수나 이벤트 핸들러를 일시 저장한다는 점에서 태스크 큐와 동일하지만 마이크로태스크 큐는 태스크 큐보다 우선순위가 높다.
  - 이벤트 루프는 콜 스택이 비면 먼저 마이크로태스크 큐에서 대기하고 있는 함수를 가져와 실행한다.
  - 이후 마이크로태스크 큐가 비면 태스크 큐에서 대기하고 있는 함수를 가져와 실행한다.

## 08. fetch

- fetch 함수는 XMLHttpRequest 객체와 마찬가지로 HTTP 요청 전송 기능을 제공하는 클라이언트 사이드 Web API다.
- fetch 함수는 XMLHttpRequest 객체보다 사용법이 간단하고 프로미스를 지원하기 때문에 비동기 처리를 위한 콜백 패턴의 단점에서 자유롭다.

```js
const promise = fetch(url [, options])
```

- fetch 함수는 HTTP 응답을 나타내는 Response 객체를 래핑한 Promise 객체를 반환한다.
- fetch 함수는 HTTP 응답을 나타내는 Response 객체를 래핑한 프로미스를 반환하므로 후속 처리 메서드 `then`을 통해 프로미스가 resolve한 Response 객체를 전달받을 수 있다.
  - Response 객체는 HTTP 응답을 나타내는 다양한 프로퍼티를 제공한다.
- fetch 함수를 사용할 때는 에러 처리에 주의해야 한다.
  - `catch`후속 처리 메서드에 의해 error이 출력되어야 할 시점처럼 보이는 순간에 ok 가 출력되는 경우가 있다.
  - fetch 함수가 반환하는 프로미스는 기본적으로 `404 Not Found`나 `500 Internal Server Error`와 같은 HTTP 에러가 발생해도 에러를 reject하지 않고 불리언 타입의 ok상태를 false로 설정한 Response 객체를 resolve한다.
  - 오프라인 등의 네트워크 장애나 CORS 에러에 의해 요청이 완료되지 못한 경우에만 프로미스를 reject한다.
- 따라서 fetch 함수를 사용할 때는 반환된 프로미스가 resolve한 불리언 타입의 ok상태를 확인해 명시적으로 에러를 처리할 필요가 있다.

> 참고로 axios는 모든 HTTP 에러를 reject하는 프로미스를 반환한다. 따라서 모든 에러를 `catch`에서 처리 가능하다.
