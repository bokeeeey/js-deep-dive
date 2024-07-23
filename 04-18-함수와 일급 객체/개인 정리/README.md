# 18장 함수와 일급 객체

## 01. 일급 객체

- 일급 객체를 만족하는 조건은 다음과 같다
  1. 무명의 리터럴로 생성할 수 있다. 즉, 런타임에 생성이 가능하다.
  2. 변수나 자료구조(객체, 배열 등)에 저장할 수 있다.
  3. 함수의 매개변수에 전달할 수 있다.
  4. 함수의 반환값으로 사용할 수 있다.
- 함수가 일급 객체라는 것은 함수를 객체와 동일하게 사용할 수 있다는 의미다.
- 객체는 값이므로 함수는 값과 동일하게 취급할 수 있다.
- 일급 객체로서 함수가 가지는 가장 큰 특징은 일반 객체와 같이 함수의 매개변수에 전달할 수 있으며, 함수의 반환값으로 사용할 수 도 있다는 것이다.
  - 이는 함수형 프로그래밍을 가능케 하는 자바스크립트의 장점 중 하나다.
- 자바스크립트의 함수는 아래 예제와 같은 조건을 모두 만족하므로 일급 객체다.

```js
// 1. 함수는 무명의 리터럴로 생성할 수 있다.
// 2. 함수는 변수에 저장할 수 있다.
// 런타임(할당 단계)에 함수 리터럴이 평가되어 함수 객체가 생성되고 변수에 할당된다.
const increase = function (num) {
  return ++num;
};

const decrease = function (num) {
  return --num;
};

// 2. 함수는 객체에 저장할 수 있다.
const auxs = { increase, decrease };

// 3. 함수는 매개변수에 전달할 수 있다.
// 4. 함수의 반환값으로 사용할 수 있다.
function makeCounter(aux) {
  let num = 0;

  return function () {
    num = aux(num);
    return num;
  };
}

// 3. 함수는 매개변수에게 함수를 전달할 수 있다.
const increaser = makeCounter(aux.increase);
console.log(increaser()); // 1
console.log(increaser()); // 2

// 3. 함수는 매개변수에게 함수를 전달할 수 있다.
const increaser = makeCounter(aux.decrease);
console.log(decreaser()); // -1
console.log(decreaser()); // -2
```

## 02. 함수 객체의 프로퍼티

- 함수는 객체이므로 프로퍼티를 가질 수 있다.
  - `argunemts, caller, length, name, prototype`프로퍼티는 모두 함수 객체의 데이터 프로퍼티다.

```js
function square(number) {
  return number * number;
}

console.log(Object.getOwnPropertyDescriptors(square));
/*
{
  length: { value: 1, writable: false, enumerable: false, configurable: true },
  name: { value: "square", writable: false, enumerable: false, configurable: true },
  arguments: { value: null, writable: false, enumerable: false, configurable: false },
  caller: { value: null, writable: false, enumerable: false, configurable: false },
  prototype: { value: {...}, writable: true, enumerable: false, configurable: false }
}
*/

// __proto__ 는 square 함수의 프로퍼티가 아니다.
console.log(Object.getOwnPropertyDescriptor(square, "__proto__")); // undefined

// __proto__ 는 Object.prototype 객체의 접근자 프로퍼티이다.
// square 함수는 Object.prototype 객체로부터 __proto__ 접근자 프로퍼티를 상속받는다.
console.log(Object.getOwnPropertyDescriptor(Object.prototype, "__proto__"));
// { get: f, set: f, enumerable: false, configurable: true }
```

### 02-01. arguments 프로퍼티

- 함수 객체의 arguments 프로퍼티 값은 arguments 객체다.
- arguments 객체는 함수 호출 시 전달된 인수(argument)들의 정보를 담고 있는 순회 가능한 유사 배열 객체이며, 함수 내부에서 지역 변수처럼 사용된다.
  - 함수가 호출되면 함수 몸체 내에서 암묵적으로 매개변수가 선언되고 undefined로 초기화된 이후 인수가 할당된다.
  - 선언된 매개변수의 개수보다 인수를 적게 전달했을 경우 인수가 전달되지 않은 매개변수는 undefined로 초기화된 상태를 유지한다.
  - 매개변수보다 인수를 더 많이 전달한 경우 초과된 인수는 무시된다.
  - 초과된 인수는 그냥 버려지지 않고 암묵적으로 arguments 객체의 프로퍼티로 보관된다.
- arguments 객체는 매개변수 개수를 확정할 수 없는 가변 인자
  함수를 구현할 때 유용하다.
  - 배열 형태로 인자 정보를 담고 있지만 실제 배열이 아닌 유사 배열 객체이다.
  - 유사 배열 객체란 length 프로퍼티를 가진 객체로 for 문으로 순회할 수 있는 객체를 말한다.
  - 유사 배열 객체는 배열이 아니므로 배열 메서드를 사용할 경우 에러가 발생한다.

