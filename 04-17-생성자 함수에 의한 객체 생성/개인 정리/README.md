# 17장 생성자 함수에 의한 객체 생성

## 01. Object 생성자 함수

- new 연산자와 함께 Object 생성자 함수를 호출하면 빈 객체를 생성하여 반환한다.
- 빈 객체를 생성한 이후 프로퍼티 또는 메서드를 추가하여 객체를 완성할 수 있다.
- 생성자 함수란 new 연산자와 함께 호출하여 객체(인스턴스)를 생성하는 함수를 말한다.
- 성성자 함수에 의해 생성된 객체를 인스턴스라 한다.
- 자바스크립트는 `Object` 생성자 함수 이외에도 `String, Number, Boolean, Function, Array, Date, RegExp, Promise` 등의 빌트인 생성자 함수를 제공한다.

```js
// String 생성자 함수에 의한 String 객체 생성
const strObj = new String("Kim");
console.log(typeof strObj); // object
console.log(strObj); // String { "Kim" }

// Number 생성자 함수에 의한 Number 객체 생성
const numObj = new Number(123);
console.log(typeof numObj); // object
console.log(numObj); // Number { 123 }

// Boolean 생성자 함수에 의한 Boolean 객체 생성
const boolObj = new Boolean(true);
console.log(typeof boolObj); // object
console.log(boolObj); // Boolean { true }

// Function 생성자 함수에 의한 Function 객체(함수) 생성
const func = new Function("x", "return x + x");
console.log(typeof func); // function
console.log(func); // function anonymous(x)

// Array 생성자 함수에 의한 Array 객체(배열) 생성
const arr = new Array(1, 2, 3);
console.log(typeof arr); // object
console.log(arr); // [1,2,3]

// RegExp 생성자 함수에 의한 RegExp 객체(정규 표현식) 생성
const regExp = new RegExp(/ab+c/i);
console.log(typeof regExp); // object
console.log(regExp); // /ab+c/i

// Date 생성자 함수에 의한 Date 객체 생성
const date = new Date();
console.log(typeof date); // object
console.log(date); // Fri May 04 ~~~~ (대한민국 표준시)
```

## 02. 생성자 함수

### 02-01. 객체 리터럴에 의한 객체 생성 방식의 문제점

- 객체 리터럴에 의한 객체 생성 방식은 직관직이고 간편하지만, 객체 리처럴에 의한 객체 생성 방식은 단 하나의 객체만 생성한다.

```js
const circle1 = {
  radius: 5,
  getDiameter() {
    return 2 * this.radius;
  },
};

console.log(circle.getDiameter()); // 10

const circle2 = {
  radius: 10,
  getDiameter() {
    return 2 * this.radius;
  },
};

console.log(circle2.getDiameter()); // 20
```

- 객체 리터럴에 의해 객체를 생성하는 경우 프로퍼티 구조가 동일함에도 불구하고 매번 같은 프로퍼티와 메서드를 기술해야 한다.

### 02-02. 생성자 함수에 의한 객체 생성 방식의 장점

- 생성자 함수에 의한 객체 생성 방식은 마치 객체(인스턴스)를 생성하기 위한 템플릿(클래스)처럼 생성자 함수를 사용하여 프로퍼티 구조가 동일한 객체 여러 개를 간편하게 생성할 수 있다.
- new 연산자와 함께 호출하면 해당 함수는 생성자 함수로 동작한다.
  - 만약 new 연산자와 함께 생성자 함수를 호출하지 않으면 생성자 함수가 아니라 일반 함수로 동작한다.

```js
// 생성자 함수
function Circle(radius) {
  //생성자 함수 내부의 this는 생성자 함수가 생성할 인스턴스를 가리킨다.
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}

// 인스턴스의 생성
const circle1 = new Circle(5); // 반지름이 5인 Circle 객체를 생성
const circle2 = new Circle(10); // 반지름이 10인 Circle 객체를 생성

console.log(circle1.getDirmeter()); // 10
console.log(circle1.getDirmeter()); // 20
```

