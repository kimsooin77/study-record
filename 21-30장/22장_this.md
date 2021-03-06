# 22장\_this

## this가 필요한 주요한 이유

<br>

생성자 함수 내부에서 프로퍼티 또는 메서드를 추가하고자 함.  
 ＞ 자신이 생성할 인스턴스를 참조할 수 있어야함.  
 ＞ 인스턴스를 참조하기 위해서 인스턴스를 먼저 생성해야 함.  
 ＞ 인스턴스를 생성하려면 먼저 생성자 함수를 정의해야 함.  
 ＞ 생성자 함수를 정의하는 시점에서는 아직 인스턴스를 생성하기 전이므로 생성자 함수가 생성할 인스턴스를 가리키는 식별자를 알 수 없음.  
 ＞ 따라서 자바스크립트는 `this`라는 식별자를 사용하여 자신이 속한 객체 또는 자신이 생성할 인스턴스를 가리킨다.

 <br>
 <br>

## this 키워드

- - 함수를 호출하면 arguments 객체와 this가 암묵적으로 함수 내부에 전달된다.
  - 함수 내부에서 this도 지역 변수처럼 사용할 수 있다.
  - 코드 어디서든 참조가 가능하다.
  - `this가 가리키는 값은 함수 호출 방식에 의해 동적으로 결정된다.`

<br>

<table>
    <thead align="center">
        <th>함수 호출 방식</th>
        <th>this가 가리키는 값</th>
    </thead>
    <tbody>
        <tr>
            <td>일반 함수로서 호출</td>
            <td>전역 객체(window 또는 Node.js)</td>
        </tr>
        <tr>
            <td>메서드로서 호출</td>
            <td>메서드를 호출한 객체(마침표 앞의 객체)</td>
        </tr>
        <tr>
            <td>생성자 함수로서 호출</td>
            <td>생성자 함수가 생성할 인스턴스</td>
        </tr>
        <tr>
            <td>Function.prototype.apply/call/bind 메서드에 의한 간접 호출</td>
            <td>Function.prototype.apply/call/bind 메서드에 첫 번째 인수로 전달한 객체</td>
        </tr>
    </tbody>
</table>

<br>

```js
// 전역에서 호출
console.log(this); // window

// 일반 함수로서 호출
function square(number) {
  console.log(this); // window
  return number * number;
}

// 메서드로서 호출
const person = {
  name: "Lee",
  getName() {
    console.log(this); // {name : "Lee", getName : f}
    return this.name;
  },
};
console.log(person.getName()); // Lee

// 생성자 함수로서 호출
function Person(name) {
  this.name = name;
  console.log(this); // Person {name:"Lee"}
}

const me = new Person("Lee");

// Function.prototype.apply/call/bind 메서드에 의한 간접 호출
// 아래의 예시 참조
```

<br>
<br>

## 일반 함수로서 호출

> 일반 함수로 호출된 모든 함수(중첩 함수, 콜백 함수 포함) 내부의 this에는 전역 객체가 바인딩된다.

<br>

❓ 중첩 함수와 콜백 함수의 this에 전역 객체가 바인딩되면 외부 함수인 메서드와 this가 일치하지 않아 외부 함수를 돕는 역할을 하지 못하게 되는 문제점이 있다.

```js
var value = 1;

const obj = {
  value: 100,
  foo() {
    console.log(this); // {value : 100, foo :f}
    // 콜백 함수 내부의 this에는 전역 객체가 바인딩된다.
    setTimeout(function () {
      console.log(this); // window
      console.log(this.value); // 1
    }, 100);
  },
};

obj.foo();
```

❗ 메서드의 this와 내부의 중첩 함수나 콜백 함수의 this를 일치시키기 위해서는 다음과 같은 방법을 사용할 수 있다.

- - this 바인딩을 변수 that에 할당한 후 콜백 함수 내부에서 this 대신 that을 참조한다.
  - this를 명시적으로 바인딩 할 수 있는 Function.prototype.apply/call/bind 메서드를 사용한다.
  - 화살표 함수를 사용한다.(화살표 함수 내부의 this는 상위 스코프의 this를 가리킴.)

<br>
<br>
<br>

## 메서드 호출

> 메서드 내부(일반 메서드, 프로토타입 메서드)의 this는 메서드를 소유한 객체가 아닌 메서드를 호출한 객체가 바인딩된다.

<br>

```js
function Person(name) {
  this.name = name;
}

Person.prototype.getName = function () {
  return this.name;
};

const me = new Person("Lee");

// getName 메서드를 호출한 객체는 me다.
console.log(me.getName()); // Lee

Person.prototype.name = "Kim";

// getName 메서드를 호출한 객체는 Person.prototype이다.
console.log(Person.prototype.getName()); // Kim
```

![1](https://user-images.githubusercontent.com/82991292/135403195-98188f17-8601-4130-ba9c-b7fa99807464.JPG)

<br>
<br>
<br>

## 생성자 함수 호출

> 생성자 함수 내부의 this는 생성자 함수가 생성할 인스턴스가 바인딩 된다. 만약 new 연산자로 호출하지 않으면 생성자 함수가 아닌 일반 함수로서 호출된다.

위의 예시 참조

<br>
<br>
<br>

## Function.prototype.apply / call / bind 메서드에 의한 간접 호출

> Function.prototype의 메서드이므로 모든 함수가 상속받아 사용가능하다.

- apply / call

  - 함수를 호출하면서 첫 번째 인수로 this로 사용할 객체를 전달한다.
  - 두 번쩨 인수로 apply는 함수에게 전달할 인수 리스트의 배열 또는 유사 배열 객체를 call은 쉼표 형식의 리스트를 전달한다.
  - return 값으로 호출된 함수의 반환값을 지정한다.
  - 주로 arguments 객체와 같은 유사 배열 객체에 배열 메서드를 사용하는 경우 쓰인다.

    ```js
    function getThisBinding() {
      console.log(arguments);
      return this;
    }

    // this로 사용할 객체
    const thisArg = { a: 1 };

    // getThisBinding 함수를 호출하면서 인수로 전달한 객체를 getThisBinding 함수의 this에 바인딩한다.
    // apply 메서드는 호출할 함수의 인수를 배열로 묶어 전달한다.
    console.log(getThisBinding.apply(thisArg, [1, 2, 3]));
    // Arguments(3) [1,2,3, callee : f, Symbol(Symbol.iterator): f]
    // {a : 1}

    //call 메서드는 호출할 함수의 인수를 쉼표로 구분한 리스트 형식으로 전달한다.
    console.log(getThisBinding.call(thisArg, 1, 2, 3));
    // Arguments(3) [1,2,3, callee : f, Symbol(Symbol.iterator): f]
    // {a : 1}
    ```

<br>

- bind

  - 함수를 호출하지 않고 this로 사용할 객체만 전달한다.
  - 주로 메서드의 this와 메서드 내부의 중첩 함수 또는 콜백 함수의 this가 불일치하는 문제를 해결할 때 사용된다.

  ```js
  const person = {
    name: "Lee",
    foo(callback) {
      // bind 메서드로 callback 함수 내부의 this 바인딩을 전달
      setTimeout(callback.bind(this), 100);
    },
  };

  person.foo(function () {
    console.log(`Hi! my name is ${this.name}.`);
    // Hi! my name is Lee.
  });
  ```