> #### arguments 객체의 Symbol(Symbol.iterator) 프로퍼티
>
> - arguments 객체의 Symbol(Symbol.iterator) 프로퍼티는 arguments 객체를 순회 가능한 자료구조인 이터러블(iterable)로 만들기 위한 프로퍼티다. Symbol.iterator 를 프로퍼티 키로 사용한 메서드를 구현하는 것에 의해 이터러블이 된다.
> - ES6에서 도입된 이터레이션 프로토콜을 준수하면 순회 가능한 자료구조인 이터러블이 된다. 이터러블의 개념이 없었던 ES5에서 arguments 객체는 유사 배열 객체로 구분되었다. 하지만 이터러블이 도입된 이후로는 유사 배열 객체이면서 동시에 이터러블로 취급된다.

```js
// ES5
function sum() {
  // arguments 객체를 배열로 변환
  const array = Array.prototype.slice.call(arguments);
  return array.reduce(function (pre, cur) {
    return pre + cur;
  }, 0);
}

console.log(sun(1, 2)); // 3
consolo.log(sum(1, 2, 3, 4, 5)); // 15

// ES6 Rest parameter
function sum(...args) {
  return args.reduce((pre, cur) => pre + cur, 0);
}

console.log(sun(1, 2)); // 3
consolo.log(sum(1, 2, 3, 4, 5)); // 15
```

### 02-02. caller 프로퍼티

- caller 프로퍼티는 ECMAScript 사양에 포함되지 않은 비표준 프로퍼티다.
- 함수 객체의 caller 프로퍼티는 함수 자신을 호출한 함수를 가리킨다.
- 이후 표준화될 예정도 없으니까 참고로만 알아두고 바쁘면 패스해라

```js
function foo(func) {
  return func();
}

function bar() {
  return "caller : " + bar.caller;
}

// 브라우저에서 실행한 결과
// caller는 함수를 호출한 함수를 가리킨다
// 함수호출 bar()의 caller는 bar()함수를 호출한 함수는 없으므로 null을 가리킨다.
console.log(foo(bar)); // caller: function foo(func) {...}
console.log(bar()); // caller: null
```

### 02-03.length 프로퍼티

- 함수 객체의 length 프로퍼티는 함수를 정의할 때 선언한 매개변수의 개수를 가리킨다.
- arguments 객체의 length 프로퍼티와 함수 객체의 length 프로퍼티의 값은 다를 수 있으므로 주의해야 한다.
  - argument 객체의 length 프로퍼티는 인자(argument)의 개수를 가리킨다.
  - 함수 객체의 length 프로퍼티는 매개변수(prameter)의 개수를 가리킨다.

### 02-04. name 프로퍼티

- 함수 객체의 name 프로퍼티는 함수 이름을 나타낸다.
- name 프로퍼티는 ES6에서 정식 표준이 되었다.
- name 프로퍼티는 ES5와 ES6에서 동작을 달리하므로 주의해야 한다.
  - ES5에서 name 프로퍼티는 빈 문자열을 값으로 찾는다.
  - ES6에서는 함수 객체를 가리키는 식별자를 값으로 갖는다.
    (함수를 호출할 때는 함수 이름 아닌 함수 객체를 가리키는 식별자로 호출한다.)

```js
// 기명 함수 표현식
var namedFunc = function foo() {};
console.log(namedFunc.name); // foo

// 익명 함수 표현식
var anonymousFunc = function () {};
// ES5: name 프로퍼티는 빈 문자열("")을 값으로 갖는다.
// ES6: name 프로퍼티는 함수 객체를 가리키는 변수 이름을 값으로 갖는다.
console.log(anonymousFunc.name); // anonymousFunc

// 함수 선언문(Function declaration)
function bar() {}
console.log(bar.name); // bar
```

### 02-05. `__proto__` 접근자 프로퍼티

- 모든 객체는 `[[Prototype]]`이라는 내부 슬롯을 갖는다.
- `__proto__` 프로퍼티는 `[[Prototype]]` 내부 슬롯이 가리키는 프로토타입 객체에 접근하기 위해 사용하는 접근자 프로퍼티다.
  - 내부 슬롯에는 직접 접근할 수 없고 간접적인 접근 방법을 제공하는 경우에 한하여 접근할 수 있다.

### 02-06. prototype 프로퍼티

- prototype 프로퍼티는 생성자 함수로 호출할 수 있는 함수 객체이다.
  - constructor만이 소유하는 프로퍼티다.
  - 일반 객채와 생성자 함수로 호출할 수 없는 non-constructor 에는 prototype 프로퍼티가 없다.
- prototype 프로퍼티는 함수가 객체를 생성하는 생성자 함수로 호출될 때 생성자 함수가 생성할 인스턴스의 프로토타입 객체를 가리킨다