> #### this
>
> this는 객체 자신의 프로퍼티나 메서드를 참조하기 위한 자기 참조 변수다. this가 가리키는 값, 즉 this바인딩은 함수 호출 방식에 따라 동적으로 결정된다.
> | 함수 호출 방식 | this가 가리키는 값(this 바인딩) |
> | ------------------- | ----------------------------------------------------- |
> | 일반 함수로서 호출 | 전역 객체 |
> | 메서드로서 호출 | 메서드를 호출한 객체(마침표 앞의 객체) |
> | 생성자 함수로서 호출 | 생성자 함수가 (미래에) 생성할 인스턴스 |

### 02-03. 생성자 함수의 인스턴스 생성 과정

- 생성자 함수의 역할은 프로퍼티 구조가 동일한 인스턴스를 생성하기 위한 템플릿(클래스)으로서 동작하여 인스턴스를 생성하는 것과 생성된 인스턴스를 초기화(인스턴스 프로퍼티 추가 및 초기값 할당)하는 것이다.
- 생성자 함수가 인스턴스를 생성하는 것은 필수이고, 생성된 인스턴스를 초기화하는 것은 옵션이다.

```js
// 생성자 함수
function Circle(radius) {
  // 인스턴스 초기화
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}

// 인스턴스 생성
const circle = new Circle(5); // 반지름이 5인 Circle 객체를 생성
```

- 생성자 함수의 내부 코드를 살펴보면 this에 프로퍼티를 추가하고 필요에 따라 전달된 인수를 프로퍼티의 초기값으로서 할당하여 인스턴스를 초기화한다.
- 하지만, 인스턴스를 생성하고 반환하는 코드는 보이지않는다.
- 자바스크립트 엔진은 암묵적인 처리를 통해 인스턴스를 생성하고 반환한다.
- new 연산자와 함께 생성자 함수를 호출하면 자바스크립트 엔진은 다음과 같은 과정을 거쳐 암묵적으로 인스턴스를 생성하고 인스턴스를 초기화한 후 암묵적으로 인스턴스를 반환한다.

#### 02-03-01. 인스턴스 생성과 this 바인딩

- 암묵적으로 빈 객체가 생성된다
- 이 빈 객체가(아직 완성되지는 않았지만) 생성자 함수가 생성한 인스턴스이다.
- 그리고 암묵적으로 생성된 빈 객체(인스턴스)는 this에 바인딩된다.
  - 생성자 함수 내부의 this가 생성자 함수가 생성할 인스턴스를 가리키는 이유가 바로 이것이다.
- 이 처리는 함수 몸체의 코드가 한줄씩 실행되는 런타임 이전에 실행된다.

```js
function Circle(radius) {
  // 1. 암묵적으로 인스턴스가 생성되고 this에 바인딩된다.
  console.log(this); // Circle {}

  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}
```

> #### 바인딩
>
> 바인딩이란 식별자와 값을 연결하는 과정을 의미한다.
> 예를 들어, 변수 선언은 변수 이름(식별자)과 확보된 메모리 공간의 주소를 바인딩하는 것이다. this 바인딩은 this(키워드로 분류되지만 식별자 역할을 한다)와 this가 가리킬 객체를 바인딩하는 것이다.

#### 02-03-02. 인스턴스 초기화

- 생성자 함수에 기술되어 있는 코드가 한 줄씩 실행되어 this에 바인딩되어 있는 인스턴스를 초기화 한다.
- this에 바인딩되어 있는 인스턴스에 프로퍼티나 메서드를 추가하고 생성자 함수가 인수로 전달받은 초기값을 인스턴스 프로퍼티에 할당하여 초기화하거나 고정값을 할당한다.
- 이 처리는 개발자가 기술한다.

```js
function Circle(radius) {
  // 1. 암묵적으로 인스턴스가 생성되고 this에 바인딩된다.

  // 2. this에 바인딩되어 있는 인스턴스를 초기화 한다.
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}
```

#### 02-03-03. 인스턴스 반환

- 생성자 함수 내부에서 모든 처리가 끝나면 완성된 인스턴스가 바인딩된 this를 암묵적으로 반환한다.
- 생성자 함수 내부에서 명시적으로 this가 아닌 다른 값을 반환하는 것은 생성자 함수의 기본 동작을 훼손한다. 따라서 함수 내부에서 retern 문을 반드시 생략해야 한다.

