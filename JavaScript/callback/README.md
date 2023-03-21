# Callback 함수

: 다른 코드(함수 또는 메서드)에게 인자로 넘겨줌으로써 그 `제어권`(실행 시점)도 함께 위임한 함수
![](https://upload.wikimedia.org/wikipedia/commons/thumb/d/d4/Callback-notitle.svg/740px-Callback-notitle.svg.png)

콜백 함수란? 어떤 이벤트가 발생한 후에 수행될 함수를 의미한다
다른 함수의 매개변수로 함수를 전달하고, 어떠한 이벤트가 발생한 후 매개변수로 전달한 함수가 다시 호출되는 것!

> 콜백을 쓰는이유: 함수의 과정이 끝나기 전에 다음 프로세스가 진행되는 것을 막기위해서

> **자세히**
> js에서 함수는 object라고 한다.
> 함수는 다른 함수의 인자로 쓰일 수도 어떤 함수에 의해 리턴될 수도 있다. 이런 함수를 고차 함수라고 한다.
> 결국, 인자로 넘겨지는 함수를 콜백 함수라고 한다. 또한, 단지 함수를 등록하기만 하고 어떤 이벤트가 발생했거나 특정 시점에 도달했을 때 시스템에서 호출하는 함수이다.

## 콜백함수 유형

#### 기본유형

```js
function introduce(lastName, firstName, callback) {
  var fullName = lastName + firstName;

  callback(fullName);
}

introduce("홍", "길동", function (name) {
  console.log(name);
});
// 결과 -> 홍길동
```

`introduce` 함수를 실행할 때, 첫번째와 두번째 인풋으로는 "홍"과 "길동"을 넣어주고, 마지막 세번째 인풋으로는 *새로운 함수*를 지정해주었다.

여기서 지정되는 함수가 `introduce` 함수 안에서 `callback(fullName)`으로 실행되는 함수가 된다.

콜백이 유용한 이유는, 콜백 함수만을 바꿔줌으로서 **하나의 함수를 여러가지로 응용**할 수 있기 때문이다.

```js
function introduce(lastName, firstName, callback) {
  var fullName = lastName + firstName;

  callback(fullName);
}

function say_hello(name) {
  console.log("안녕하세요 제 이름은 " + name + "입니다");
}

function say_bye(name) {
  console.log("지금까지 " + name + "이었습니다. 안녕히계세요");
}

introduce("홍", "길동", say_hello);
// 결과 -> 안녕하세요 제 이름은 홍길동입니다

introduce("홍", "길동", say_bye);
// 결과 -> 지금까지 홍길동이었습니다. 안녕히계세요
```

위와 같이 다른 동작을 수행하는 함수 say_hello 와 say_bye 를 정의해두고 introduce 함수에 인풋으로 사용해주면, introduce라는 함수에서 받아들이는 같은 인풋들을 가지고 다른 동작을 수행하는 것이 가능해진다.

즉, 이런식으로 함수를 나눠줌으로써 코드를 재활용 할 수 있고, 관리도 더 쉬워지게 되는 것

> **주의**
> 콜백 자체는 코드를 더 읽기 편하게 해주지만, 너무 많이 콜백을 연결하면 오히려 나중에 디버깅 하기가 힘들어지기 때문에 적당하게 나누어서 사용하는 것이 좋다.

#### setTimeout()

자바스크립트에 존재하는 비동기 함수의 동작은 조금 다르다. 예제를 보며 이해해보자.

```js
// #1
console.log("양말을 신는다");
// #2
setTimeout(() => {
  console.log("신발을 신는다");
}, 1000);
// #3
console.log("밖으로 나간다");
```

자바스크립트의 대표적인 내장 비동기 함수인 `setTimeout()` 이다.
첫번째 인자로 실행할 콜백함수를 담고 두번째 인자로 들어온 시간만큼 기다린 후에 콜백함수를 실행

코드의 실행을 예측한다면,

```text
양말을 신는다.
// 1초 후
신발을 신는다.
밖으로 나간다.
```

와 같이 동작하리라고 예측할 수 있다. 하지만, (_타이머가 0초여도 같은 결과값_)

```text
양말을 신는다.
밖으로 나간다.
신발을 신는다.
```

와 같이 콘솔창에 나타나게 될거다.

특정 로직의 실행이 끝날 때까지 기다려주지 않고 나머지 코드를 먼저 실행하는 것이 비동기 처리라고 하였다.
만약 동기적으로만 코드가 실행된다면, 앞에서 실행된 코드가 모두 실행되는 것을 기다리고 다음 코드가 실행되기 때문에 유저에게 좋지 못한 사용자 경험을 제공하게 된다.

## 콜백 함수를 통한 비동기 처리

실제 프로젝트에서 DB나 API를 통해서 유저 데이터를 얻어오는 경우, 필연적으로 지연시간이 발생하게 된다.

이러한 상황을 시뮬레이션 하기 위해서 `setTimeout()`을 이용하여 `findUser()`함수를 작성
const가 아닌 let을 이용해서 user 로컬 변수를 선언하고 `setTimeout()` 함수를 통해 0.1초 후에 user 변수에 객체를 할당했다.

```js
function findUser(id) {
  let user;
  // 1. 기다려~ 할당할게 0.1초 뒤에~
  setTimeout(function () {
    // 4. 0.1초 뒤
    console.log("waited 0.1 sec.");
    user = {
      id: id,
      name: "User" + id,
      email: id + "@test.com",
    };
  }, 100);
  // 2. ??: 저는 왜 나가요? EventLoop
  return user;
}

// 3. user = undefined
const user = findUser(1);

console.log("user:", user);

// Return
// user: undefined
// waited 0.1 sec.
```

코드를 실행해보면 예상치못한 순서로 코드가 실행됨을 알 수 있다.
3번째 줄의 `setTimeout()`은 비동기 함수의 호출이기 때문에 실행이 완료될 때 까지 기다리지 않고
다음 라인인 11번째 줄로 넘어가버림.
따라서 14번째 줄의 `findUser(1)`는 `undefined`를 리턴하게 되고 user 변수에는 그대로 `undefined`가 할당된다.
0.1초 후에 `setTimeout()` 함수의 첫번째 인자로 넘어간 콜백 함수가 실행되면서
`waited 0.1 sec.`가 출력되고 user 로컬 변수에 원하는 객체가 할당되었지만 이미 때 늦은 상황.

---

이와 같이 코드 실행 순서가 뒤죽박죽이 될 수 있는 난처한 상황에서는 이전 섹션에서 했던 것 처럼 콜백 함수를 이용해서 해결할 수 있다.
함수로 부터 결과값을 리턴 받기를 포기하고, 결과값을 이용해서 처리할 로직을 콜백 함수에 담아 인자로 던지면 된다. (예제를 통해 확인)

```js
function findUserAndCallBack(id, cb) {
  setTimeout(function () {
    console.log("waited 0.1 sec.");
    const user = {
      id: id,
      name: "User" + id,
      email: id + "@test.com",
    };
    cb(user);
  }, 100);
}

findUserAndCallBack(1, function (user) {
  console.log("user:", user);
});

// Return
// waited 0.1 sec.
// user: {id: 1, name: "User1", email: "1@test.com"}
```

이번에는 `findUserAndCallBack()` 함수의 2번째 인자로 결과값을 이용해서 실행될 로직을 넘겼고, `setTimeout()` 함수는 0.1초 후에 이 콜백 함수를 호출.
이와 같이 비동기 함수를 호출할 때는 결과값을 리턴 받으려고 하지말고, 결과값을 통해 처리할 로직을 콜백 함수로 넘기는 스타일로 코딩을 해줘야 예상된 결과를 얻을 수 있다.

> **하지만** 자바스크립트 프로젝트가 점점 더 복잡해지면서 최근에는 콜백 함수를 인자로 넘겨서 비동기 처리를 하는 스타일을 피하는 추세이다.
> **왜냐하면** 콜백 함수를 중첩해서 사용하게 되면 계속해서 코드를 들여쓰기 해야하고 그러다보면 코드 가독성이 현저하게 떨어지게 되기 때문
> 결국, 많은 개발자들이 **콜백 지옥**이라고 불리는 끔찍한 상황을 겪게 되었고 최근에는 **Promise나 async/await를 이용하는 방법들로 대체**되고 있다.

### 콜백지옥

콜백 지옥은 비동기 처리 로직을 위해 콜백 함수를 연속해서 사용할 때 발생하는 문제입니다.
콜백함수를 이용해서 비동기 처리를 해주다가 코드가 깊어지는 것을 말합니다.
일반적으로 콜백 지옥을 해결하는 방법에는 Promise 나 Async를 사용하는 방법이 있습니다.

![콜백지옥](https://i0.wp.com/hanamon.kr/wp-content/uploads/2021/08/%E1%84%8F%E1%85%A9%E1%86%AF%E1%84%87%E1%85%A2%E1%86%A8%E1%84%8C%E1%85%B5%E1%84%8B%E1%85%A9%E1%86%A8.png?w=640&ssl=1)

다음과 같이 콜백 함수의 콜백 함수의 콜백 함수를 넣어서 비동기 처리의 결과를 또 다른 비동기 처리의 매개변수로 전달 할 수 있다.

콜백 함수는 주로 이벤트 처리나 서버 통신과 같은 비동기 작업을 제어 하기 위해 사용되지만 콜백 함수를 반복 해서 사용하게 되면, 들여 쓰기 수준이 감당할 수 없을 정도록 깊어지게 된다.
**-> 이것이 콜백지옥**
