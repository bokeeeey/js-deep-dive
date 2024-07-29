# 22장 this

## 01. this 키워드

- 동작을 나타내는 메서드는 자신이 속한 객체의 상태, 즉 프로퍼티를 참조하고 변경할 수 있어야 한다.
- 이때 메서드가 자신이 속한 객체의 프로퍼티를 참조하려면 먼저 자신이 속한 객체를 가리키는 식별자를 참조할 수 있어야 한다.
- 생성자 함수 방식으로 인스턴스를 생성하는 경우를 생각해보자
  - 생성자 함수 내부에서는 프로퍼티 또는 메서드를 추가하기 위해 자신이 생성할 인스턴스를 참조할 수 있어야 한다.
  - 하지만 생성자 함수에 의한 객체 생성 방식은 먼저 생성자 함수를 정의한 이후 new 연산자와 함께 생성자 함수를 호출하는 단계가 추가로 필요하다.
  - 즉, 생성자 함수로 인스턴스를 생성하려면 먼저 생성자 함수가 존재해야 한다.
  - 따라서 자신이 속한 객체 또는 자신이 생성할 인스턴스를 가리키는 특수한 식별자가 필요하다.
  - 이를 위해 자바스크립트는 this라는 특수한 식별자를 제공한다.
- this는 자신이 속한 객체 또는 자신이 생성할 인스턴스를 가리키는 자기 참조 변수다.
- this를 통해 자신이 속한 객체 또는 자신이 생성할 인스턴스의 프로퍼티나 메서드를 참조할 수 있다.
- this는 자바스크립트 엔진에 의해 암묵적으로 생성되며, 코드 어디서든 참조할 수 있다.
  - 함수를 호출하면 arguments 객체와 this가 암묵적으로 함수 내부에 전달된다.
- this가 가리키는 값, 즉 this 바인딩은 함수 호출 방식에 의해 동적으로 결정된다.
- 자바스크립트의 this는 함수가 호출되는 방식에 따라 this 바인딩이 동적으로 결정된다.
  - 객체 리터럴의 메서드 내부에서는 this는 메서드를 호출한 객체를 가리킨다.
  - 생성자 함수 내부의 this는 생성자 함수가 생성할 인스턴스를 가리킨다.
  - strict mode 또한 this 바인딩에 영향을 준다.

```js
// this는 어디서든지 참조 가능하다
// 전역에서 this는 전역 객체 window를 가리킨다.
console.log(this); // window

function square(number) {
  // 일반 함수 내부에서 this는 전역 객체  window를 가리킨다.
  console.log(this); // window
  return number * number;
}
square(2);

const person = {
  name: "Kim",
  getName() {
    // 메서드 내부에서 this는 메서드를 호출한 객체를 가리킨다.
    console.log(this); // { name: "Kim", getName: f }
    return this.name;
  },
};
console.log(person.getName()); // Kim

function Person(name) {
  this.name = name;
  // 생성자 함수 내부에서 this는 생성자 함수가 생성할 인스턴스를 가리킨다.
  console.log(this); // Person { name: "Kim" }
}

const me = new Person("Kim");
```

> this는 객체의 프로퍼티나 메서드를 참조하기 위한 자기 참조 변수 이므로 일반적으로 객체의 메서드 내부 또는 생성자 함수 내부에서만 의미가 있다.
> 따라서 strict mode가 적용된 일반 함수 내부의 this에는 `undefined`가 바인딩된다.
> 일반 함수 내부에서 this를 사용할 필요가 없기 때문이다.

> #### this 바인딩
>
> 바인딩이란? 식별자와 값을 연결하는 과정을 의미한다.
> 예를 들어, 변수 선언은 변수 이름과(식별자)과 확보된 메모리 공간의 주소를 바인딩하는 것이다. this 바인딩은 this(키워드로 분류되지만 식별자 역할을 한다.)와 this가 가리킬 객체를 바인딩하는 것이다.

## 02. 함수 호출 방식과 this 바인딩

- this 바인딩은 함수 호출 방식, 즉 함수가 어떻게 호출되었는지에 따라 동적으로 결정된다.
- 주의할 것은 동일한 함수도 다양한 방식으로 호출할 수 있다는 것이다.
  - 일반 함수 호출
  - 메서드 호출
  - 생성자 함수 호출
  - `Function.prototype.applt/call/bind` 메서드에 의한 간접 호출

> #### 렉시컬 스코프와 this 바인딩은 결정 시기가 다르다.
>
> 함수의 상위 스코프를 결정하는 방식인 렉시컬 스코프는 함수 정의가 평가되어 함수 객체가 생성되는 시점에 상위 스코프를 결정한다.
> 하지만 this 바인딩은 함수 호출 시점에 결정된다.

### 02-01. 일반 함수 호출

- 기본적으로 this에는 전역 객체가 바인딩된다.

  - 전역함수는 물론이고 중첩 함수를 일반 함수로 호출하면 함수 내부의 this에는 전역 객체가 바인딩된다.
  - 메서드 내에서 정의한 중첩 함수도 일반 함수로 호출되면 중첩 함수 내부의 this에는 전역 객체가 바인딩된다.

  ```js
  // var 키워드로 선언한 전역 변수 value는 전역 객체의 프로퍼티다.
  var value = 1;
  // const 키워드로 선언한 전역 변수 value는 전역 객체의 프로퍼티가 아니다.
  // const value = 1;

  const obj = {
    value: 100,
    foo() {
      console.log("foo's this: ", this); // { value: 100, foo: f }
      console.log("foo's this.value: ", this.value); // 100

      // 메서드 내에서 정의한 중첩 함수
      function bar() {
        console.log("bar's this: ", this); // window
        console.log("bar's this.value: ", this.value); // 1
      }

      // 메서드 내에서 정의한 중첩 함수도 일반 함수로 호출되면 중첩 함수 내부의 this에는 전역 객체가 바인딩된다.
      bar();
    },
  };

  obj.foo();
  ```

  - 콜백 함수가 일반 함수로 호출된다면 콜백 함수 내부의 this에도 전역 객체가 바인딩된다.
  - 어떠한 함수라도 일반 함수로 호출된 모든 함수(중첩 함수, 콜백 함수 포함) 내부의 this에는 전역 객체가 바인딩된다.