```js
function Circle(radius) {
  // 1. 암묵적으로 빈 객체가 생성되고 this에 바인딩된다.

  // 2. this에 바인딩되어 있는 인스턴스를 초기화 한다.
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };

  // 3. 완성된 인스턴스가 바인딩된 this가 암묵적으로 반환된다.
  // 명시적으로 원시 값을 반환하면 원시 값 반환은 무시되고 암묵적으로 this가 반환된다.
}

// 인스턴스 생성. Circle 생성자 함수는 암묵적으로 this를 반환한다.
const circle = new Circle(1);
console.log(circle); // Circle { radius: 1, getDiameter: f }
```

### 02-04. 내부 메서드 `[[Call]]`과 `[[Construct]]`

- 함수 선언문 또는 함수 표현식으로 정의한 함수는 일반적인 함수로서 호출할 수 있는 것은 물론 생성자 함수로서 호출할 수 있다.
  - 생성자 함수로서 호출한다는 것은 new 연산자와 함꼐 호출하여 객체를 생성하는 것을 의미한다.
- 함수 객체는 일반 객체가 가지고 있는 내부 슬롯과 내부 메서드를 모두 가지고 있기 때문에 일반 객체와 동일하게 동작할 수 있다.
- 일반객체와 다른점도 존재한다
  - 일반 객체는 호출할 수 없지만 함수는 호출할 수 있다.
  - 함수로서 동작하기 위한 함수 객체만을 위한 `[[Environment]], [[FormalParameters]]`등의 내부 슬롯과 `[[Call]], [[Construct]]`같은 내부 메서드를 추가로 가지고 있다.
- 함수가 일반 함수로서 호출되면 함수 객체의 내부 메서드 `[[Call]]`이 호출되고, new 연산자와 함께 생성자 함수로서 호출되면 내부 메서드 `[[Construct]]`가 호출된다.
- 모든 함수 객체는 내부 메서드 `[[Call]]`을 갖고 있으므로 호출할 수 있다.
- 하지만 모든 함수 객체가 `[[Construct]]`를 갖는 것은 아니다.
- 결론적으로 함수 객체는 호출할 수 있지만 모든 함수 객체를 생성자 함수로서 호출할 수 있는 것은 아니다.
  - 모든 함수 객체는 callable이지만 모든 함수 객체가 constructor인 것은 아니다.

### 02-05. constructor와 non-constructor의 구분

- 자바스크립트 엔진은 함수 정의를 평가하여 함수 객체를 생성할 때 함수 정의 방식에 따라 함수를 constructor와 non-constructor로 구분한다.
  - constructor: 함수 선언문, 함수 표현식, 클래스(클래스도 함수다)
  - non-constructor: 메서드(ES6 메서드 축약 표현), 화살표 함수

### 02-06. new 연산자

- new 연산자와 함께 함수를 호출하면 해당 함수는 생성자 함수로 동작한다.
  - 함수 객체의 내부 메서드 `[[Call]]`이 호출되는 것이 아니라 `[[Constructor]]`가 호출된다.
  - new 연산자와 함께 호출하는 함수는 constructor이어야 한다.
- new 연산자 없이 생성자 함수를 호출하면 일반 함수로 호출된다.
  - 함수 객체 내부의 메서드 `[[Construct]]`가 호출되는 것이 아니라 `[[Call]]`이 호출된다.

### 02-07. new.target

- new.target은 this와 유사하게 constructor인 모든 함수 내부에서 암묵적인 지역 변수와 같이 사용되며 메타 프로퍼티라고 부른다.
- 함수 내부에서 new.target을 사용하면 new 연산자와 함께 생성자 함수로서 호출되었는지 확인할 수 있다.
- new 연산자와 함께 생성자 함수로서 호출되면 함수 내부의 new.target은 함수 자신을 가리킨다.
- new 연산자 없이 일반 함수로서 호출된 함수 내부의 new.target은 undefined다.
