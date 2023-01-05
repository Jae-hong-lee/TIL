# This

**This는 함수가 호출될 때 결정된다!?** 로 알고만 있었던 내 자신을 돌어보자.

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

---

[MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/this)
[This - 블로그](https://wormwlrm.github.io/2019/03/04/You-should-know-JavaScript-this.html.html)
[This - 깃허브 프론트엔드 핸드북](https://github.com/junh0328/prepare_frontend_interview/blob/main/js.md#this)
[유튜브 - This 별코딩](https://www.youtube.com/watch?v=j6VkGimAs-E&t=1s&ab_channel=%EB%B3%84%EC%BD%94%EB%94%A9)
[This - 코알누(예제 굳)](https://youtu.be/tDZROpAdJ9w)
