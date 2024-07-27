# 20장 strict mode

## 01. strict mode란?

- 잠재적인 오류를 발생시키기 어려운 개발 환겅을 만들고 오류를 줄여 안정적인 코드를 생산하기 위한 목적으로 ES5부터 strict mode(엄격 모드)가 추가 되었다.
  - strict mode는 자바스크립트의 언어 문법을 좀 더 엄격히 적용하여 오류를 발생시킬 가능성이 높거나 자바스크립트 엔진의 최적화 작업에 문제를 일으킬 수 있는 코드에 대해 명시적인 에러를 발생시킨다.
- ESLint같은 린트 도구를 사용해도 strict mode와 유사한 효과를 얻을 수 있다.
  - 린트 도구는 정적 분석 기능을 통해 소스코드를 실행하기 전에 소스코드를 스캔하여 문법적 오류만이 아니라 잠재적 오류까지 찾아내고 오류의 원인을 리포팅해주는 유용한 도구다.
  - 린트 도구는 strict mode가 제한하는 오류는 물론 코딩 컨벤션을 설정 파일 형대로 정의하고 강제할 수 있기 때문에 더욱 강력한 효과를 얻을 수 있다.

## 02. strict mode 적용

- 전역의 선두 또는 함수 몸체의 선두에 `use strict`를 추가하면 스크립트 또는 함수에 적용된다.

## 03. 전역에 strict mode를 적용하는 것은 피하자

- 전역에 적용한 strict mode는 스크립트 단위로 적용된다.
- 스크립트 단위로 적용된 strict mode는 다른 스크립트에 영향을 주지 않고 해당 스크립트에만 한정되어 적용된다.
- strict mode 스크립트와 non-strict mode 스크립트를 혼용하는 것은 오류를 발생시킬 수 있다.

  - 특히 외부 라이브러리를 사용하는 경우 라이브러리가 non-strict mode인 경우도 있기 때문에 전역에 strict mode를 적용하는 것은 바람직하지 않다.
  - 이러한 경우에는 즉시 실행 함수로 스크립트 전체를 감싸서 스코프를 구분하고 직시 실행 함수의 선두에 strict mode를 적용한다.

  ```js
  // 즉시 실행 함수의 선두에 strict mode 적용
  (function () {
    "use strict";

    // 나머지 코드
  })();
  ```

## 04. 함수 단위로 strict mode를 적용하는 것도 피하자

- 함수마다 strict mode의 적용 유무가 다른 경우는 바람직 하지 않다.
- 모든 함수에 일일히 적용하는 것은 번거로운 일이다.
- strict mode가 적용된 함수가 참조할 함수 외부의 컨텍스트에 strict mode를 적용하지 않는다면 이 또한 문제가 발생할 수 있다.

> strict mode는 즉시 실행 함수로 감싼 스크립트 단위로 적용하는 것이 바람직하다.

## 05. strict mode가 발생시키는 에러

- 다음은 strict mode를 적용했을 때 에러가 발생하는 대표적인 사례다.

### 05-01. 암묵적 전역

- 선언하지 않은 변수를 참조하면 ReferenceError가 발생한다.

```js
(function () {
  "use strict";

  x = 1;
  console.log(x); // ReferenceError: x is not defined
})();
// strict mode를 적용하지 않은 경우에는 암묵적 전역 객체가 생성되어 의도하지 않은 에러를 발생시킬 가능성이 있다.
```

### 05-02. 변수, 함수 매개변수의 삭제

- delete 연산자로 함수, 변수, 매개변수를 삭제하면 SyntaxError가 발생한다.

```js
(function() {
  'use strict'

  var x = 1;
  delete x; // SyntaxError: Delete of an unqualiflied identifier in strict mode.

  function foo(a) {
    deleta a; // SyntaxError: Delete of an unqualiflied
  }
  delete foo; // SyntaxError: Delete of an unqualiflied
}())
```

### 05-03. 매개변수 이름의 중복

- 중복된 매개변수 이름을 사용하면 SyntaxError가 발생한다.

### 05-04. with 문의 사용

- with 문을 사용하면 SyntaxError가 발생한다.
- with 문은 전달된 객체를 스코프 체인에 추가한다.

```js
(function () {
  "use strict";

  //SyntaxError: Strict mode code may not include a with statement
  with ({ x: 1 }) {
    console.log(x);
  }
})();
```

> with 문은 동일한 객체의 프로퍼티를 반복해서 사용할 때 객체 이름을 생략할 수 있어서 코드가 간단해지는 효과가 있지만 성능과 가독성이 나빠지는 문제가 있다. 따라서 사용하지 않는 것이 좋다.

## 06 strict mode 적용에 의한 변화

### 06-01. 일반 함수의 this

- strict mode에서 함수를 일반 함수로서 호출하면 this에 `undefined`가 바인딩된다.
  - 생성자 함수가 아닌 일반 함수 내부에서는 this를 사용할 필요가 없기 때문이다.
  - 이때 에러는 발생하지 않는다.

### 06-02. arguments 객체

- strict mode에서는 매개변수에 전달된 인수를 재할당하여 변경해도 arguments 객체에 반영되지 않는다.

```js
(function (a) {
  "use strict";
  // 매개변수에 전달된 인수를 재할당하여 변경
  a = 2;

  // 변경된 인수가 arguments 객체에 반영되지 않는다.
  console.log(arguments); // { 0: 1, length: 1 }
})(1);
```
