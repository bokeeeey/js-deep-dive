# 16장 프로퍼티 어트리뷰트

## 01. 내부 슬롯과 내부 메서드

- 프로퍼티 어트리뷰트를 이해하기 위해서는 내부 슬롯과 내부 메서드의 개념에 대해 이해가 필요하다.
- 내부 슬롯과 내부 메서드는 자바스크립트 엔진의 구현 알고리즘을 설명하기 위해 ECMAScript 사양에서 사용하는 의사 프로퍼티와 의사 메서드다.
- ECMAScript 사양에 등장하는 이중 대괄호(`[[...]]`)로 감싼 이름들이 내부 슬롯과 내부 메서드다.
- 내부 슬롯과 내부 메서드는 자바스크립트 엔진의 내부 로직이므로 원칙적으로 자바스크립트는 직접적으로 접근하거나 호출할 수 있는 방법을 제공하지 않는다.
- 예를 들어, 모든 객체는 `[[Prototype]]`이라는 내부 슬롯을 갖는다.
  - 내부 슬롯은 자바스크립트 엔진의 내부 로직이므로 원칙적으로 직접 접근할 수 없지만 `[[Prototype]]` 내부 슬롯의 경우, `__proto__`를 통해 간접적으로 접근할 수 있다.

```js
const 0 = {};

// 내부 슬롯은 자바스크립트 엔진의 내부 로직이므로 직접 접근할 수 없다.
o.[[Prototype]] // Uncaught SyntaxError: Unexpected token '['

// 단, 일부 내부 슬롯과 내부 메서드에 한하여 간접적으로 접근할 수 있는 수단을 제공하기는 한다.
o.__proto__ // Object.prototype
```

## 02. 프로퍼티 어트리뷰트(Property Atrribute)와 프로퍼티 디스크립터 객체(Property Descripter Object)

- 자바스크립트 엔진은 프로퍼티를 생성할 때 프로퍼티의 상태를 나타내는 프로퍼티 어트리뷰트를 기본값으로 자동 정의한다.
  - 프로퍼티의 상태란
    `프로퍼티의 값(value)`
    `값의 갱신 가능 여부(writable)`
    `열거 가능 여부(enumerable)`
    `재정의 가능 여부(configurable)`를 말한다.
  - 프로퍼티 어트리뷰트는 자바스크립트 엔진이 관리하는 내부 상태 값인 내부 슬롯 `[[Value]], [[Writable]], [[Enumerable]], [[Configurable]]`이다.
- 따라서 프로퍼티 어트리뷰트에 직접 접근할 수 없지만, `Object.getOwnPropertyDescriptor` 메서드를 사용하여 간접적으로 확인할 수는 있다.

```js
const person = {
  name: "Kim",
};

// Property Attribute 정보를 제공하는 Property Descriptor Object를 반환한다.
console.log(Object.getOwnPropertyDescriptor(person, "name"));
// { value: "Kim", writable: true, enumerable: true, configurable: true }

// ES8에서 도입된 Object.getOwnPropertyDescriptors 메서드는 모든 프로퍼티의 프로퍼티 어트리뷰트 정보를 제공하는 프로퍼티 디스트립터 객체들을 반환한다.
Object.getOwnPropertyDescriptors(person);
```

## 03. 데이터 프로퍼티(data property)와 접근자 프로퍼티(accessor property)

- 프로퍼티는 데이터 프로퍼티와 접근자 프로퍼티로 구분할 수 있다.
  - 데이터 프로퍼티: key와 value값으로 구성된 일반적인 프로퍼티다. 지금까지 살펴본 모든 프로퍼티는 데이터 프로퍼티이다.
  - 접근자 프로퍼티: 자체적으로는 값을 갖지 않고 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 호출되는 접근자 함수로 구성된 프로퍼티이다.

### 03-01. 데이터 프로퍼티

