# This

자바스크립트 내에서 this는 '누가 나를 불렀느냐'를 뜻한다,
**This는 함수가 호출될 때 결정된다!?**
로만 있었던 내 자신을 돌어보자.

## MDN

대부분의 경우 this의 값은 함수를 호출한 방법이 결정합니다. 실행하는 중 할당으로 설정할 수 없고 함수를 호출할 때 마다 다를 수 있습니다.
**즉 this의 값이 내가 어디에서 어떻게 호출하느냐에 따라 변한다는 것**

## 전역 범위(Global context)

JavaScript에서 가장 평범하게 일반적으로 this를 호출한다면, this는 **window라는 전역 객체**를 가리킨다.

```js
// 웹 브라우저에서는 window 객체가 전역 객체
console.log(this === window); // true

a = 37;
console.log(window.a); // 37

this.b = "MDN";
console.log(window.b); // "MDN"
console.log(b); // "MDN"
```

> globalThis 프로퍼티를 사용하여 코드가 실행중인 현재 컨텍스트와 관계없이 항상 전역 객체를 얻을 수 있습니다.

## 함수범위

함수 내부에서 this의 값은 함수를 호출한 방법에 의해 좌우됩니다.

### 단순호출

```js
function howAreYouToday() {
  console.log(this); // global
}
// 1. Arrow Function 안에 IIFE+
const howAreYouToday = () => {
  (function () {
    (function () {
      console.log(this); // global
    })();
  })();
};

// 2. Object Method를 통한 호출
const howAreYouToday = {
  getThisToInnerFunc: function () {
    function inner() {
      console.log(this); // global
    }
    inner();
  },
};

console.log(howAreYouToday.getThisToInnerFunc());
```

정말로 다양하게 호출해 보아도 위치에 상관없이 global을 참조하는 것을 볼 수 있다.

### 객체의 메소드(Method)

함수를 어떤 객체의 메소드로 호출하면 this의 값은 그 객체를 사용한다.

```js
// 1. 객체 또는 클래스 안에서 메소드를 실행한다면 this는 Object 자기 자신
var man = {
  name: "john",
  hello: function () {
    // 2. 객체이므로 this는 man을 가리킴
    console.log("hello " + this.name);
  },
};
man.hello(); // 3. hello john
```

this를 호출하면, this는 자신의 객체를 참조하는데, 자신이 속해있는 객체에 대해서만 참조한다.

```js
const howAreYouToday = {
  woowak: "good",
  getProperties: function () {
    console.log(this); // { woowak: 'good', getProperties: [Function] }
  },
  haku: {
    mitsune: "haku",
    getProperties: function () {
      console.log(this); // { mitsune: 'haku', getProperties: [Function] }
    },
  },
};
// 함수를 객체 외부에서 선언하고, 객체 안에서 호출하는 경우에도
// this는 해당 객체의 this를 참조함을 알 수 있다.
```

함수를 객체 외부에서 선언하고, 객체 안에서 호출하는 경우에도 this는 해당 객체의 this를 참조

```js
// 3. 일반 함수 welcome을 선언
function welcome() {
  // 4. 여기서 this는 전역 객체 Window이므로, 만약 실행시키면 undefined가 뜸
  console.log("welcome " + this.name);
}

// 5. man 객체의 welcome 속성에 일반 함수 welcome을 추가
man.welcome = welcome;

// 6. welcome이 man 객체에서 호출되었으므로 welcome john이 출력됨
man.welcome();
```

이와는 반대로, 객체의 함수를 외부에서 호출할 때 this는 Window가 된다.

```js
// 7. man 객체의 thanks 속성에 함수를 선언
man.thanks = function () {
  // 8. man.thanks()를 호출하면 thanks john이 출력
  console.log("thanks " + this.name);
};

// 8. 이 함수를 객체 외부에서 참조
var thankYou = man.thanks;

// 9. 객체 외부이므로 this가 Window 객체가 되어서 thanks (undefined)가 출력
thankYou();

// 10. 그럼 또 다른 객체에서 이 함수를 호출하면 어떻게 될까?
women = {
  name: "barbie",
  thanks: man.thanks, // 11. man.thanks 함수를 women.thanks에 참조
};

// 12. this가 포함된 함수가 호출된 객체가 women이므로 thanks barbie가 출력
women.thanks();
```

