## 15장\_let,const 키워드와 블록 레벨 스코프

<br>

## var 키워드로 선언한 변수의 문제점

앞서 14장에서 살펴본 바와 같이 var 키워드로 선언한 변수는

- 변수 중복 선언 허용(의도치 않은 변수 값 변경)
- "함수"의 코드 블록만을 지역 스코프로 인정함.  
  (코드 블록 내에서 선언해도 전역 변수가 된다.)
- 변수 호이스팅( 흐름에 맞지 않아 가독성이 떨어지고 오류를 발생시킬 가능성이 있다.)

이러한 문제점들이 있었다. 이러한 단점을 보완하기 위해 ES6에서는 새로운 변수 키워드 let과 const가 도입되었다.

<br>
<br>

## var와 let의 차이

① 변수 중복 선언 금지

> 동일한 변수 중복 선언하면 값의 재할당이 이뤄졌던 var와 닫리 let은 중복 선언이 금지되고 에러를 출력한다.

```js
var foo = 123;
var foo = 436;

console.log(foo); // 436

let bar = 123;
let bar = 456; // SyntaxError : Indentifier 'bar' has already been declared.
```

 <br>
 <br>

② 블록 레벨 스코프

> let 키워드로 선언한 변수는 모든 코드 블록을 지역 스코프로 인정하는 블록 레벨 스코프를 따른다.

```js
// 전역 스코프
let i = 10;

// 함수 레벨 스코프
function foo() {
  let i = 100;

  // 블록 레벨 스코프
  for (let i = 1; i < 3; i++) {
    console.log(i); // 1 2
  }

  console.log(i); // 100
}
foo();
console.log(i); // 10
```

 <br>
 <br>

③ 변수 호이스팅

> var와 달리 let으로 선언한 변수는 "선언 단계"와 "초기화 단계"가 분리되어 진행되기 때문에 스코프에 변수를 등록(선언단계)하지만 초기화 단계는 변수 선언문에 도달했을 때 이루어진다. 초기화 이전에 변수에 접근하려고 하면 참조 에러(ReferenceError)가 발생한다.

<br>

var로 선언된 변수는 "선언 단계"와 "초기화 단계"가 한번에 이루어진다. 즉, 스코프에 변수를 등록(선언 단계)하고 메모리에 변수를 위한 공간을 확보한 후, undefined로 초기화(초기화 단계)한다. 따라서 변수 선언문 이전에 변수에 접근하여도 스코프에 변수가 존재하기 때문에 에러가 발생하지 않는다.

![var](https://user-images.githubusercontent.com/82991292/133871505-ca47b97e-56cd-415f-97be-ceb84445232b.JPG)

```js
console.log(foo); // undefined
console.log(bar); // ReferenceError : bar is not defined

var foo;
let bar;
```

<br>

let으로 선언한 변수를 위한 메모리 공간이 아직 확보되지 않은 구간 즉, 스코프의 시작 지점부터 초기화 시작 지점까지의 구간을 "일시적 사각지대"라고 한다.

![let](https://user-images.githubusercontent.com/82991292/133871519-393d7cb7-ef13-4736-a150-ef26b9e9b78d.JPG)

<br>

하지만 기억해야 할 것은 let으로 선언한 변수도 여전히 호이스팅이 발생한다는 것이다. 지역 변수 foo도 해당 스코프에서 호이스팅되고 코드 블록의 선두부터 초기화가 이루어지는 지점까지 "일시적 사각지대"에 빠진다. 따라서 전역 변수 foo의 값이 출력되지 않고 참조 에러가 발생한다.

```js
let foo = 1;
{
  console.log(foo); // ReferenceError : Cannot access 'foo' before initialization
  let foo = 2;
}
```

<br>
<br>

④ 전역 객체와 let

> var와 달리 let 키워드로 선언한 전역 변수는 전역 객체의 프로퍼티가 아니다.

```js
let foo = 123; // 전역변수

console.log(window.foo); // undefined
```

<br>
<br>

## const 키워드의 특징

- const 키워드로 선언한 변수는 반드시 선언과 동시에 초기화해야한다.

```js
const foo = 1;
```

<br>

- const 키워드로 선언한 변수에 원시 값을 할당하면 변수 값을 변경할 수 없다. 즉 상수를 할당한 경우 재할당이 금지된다.  
   ＋ 일반적으로 상수의 이름은 대문자로 선언하며 여러 단어인 경우 언더스코어로 구분해 스네이크 케이스로 표현한다.

```js
const foo = 1;
foo = 2; // TypeError : Assignment to constant variable.

const TAX_RATE = 0.1;
```

<br>

- const 키워드로 선언된 변수에 "객체"를 할당한 경우 값을 변경할 수 있다. 새로운 값을 재할당하는 것은 금지하지만 변수에 할당된 참조값은 변하지 않기 때문에 프로퍼티 동적 생성, 삭제, 값 변경을 통해 객체를 변경하는 것은 가능하다.

```js
const person = {
  name: "Lee",
};

person.name = "Kim";

console.log(person.name); // "kim"
```

<br>
<br>

## 정리

- ES6에서는 var 키워드는 사용하지 않는다.
- 재할당이 필요한 경우에 한정해 let 키워드를 사용한다. 이때 변수의 스코프는 최대한 좁게 만든다.
- 읽기 전용 원시 값과 객체에는 const 키워드를 사용한다.
