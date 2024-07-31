# 25장 클래스

## 01. 클래스는 프로토타입의 문법적 설탕인가?

- ES6에서 도입된 클래스는 기존 프로토타입 기반 객체지향 프로그래밍보다 자바나 C#과 같은 클래스 기반 객체지향 프로그래밍에 익숙한 프로그래머가 더욱 빠르게 학습할 수 있도록 클래스 기반 객체지향 프로그래밍 언어와 매우 흡사한 새로운 객체 생성 메커니즘을 제시한다.
- 클래스는 함수이며 기존 프로토타입 기반 패턴을 클래스 기반 패턴처럼 사용할 수 있도록 하는 문법적 설탕이라고 볼 수도 있다.(새로운 객체 생성 메커니즘으로 보는 것이 좀 더 합당하다.)
- 클래스는 생성자 함수보다 엄격하며 생성자 함수에서는 제공하지 않는 기능도 제공한다.
- 클래스는 생성자 함수와 매우 유사하게 동작하지만 몇가지 차이가 존재한다.
  > 1. 클래스를 new 연산자 없이 호출하면 에러가 발생한다. 하지만 생성자 함수를 new 연산자 없이 호출하면 일반 함수로서 호출된다.
  > 2. 클래스는 상속을 지원하는 extends와 super 키워드를 제공한다.
  > 3. 클래스는 호이스팅이 발생하지 않는 것처럼 동작한다.
  > 4. 클래스 내의 모든 코드에는 암묵적으로 strict mode가 지정되어 실행되며 strict mode를 해제할 수 없다.
  > 5. 클래스의 constructor, 프로토타입 메서드, 정적 메서드는 모두 프로퍼티 어트리뷰트 `[[Enumerable]]`의 값이 false다. 열거되지 않는다.

## 02. 클래스 정의

- 클래스는 class 키워드를 사용하여 정의한다.
- 클래스 이름은 생성자 함수와 마찬가지로 파스칼 케이스를 사용하는 것이 일반적이다.
- 표현식으로 클래스를 정의할 수도 있다.
  - 표현식으로 클래스를 정의할 수 있다는 것은 클래스가 값으로 사용할 수 있는 일급 객체라는 것을 의미한다.
    > - 무명의 리터럴로 생성할 수 있다.
    > - 변수나 자료구조(객체, 배열 등)에 저장할 수 있다.
    > - 함수의 매게변수에게 전달할 수 있다.
    > - 함수의 반환값으로 사용할 수 있다.

```js
// 클래스 선언문
class Person {
  // 생성자
  constructor(name) {
    // 인스턴스 생성 및 초기화
    this.name = name; // name 프로퍼티는 public하다.
  }

  // 프로토타입 메서드
  sayHi() {
    console.log(`Hi! My name is ${this.name}`);
  }

  // 정적 메서드
  static sayHello() {
    console.log("Hello");
  }
}

// 인스턴스 생성
const me = new Person("Kim");

// 인스턴스 프로퍼티 참조
console.log(me.name); // Kim
// 프로토타입 메서드 호출
me.sayHi(); // Hi! My name is Kim
// 정적 메서드 호출
Person.sayHello(); // Hello

// 클래스와 생성자 함수의 정의 방식은 형태적인 면에서 매우 유사하다.
// 만약 생성자 함수로 정의했다면?
var Person = function () {
  // 생성자 함수
  function Person(name) {
    this.name = name;
  }

  // 프로토타입 메서드
  Person.prototype.sayHi = function () {
    console.log(`Hi! My name is ` + this.name);
  };

  // 정적 메서드
  Person.sayHello = function () {
    console.log("Hello");
  };
};
```

## 03. 클래스 호이스팅

- 클래스는 함수로 평가된다.
  - 클래스 선언문으로 정의한 클래스는 함수 선언문과 같이 소스코드 평가 과정(런타임) 이전에 먼저 평가되어 객체를 생성한다.
  - 이때 클래스가 평가되어 생성한 함수 객체는 생성자 함수로서 호출할 수 있는 함수 constructor다.
  - 생성자 함수로서 호출할 수 있는 함수는 함수 정의가 평가되어 함수 객체를 생성하는 시점에 프로토타입도 더불어 생성된다.
    - 프로토타입과 생성자 함수는 단독으로 존재할 수 없고 언제나 쌍으로 존재하기 때문
  - 단, 클래스는 클래스 정의 이전에 참조할 수 없다.