**메소드에서 내부 함수를 선언하는 경우는 this가 어떻게 될까?**

```js
var man = {
  name: "john",
  // 1. 이것은 객체의 메소드
  hello: function () {
    // 2. 객체의 메소드 안에서 함수를 선언하는 것이니까 내부 함수
    function getName() {
      // 3. 여기서 this가 무엇을 가리키고 있을까?
      return this.name;
    }
    console.log("hello " + getName()); // 4. 내부 함수를 출력시키고
  },
};
man.hello(); // 메소드를 실행시키면 undefined가 뜬다! this는 Window였던 것
```

객체의 메소드에서 this가 객체를 가리키고 있던 것과는 다르게, 내부 함수에서 this는 Window 객체를 가리키고 있습니다. 내부 함수는 엄밀히 말해 메소드가 아니기 때문에, 단순 함수 호출 규칙에 따라 Window를 가리키고 있다는 점에 유의하자!!

### call(), apply(), bind()

this를 자바스크립트 코드로 주입 또는 설정할 수 있는 방법이다.
JavaScript에서는 각각 다른 문맥의 this를 필요에 따라 변경할 수 있도록 함수를 제공

> call(), apply(), bind() 등이 있음.

```js
function func() {
  console.log(this.name);
}

var obj = { name: "obj name" };
func.call(obj); // obj name
func.apply(obj); // obj name
func.bind(obj)(); // obj name
```

#### bind(), call()

`func2`를 호출할 때, `func1`에서의 this 를 주입하기 위해서 위 세가지 메소드(bind,call,apply)를 사용할 수 있다.
그리고 세 메소드의 차이점을 파악하기 위해 `func2`에 파라미터를 받을 수 있도록 수정해보자.

```js
var value = 100;
// 객체 메소드
var myObj = {
  value: 1,
  // 객체의 메소드 안에서 함수 선언 = 내부함수
  func1: function () {
    // 여기서 this는 무엇일까?
    console.log(`func1's this.value: ${this.value}`);

    // bind()를 통해 현재 문맥에서의 this를 바인딩.
    var func2 = function (val1, val2) {
      // 여기서 this는 무엇일까? (2)
      console.log(`func2's this.value ${this.value} and ${val1} and ${val2}`);
    }.bind(this, `param1`, `param2`);

    // 여기서 this는 무엇일까? (3)
    var func3 = function (val1, val2) {
      console.log(`func3's this.value ${this.value} and ${val1} and ${val2}`);
    };
    func3.call(this, `param1`, `param2`);
    func3();
  },
};

myObj.func1();
// console> func1's this.value: 1
// console> func2's this.value: 1 and param1 and param2
// console> func3's this.value: 1 and param1 and param2
```

우선 `bind`는 함수를 선언할 때, `this` 와 파라미터를 지정해줄 수 있으며,
`call`과 `apply`는 함수를 호출할 때, `this`와 파라미터를 지정해준다.

#### apply()

```js
var value = 100;
var myObj = {
  value: 1,
  func1: function () {
    console.log(`func1's this.value: ${this.value}`);

    var func2 = function (val1, val2) {
      console.log(`func2's this.value ${this.value} and ${val1} and ${val2}`);
    };
    func2.apply(this, [`param1`, `param2`]);
  },
};

