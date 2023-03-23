# Promise

> Promise는 현재에는 당장 얻을 수는 없지만 가까운 미래에는 얻을 수 있는 어떤 데이터에 접근하기 위한 방법을 제공하는 객체.

![callback hell](https://velog.velcdn.com/images%2Fopen_h%2Fpost%2F73779b1e-7025-4548-8e69-b1e4362fb8fd%2Fcallbackhell.jpeg)

`Javascript` 에서는 대부분의 작업들이 비동기로 이루어진다. 콜백 함수로 처리하면 되는 문제였지만 요즘에는 프론트엔드의 규모가 커지면서 코드의 복잡도가 높이지는 현상이 발생한다.
콜백 함수가 콜백 함수를 부르고, 그 콜백 함수가 또 다른 콜백함수를 부르는 이른바 **콜백 지옥**이 발생하는 것
이를 해결할 방안으로 등장한 것이 `Promise` 패턴이다.

`Promise` 패턴을 사용하면 비동기 작업들을 순차적으로 진행하거나, 병렬로 진행하는 등의 컨트롤이 보다 수월해진다.
또한 예외처리에 대한 구조가 존재하기 때문에 오류 처리 등에 대해 보다 가시적으로 관리할 수 있다. 이 `Promise` 패턴은 `ECMAScript6` 스펙에 정식으로 포함되었다.

## Promise 이해하기

Promise 객체는 비동기 처리의 결과 값을 나타냅니다.
그 결과가 성공적으로 받아졌을 경우와 그렇지 않을 경우로 나눠서 **에러처리 또한 가능한 것**

![MDN: Promise](https://velog.velcdn.com/images%2Fjuno7803%2Fpost%2Fed63c6ea-a40b-4deb-bc81-080256f82f25%2Fimage.png)

Promise는 _크게_ 다음의 세 가지 상태를 가질 수 있다.
대기(**pending**): 이행하거나 거부되지 않은 **초기 상태**
이행(**fulfilled**): 연산이 **성공적으로** 완료됨
거부(**rejected**): 연산이 **실패함**

> settled: 연산이 수행된 상태 (성공 또는 실패)

### Pending: 대기

먼저 아래와 같이 `new Promise()` 메서드를 호출하면 대기(**Pending**) 상태가 된다.

```js
new Promise();
```

`new Promise()` 메서드를 호출할 때 콜백 함수를 선언할 수 있고, 콜백 함수의 인자는 `resolve`, `reject` 이다

```js
new Promise(function (resolve, reject) {
  // ...
});
```

### Fulfilled: 이행 (완료)

여기서 콜백 함수의 인자 `resolve`를 아래와 같이 실행하면 이행(**Fulfilled**) 상태가 된다.

```js
new Promise(function (resolve, reject) {
  resolve();
});
```

그리고 이행 상태가 되면 아래와 같이 `then()`을 이용하여 처리 결과 값을 받을 수 있다.

```js
function getData() {
  return new Promise(function (resolve, reject) {
    var data = 100;
    resolve(data);
  });
}

// resolve()의 결과 값 data를 resolvedData로 받음
getData().then(function (resolvedData) {
  console.log(resolvedData); // 100
});
```

### Rejected: 실패

`new Promise()`로 프로미스 객체를 생성하면 콜백 함수 인자로 `resolve`와 `reject`를 사용할 수 있다고 했는데 여기서 `reject`를 아래와 같이 호출하면 실패(**Rejected**) 상태가 됩니다.

```js
new Promise(function (resolve, reject) {
  reject();
});
```

그리고, 실패 상태가 되면 실패한 이유(실패 처리의 결과 값)를 `catch()`로 받을 수 있다.

```js
function getData() {
  return new Promise(function (resolve, reject) {
    reject(new Error("Request is failed"));
  });
}

// reject()의 결과 값 Error를 err에 받음
getData()
  .then()
  .catch(function (err) {
    console.log(err); // Error: Request is failed
  });
```

#### Rejected 의 두가지 상태

**1. `then()`의 두번째 인자를 통한 에러처리**

```js
getData().then(
  () => {
    // Success
  },
  (err) => {
    console.log(err);
  }
); // Error: Request is failed
```

**2. `catch()`를 통한 에러처리**

```js
getData()
  .then()
  .catch((err) => {
    console.log(err); // Error: Request is failed
  });
```

방식은 다르지만, 두 방법 모두 Promise의 reject가 호출되었을 때 실행된다.
하지만, 가급적 `catch()`를 사용하시는게 좋다. `then()`의 두번째 인자에서 에러를 핸들링 할 땐 첫번째 인자로 들어간 콜백함수 내부에서 오류가 나는 경우엔 **reject** 부분에서의 **오류를 제대로 잡아내지 못하는 문제점**이 있다.

## Promise 예제

**`Promise` 이해를 위한 쉬한 예제**

```js
function getData() {
  return new Promise(function (resolve, reject) {
    $.get("url 주소/products/1", function (response) {
      if (response) {
        resolve(response);
      }
      reject(new Error("Request is failed"));
    });
  });
}

// 위 $.get() 호출 결과에 따라 'response' 또는 'Error' 출력
getData()
  .then(function (data) {
    console.log(data); // response 값 출력
  })
  .catch(function (err) {
    console.error(err); // Error 출력
  });
```

위 코드는 서버에서 제대로 응답을 받아오면 `resolve()` 메서드를 호출하고, 응답이 없으면 `reject()` 메서드를 호출하는 예제.
호출된 메서드에 따라 then()이나 catch()로 분기하여 응답 결과 또는 오류를 출력!

**콜백지옥 탈출**

```js
setTimeout(
  (garment) => {
    var ootd = garment;
    console.log(ootd);
    setTimeout(
      (garment) => {
        ootd += "," + garment;
        console.log(ootd);
        setTimeout(
          (garment) => {
            ootd += "," + garment;
            console.log(ootd);
            setTimeout(
              (garment) => {
                ootd += "," + garment;
                console.log(ootd);
              },
              500,
              "스니커즈"
            );
          },
          500,
          "블레이저"
        );
      },
      500,
      "데님팬츠"
    );
  },
  500,
  "반팔티"
);
```

5초마다 옷을 입는 callback 지옥 코드이다. Promise 를 활용해서 바꾸어 보면

```js
const wearOotd = (garment) => {
  return (prevGarment) => {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        var ootd = prevGarment ? prevGarment + "," + garment : garment;
        console.log(ootd);
        resolve(ootd);
      }, 500);
    });
  };
};

wearOotd("반팔티")()
  .then(wearOotd("블레이저"))
  .then(wearOotd("데님팬츠"))
  .then(wearOotd("스니커즈"));
```

Promise를 이용함으로써 좀 더 직관적으로 바꿀 수 있었고, then() 과 catch() 덕분에 에러 핸들링도 수월해짐

> 이렇게 then()을 통해서 Promise를 계속 연결하여 호출하는 것을 **프로미스 체이닝(Promise chaining)** 이라고 한다.

> 다음과 같이 then()이 여러번 연결된 경우, Promise의 비동기 처리에선 then()의 스코프 내에서 코드를 작성해야 하기 때문에 여전히 가독성에서 문제가 생기게 된다.
> 이 문제를 해결하기 위해 ES6 문법에 async/await이 등장.

---

[캡틴 판교 - Promise](https://joshua1988.github.io/web-development/javascript/promise-for-beginners/#%ED%94%84%EB%A1%9C%EB%AF%B8%EC%8A%A4%EC%9D%98-3%EA%B0%80%EC%A7%80-%EC%83%81%ED%83%9Cstates)

[JS비동기-처리와-에러-핸들링](https://velog.io/@juno7803/JS%EB%B9%84%EB%8F%99%EA%B8%B0-%EC%B2%98%EB%A6%AC%EC%99%80-%EC%97%90%EB%9F%AC-%ED%95%B8%EB%93%A4%EB%A7%81)