- 클래스 선언문도 변수 선언, 함수 정의와 마찬가지로 호이스팅이 발생한다.
  - 단, 클래스는 let, const 키워드로 선언한 변수처럼 호이스팅된다.
  - 클래스 선언문 이전에 일시적 사각지대(TDZ)에 빠지기 때문에 호이스팅이 발생하지 않는 것처럼 동작한다.

> var, let, const, function, function\*, class 키워드를 사용하여 선언된 모든 식별자는 호이스팅된다. 모든 선언문은 런타임 이전에 먼저 실행되기 때문이다.

## 04. 인스턴스 생성

- 클래스는 생성자 함수이며 new 연산자와 함께 호출되어 인스턴스를 생성한다.
- 클래스는 인스턴스를 생성하는 것이 유일한 존재 이유므로 반드시 new 연산자와 함께 호출해야 한다.
  - 함수는 new 연산자의 사용 여부에 따라 일반 함수로 호출되거나 인스턴스 생성을 위한 생성자 함수로 호출된다.
- 인스턴스를 생성하지 않고 기명 클래스 표현식의 클래스 이름을 사용해 인스턴스를 생성하면 에러가 발생한다.
  - 이는 기명 함수 표현식과 마찬가지로 클래스 표현식에서 사용한 클래스 이름은 외부 코드에서 접근 불가능하기 때문이다.

```js
class Person {}

// 클래스를 new 연산자 없이 호출하면 타입 에러가 발생한다.
const me = Person();
// TypeError: Class constructor Person cannot be invoked without 'new'

const Person = class MyClass {};

// 함수 표현식과 마찬가지로 클래스를 가리키는 식별자로 인스턴스를 생성해야 한다.
const me = new Person();

// 클래스 이름 MyClass는 함수와 동일하게 클래스 몸체 내부에서만 유효한 식별자다.
console.log(MyClass); // ReferenceError: MyClass is not defined

const you = new MyClass(); // ReferenceError: MyClass is not defined
```

## 05. 메서드

- 클래스 몸체에는 0개 이상의 메서드만 선언할 수 있다.
- 클래스 몸체에서 정의할 수 있는 메서드는 constructor(생성자), 프로토타입 메서드, 정적 메서드의 세가지가 있다.

### 05-01. constructor

- constructor는 인스턴스를 생성하고 초기화하기 위한 특수한 메서드다.
- constructor는 이름을 변경할 수 없다.
- 함수 객체가 가지고 있는 prototype 프로퍼티가 가리키는 프로토타입 객체의 constructor 프로퍼티는 클래스 자신을 가리키고 있다.
  - 이는 클래스가 인스턴스를 생성하는 생성자 함수라는 것을 의미한다.
  - new 연산자와 함께 클래스를 호출하면 클래스는 인스턴스를 생성한다.
  - 생성자 함수와 마찬가지로 constructor 내부에서 this에 추가한 프로퍼티는 인스터는 프로퍼티가 된다.
  - constructor 내부의 this는 생성자 함수와 마찬가지로 클래스가 생성한 인스턴스를 가리킨다.
- 클래스 몸체에 정의한 constructor는 단순한 메서드가 아니다.
  - constroctor는 메서드로 해석되는 것이 아니라 클래스가 평가되어 생성한 함수 객체 코드의 일부가 된다.
  - 다시 말해, 클래스 정의가 평가되면 constoructor의 기술된 동작을 하는 함수 객체가 생성된다.
- constructor는 생성자 함수와 유사하지만 몇 가지 차이가 있다.
  - constructor는 클래스 내에 최대 한 개만 존재할 수 있다
  - constructor는 생략할 수 있다.
    - constructor를 생략하면 클래스에 빈 constructor가 암묵적으로 정의된다.
    - constructor를 생략한 클래스는 빈 constructor에 의해 빈 객체를 생성한다.

