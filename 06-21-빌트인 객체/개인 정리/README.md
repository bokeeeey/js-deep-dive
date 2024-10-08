# 21장 빌트인 객체

## 01. 자바스크립트 객체의 분류

- 자바스크립트 객체는 다음과 같이 크게 3개의 객체로 분류할 수 있다.
  - 표준 빌트인 객체(standard built-in objects/native objects/global objects)
    - 표준 빌트인 객체는 ECMAScript 사양에 정의된 객체를 말하며, 애플리케이션 전역의 공통 기능을 제공한다.
    - ECMAScript 사양에 정의된 객체이므로 자바스크립트 실행환경(브라우저 또는 Node.js)과 관계없이 언제나 사용할 수 있다.
    - 표준 빌트인 객체는 전역 객체의 프로퍼티로서 제공된다. 따라서 별도의 선언 없이 전역 변수처럼 언제나 참조할 수 있다.
  - 호스트 객체(host objects)
    - 호스트 객체는 ECMAScript 사양에 정의되어 있지 않지만 자바스크립트 실행 환경(브라우저 또는 node.js)에서 추가로 제공하는 객체를 말한다.
    - 브라우저 환경에서는 다음과 같은 클라이언트 사이드 Web API를 호스트 객체로 제공
      - DOM
      - BOM
      - Canvas
      - XMLHttpRequest
      - fetch
      - requestAnimationFrame
      - SVG
      - Web
      - Storage
      - Web Component
      - Web Worker
    - Node.js 환경에서는 고유의 API를 호스트 객체로 제공한다.
  - 사용자 정의 객체(user-defined objects)
    - 사용자 정의 객체는 표준 빌트인 객체와 호스트 객체처럼 기본 제공되는 객체가 아닌 사용자가 직접 정의한 객체를 말한다.

## 02. 표준 빌트인 객체

- 자바스크립트는 `Object, Number, Boolean, Symbol, Date, Math, RegExp, Array, Map/Set, WeakMap/WeakSet, Function, Promise, Reflect, Proxy, JSON, Error`등 40여 개의 표준 빌트인 객체를 제공한다.
  - `Math, Reflect, JSON`을 제외한 표준 빌트인 객체는 모두 인스턴스를 생성할 수 있는 생성자 함수 객체다.
  - 생성자 함수 객체인 표준 빌트인 객체는 프로토타입 메서드와 정적 메서드를 제공하고 생성자 함수 객체가 아닌 표준 빌트인 객체는 정적 메서드만 제공한다.
- 생성자 함수인 표준 빌트인 객체가 생성한 인스턴스의 프로토타입은 표준 빌트인 객체의 prototype 프로퍼티에 바인딩된 객체다.
  - 예를 들어, 표준 빌드인 객체는 `String`을 생성자 함수로서 호출하여 생성한 String 인스턴스의 프로토타입은 `String.prototype`이다.
- 표준 빌트인 객체의 prototype 프로퍼티에 바인딩된 객체는 다양한 기능의 빌트인 프로토타입 메서드를 제공한다.
  - 표준 빌트인 객체는 인스턴스 없이도 호출 가능한 빌트인 정적 메서드를 제공한다.

## 03. 원시 값과 래퍼 객체(wrapper object)

```js
const str = "hello";

// 원시 타입인 문자열이 프로퍼티와 메서드를 갖고 있는 객체처럼 동작한다.
console.log(str.length); // 5
console.log(str.toUpperCase()); // HELLO
```

- 원시값은 객체가 아니므로 프로퍼티나 메서드를 가질 수 없는데도 원시값인 문자열이 마치 객체처럼 동작한다.
  - 원시값인 문자열, 숫자, 불리언 값의 경우 이들 원시값에 대해 마치 객체처럼 마침표 표기법(또는 대괄호 표기법)으로 접근하면 자바스크립트 엔진이 일시적으로 원시값을 연관된 객체로 변환해준다.
  - 즉, 원시값을 객체처럼 사용하면 자바스크립트 엔진은 암묵적으로 연관된 객체를 생성하여 생성된 객체로 프로퍼티에 접근하거나 메서드를 호출하고 다시 원시값으로 되돌린다.
  - 문자열, 숫자, 불리언 값에 대해 객체처럼 접근하면 생성되는 임시 객체를 래퍼 객체라 한다.
    - 예를 들어, 문자열에 대해 마침표 표기법으로 접근하면 그 순간 래퍼 객체인 String 생성자 함수의 인스턴스가 생성되고 문자열은 래퍼 객체의 `[[StirngData]]`내부 슬롯에 할당된다.
    - 이때 문자열 래퍼 객체인 String 생성자 함수의 인스턴스는 `Stirng.prototype`의 메서드를 상속받아 사용할 수 있다.
    - 후에 래퍼 객체의 처리가 종료되면 래퍼 객체의 `[[StringData]]`내부 슬롯에 할당된 원시값으로 식별자가 원래의 상태를 갖도록 되돌리고 래퍼 객체는 가비지 컬렉션의 대상이 된다.

