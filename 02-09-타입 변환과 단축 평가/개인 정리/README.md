# 09장 타입 변환과 단축 평가

### 01. 타입 변환이란?

- 자바스크립트의 모든 값은 타입이 있다.
- 값의 타입은 개발자의 의도에 따라 다른 타입으로 변환할 수 있다.
- 개발자가 의도적으로 값의 타입을 변환하는 것을 `명시적 타입 변환(explicit coercion)` 또는 `타입 캐스팅(type casting)`이라 한다.
- 개발자의 의도와는 상관없이 표현식을 평가하는 도중에 자바스크립트 엔진에 의해 암묵적으로 타입이 자동 변환되기도 한다. 이를 `암묵적 타입 변환(implicit coercion)` 또는 `타입 강제 변환(type coercion)`이라 한다.
- `명시적 타입 변환`이나 `암묵적 타입 변환`이 기존 원시 값을 직접 변경하는 것은 아니다. 원시 값은 변경 불가능한 값이므로 변경할 수 없다.
- 타입 변환이란 기존 원시 값을 사용해 다른 타입의 새로운 원시 값을 생성하는 것이다.

### 02. 암묵적 타입 변환

- 자바스크립트 엔진은 표현식을 평가할 때 개발자의 의도와는 상관없이 코드의 문맥을 고려해 암묵적으로 데이터 타입을 강제 변환(암묵적 타입 변환)할 때가 있다.

#### 02-01. 문자열 타입으로 변환

- 자바스크립트 엔진은 문자열 타입 아닌 값을 문자열 타입으로 암묵적 타입 변환을 수행할 때 다음과 같이 동작한다.

```js
// 숫자타입
0 + '' // '0'
-0 + '' // '0'
1 + '' // '1'
-1 + '' // '-1'
NaN + '' // 'NaN'
Infinity + '' // 'Infinity'
-Infinity + '' // '-Infinity'

// 불리언 타입
true + '' // 'true'
false + '' // 'false'

// null 타입
null + '' // 'null'

// undefined 타입
undefined + '' // 'undefined'

// symbol 타입
(Symbol()) + '' // TypeError: Cannot convert a Symbol value to a string

// 객체 타입
({}) + '' // '[object Object]'
Math + '' // '[object Math]'
[] + '' // ''
[10, 20] + '' // '10, 20'
(function(){}) + '' // 'function(){}'
Array + '' // 'function Array(){ [native code] }
```

#### 02-02. 숫자 타입으로 변환

- 산술 연산자의 역할은 숫자 값을 만드는 것이다.
  - 따라서 산술 연산자의 모든 피연산자는 코드 문백상 모두 숫자 타입이어야 한다.
  - 자바스크립트 엔진은 산술 연산자 표현식을 평가하기 위해 산술 연산자의 피연산자 중에서 숫자 타입이 아닌 피연산자를 숫자 타입으로 암묵적 타입 변환한다.
  - 이때 피연산자를 숫자 타입으로 변환할 수 없는 경우는 산술 연산을 수행할 수 없으므로 표현식의 평가 결과는 `NaN`이 된다.
- 비교 연산자의 역할은 불리언 값을 만드는 것이다.
  - 비교 연산자는 피연산자의 크기를 비교하므로 모든 피연산자의 코드는 문맥상 모두 숫자 타입이어야 한다.
  - 자바스크립트 엔진은 비교 연산자 표현식을 평가하기 위해 비교 연산자의 피연산자 중에서 숫자 타입이 아닌 피연산자를 숫자 타입으로 암묵적 타입 변환한다.

```js
// 문자열 타입
+""; // 0
+"0"; // 0
+"1"; // 1
+"string"; // NaN

// 불리언 타입
+true; // 1
+false; // 0

// null 타입
+null; // 0

// undefined 타입
+undefined; // NaN

// symbol 타입
+Symbol(); // TypeError: Cannot convert a Symbol value to a number

// 객체 타입
+{}; // NaN
+[]; // 0
+[10, 20]; // NaN
+function () {}; // NaN
```

#### 02-03. 불리언 타입으로 변환

- if 문이나 for 문과 같은 제어문 또는 삼항 조건 연산자의 조건식은 불리언 값, 즉 논리적 참/거짓으로 평가되어야 하는 표현식이다.
- 자바스크립트 엔진은 조건식의 평가 결과를 불리언 타입을 암묵적 타입 변환한다.
- 이때 자바스크립트 엔진은 불리언 타입이 아닌 값을 Truthy 값(참으로 평가되는 값) 또는 Falsy 값(거짓으로 평가되는 값)으로 구분한다.
- 즉, 제어문의 조건식과 같이 불리언 값으로 평가되어야 할 문맥에서 암묵적 타입 변환이 진행된다.

> #### false로 평가되는 Falsy 값들
>
> - false
> - undefined
> - null
> - 0, -0
> - NaN
> - '' (빈 문자열)

### 03. 명시적 타입 변환

#### 03-01. 문자열 타입으로 변환

- 문자열 타입이 아닌 값을 문자열 타입으로 변환하는 방법은 다음과 같다
  - String 생성자 함수를 new 연산자 없이 호출하는 방법
  - Object.prototype.toString 메서드를 사용하는 방법
  - 문자열 연결 연산자를 이용하는 방법

