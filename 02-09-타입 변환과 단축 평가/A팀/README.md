# 타입 변환과 단축 평가

## 01. 타입 변환

- 명시적 타입 변환 (or 타입 캐스팅)

  - 개발자가 의도적으로 값의 타입을 변환하는 것

- 암묵적 타입 변환 (or 타입 강제 변환)

  - 개발자 의도와 상관 없이 표현식 평가 도중에 자바스크립트 엔진에 의해 암묵적으로 타입이 자동 변환되는 것
  - **기존 변수 값을 재할당하여 변경하는 것이 아님**

## 02. 암묵적 타입 변환

### 문자열 타입으로 변환

```js
1 + "2"; // "12"
```

### 숫자 타입으로 변환

```js
1 - "1"; // 0
1 * "10"; // 10
1 / "one"; // NaN

"1" > 0; // true
```

- 빈 문자열, 빈 배열, null, false는 0, true는 1로 변환됨
- 객체, 빈 배열이 아닌 배열, undefined는 변환되지 않아 NaN이 됨

### 불리언 타입으로 변환

- 조건식의 평가 결과를 불리언 타입으로 암묵적 타입 변환
- 자바스크립트 엔진은 불리언 타입이 아닌 값을 Truthy 값(참으로 평가되는 값), Falsy 값(거짓으로 평가되는 값)으로 구분

  - Falsy 값

    - false
    - undefined
    - null
    - 0, -0
    - NaN
    - ''

```js
if ("") console.log(x);
```

## 03. 명시적 타입 변환

- 개발자 의도에 따라 명시적으로 타입을 변경하는 방법
  - 생성자 함수(String, Number, Boolean)를 new 연산자 없이 호출하는 방법
  - 빌트인 메서드 사용하는 방법
  - 암묵적 타입 변환

### 문자열 타입으로 변환

- 방법

  1. String 생성자 함수를 new 연산자 없이 호출하는 방법
  2. Object.prototype.toString 메서드를 사용하는 방법
  3. 문자열 연결 연산자를 이용하는 방법

```js
// 방법 1
String(1); // "1"

// 방법 2
(1).toString(); // "1"

// 방법 3
1 + ""; // "1"
```

### 숫자 타입으로 변환

- 방법

  1. Number 생성자 함수를 new 연산자 없이 호출하는 방법
  2. parseInt, parseFloat 함수 사용하는 방법
  3. - 단항 산술 연산자 이용하는 방법
  4. - 산술 연산자를 이용하는 방법

```js
// 방법 1
Number("0"); // 0

// 방법 2
parseInt("0") + // 0
  // 방법 3
  "0"; // 0

// 방법 4
"0" * 1; // 0
```

### 불리언 타입으로 변환

- 방법

  1. Boolean 생성자 함수를 new 연산자 없이 호출하는 방법
  2. ! 부정 논리 연산자를 두 번 사용하는 방법

```js
// 방법 1
Boolean("x"); // true
Boolean(""); // false
Boolean("false"); //true

// 방법 2
!!"x"; // true
!!""; // false
!!"false"; // true
```

## 04. 단축 평가

### 논리 연산자를 사용한 단축 평가

- 2개의 피연산자 중 어느 한 쪽으로 평가됨
- 논리곱(&&) 연산자는 두 개 피연산자 모두 true로 평가될 때 true 반환

  > 'Cat' && 'Dog' // "Dog"

- 논리합(||) 연산자는 두 개 피연산자 중 하나만 true로 평가되어도 true 반환

  > 'Cat' || 'Dog' // "Cat"

- 단축 평가

  - 논리 연산의 결과를 결정하는 피연산자를 타입 변환하지 않고 그대로 반환
  - 표현식 평가 도중 평가 결과가 확정된 경우 나머지 평가 과정을 생략

- 언제 사용?

  - 객체를 가리키기를 기대하는 변수가 null 또는 undefined가 아닌지 확인하고 프로퍼티를 참조할 때
  - 함수 매개변수에 기본값을 설정할 때

### 옵셔널 체이닝 연산자

**?.**는 좌항의 피연산자가 null 또는 undefined인 경우 undefined를 반환, 그렇지 않으면 우항의 프로퍼티 참조

### null 병합 연산자

**??**는 좌항의 피연산자가 null 또는 undefined인 경우 우항의 피연산자를 반환, 그렇지 않으면 좌항의 피연산자를 반환