- 데이터 프로퍼티 어트리뷰트는 자바스크립트 엔진이 프로퍼티를 생성할 때 기본값으로 자동 정의된다.
  | 프로퍼티 어트리뷰트 | 프로퍼티 디스크립터 객체의 프로퍼티 | 설명 |
  |-----------------|-----------------------------------|------|
  | `[[Value]]` | value | 프로퍼티 키를 통해 프로퍼티 값에 접근하면 반환되는 값이다. 프로퍼티 키를 통해 프로퍼티 값을 변경하면 `[[Value]]`에 값을 재할당한다. 이때 프로퍼티가 없으면 프로퍼티를 동적 생성하고 생성된 프로퍼티의 `[[Value]]`에 값을 저장한다. |
  | `[[Writable]]` | writable | 프로퍼티 값의 변경 가능 여부를 나타내며 불리언 값을 갖는다. `[[Writable]]`의 값이 false인 경우 해당 프로퍼티의 `[[Value]]`의 값을 변경할 수 없는 읽기 전용 프로퍼티가 된다. |
  | `[[Enumerable]]` | enumerable | 프로퍼티의 열거 가능 여부를 나타내며 불리언 값을 갖는다. `[[Enumerable]]`의 값이 false인 경우 해당 프로퍼티는 for...in 문이나 Object.keys 메서드 등으로 열거할 수 없다. |
  | `[[Configurable]]`| configurable | 프로퍼티의 재정의 가능 여부를 나타내며 불리언 값을 갖는다. `[[Configurable]]`의 값이 false인 경우 해당 프로퍼티의 삭제, 프로퍼티 어트리뷰트 값 변경이 금지된다. 단, `[[Writable]]`이 true인 경우 `[[Value]]`의 변경과 `[[Writable]]`을 false로 변경하는 것은 허용된다. |

- 프로퍼티가 생성될 때 `[[Value]]`의 값은 프로퍼티 값으로 초기화되며, `[[Writable]], [[Enumerable]], [[Configurable]]`의 값은 true로 초기화된다.
  - 이는 프로퍼티를 동적으로 추가해도 동일하다.

### 03-02. 접근자 프로퍼티

- 접근자 프로퍼티는 자체적으로 값을 갖지 않고 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 사용하는 접근자 함수로 구성된 프로퍼티다.
- 접근자 함수는 getter/setter 함수라고도 부른다.
- 접근자 프로퍼티는 getter와 setter 함수를 모두 정의할 수도 있고 하나만 정의할 수도 있다.

| 프로퍼티 어트리뷰트 | 프로퍼티 디스크립터 객체의 프로퍼티 | 설명                                                                                                                                                                                                                              |
| ------------------- | ----------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `[[Get]]`           | get                                 | 접근자 프로퍼티를 통해 데이터 프로퍼티의 값을 읽을 때 호출되는 접근자 함수다. 즉, 접근자 프로퍼티 키로 프로퍼티 값에 접근하면 프로퍼티 어트리뷰트 `[[Get]]`의 값, 즉 getter 함수가 호출되고 그 결과가 프로퍼티 값으로 반환된다.   |
| `[[Set]]`           | set                                 | 접근자 프로퍼티를 통해 데이터 프로퍼티의 값을 저장할 때 호출되는 접근자 함수다. 즉, 접근자 프로퍼티 키로 프로퍼티 값을 저장하면 프로퍼티 어트리뷰트 `[[Set]]`의 값, 즉 setter 함수가 호출되고 그 결과가 프로퍼티 값으로 저장된다. |
| `[[Enumerable]]`    | enumerable                          | 데이터 프로퍼티의 `[[Enumerable]]`과 같다.                                                                                                                                                                                        |
| `[[Configurable]]`  | configurable                        | 데이터 프로퍼티의 `[[Configurable]]`과 같다.                                                                                                                                                                                      |

```js
const person = {
  // 데이터 프로퍼티
  firstName: "Bokyeong",
  lastName: "Kim"

  // fullName은 접근자 함수로 구성된 접근자 프로퍼티다.
  // getter 함수 사용
  get fullName() {
    return `${this.firstName} ${this.lastName}`;
  },
  // settet 함수 사용
  set fullName(name) {
    // 배열 디스트럭처링 할당: 추후 단원으로 나올 예정(Chap.31)
    [this.firstName, this.lastName] = name.split(' ');
  }
};

// 데이터 프로퍼티를 통한 프로퍼티 값의 참조.
console.log(person.firstName + ' ' + person.lastName); // Bokyeong Kim

// 접근자 프로퍼티를 통한 프로퍼티 값의 저장
// 접근자 프로퍼티 fullName에 값을 저장하면 setter함수가 호출된다.
person.fullName = 'Bongchan Jung';
console.log(person); // { firstName: "Bongchan", lastName: "Jung" }

// 접근자 프로퍼티를 통한 값의 참조
// 접근자 프로퍼티 fullName에 접근하면 getter함수가 호출된다.
console.log(person.fullName); // Bongchan Jung

// firstName은 데이터 프로퍼티다.
// 데이터 프로퍼티는 [[Value]], [[Writable]], [[Enumerable]], [[Configurable]] 프로퍼티 어트리뷰트를 갖는다.
let descriptor = Object.getOwnPropertyDescriptor(person, 'firstName');
console.log(descriptor);
// { value: 'Bongchan', writable: true, enumerable: true, configurable: true }

// fullName은 접근자 프로퍼티다.
// 접근자 프로퍼티는 [[Get]], [[Set]], [[Enumerable]], [[Configurable]] 접근자 어트리뷰트를 갖는다.
descriptor = Object.getOwnPropertyDescriptor(person, 'fullName');
console.log(descriptor);
// { get: f, set: f, enumerable: true, configurable: true }
```