## 04. 전역 객체(global object)

- 전역 객체는 코드가 실행되기 이전 단계에 자바스크립트 엔진에 의해 어떤 객체보다도 먼저 생성되는 특수한 객체이며, 어떤 객체에도 속하지 않은 최상위 객체다.
- 전역 객체는 브라우저 환경에서는 window(또는 self, thism frame)가 전역 객체를 가리키지만 Node.js 환경에서는 global이 전역 객체를 가리킨다.
- 전역 객체는 표준 빌트인 객체`(Object, String, Number, Function, Array 등)`와 환경에 따른 호스트 객체(클라이언트 Web API 또는 Node.js의 호스트 API), 그리고 var 키워드로 선언한 전역 변수와 전역 함수를 프로퍼티로 갖는다.
- 전역 객체의 특징은 다음과 같다.
  - 전역 객체는 개발자가 의도적으로 생성할 수 없다. 즉, 전역 객체를 생성할 수 있는 생성자 함수가 제공되지 않는다.
  - 전역 객체의 프로퍼티를 참조할 때 window(또는 global)를 생략할 수 있다.
  - 전역 객체는 `Object, String, Number, Boolean, Function, Array, RegExp, Date, Math, Promise`같은 표준 빌트인 객체를 프로퍼티로 가지고 있다.
  - 자바스크립트 실행 환경에 따라 추가적으로 프로퍼티와 메서드를 갖는다.
  - var 키워드로 선언한 전역 변수와 선언하지 않은 변수에 값을 할당한 암묵적 전역, 그리고 전역 함수는 전역 객체의 프로퍼티가 된다.
  - let이나 const 키워드로 선언한 전역 변수는 전역 객체의 프로퍼티가 아니다. 보이지 않는 개념적인 블록(전역 렉시컬 환경의 선언적 환경 레코드)내에 존재하게 된다.
  - 브라우저 환경의 모든 자바스크립트 코드는 하나의 전역 객체 window를 공유한다.
    여러 개 script 태그를 통해 자바스크립트 코드를 분리해도 하나의 전역 객체 window를 공유하는 것은 변함이 없다.

> #### globalThis
>
> ECMAScript2020(ES11)에서 도입된 globalThis는 브라우저 환경과 Node.js 환경에서 전역 객체를 가리키던 다양한 식별자를 통일한 식별자다. globalThis는 표준 사양이므로 ECMAScript 표준 사양을 준수하는 모든 환경에서 사용할 수 있다.

### 04-01. 빌트인 전역 프로퍼티(built-in global property)

- 빌트인 전역 프로퍼티는 전역 객체의 프로퍼티를 의미한다.
  - 주로 애플리케이션 전역에서 사용하는 값을 제공한다.

#### 1. Infinity

- `Infinity` 프로퍼티는 무한대를 나타내는 숫자값 Infinity를 갖는다.

#### 2. NaN

- `NaN` 프로퍼티는 숫자가 아님을 나타내는 숫자값 Not a Number를 갖는다.
- `NaN` 프로퍼티는 `Number.NaN` 프로퍼티와 같다.

#### 3. undefined

- `undefined` 프로퍼티는 원시 타입 undefined를 값으로 갖는다.

### 04-02. 빌트인 전역 함수(built-in global function)

- 빌트인 전역 함수는 애플리케이션 전역에서 호출할 수 있는 빌트인 함수로서 전역 객체의 메서드다.

#### 1. eval