```js
// 클래스
class Person {
  // 생성자
  constructor(name) {
    // 인스턴스 생성 및 초기화
    this.name = name;
  }
}

// 생성자 함수
function Person(name) {
  // 인스턴스 생성 및 초기화
  this.name = name;
}
```

> ##### 클래스의 constructor 메서드와 프로토타입의 constructor 프로퍼티
>
> - 클래스의 constructor 메서드와 프로토타입의 constructor 프로퍼티는 이름이 같아 혼동하기 쉽지만 직접적인 관련이 없다.
> - 프로토타입의 constructor 프로퍼티는 모든 프로토타입이 가지고 있는 프로퍼티이며, 생성자 함수를 가리킨다.

### 05-02. 프로토타입 메서드

- 생성자 함수에서는 인스턴스를 생성하는 경우에 프로토타입 메서드를 생성하기 위해서는 명시적으로 프로토타입에 메서드를 추가해야 한다.
- 클래스 몸체에서 정의한 메서드는 생성자 함수에 의한 객체 생성 방식과는 다르게 클래스의 prototype 프로퍼티에 메서드를 추가하지 않아도 기본적으로 프로토타입 메서드가 된다.
  - 생성자 함수와 마찬가지로 클래스가 생성한 인스턴스는 프로토타입 체인의 일원이 된다.

```js
class Person {
  // 생성자
  constructor(name) {
    // 인스턴스 생성 및 초기화
    this.name = name;
  }

  // 프로토타입 메서드
  sayHi() {
    console.log(`Hi! My name is ${this.name}`);
  }
}

const me = new Person("Kim");
me.sayHi(); // Hi My name is Kim

// me 객체의 프로토타입은 Person.prototype이다.
Object.getPrototypeOf(me) === Person.prototype; // true
me instanceof Person; // true

// Person.prototype의 프로토타입은 Object.prototype이다.
Object.getPrototypeOf(Person.prototype) === Object.prototype; // true
me instanceof Object; // true

// me 객체의 constructor는 Person 클래스다.
me.constructor === Person; // true
```

### 05-03. 정적 메서드

- 정적 메서드란 인스턴스를 생성하지 않아도 호출할 수 있는 메서드를 말한다.
- 클래스에서는 메서드에 static 키워드를 붙이면 메서드(클래스 메서드)가 된다.
  - 생성자 함수의 경우 정적 메서드를 생성하기 위해서는 명시적으로 생성자 함수에 메서드를 추가 해야 한다.

```js
class Person {
  // 생성자
  constructor(name) {
    // 인스턴스 생성 및 초기화
    this.name = name;
  }

  // 정적 메서드
  static sayHi() {
    console.log("Hi!");
  }
}

// 정적 메서드는 클래스로 호출한다.
// 정적 메서드는 인스턴스 없이도 호출할 수 있다.
Person.sayHi(); // Hi!
```

### 05-04. 정적 메서드와 프로토타입 메서드의 차이

- 정적 메서드와 프로토타입 메서드의 차이는 다음과 같다.
  1.  정적 메서드와 프로토타입 메서드는 자신이 속해 있는 프로토타입 체인이 다르다.
  2.  정적 메서드는 클래스로 호출하고 프로토타입 메서드는 인스턴스로 호출한다.
  3.  정적 메서드는 인스턴스 프로퍼티를 참조할 수 없지만 프로토타입 메서드는 인스턴스 프로퍼티를 참조할 수 있다.
- 메서드 내부에서 인서튼스 프로퍼티를 참조할 필요가 있다면 this를 사용해야 하며, 이러한 경우 프로토타입 메서드로 정의해야 한다.

```js
class Square {
  constructor(width, height) {
    this.width = width;
    this.height = height;
  }

  // 프로토타입 메서드
  area() {
    return this.width * this.height;
  }
}

const square = new Square(10, 10);
console.log(square.area()); // 100
```

### 05-05. 클래스에서 정의한 메서드의 특징

