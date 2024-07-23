# 08장 제어문

### 01. 블록문

> 블록문(block statement / compound statement)은 0개 이상의 문을 중괄호로 묶은 것으로, 코드 블록 또는 블록 이라고 부르기도 한다.

- 자바스크립트는 블록문을 하나의 실행 단위로 취급한다.
- 문의 끝에는 세미콜론을 붙이는 것이 일반적 이지만, 블록문은 언제나 문의 종료를 의미하는 자체 종결성을 갖기 때문에 블록문의 끝에는 세미콜론을 붙이지 않는다.

```js
// 블록문
{
  var foo = 10;
}

// 제어문
var x = 1;
if (x < 10) {
  x++;
}

// 함수 선언문
function sum(a, b) {
  return a + b;
}
```

### 02. 조건문

> 조건문(conditional statement)은 주어진 조건식(conditional expression)의 평가 결과에 따라 코드 블록(블록문)의 실행을 결정한다.
> 조건식은 불리언 값으로 평가될 수 있는 표현식이다.

#### 02-1. switch문

```js
var year = 2000; // 2000년은 윤년으로 2월이 29일이다.
var month = 2;
var days = 0;

switch (month) {
  case 1:
  case 3:
  case 5:
  case 7:
  case 8:
  case 10:
  case 12:
    days = 31;
    break;
  case 4:
  case 6:
  case 9:
  case 11:
    days = 30;
    break;
  case 2:
    // 윤년 계산 알고리즘
    // 1. 연도가 4로 나누어떨어지는 해(2000, 2004, 2008, 2012, 2016, 2020...)는 윤년이다.
    // 2. 연도가 4로 나누어떨어지더라도 연도가 100으로 나누어떨어지는 해(2000, 2100, 2200...)는 평년이다.
    // 3. 연도가 400으로 나누어떨어지는 해(2000, 2400, 2800..)는 윤년이다.
    days = (year % 4 === 0 && year % 100 !== 0) || year % 400 === 0 ? 29 : 28;
    break;
  default:
    console.log("Invalid month");
}

console.log(days); // 29
```

- 여러개의 case문을 하나의 조건으로 사용할 수 있다.

> switch문은 다양한 키워드와 복잡한 문법이 존대한다.
> C 언어를 기반으로 하는 프로그래밍 언어는 대부분 switch 문을 지원하지만 파이썬과 같이 switch 문들 지원하지 않는 프로그래밍 언어도 있다.

> 만약 if...else 문으로 해결할 수 있다면 if...else 문을 사용하는 편이 좋다. 하지만 조건이 너무 많아서 switch 문을 사용했을 때 가독성이 더 좋다면 switch문을 사용하는 편이 좋다.

### 03. 반복문

- 반복문(loop statement)은 조건식의 평가 결과가 참인 경우 코드 블록을 실행한다. 그 후 조건식을 다시 평가하여 여전히 참인 경우 코드 블록을 다시 실행한다.
- 이는 조건식이 거짓일 때 까지 반복된다.
- 자바스크립트는 세가지 반복문인 for 문, while 문, do...while 문을 제공한다.

> #### 반복문을 대체할 수 있는 다양한 기능
>
> 자바스크립트는 배열을 순회할 때 사용하는
> forEach 메서드,
> 객체의 프로퍼티를 열거할 때 사용하는 for...in 문,
> ES6에서 도입된 이터러블을 순회할 수 있는 for...of 문
> 과 같이 반복문을 대체할 수 있는 다양한 기능을 제공한다.

#### 03-01. while 문

- while 문은 반복횟수가 불명확할 때 주로 사용한다.

```js
// 무한루프
var count = 0;

while (true) {
  console.log(count);
  count++;
  // count가 3이면 코드 블록을 탈출한다.
  if (conut === 3) break;
}
// 0 1 2
```

- 조건식의 평가 결과가 언제나 참이면 무한루프가 된다.
- 무한루프에서 탈출하기 위해서는 코드 블록 내에 if 문으로 탈출 조건을 만들고 break 문으로 코드 블록을 탈출한다.

#### 03-02. do...while 문

> do...while 문은 코드 블록을 먼저 실행하고 조건식을 평가한다. 따라서 코드블록은 무조건 한 번 이상 실행된다.

```js
var count = 0;

// count가 3보다 작을 때까지 코드 블록을 계속 반복 실행한다.
do {
  console.log(count); // 0 1 2
  count++;
} while (count < 3);
```

#### 03-03. break 문

- break 문을 사용하면 레이블 문, 반복문`(for, for...in, fir...of, while, do...while)` 또는 switch 문의 코드 블록을 탈출한다.
- 레이블 문은 일반적으로 권장하지 않는다.
- 레이블 문을 사용하면 프로그램의 흐름이 복잡해져서 가독성이 나빠지고 오류를 발생시킬 가능성이 높아지기 때문이다.

#### 03-04. continue 문

- continue 문은 반복문의 코드 블록 실행을 현 지점에서 중단하고 반복문의 증감식으로 실행 흐름을 이동시킨다.

```js
var string = 'Hello World';
var search = 'l';
var count = 0;

// 문자열은 유사 배열이므로 for 문으로 순회할 수 있다.
for (var i = 0; i < string.length; i++) {
  // 'l'이 아니면 현 지점에서 실행을 중단하고 반복문의 증감식으로 이동한다.
  if (string[i] !== search) continue;
  const++;
}

console.log(count); // 3
```
