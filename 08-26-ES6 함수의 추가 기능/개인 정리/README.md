# 26장 ES6 함수의 추가 기능

## 01. 함수의 구분

- ES6 이전의 모든 함수는 사용 목적에 따라 명확한 구분이 없으므로 호출 방식에 특별한 제약이 없고 생성자 함수로 호출되지 않아도 프로토타입 객체를 생성한다.
  - ES6 이전의 모든 함수는 일반 함수로서 호출할 수 있는 것은 물론 생성자 함수로서 호출할 수 있다.
  - ES6 이전의 함수는 다르게 말하면 callable이면서 constructor다.
    > 호출할 수 있는 함수 객체를 callable이라 하며, 인스턴스를 생성할 수 있는 객체를 constructor라 부른다.
  - 이는 혼란스러운 실수를 유발할 가능성이 있고 성능에도 좋지 않다.
- 주의할 것은 ES6 이전에 일반적으로 메서드라고 부르던 객체에 바인딩된 함수도 callable이며 constructor라는 것이다.
  - 따라서 객체에 바인딩된 함수도 일반 함수로서 호출할 수 있는 것은 물론 생성자 함수로서 호출할 수도 있다.
- 이러한 문제를 해결하기 위해 ES6에서는 함수를 사용 목적에 따라 세 가지 종류로 명확히 구분했다.
  | ES6 함수의 구분 | constructor | prototype | super | arguments |
  | ----------------------- | ----------- | --------- | ----- | --------- |
  | 일반 함수(Normal) | ⭕ | ⭕ | ❌ | ⭕ |
  | 메서드(Method) | ❌ | ❌ | ⭕ | ❌ |
  | 화살표 함수(Arrow) | ❌ | ❌ | ❌ | ❌ |

## 02. 메서드

- ES6 이전 사양에는 메서드에 대한 명확한 정의가 없었다.
  - 일반적으로 객체에 바인딩된 함수를 일컫는 의미로 사용되었다.
- ES6 사양에서 메서드는 축약 표현으로 정의된 함수만을 의미한다.
  - ES6 메서드는 인스턴스를 생성할 수 없는 non-constructor다.
  - ES6 메서드는 인스턴스를 생성할 수 없으므로 prototype 프로퍼티가 없고 프로토타입도 생성하지 않는다.
  - ES6 메서드는 자신을 바인딩한 객체를 가리키는 내부 슬롯 `[[HomeObject]]`를 갖는다.

## 03. 화살표 함수(arrow function)

- 화살표 함수는 표현뿐만 아니라 내부 동작도 기존의 함수보다 간략하다.
- 특히 화살표 함수는 콜백 함수 내부에서 this가 전역 객체를 가리키는 문제를 해결하기 위한 대안으로 유용하다.

### 03-01. 화살표 함수 정의

#### 다 아는내용이니까 일단 넘어가자

### 03-02. 화살표 함수와 일반 함수의 차이

1.  화살표 함수는 인스턴스를 생성할 수 없는 non-constructor다.

- 화살표 함수는 인스턴스를 생성할 수 없으므로 prototype 프로퍼티가 없고 프로토타입도 생성하지 않는다.

2. 중복된 매개변수 이름을 선언할 수 없다.

- 일반 함수는 중복된 매개변수 이름을 선언해도 에러가 발생하지 않는다 (strict mode 제외)
- 화살표 함수에서는 중복된 매개변수 이름을 선언하면 에러가 발생한다.

3. 화살표 함수는 함수 자체의 this, arguments, super, new, target 바인딩을 갖지 않는다.

- 화살표 함수 내부에서 this, arguments, super, newm target을 참조하면 스코프 체인을 통해 상위 스코프의 this, arguments, super, new, target을 참조한다.
- 만약 화살표 함수의 화살표 함수가 중첩되어 있다면 상위 화살표 함수에도 this, arguments, super, new, target 바인딩이 없으므로 스코프 체인 상에서 가장 가까운 상위 함수 중에서 화살표 함수가 아닌 함수의 this, arguments, super, new, target을 참조한다.

### 03-03. this

- 화살표 함수가 일반 함수와 구별되는 가장 큰 특징은 this다.
- 화살표 함수는 다른 함수의 인수로 전달되어 콜백 함수로 사용되는 경우가 많다.
- 화살표 함수의 this는 일반 함수의 this와 다르게 동작한다.
  - 이는 `콜백 함수 내부의 this문제`, 즉 콜백 함수 내부의 this가 외부 함수의 this와 다르기 때문에 발생하는 문제를 해결하기 위해 의도적으로 설계된 것이다.
    - 함수를 정의할 때 this에 바인딩할 객체가 정적으로 결정되는 것이 아니고, 함수를 호출할 때 함수가 어떻게 호출되었는지에 따라 this에 바인딩할 객체가 동적으로 결정된다.
  - 주의할 것은 일반 함수로서 호출되는 콜백 함수의 경우다.
    - 고차함수의 인수로 전달되어 고차 함수 내부에서 호출되는 콜백 함수도 중첩 함수라고 할 수 있다.

```js
class Prefixer {
  constructor(perfix) {
    this.prefix = prefix;
  }

  add(arr) {
    // add 메서드는 인수로 전달된 배열 arr을 순회하며 배열의 모든 요소에 prefix를 추가한다.
    return arr.map(function (item) {
      return this.prefix + item;
      // TypeError: Cannot read property "prefix" of undefined
    });
  }
}

const prefixer = new Prefixer("-webkit-");
console.log(prefixer.add(["transition", "user-select"]));
```