- 접근자 프로퍼티는 자체적으로 `값(프로퍼티 어트리뷰트 [[Value]])`을 가지지 않으며 다만 데이터 프로퍼티의 값을 읽거나 저장할 때 관여할 뿐이다.

## 04. 프로퍼티 정의

- 프로퍼티 정의란 새로운 프로퍼티를 추가하면서 프로퍼티 어트리뷰트를 명시적으로 정의하거나, 기존 프로퍼티의 프로퍼티 어트리뷰트를 재정의하는 것을 말한다.
- `Object.defineProperty`메서드를 사용하면 프로퍼티의 어트리뷰트를 정의할 수 있다.
  - `Object.defineProperty`메서드로 프로퍼티를 정의할 때 프로퍼티 디스크립터 객체의 프로퍼티를 일부 생략할 수 있다.
  - 프로퍼티 디스크립터 객체에서 생략된 어트리뷰트는 기본값이 적용된다.
    | 프로퍼티 디스크립터 객체의 프로퍼티 | 대응하는 프로퍼티 어트리뷰트 | 생략했을 때의 기본값 |
    | --- | --- | --- |
    | value | `[[Value]]` | undefined |
    | get | `[[Get]]` | undefined |
    | set | `[[Set]]` | undefined |
    | writable | `[[Writable]]` | false |
    | enumerable | `[[Enumerable]]` | false |
    | configurable | `[[Configurable]]` | false |

## 05. 객체 변경 방지

- 객체는 변경 가능한 값이므로 재할당 없이 직접 변경할 수 있다.
  - 프로퍼티 값을 추가하거나 삭제 가능하다.
  - 프로퍼티 값을 갱신할 수 있다.
  - `Object.defineProperty` 또는 `Object.defineProperties`메서드를 사용하여 프로퍼티 어트리뷰트를 재정의 할 수 있다.
- 객체 변겅 방지 메서드들은 객체의 변경을 금지하는 강도가 다르다.
  | 구분 | 메서드 | 프로퍼티 추가 | 프로퍼티 삭제 | 프로퍼티 값 읽기 | 프로퍼티 값 쓰기 | 프로퍼티 어트리뷰트 재정의 |
  | ------------ | --------------------------- | ------------- | ------------- | ---------------- | ---------------- | --------------------------- |
  | 객체 확장 금지 | Object.preventExtensions | X | ◯ | ◯ | ◯ | ◯ |
  | 객체 밀봉 | Object.seal | X | X | ◯ | ◯ | X |
  | 객체 동결 | Object.freeze | X | X | ◯ | X | X |

### 05-01. 객체 확장 금지

- `Object.preventExtansions` 메서드는 객체의 확장을 금지한다.
- 객체의 확장 금지란 프로퍼티 추가 금지를 의미한다.
- 확장이 금지된 객체는 프로퍼티 추가가 금지된다.
- 확장이 가능한지의 여부는 `Object.isExtensible` 메서드로 확인할 수 있다.

### 05-02. 객체 밀봉

- `Object.seal` 메서드는 객체를 밀봉한다.
- 객체 밀봉이란 프로퍼티 추가 및 삭제와 프로퍼티 어트리뷰트 재정의 금지를 의미한다.
- 밀봉된 객체는 읽기와 쓰기만 가능해진다.
- 밀봉된 객체인지 여부는 `Object.isSealed` 메서드로 확인할 수 있다.

### 05-03. 객체 동결

- `Object.freeze` 메서드는 객체를 동결한다.
- 객체 동결이란 프로퍼티 추가 및 삭제와 프로퍼티 어트리뷰트 재정의 금지, 프로퍼티 값 갱신 금지를 의미한다.
- 동결된 객체는 읽기만 가능하다.
- 동결된 객체인지 여부는 `Object.isFrozen` 메서드로 확인할 수 있다.

### 05-04. 불변 객체

- 위에서 언급된 변경 방지 메서드들은 얕은 변경 방지로 직속 프로퍼티만 변경이 방지되고 중첩 객체까지는 영향을 주지 않는다.
- 객체의 중첩 객체까지 동결하여 변경이 불가능한 읽기 전용의 불변 객체를 구현하려면 객체를 값으로 갖는 모든 프로퍼티에 대해 재귀적으로 `Object.freeze` 메서드를 호출해야 한다.