- 클래스에서 정의한 메서드는 다음과 같은 특징을 갖는다.
  1.  function 키워드를 생략한 메서드 축약 표현을 사용한다.
  2.  객체 리터럴과는 다르게 클래스에 메서드를 정의할 때는 콤마가 필요 없다.
  3.  암묵적으로 strict mode로 실행된다
  4.  `for...in문`이나 `Object.keys` 메서드 등으로 열거할 수 없다.
      - 프로퍼티 열거 가능 여부를 나타내며, 불리언 값을 갖는 프로퍼티 어트리뷰트 `[[Enumerable]]`의 값이 false다.
  5.  내부 메서드 `[[Construct]]`를 갖지 않는 non-constructor다. 따라서 new 연산자와 함께 호출할 수 없다.

## 06. 클래스의 인스턴스 생성 과정

#### 1. 인스턴스 생성과 this 바인딩

- new 연산자와 함께 클래스를 호출하면 constructor의 내부 코드가 실행되기에 앞서 암묵적으로 빈 객체가 생성된다.
  - 이 빈 객체가 클래스가 생성한 인스턴스다.
- 암묵적으로 생성된 빈 객체(인스턴스)는 this에 바인딩 된다.
  - constructor 내부의 this는 클래스가 생성한 인스턴스를 가리킨다.

#### 2. 인스턴스 초기화

- constructor의 내부 코드가 실행되어 this에 바인딩되어 있는 인스턴스를 초기화한다.
  - this에 바인딩되어 있는 인스턴스에 프러퍼티를 추가하고 constructor가 인수로 전달받은 초기값으로 인스턴스의 프로퍼티 값을 초기화한다.
  - 만약 constructor가 생략되었다면 이 과정도 생략된다.

#### 3. 인스턴스 반환

- 클래스의 모든 처리가 끝나면 완성된 인스턴스가 바인딩된 this가 암묵적으로 반환된다.

```js
class Person {
  constructor(name) {
    // 1. 암묵적으로 인스턴스가 생성되고 this에 바인딩된다.
    console.log(this); // Person {}
    console.log(Object.getPrototypeOf(this) === Person.prototype); // true

    // 2. this에 바인딩되어 있는 인스턴스를 초기화한다.
    this.name = name;

    // 3. 완성된 인스턴스가 바인딩된 this가 암묵적으로 반환된다.
  }
}
```

## 07. 프로퍼티

### 07-01. 인스턴스 프로퍼티

- 인스턴스 프로퍼티는 constructor 내부에서 정의해야 한다.
  - constructor 내부 코드가 실행되기 이전에 constructor 내부의 this에는 이미 클래스가 암묵적으로 생성한 인스턴스 빈 객체가 바인딩되어 있다.
- constructor 내부에서 this에 인스턴스 프로퍼티를 추가한다.
  - 이로써 클래스가 암묵적으로 생성한 빈 객체(인스턴스)에 프로퍼티가 추가되어 인스턴스가 초기화된다.
  - constructor 내부에서 this에 추가한 프로퍼티는 언제나 클래스가 생성한 인스턴스의 프로퍼티가 된다.

```js
class Person {
  constructor(name) {
    // 인스턴스 프로퍼티
    this.name = name; // name 프로퍼티는 public하다.
  }
}

const me = new Person("Kim");

// name은 public하다.
console.log(me.name); // Kim
```

### 07-02. 접근자 프로퍼티

- 접근자 프로퍼티는 자체적으로는 값을 갖지 않고 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 사용하는 접근자 함수로 구성된 프로퍼티이다.
- 접근자 프로퍼티는 클래스에서도 사용할 수 있다.
- 클래스의 메서드는 기본적으로 프로토타입 메서드가 된다
  - 클래스의 접근자 프로퍼티 또한 인스턴스 프로퍼티가 아닌 프로토타입의 프로퍼티가 된다.