- 일반 함수로서 호출되는 모든 함수 내부의 this는 전역 객체를 가리킨다.
  - 하지만 클래스에는 strict mode가 암묵적으로 적용된다.
  - strict mode에서 일반 함수로 호출된 모든 함수 내부의 this에는 전역 객체가 아닌 `undefined`가 바인딩된다.

#### ES6 이전의 콜백 함수 내부의 this문제 해결 방법

##### 01. add 메서드를 호출한 perfixer 객체를 가리키는 this를 일단 회피시킨 후에 콜백 함수 내부에서 사용한다.

```js
...
add(arr) {
  // this를 일단 회피시킨다.
  const that = this;
  return arr.map(function(item) {
    // this 대신 that을 참조한다.
    return that.prefix + " " + item;
  })
}
```

##### 02. `Array.prototype.map`의 두 번째 인수로 add 메서드를 호출한 prefixer 객체를 가리키는 this를 전달한다.

- ES5에서 도입된 `Array.prototype.map`은 `콜백 함수 내부의 this 문제`를 해결하기 위해 두 번째 인수로 콜백 함수 내부에서 this로 사용할 객체를 전달할 수 있다.

```js
...
add(arr) {
  return arr.map(function (item) {
    return this.prefix + " " + item;
  }, this) // this에 바인딩된 값이 콜백 함수 내부의 this에 바인딩된다.
}
```

##### 03. `Function.prototype.bind` 메서드를 사용하여 add 메서드를 호출한 prefixer 객체를 가리키는 this를 바인딩 한다.

```js
...
add(arr) {
  return arr.map(function (item) {
    return this.prefix + " " + item;
  }.bind(this)) // this에 바인딩된 값이 콜백 함수 내부의 this에 바인딩된다.
}
```

#### ES6에서는 화살표 함수를 사용하요 `콜뱍 함수 내부의 this문제`를 해결할 수 있다.

```js
class Prefixer {
  constructor(prefix) {
    this.prefix = prefix;
  }

  add(arr) {
    return arr.map((item) => this.prefix + item);
  }
}

const prefixer = new Prefixer("-webkit-");
console.log(prefixer.add(["transition", "user-select"])); //  ["-webkit-transition", "-webkit-user-select"]
```

- 화살표 함수는 함수 자체의 this 바인딩을 갖지 않는다.
- 따라서 화살표 함수 내부에서 this를 참조하면 상위 스코프의 this를 그대로 참조한다.
  - 이를 `lexical this`라 한다
- 이는 마치 렉시컬 스코프와 같이 화살표 함수의 this가 함수가 정의된 위치에 의해 결정된다는 것을 의미한다.

### 03-04. super

- 화살표 함수는 함수 자체dml super 바인딩을 갖지 않는다.
- 따라서 화살표 함수 내부에서 super를 참조하면 this와 마찬가지로 상위 스코프의 super를 참조한다.

### 03-05. arguments

- 화살표 함수는 함수 자체의 arguments 바인딩을 갖지 않는다.
- 따라서 화살표 함수 내부에서 arguments를 참조하면 this와 마찬가지로 상위 스코프의 arguments를 참조한다.

## 04. Rest 파라미터

### 04-01. 기본 문법

- Rest 파라미터(나머지 매개변수)는 매개변수 이름 앞에 세개의 점 `...`을 붙여서 정의한 매게변수를 의미한다.
- Rest 파라미터는 함수에 전달된 인수들의 목록을 배열로 전달받는다.

  ```js
  function foo(...rest) {
    // 매개변수 rest는 인수들의 목록을 배열로 전달받는 Rest 파라미터다.
    console.log(rest); // [1, 2, 3, 4, 5]
  }

  foo(1, 2, 3, 4, 5);
  ```

### 04-02. Rest 파라미터와 arguments 객체

#### ES5

- ES5에서는 함수를 정의할 때 매개변수의 개수를 확정할 수 없는 가변 인자 함수의 경우 매개변수를 통해 인수를 전달받는 것이 불가능하므로 arguments 객체를 활용하여 인수를 전달받았다.
  - arguments 객체는 함수 호출 시 전달된 인수들의 정보를 담고 있는 순회 가능한 유사 배열 객체이며, 함수 내부에서 지역 변수처럼 사용할 수 있다.
  - 하지만 arguments 객체는 유사 배열 객체이므로 배열 메서드를 사용하기 위해서는 `Function.prototype.call`이나 `Function.prototype.apply`메서드를 사용해 arguments 객체를 배열로 변환해야 하는 번거로움이 있었다.

#### ES6

- ES6에서는 rest 파라미터를 사용하여 가변 인자 함수의 인수 목록을 배열로 직접 전달받을 수 있다.
  - 이를 통해 유사 배열 객체인 arguments 객체를 배열로 변환하는 번거로움을 피할 수 있었다.
- 함수와 ES6 메서드는 Rest 파라미터와 arguments 객체를 모두 사용할 수 있다.
- 화살표 함수는 함수 자체의 arguments 객체를 갖지 않는다.
  - 화살표 함수로 가변 인자 함수를 구현해야 할 때는 반드시 Rest 파라미터를 사용해야 한다.

## 05. 매개변수 기본값

- ES6에서 도입된 매개변수 기본값을 사용하면 함수 내에서 수행하던 인수 체크 및 초기화를 간소화할 수 있다.
- 매개변수 기본값은 매개변수에 인수를 전달하지 않은 경우와 `undefined`를 전달한 경우에만 유효하다.

```js
function sum(x = 0, y = 0) {
  return x + y;
}

console.log(sum(1, 2)); // 3
console.log(sum(1)); // 1
```
