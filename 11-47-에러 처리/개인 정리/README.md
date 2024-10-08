# 47장 에러처리

## 01. 에러 처리의 필요성

- 에러가 발생하지 않는 코드를 작성하는 것은 불가능하다. 다라서 에러는 언제나 발생할 수 있다. 발생한 에러에 대해 대처하지 않고 방치하면 프로그램은 강제 종료된다.
- `try...catch`문을 사용해 발생한 에러에 적절하게 대응하면 프로그램이 강제 종료되지 않고 계속해서 코드를 실행시킬 수 있다.
- 우리가 작성한 코드에서 언제나 에러나 예외적인 상황이 발생할 수 있다는 것을 전제하고 이에 대응하는 코드를 작성하는 것이 중요하다.

```js
console.log("[start]");

foo(); // ReferenceError: foo is not defined
// 발생한 에러를 방치하면 프로그램은 강제 종료된다.

// 에러에 의해 프로그램이 강제 종료되어 아래 코드는 실행되지 않는다.
console.log("[end]");
```

## 02. try...catch..finally 문

- 기본적으로 에러 처리를 구현하는 방법은 크게 두 가지가 있다.
  - `querySelector`나 `Array#find`매서드처럼 예외적인 상황이 발생하면 반환하는 값(null 또는 -1)을 if 문이나 단축 평가 또는 옵셔널 체이닝 연산자를 통해 확인해서 처리하는 방법과 에러 처리 코드를 미리 등록해두고 에러가 발생하면 에러 처리 코드로 점프하도록 하는 방법이 있다.
  - `try...catch...finally`문은 두번째 방법이다. 일반적으로 이 방법을 에러 처리라고 한다.
    - `finally`문은 불필요하다면 생략 가능하다.
- 에러를 처리하면 프로그램이 강제 종료되지 않는다.

## 03. Error 객체

- Error 생성자 함수는 에러 객체를 생성한다.
- Error 생성자 함수에는 에러를 상세히 설명하는 에러 메세지를 인수로 전달할 수 있다.
- Error 생성자 함수가 생성한 에러 객체는 `message`프로퍼티와 `stack`프로퍼티를 갖는다.
  - `message`프로퍼티의 값은 Error 생성자 함수에 인수로 전달한 에러 메세지이고, `stack`프로퍼티의 값은 에러를 발생시킨 콜스택의 호출 정보를 나타내는 문자열이며 디버깅 목적으로 사용한다.
- 자바스크립트는 Error 생성자 함수를 포함해 7가지의 에러 객체를 생성할 수 있는 Error 생성자 함수를 제공한다.
  | 생성자 함수 | 인스턴스 |
  |-----------------|--------------------------------------------------------------------------|
  | `Error` | 일반적 에러 객체 |
  | `SyntaxError` | 자바스크립트 문법에 맞지 않는 문을 해석할 때 발생하는 에러 객체 |
  | `ReferenceError`| 참조할 수 없는 식별자를 참조했을 때 발생하는 에러 객체 |
  | `TypeError` | 피연산자 또는 인수의 데이터 타입이 유효하지 않을 때 발생하는 에러 객체 |
  | `RangeError` | 숫자값의 허용 범위를 벗어났을 때 발생하는 에러 객체 |
  | `URIError` | `encodeURI` 또는 `decodeURI` 함수에 부적절한 인수를 전달했을 때 발생하는 에러 객체 |
  | `EvalError` | `eval` 함수에서 발생하는 에러 객체 |

## 04. throw 문

- Error 생성자 함수로 에러 객체를 생성한다고 에러가 발생하는 것은 아니다.
  - 에러 객체 생성과 에러 발생은 의미가 다르다.
  - 에러를 발생시키려면 `try`코드 블록에서 `throw`문으로 에러 객체를 던져야 한다.
- `throw`문의 표현식은 어떤 값이라도 상관없지만 일반적으로 에러 객체를 지정한다.
  - 에러를 던지면 `catch`문의 에러 변수가 생성되고 던져진 에러 객체가 할당된다. 그리고 `catch`코드 블록이 실행되기 시작한다.

## 05. 에러의 전파

- 에러는 호출자 방향으로 전파된다. 즉, 콜 스택의 아래방향(실행 중인 실행 컨텍스트가 push되기 직전에 push된 실행 컨텍스트 방향)으로 전파된다.
  - `throw`된 에러를 캐치하지 않으면 호출자 방향으로 전파된다.
  - `throw`된 에러를 어디에서도 캐치하지 않으면 프로그램은 강제 종료된다.
- 주의할 것은 비동기 함수인 `setTimeout`이나 프로미스 후속 처리 메서드의 콜백 함수는 호출자가 없다는 것이다.
