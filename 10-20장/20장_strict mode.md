# 20장\_strict mode

## strict mode란?

> 자바스크립트 언어의 문법을 좀 더 엄격히 적용하여 오류를 발생시킬 가능성이 높거나 자바스크립트 엔진의 최적화 작업에 문제를 일으킬 수 있는 코드에 대해 명시적인 에러를 발생시키는 기능이며 ES5부터 추가되었다.

<br>
<br>

- strict mode가 등장하게 된 배경

  자바 스크립트는 기존 기능을 변경하지 않으면서 새로운 기능을 추가해왔기 때문에 호환성 문제가 없었다.
  하지만 ES5부터 새로운 기능이 추가되고 기존 기능의 일부가 변경되었다.

  기존 기능이 변경되어 호환성에 문제가 생기게 되었고, 이를 방지하기 위해 등장한 것이 엄격 모드(strict mode) 이다.

  <br>
  <br>

- strict mode의 적용법

  - 전역의 선두 또는 함수 몸체의 선두에 `'use strict';`을 추가한다.

  ```js
  "use strict";
  let v = "strict mode script!";

  function strict() {
    "use strict";
    let test = "strict mode function";
  }
  ```

  - 전역에 적용한 strict mode는 스크립트 단위로 적용되며 스크립트 단위로 적용하는 것보다는 즉시 실행 함수로 스크립트 전체를 감싼후 선두에 strict mode를 적용하는 것이 바람직하다.

  - ES6에서 추가된 class와 모듈은 별도로 적용하지 않아도 strict mode가 적용된다.(ES5 이전의 환경에선 해당 X)

<br>
<br>

- strict mode가 발생시키는 에러  
  ① 암묵적 전역

> 선언하지 않은 변수를 참조하면 ReferenceError가 발생한다.

```js
(function () {
  "use strict";

  x = 1;
  console.log(x); // ReferenceError : x is not defined
})();
```

<br>
<br>

② 변수, 함수, 매개변수의 삭제

> delete 연산자로 변수, 함수, 매개변수를 삭제하면 SyntaxError가 발생한다.

```js
(function () {
    'use strict';

    var x = 1;
    delete x; // SyntaxError : Delete of an unqualified identifier in strict mode.

    function foo(a) {
        delete a; // SyntaxError : Delete of an unqualified identifier in strict mode.
    }

    delete foo; // SyntaxError : Delete of an unqualified identifier in strict mode.
}());
```

＋ // 구문오류 : strict mode에서 정규화되지 않은 식별자의 삭제  
SyntaxError : Delete of an unqualified identifier in strict mode.

<br>
<br>

③ 매개변수 이름의 중복

> 중복된 매개변수 이름을 사용하면 SyntaxError가 발생한다.

```js
(function () {
  "use strict";

  // SyntaxError : duplicate parameter name not allowed in this context
  function foo(x, x) {
    return x + x;
  }
  console.log(foo(1, 2));
})();
```

<br>
<br>

④ with 문의 사용

> 전달된 객체를 스코프 체인에 추가할 수 있는 with 문을 사용하면 SyntaxError가 발생한다.

```js
(function () {
  "use strict";

  // SyntaxError : Strict mode code may not include a with statement
  with ({ x: 1 }) {
    console.log(x);
  }
})();
```

<br>

＋ 스코프 체인을 확장시켜 반복되는 객체 이름을 생략할 수 있어 유용하지만 성능과 가독성이 나빠지는 문제가 있기 때문에 사용하지 않는 것이 좋다.

```js
let a, b, c;
let d = 5;

with (Math) {
  a = d * random();
  b = d * PI;
  c = sqrt(d);
}

console.log(a); // 2.5252127106198294
console.log(b); // 15.707963267948966
console.log(c); // 2.23606797749979
```

<br>
<br>

- strict mode 적용에 의한 변화

① 일반 함수의 this

> strict mode에서 생성자 함수가 아닌 일반 함수로서 호출하면 this에 undefined가 바인딩된다. 이때 에러는 발생하지 않는다.

```js
(function () {
  "use strict";

  function foo() {
    //strict mode가 아니면 window가 출력됨.
    console.log(this); // undefined
  }
  foo();

  function Foo() {
    console.log(this); // Foo
  }
  new Foo();
})();
```

<br>

② arguments 객체

> strict mode에서는 매개변수에 전달된 인수를 재할당하여 변경해도 arguments 객체에 반영되지 않는다.

```js
(function (a) {
  "use strict";
  // 매개변수에 전달된 인수를 재할당하여 변경
  a = 2;

  // 변경된 인수가 arguments 객체에 반영되지 않는다.
  console.log(arguments); // { 0 : 1, length : 1}

  // strict mode가 아닐 때 출력값 (변경된 인수가 반영됨.)
  // Arguments [2, callee: ƒ, Symbol(Symbol.iterator): ƒ]
})(1);
```