```js
class Person {
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  }

  // fullName은 접근자 함수로 구성된 접근자 프로퍼티다.
  // getter 함수
  get fullName() {
    return `${this.firstName} ${this.lastName}`;
  }

  // setter 함수
  set fullName(name) {
    [this.fitstName, this.lastName] = name.split(" ");
  }
}

const me = new Person("bokyeong", "Kim");

// 데이터 프로퍼티를 통한 프로퍼티 값의 참조
console.log(`${me.firstName} ${me.lastName}`); // bokyeong Kim

// 접근자 프로퍼티를 통한 프로퍼티 값의 저장
// 접근자 프로퍼티 fullName에 값을 저장하면 setter 함수가 호출된다.
me.fullName = "Bokyeong Kim";
console.log(me); // { firstName: "Bokyeong", lastName: "Kim" }

// 접근자 프로퍼티를 통한 프로퍼티 값의 참조
// 접근자 프로퍼티 fullName에 접근하면 getter 함수가 호출된다.
console.log(me.fullName); // bokyeong Kim

// fullName은 접근자 프로퍼티다.
// 접근자 프로퍼티는 get, set, enumerable, configurable 프로퍼티 어트리뷰트를 갖는다.
console.log(Object.getOwnPropertyDescription(Person.prototype, "fullName"));
// { get: f, set: f, enumerable: false, configurable: true }
```

### 07-03. private 필드 정의 제안

- 클로저의 활용에서 나왔듯이 자바스크립트는 캡슐화는 완전하게 지원하지 않는다.
  - 인스턴스 프로퍼티는 인스턴스를 통해 클래스 외부에서 언제나 참조할 수 있는 public이다.
- 클래스 필드 정의 제안을 사용하더라도 클래스 필드는 기본적으로 public하기 때문에 외부에 그대로 노출된다.
- 하지만 현대 private 필드를 정의할 수 있는 새로운 표준 사양이 제안되어있는 상태이다.

```js
class Person {
  // private 필드 정의
  #name = "";

  constructor(name) {
    // private 필드 참조
    this.#name = name;
  }
}

const me = new Person("Kim");

// private 필드 #name은 클래스 외부에서 참조할 수 없다.
console.log(me.#name);
// SyntaxError: Private field '#name' must be declared in an enclosing class
```

- public 필드는 어디서든 참조할 수 있지만 private 필드는 클래스 내부에서만 참조할 수 있다.
  - 외부에서 private 필드에 직접 접근할 수 있는 방법은 없다.
    - 다만 접근자 프로퍼티를 통해 간접적으로 접근하는 방법은 유효하다.
      | 접근 가능성 | public | private |
      | ------------------------ | ------ | ------- |
      | 클래스 내부 | ⭕ | ⭕ |
      | 자식 클래스 내부 | ⭕ | ❌ |
      | 클래스 인스턴스를 통한 접근 | ⭕ | ❌ |

> #### 타입스크립트(TypeScript)
>
> C#의 창시자인 덴마크 출신 소프트웨어 엔지니어 Anders Hejisberg가 개발을 주도한 자바스크립트의 상위 확장 superset인 타입스크립트는 클래스 기반 객체지향 언어가 지원하는 접근 제한자인 public, private, protected를 모두 지원하며, 의미 또한 기본적으로 동일하다.

## 08. 상속에 의한 클래스 확장

### 08-01. 클래스 상속과 생성자 함수 상속

- 상속에 의한 클래스 확장은 프로토타입 기반 상속과는 다른 개념이다.
  - 프로토타입 기반 상속은 프로토타입 체인을 통해 다른 객체의 자산을 상속받는 개념이다
- 상속에 의한 클래스 확장은 기존 클래스를 상속받아 새로운 클래스를 확장하여 정의한다.
- 클래스는 상속을 통해 다른 클래스를 확장할 수 있는 문법은 extends 키워드가 기본적으로 제공된다.

### 08-02. extends 키워드

- 상속을 통해 클래스를 확장하려면 extends 키워드를 사용하여 상속받을 클래스를 정의한다.
- 상속을 통해 확장된 클래스를 서브클래스라 부르고, 서브클래스에게 상속된 클래스를 수퍼클래스라 부른다.
  - 서브클래스를 파생클래스 또는 자식클래스 라고 부르기도 한다.
  - 수퍼클래스를 베이스 클래스 또는 부모 클래스 라고 부르기도 한다.