### 02-02. 메서드 호출

- 메서드 내부의 this에는 메서드를 호출한 객체, 즉 메서드를 호출할 때 메서드 이름 앞의 마침표(.) 연산자 앞에 기술한 객체가 바인딩된다.
  - 주의할 것은 메서드 내부의 this는 메서드를 소유한 객체가 아닌 메서드를 호출한 객체에 바인됭된다는 것이다.

```js
const person = {
  name: "Kim",
  getName() {
    // 메서드 내부의 this는 메서드를 호출한 객체에 바인딩된다.
    return this.name;
  },
};

// 메서드 getName을 호출한 객체는 person이다.
console.log(person.getName()); // Kim
```

- 메서드 내부의 this는 프로퍼티로 메서드를 가리키고 있는 객체와는 관계가 없고 메서드를 호출한 객체가 바인딩된다.
- 프로토타입 메서드 내부에서 사용된 this도 일반 메서드와 마찬가지로 해당 메서드를 호출한 객체에 바인딩된다.

```js
function Person(name) {
  this.name = name;
}

Person.prototype.getName = function () {
  return this.name;
};

const me = new Person("Kim");

// getName 메서드를 호출한 객체는 me다.
console.log(me.getName()); // Kim

Person.prototype.name = "Lee";

// getName 메서드를 호출한 객체는 Person.prototype이다.
console.log(Person.prototype.getName()); // Lee
```

### 02-03. 생성자 함수 호출

- 생성자 함수 내부의 this에는 생성자 함수가 (미래에) 생성할 인스턴스가 바인딩된다.

```js
// 생성자 함수
function Circle(radius) {
  // 생성자 함수 내부의 this는 생성자 함수가 생성할 인스턴스를 가리킨다.
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}

// 반지름이 5인 Circle 객체를 생성
const circle1 = new Circle(5);
// 반지름이 10인 Circle 객체를 생성
const circle2 = new Circle(10);

console.log(circle1.getDiameter()); // 10
console.log(circle2.getDiameter()); // 20

// 만약 new 연산자와 함께 호출하지 않으면 생성자 함수로 동작하지 않는다. 즉, 일반 함수로의 호출이다.
const circle3 = Circle(15);

// 일반 함수로 호출된 Circle에는 반환문이 없으므로 암묵적인 undefined를 반환한다.
console.log(circle3); // undefined

// 일반 함수로 호출된 Circle 내부의 this는 전역 객체를 가리킨다.
console.log(radius); // 15
```

### 02-04. Function.prototype.apply/call/bind 메서드에 의한 간접 호출

- apply, call, bind 메서드는 `Function.prototype`의 메서드다.
  - 이들 메서드는 모든 함수가 상속받아 사용할 수 있다.
  - apply, call 메서드의 본질적인 기능은 함수를 호출하는 것이다.
  - apply와 call 메서드는 함수를 호출하면서 첫 번째 인수로 전달한 특정 객체를 호출한 함수의 this에 바인딩한다.
- `Function.prototype.apply`, `Function.prototype.call`메서드는 this로 사용할 객체와 인수 리스트를 인수로 전달받아 함수를 호출한다.

```js
function convertArgsToArray() {
  console.log(arguments);

  // arguments 객체를 배열로 반환
  // Array.prototype.slice를 인수 없이 호출하면 배열의 복사본을 생성한다.
  const arr = Array.prototype.slice.call(arguments);
  // const arr = Array.prototype.slice.apply(arguments);
  console.log(arr);

  return arr;
}

convertArgsTiArray(1, 2, 3); // [1, 2, 3]
```

- `Function.prototype.bind`메서드는 apply와 call 메서드와 달리 함수를 호출하지 않는다.
  - 다만 첫 번째 인수로 전달한 값으로 this 바인딩이 교체된 함수를 새롭게 생성해 반환한다.
  - bind 메서드는 메서드의 this와 메서드 내부의 중첩 함수 또는 콜백 함수의 this가 불일치하는 문제를 해결하기 위해 유용하게 사용된다.

```js
const person = {
  name: "Kim",
  foo(callback) {
    // bind 메서드로 callback 함수 내부의 this 바인딩을 전달
    setTimeout(callback.bind(this), 100);
  },
};

person.foo(function () {
  console.log(`Hi! My name is ${this.name}.`); // Hi My name is Kim
});
```

### 02-05. 정리

- 함수 호출방식에 따라 this 바인딩이 동적으로 결정되는 것을 정리해 보면 아래와 같다
  | 함수 호출 방식 | this 바인딩 |
  | ----------------------- | --------------------------- |
  | 일반 함수 호출 | 전역 객체 |
  | 메서드 호출 | 메서드를 호출한 객체 |
  | 생성자 함수 호출 | 생성자 함수가 (미래에) 생성할 인스턴스 |
  | Function.prototype.apply/call/bind 메서드에 의한 간접 호출 | Function.prototype.apply/call/bind 메서드에 첫 번째 인수로 전달한 객체 |