myObj.func1();
// console> func1's this.value: 1
// console> func2's this.value: 1 and param1 and param2
```

`call` `apply` 메소드에는 첫번째 인자로 `this`를 넘겨주고 두번째 인자로 넘겨줘야 하는 파라미터를 배열의 형태로 전달한다. `bind`메소드와 `call`메소드는 각각의 파라미터를 하나씩 넘겨주는 형태임.

### 생성자 (new)

그냥 함수를 호출하는 것이 아니라 new키워드를 통해 생성자 함수를 호출할 때는 또 this가 다르게 바인딩 된다.
new 키워드를 통해서 호출된 함수 내부에서의 this는 객체 자신이 된다.
생성자 함수를 호출할 때의 this 바인딩은 생성자 함수가 동작하는 방식을 통해 이해할 수 있다.

new 연산자를 통해 함수를 생성자로 호출하게 되면, 일단 빈 객체가 생성되고 this 가 바인딩 된다.
이 객체는 함수를 통해 생성된 객체이며, 자신의 부모인 프로토타입 객체와 연결되어 있다.
그리고 return 문이 명시되어 있지 않은 경우에는 this로 바인딩 된 새로 생성한 객체가 리턴된다.

```js
var Person = function (name) {
  console.log(this);
  this.name = name;
};

var foo = new Person("foo"); // Person
console.log(foo.name); // foo
```

### 화살표 함수(Arrow function)

> 화살표 함수는 ECMAScript 6에서 새로 추가된, 함수를 축약해서 사용할 수 있는 문법입니다.

> 정적 컨텍스트(Lexical context)는 소스코드가 작성된 그 문맥의 실행 컨텍스트나 호출 컨텍스트에 의해 결정된다.

단순히 함수를 축약해서 사용하는 것 뿐만이 아니라 this를 외부 스코프에서 정적으로 바인딩된 문맥(정적 컨텍스트, Lexical context)을 가진다는 특징을 갖고 있다.

JavaScript 코드를 실행하기 위한 변수, 함수 등의 정보를 담고 있는 환경이라고만 이해하고,
정적 컨텍스트는 함수가 실행된 위치가 아닌, 정의(defined)된 위치에서의 컨텍스트를 참조한다고 이해하고 넘어가자.
이 코드가 어디서 실행되고 그런 것 따질 필요 없이, 그냥 정의된 부분에서 가까운 외부 함수의 this만 보면 된다.

```js
// 1. 화살표 함수
var obj = {
  a: this, // 2. 일반적인 경우 this는 window,
  b: function () {
    console.log(this); // 3. 메소드의 경우 this는 객체
  },
  c: () => {
    console.log(this);
    // 4. 화살표 함수의 경우 정적 컨텍스트를 가짐, 함수를 호출하는 것과 this는 연관이 없음
    // 5. 따라서 화살표 함수가 정의된 obj 객체의 this를 바인딩하므로 this는 window
  },
};

obj.b(); // 6. obj
obj.c(); // 7. window
```

일반적인 방법으로 함수를 선언(`function () { ... }`)하면, 일반적인 함수는 함수가 실행될 때 자체적으로 this를 할당하게 되는데,
**화살표 함수는 메소드 함수이므로 this가 메소드를 포함하는 객체로 바인딩된다.**

하지만 화살표 함수는 this가 없기 때문에, 부모 스코프의 this를 바인딩하는데 위의 예시에서 이는 곧 Window객체를 의미한다.
따라서 메소드로 화살표 함수를 쓰면, **this를 이용한 부모 객체에 접근할 수 없다.**

---

#### 인용

> **함수 내부에서 this를 사용하는 경우에 따라 값이 바뀔 수 있다**정도로만 이해하셔도 좋다고 생각한다. 크게 와닿지 않을 수도 있습니다. 저 역시도 JavaScript에서 this를 쓸 일이 그렇게 많지는 않았었기 때문입니다.

> 물론 실행 컨텍스트, 스코프 체인 등 보다 더욱 깊은 개념을 접하고 멘붕에 빠지긴 했지만요…

---

[MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/this)
[This - 블로그](https://wormwlrm.github.io/2019/03/04/You-should-know-JavaScript-this.html.html)
[This - 깃허브 프론트엔드 핸드북](https://github.com/junh0328/prepare_frontend_interview/blob/main/js.md#this)
[유튜브 - This 별코딩](https://www.youtube.com/watch?v=j6VkGimAs-E&t=1s&ab_channel=%EB%B3%84%EC%BD%94%EB%94%A9)
[This - 코알누(예제 굳)](https://youtu.be/tDZROpAdJ9w)
