Q: [민영] 객체지향 프로그래밍이란 무엇인가요? <br>
A: 객체지향 프로그래밍(OOP)은 프로그램을 독립적인 단위인 객체들의 집합으로 표현하려는 프로그래밍 패러다임입니다. 각 객체는 상태 데이터를 나타내는 프로퍼티와 동작을 나타내는 메서드를 포함합니다. OOP는 코드 재사용과 유지보수성을 높이고, 실세계의 개념을 반영한 프로그램을 만들기 위해 고안되었습니다.

Q: [아영] 자바스크립트가 상속을 구현하는 방법과 얻을 수 있는 이점은? <br>
A:

- 프로토타입
- 상속은 코드의 재사용 관점에서 유용하다.
- 인스턴스가 동일한 메서드를 중복 소유하는 것은 메모리를 불필요하게 낭비한다. 또한 인스턴스를 생성할 때마다 메서드를 생성하므로 퍼포먼스에도 악영향을 준다.
- 공통적으로 사용할 프로퍼티나 메서드를 프로토타입에 미리 규현해 두면 생성자 함수가 생성할 모든 인스턴스는 별도의 구현없이 상위 객체인 프로토타입의 자산을 공유해서 사용할 수 있다.

```js
function Circle(radius) {
  this.radius = radius;
  this.getArea = function () {
    // Math.PI는 원주율을 나타내는 상수다.
    return Math.PI * this.radius ** 2;
  };
}
```

```js
function Circle(radius) {
  this.radius = radius;
}

Circle.prototype.getArea = function () {
  return Math.PI * this.radius ** 2;
};
```

Q: [지원] 직접 상속의 장점 <br>
A:

- new 연산자 없이 객체 생성 가능
- 프로토타입을 지정하면서 객체 생성 가능
- 객체 리터럴에 의해 생성된 객체도 상속받기 가능

Q: [서인] 프로토타입 체인이란? <br>
A: 자바스크립트는 객체의 프로퍼티(메서드 포함)에 접근하려고 할 때 해당 객체에 접근하려는 프로퍼티가 없다면 [[Prototype]] 내부 슬롯의 참조를 따라 부모 역할을 하는 프로토타입의 프로퍼티를 순차적으로 검색합니다.

Q: [아영]

```js
function Person(name) {
  this.name = name;
}

Person.prototype.sayHello = function () {
  console.log(`Hi! My name is ${this.name}`);
};

const me = new Person("Lee");

// hasOwnProperty는 Object.prototype의 메서드다.
console.log(me.hasOwnProperty("name"));
me.hasOwnProperty("name");
```

이 실행되는 과정을 프로토타입체인과 연관지어서 설명해주세요. <br>
A:

- me 객체에서 hasOwnProperty 메서드를 검색한다. 해당 메서드가 없으므로 프로토타입 체인을 따라 [[Prototype]]내부 슬롯에 바인딩되어 있는 프로토타입(Person.prototype)으로 이동하여 hasOwnProperty 메서드를 검색한다.
- Person.prototype에도 hasOwnProperty 메서드가 없으므로 프로토타입 체인을 따라, [[Prototype]] 내부 슬롯에 바인딩되어 있는 프로토타입(Object.prototype)으로 이동하여 hasOwnProperty 메서드 검색한다.
- Object.prototype에는 hasOwnProperty 메서드가 존재한다. 자바스크립트 엔진은 Object.prototype.hasOwnProperty 메서드를 호출한다. 이때 Object.prototype.hasOwnProperty 메서드의 this에는 me객체가 바인딩된다.

Q: [민영] 함수 객체의 prototype 프로퍼티와 proto의 차이점은 무엇인가요? <br>
A:

- prototype 프로퍼티는 함수 객체(특히 생성자 함수)만이 가지며, 이 함수가 생성할 인스턴스의 프로토타입을 가리킵니다.
- proto는 모든 객체가 가지는 접근자 프로퍼티로, 해당 객체의 프로토타입을 가리킵니다.

**p.270 표 참고**

Q: [서인] 프로토타입 체인의 최상위 객체는 무엇이고, 여기서도 프로퍼티를 검색할 수 없을 때 어떻게 되나요? <br>
A: 프로토타입 체인의 최상위 객체는 언제나 Object.prototype입니다. 따라서 모든 객체는 Object.prototype을 상속받습니다.
여기서도 프로퍼티를 검색할 수 없을 때 undefined를 반환합니다. 이때 에러가 발생하지 않습니다.

Q: [서인] 스코프 체인과 프로토타입 체인의 연관성을 설명해주세요.
A:

- 프로토타입 체인은 상속과 프로퍼티 검색을 위한 메커니즘 입니다.
- 스코프 체인은 식별자 검색을 위한 메커니즘 입니다.
- 객체의 프로퍼티를 검색할 때 먼저 스코프 체인에서 해당 객체의 식별자를 검색하고, 해당 객체의 프로토타입 체인에서 메서드를 검색합니다.
- 이처럼 스코프 체인과 프로토타입 체인은 협력해 식별자와 프로퍼티를 검색하는 데 사용합니다.