- 클래스도 프로토타입을 통해 상속 관계를 구현한다.
  - 수퍼클래스와 서브클래스는 인스턴스의 프로토타입 체인뿐 아니라 클래스 간의 프로토타입 테인도 생성한다.
  - 이를 통해 프로토타입 메서드, 정적 메서드 모두 상속이 가능하다.

### 08-03. 동적 상속

- extends 키워드는 클래스뿐만 아니라 생성자 함수를 상속받아 클래스를 확장할 수도 있다.
  - 단, extends 키워드 앞에는 반드시 클래스가 와야 한다.
  - extends 키워드 다음에는 클래스뿐만이 아니라 `[[Construct]]`내부 메서드를 갖는 함수 객체로 평가될 수 있는 모든 표현식을 사용할 수 있다.
  - 이를 통해 동적으로 상속받을 대상을 결정할 수 있다.

```js
function Base1() {}

class Base2 {}

let condition = true;

// 조건에 따라 동적으로 상속 대상을 결정하는 서브클래스
class Derived extends (condition ? Base1 : Base2) {}

const derived = new Derived();
console.log(derived); // Derived {}

console.log(derived instanceof Base1); // true
console.log(derived instanceof Base2); // false
```

### 08-04. 서브클래스의 constructor

- 클래스에서는 constructor를 생략하면 클래스에 비어있는 constructor가 암묵적으로 정의된다.
- 서브클래스에서도 constructor를 생략하면 클래스에 다음과 같은 constructor가 암묵적으로 정의된다.
- 수퍼클래스와 서브클래스 모두 constructor를 생략하면 빈 객체가 생성된다
  - 프로퍼티를 소유하는 인스턴스를 생성하려면 constructor 내부에서 인스턴스에 프로퍼티를 추가해야 한다.

### 08-05. super 키워드

- super 키워드는 함수처럼 호출할 수도 있고 this와 같이 식별자처럼 참조할 수 있는 특수한 키워드다.
  - super를 호출하면 수퍼클래스의 constructor(super-constructor)를 호출한다.
  - super를 참조하면 수퍼클래스의 메서드를 호출할 수 있다.

#### 1. super 호출

- super를 호출하면 수퍼클래스의 constructor(super-constructor)를 호출한다.
- 수퍼클래스의 constructor 내부에서 추가한 프로퍼티를 생성한다면 서브클래스의 constructor를 생략할 수 있다.
- 이때 new 연산자와 함께 서브클래스를 호출하면서 전달한 인수는 모두 서브클래스에 암묵적으로 정의된 constructor의 super 호출을 통해 constructor에 전달된다.

```js
// 수퍼클래스
class Base {
  constructor(a, b) {
    this.a = a;
    this.b = b;
  }
}

// 서브클래스
class Derived extends Base {
  constructor(a, b, c) {
    super(a, b);
    this.c = c;
  }
}

const derived = new Derived(1, 2, 3);
console.log(derived); // Derived { a: 1, b: 2, c: 3 }
```

- super를 호출할 때 주의할 사항은 다음과 같다
  1.  서브클래스에서 constructor를 생략하지 않는 경우 서브클래스의 constructor에서는 반드시 super를 호출해야 한다.
  2.  서브클래스의 constructor에서 super를 호출하기 전에는 this를 참조할 수 없다.
  3.  super는 반드시 서브클래스의 constructor에서만 호출한다. 서브클래스가 아닌 클래스의 constructor나 함수에서 super를 호출하면 에러가 발생한다.

#### 2. super 참조

- 메서드 내에서 super를 참조하면 수퍼클래스의 메서드를 호출할 수 있다.
  1. 서브클래스의 프로토타입 메서드 내에서 `super.sayHi`는 수퍼클래스의 프로토타입 메서드 `sayHi`를 가키린다. 2.서브클래스의 정적 메서드 내에서 `super.sayHi`는 수퍼클래스의 정적 메서드 `sayHi`를 가리킨다.
