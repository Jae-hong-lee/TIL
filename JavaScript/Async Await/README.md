# Async/Await

비동기 코드를 작성하는 새로운 방법이다. Javascript 개발자들이 훌륭한 비동기 처리 방안이 Promise로 만족하지 못하고 더 훌륭한 방법을 고안 해낸 것이다(사실 async/await는 promise기반).
절차적 언어에서 작성하는 코드와 같이 사용법도 간단하고 이해하기도 쉽다. function 키워드 앞에 async를 붙여주면 되고 function 내부의 promise를 반환하는 비동기 처리 함수 앞에 await를 붙여주기만 하면 된다.
async/await의 가장 큰 장점은 Promise보다 비동기 코드의 겉모습을 더 깔끔하게 한다는 것이다.

> 이 것은 es8의 공식 스펙이며 node8LTS에서 지원된다(바벨이 async/await를 지원해서 곧바로 쓸수 있다고 한다!).

## 사용이유

async와 await 방법은 기존의 처리 방식의 문제점들을 보완하면서도 사용법이 훨씬 간단하면서도 깔끔하다.

#### Promise

```js
function p() {
  return new Promise((resolve, reject) => {
    resolve("hello");
    // or reject(new Error('error');
  });
}

p().then((n) => console.log(n));
```

#### async

```js
async function p2() {
  // async을 지정해주면 Promise를 리턴하는 함수.
  return "hello";
}

p2().then((n) => console.log(n));
```

이처럼 `async`를 사용하면 `promise` 코드를 훨씬 직관적으로 나타낼 수 있다.
(함수에 `async`만 붙이면 자동으로 `promise` 객체로 인식되고, return값은 resolve()값과 동일)

```js
async function 함수명() {
  await 비동기_처리_메서드_명();
}
```

`function` 앞에 `async`를 붙여주고, `promise` 객체 앞에 `await`를 붙여준다.
`async`가 붙은 함수는 `promise` 객체를 반환! 따라서 `.then((a) => {}`를 이용할 수 있다.

여기서 주의해야 할 점은 비동기 처리 메서드가 **꼭 프로미스 객체를 반환**해야 **await가 의도한 대로 동작**합니다.

## 예외처리

async/await의 가장 큰 특징은, 콜백패턴 이라던지, `.then()`과 `.catch()`를 이용한다던가의 특정한 문법을 이용하는게 아니라 일반적인 동기 코드를 짜는 것과 같은 방식으로 짤 수 있다는 점이다.

따라서, 에러처리도 일반적인 `try/catch` 문을 통해서 할 수 있는 것!

> fetch 함수에서 사용한 API는 가짜 데이터 REST API를 제공하는 API 주소

#### todos 일반, async/await

```js
const getTodo = async (id) => {
  const todoResponse = await fetch(`https://~~~~~~~~/todos/${id}`);
  const todo = await todoResponse.json();
  return todo;
};

getTodo(17).then(console.log);
```

간단한게 데이터를 가져오는 과정을 async await 문법을 사용한 코드
async 키워드는 함수 앞에 붙이는 키워드이며 await 키워드는 async 키워드가 붙어 있는 함수 내부에서만 사용할 수 있다. await 라는 코드로 의도한 순서대로 코드의 흐름을 제어하고 있다. 일반적인 동기 코드 처리와 동일한 흐름으로 코드를 작성할 수 있기에 코드 읽기가 수월

#### try catch 적용

```js
const getTodo = async (postId) => {
  try {
    const todoResponse = await fetch();
    // 1번 주소: `https://~~~error`
    // 2번 주소: `https://~~~~~~~~/.typicode.com/todos/${userId}`
    const todos = await userResponse.json();
    return todos[0].title;
  } catch (err) {
    console.log(err);
    return "Unknown";
  }
};

getTodo(15).then(console.log);
```

2번째 주소가 정상적 작동 코드라고 할때,
userId 를 주면 해당 user의 todo 의 0번째 title을 얻어오는 코드.
2번 주소일 때는 정상적으로 todos를 받아와 0번째 title (제일 앞)을 받아오는 걸 확인할 수있다.

그리고 1번 주소의 경우 에러 상황으로 err를 출력하고 (위 코드에서는 Error: 'Failed to fetch' 라는 에러 발생) "Unknown"을 반환하게 된다.

## 예제

마지막으로 저번에 promise callback 에서 했던 옷입히기 함수를 async/await 로 바꾼다면?

```js
const wearOotd = (garment) => {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve(garment);
    }, 500);
  });
};

const wearTodaysLook = async () => {
  var ootd = "";
  const _wearOotd = async (garment) => {
    ootd += (ootd ? "," : "") + (await wearOotd(garment));
  };
  await _wearOotd("반팔티");
  console.log(ootd);
  await _wearOotd("블레이저");
  console.log(ootd);
  await _wearOotd("데님팬츠");
  console.log(ootd);
  await _wearOotd("신발");
  console.log(ootd);
};

wearTodaysLook();
```

훨씬 깔끔해진 코드를 확인할 수 있다. 콜백지옥이나 프로미스체이닝 처럼 꼬리에 꼬리를 물고 깊어지는 구조가 아니라 일반적인 동기적 코드와 같은 구조기 때문에 가독성 측면에서 매우 유리하다.