```js
// 1. String 생성자 함수를  new 키워드 없이 호출하는 방법
String(1); // "1"
String(NaN); // "NaN"
String(Infinity); // "Infinity"

String(true); // "true"
String(false); // "false"

// 2. Object.prototype.toString 메서드를 사용하는 방법
(1).toString(); // "1"
NaN.toString(); // "NaN";
Infinity.toString(); // "Infinity"

true.toString(); // "true"
false.toString(); // "false"

// 3. 문자열 연결 연산자를 이용하는 방법
1 + ""; // "1"
NaN + ""; // "NaN"
...
```

#### 03-02. 숫자 타입으로 변환

- 숫자 타입이 아닌 값을 숫자 타입으로 변환하는 방법은 다음과 같다.
  - Number 생성자 함수를 new 연산자 없이 호출하는 방법
  - parseInt, parseFloat 함수를 사용하는 방법(문자열만 숫자 타입으로 변환 가능)
  - `+` 단항 산술 연산자를 이용하는 방법
  - `*` 산술 연산자를 이용하는 방법

```js
// 1. Number 생성자 함수를 new 연산자 없이 호출하는 방법
// 문자열 타입 => 숫자 타입
Number("0"); // 0
Number("-1"); // -1
Number("10.53"); // 10.53
// 불리언 타입 => 숫자 타입
Number(true); // 1
Number(false); // 0

// 2. parseInt, parseFloat 함수를 사용하는 방법(문자열만 변환 가능)
// 문자열 타입 => 숫자 타입
parseInt("0"); // 0
parseInt(" -1"); // -1
parseFloat("10.53"); // 10.53

// 3. + 단항 산술 연산자를 이용하는 방법
// 문자열 타입 => 숫자 타입
+"0"; // 0
+"-1"; // -1
+"10.53"; // 10.53
// 불리언 타입 => 숫자 타입
+true; // 1
+false; // 0

// 4. * 산술 연산자를 이용하는 방법
// 문자열 타입 => 숫자 타입
"0" * 1; // 0
"-1" * 1; // -1
"10.53" * 1; // 10.53
// 불리언 타입 => 숫자 타입
true * 1; // 1
false * 1; // 0
```

#### 03-03. 불리언 타입으로 변환

- 불리언 타입이 아닌 값을 불리언 타입으로 변환하는 방법은 다음과 같다.
  - Boolean 생성자 함수를 new 연산자 없이 호출하는 방법
  - `!` 부정 논리 연산자를 두번 사용하는 방법

### 04. 단축 평가

#### 04-01. 논리 연산자를 사용한 단축 평가

- 단축 평가는 표현식을 평가하는 도중에 평가 결과가 확정된 경우 나머지 평가 과정을 생략하는 것을 말한다.

  | 단축 평가 표현식  | 평가 결과 |
  | ----------------- | --------- |
  | true II anything  | true      |
  | false II anything | anything  |
  | true && anything  | anything  |
  | false && anything | false     |

```js
// 논리합(||) 연산
"Cat" || "Dog"; // "Cat"
false || "Dog"; // "Dog"
"Cat" || false; // "Cat"

// 논리곱(&&) 연산
"Cat" && "Dog"; // "Dog"
false && "Dog"; // "false"
"Cat" && false; // "false"
```

#### 04-02. 옵셔널 체이닝 연산자

- ES11에서 도입된 옵셔널 체이닝 연산자 `?.`는 좌항의 피연산자가 null또는 undefined인 경우 undefined를 반환하고, 그렇지 않으면 우항의 프로퍼티 참조를 이어간다.

```js
var elem = null;

// elem이 null 또는 undefined이면 undefined를 반환하고, 그렇지 않으면 우항의 프로퍼티 참조를 이어간다.
var value = elem?.value;
console.log(value); // undefined

// 옵셔널 체이닝 연산자가 도입되기 이전에는 논리 연산자 &&를 사용한 단축평가를 통해 확인했다.
// elem이 Falsy 값이면 elem으로 평가되고, elem이 Truthy 값이면 elem.value로 평가된다.
var value = elem && elem.value;
console.log(value); // null
```

- 논리연산자 &&는 좌항 피연산자가 false로 평가되는 Falsy 값`(false, undefined, null, 0, -0, NaN, '')`이면 좌항 피연산자를 그대로 반환한다.
  - 하지만 0이나 ''은 객체로 평가될 때도 있다.
- 옵셔널 체이닝 연산자는 좌항 피연산자가 false로 평가되는 Falsy 값`(false, undefined, null, 0, -0, NaN, '')`이라도 null 또는 undefined가 아니면 우항의 프로퍼티 참조를 이어간다.

```js
var str = "";

// 논리연산사 && 사용
var length = str && str.length;
// 문자열의 길이(length)를 참조하지 못한다.
console.log(length); // ''

// 옵셔널 체이닝 사용
var length = str?.length;
// 옵셔널 체이닝 연산자는 좌항 피연산자가 Falsy 값이라도 null 또는 undefined가 아니면 우항의 프로퍼티 참조를 이어간다.
console.log(length); // 0
```

#### 04-03. null 병합 연산자

- ES11 에서 도입된 null 병합 연산자 `??`는 좌항의 피연산자가 null 또는 undefined인 경우를 우항의 피연산자가 반환하고, 그렇지 않으면 좌항의 피연산자를 반환한다.
- null 병합 연산자 `??`는 변수에 기본값을 설정할 때 유용하다.