- `eval`함수는 자바스크립트 코드를 나타내는 문자열을 인수로 전달받는다.
- 문자열 코드를 런타임에 평가하여 값을 생성하고, 전달받은 인수가 표현식이 아닌 문이라면 문자열 코드를 런타임에 실행한다.
- 문자열 코드가 여러 개의 문으로 이루어져 있다면 모든 문을 실행한다.
- `eval`함수는 기존의 스코프를 런타임에 동적으로 수정한다.

  - 호출된 위치에 해당하는 기존의 스코프를 런타임에 동적으로 수정한다
  - `eval`함수에 전달된 코드는 이미 그 위치에 존재하던 코드처럼 동작한다.

  ```js
  const x = 1;

  function foo() {
    // eval 함수는 런타임에 foo 함수의 스코프를 동적으로 수정한다.
    eval(`var x = 2;`);
    console.log(x); // 2
  }

  foo();
  console.log(x); // 1
  ```

  - 단, strict mode에서 `eval`함수는 기존의 스코프를 수정하지 않고 함수 자신의 자체적인 스코프를 생성한다.
  - 또한 인수로 전달받은 문자열 코드가 let, const 키워드를 사용한 변수 선언문이라면 암묵적으로 strict mode가 적용된다.

> #### `eval`함수의 사용은 금지해야 한다.
>
> `eval`함수를 통해 사용자로부터 입력받은 콘텐츠를 실행하는 것은 보안에 매우 취약하다. 해킹문을 입력할 시 그대로 실행되기 때문.
> 또한 실행되는 코드는 자바스크립트 엔진에 의해 최적화가 수행되지 않으므로 일반적인 코드 실행에 비해 처리 속도가 느리다.

#### 2. isFinite

- 전달받은 인수가 정상적인 유한수인지 검사하여 유한수이면 true를 반환하고, 무한수이면 false를 반환한다.
- 전달받은 인수의 타입이 숫자가 아닌 경우, 숫자로 타입을 변환한 후 검사를 수행한다.
- 이때 인수가 NaN으로 평가되는 값이라면 false를 반환한다.

#### 3. isNaN

- 전달받은 인수가 NaN인지 검사하여 그 결과를 불리언 타입으로 반환한다.

#### 4. parseFloat

- 전달받은 문자열 인수를 부동 소수점 숫자(floating point number), 즉 실수로 해석하여 반환한다.

#### 5. parseInt

- 전달받은 문자열 인수를 정수(integer)로 해석하여 반환한다.

#### 6. encodeURI / decodeURI

- `encodeURI`함수는 완전한 URI(Uniform Resource Identifier)를 문자열로 전달받아 이스케이프 처리를 위해 인코딩한다.

  - URI는 인터넷에 있는 자원을 나타내는 유일한 주소를 말한다
    - URI의 하위개념으로 URL, URN이 있다.

  ![alt text](image.png)

  - 인코딩이란 URI의 문자들을 이스케이프 처리하는 것을 의미한다.
    - 이스케이프 처리는 네트워크를 통해 정보를 공유할 때 어떤 시스템에서도 읽을 수 있는 아스키 문제 셋으로 변환하는 것이다.
  - URI 문법 형식 표준 RFC3986
    - URL은 아스키 문자 셋으로만 구성되어야 하며 한글을 표함한 대부분의 외국어나 아스키 문자 셋에 정의되지 않은 특수 문자의 경우 URL에 포함될 수 없다.
    - URL 내에서 의미를 갖고 있는 문자(%, ?, #)나 URL에 올 수 없는 문자(한글, 공백 등) 또는 시스템에 의해 해석될 수 있는 문자(<, >)를 이스케이프 처리하여 야기될 수 있는 문제를 예방하기 위해 이스케이프 처리가 필요하다.
    - 알파벳, 0~9의 숫자, `- _ . ! ~ * ' ()`문자는 이스케이프 처리에서 제외된다.

- `decodeURI`함수는 인코딩된 URI를 인수로 전달받아 이스케이프 처리 이전으로 디코딩한다.

#### 7. encodeURIComponent / decondeURIComponent

- `encodeURIComponent`함수는 URI 구성 요소(component)를 인수로 전달받아 인코딩한다.
  - 여기서 인코딩이란 URI의 문자들을 이스케이프 처리하는 것을 의미한다.
  - 알파벳, 0~9의 숫자, `- _ . ! ~ * ' ()`문자는 이스케이프 처리에서 제외된다.
  - `encodeURIComponent`함수는 인수로 전달된 문자열을 URI의 구성요소인 쿼리 스트링의 일부로 간주한다.
    - 따라서 쿼리 스트링 구분자로 사용되는 `=, ?, &`까지 인코딩 한다.
- `decodeURIComponent`함수는 매개변수로 전달된 URI 구성 요소를 디코딩한다.
  - 반대로 `encodeURI`함수는 매개변수로 전달된 문자열을 완전한 URI 전체로 간주한다.
    - 따라서 쿼리 스트링 구분자로 사용되는 `=, ?, &`은 인코딩 하지 않는다.
