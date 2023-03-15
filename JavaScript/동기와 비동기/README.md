# 동기, 비동기

![](https://velog.velcdn.com/images/dlwoxhd/post/c965c7bc-4443-49a5-85dd-8821fa037e6a/image.png)

동기 Syncronous와 비동기 Asynchronous는 프로그래밍 언어에서 중요한 개념이다.

**동기**는 요청 후 응답을 받아야 다음 동작을 실행하는 방식을 말하며
**비동기**는 요청을 보낸 후 응답과 관계없이 다음 동작을 실행하는 방식이다.

### 동기

![동기](https://velog.velcdn.com/images%2Fwonhee010%2Fpost%2Fdd6c1aab-eeec-453d-b43c-5f3dc9db9b4c%2Fimage.png)

`Thread1`이 작업을 시작 시키고, `Task1`이 끝날때까지 기다렸다 `Task2`를 시작한다.
작업 요청을 했을 때 요청의 결과값(return)을 **직접 받는 것**이다.
**호출한 함수가 작업 완료를 신경 쓴다.**

### 비동기

![비동기](https://velog.velcdn.com/images%2Fwonhee010%2Fpost%2F132a4f9a-4468-4bce-9f49-374b2cb337bc%2Fimage.png)
`Thread1`이 작업을 시작 시키고, 완료를 기다리지 않고, `Thread1`은 다른 일을 처리할 수 있다.
작업 요청을 했을 때 요청의 결과값(return)을 **간접적으로 받는 것**
**호출된 함수(callback 함수)가 작업 완료를 신경 쓴다.**

## 자바스크립트에서 동기와 비동기

자바스크립트는 단일 스레드 프로그래밍 언어로 단일 호출 스택이 있어 한 번에 하나의 일을 처리할 수 있다. 그러므로 자바스크립트는 동기 방식으로 진행이 된다(?)
하나의 호출 스택만 있기 때문에 하나의 함수를 처리하는데 매우 오랜 시간이 걸린다면 다음 실행해야할 함수에 지장을 줄 수 있다는 문제가 발생한다.

> **비동기의 필요성**
> ex. 웹 페이지를 사용자에게 보여줄 때 해당 웹 페이지에 있는 모든 데이터(사진, 글 등)를 받고 나서야 화면이 보여진다고 하면, 서버에서 데이터를 모두 받아올 때까지 시간이 오래 걸릴 수 있으며 사용자 입장에서 웹 페이지를 보는데 너무 느려서 답답할 수 있다.
>
> > 그러나 데이터를 받아오는 일을 하는 도중에 우선 웹 페이지의 기본 레이아웃을 보여주고 보여줄 수 있는 것들을 우선 보여주는 것이 더 바람직할 것이다.

## 비동기 예제

**비동기 예제/setTimeout()**

```js
console.log("1");
setTimeout(function () {
  console.log("2");
}, 3000);
console.log("3");
// 1->3->2 순으로 출력
```

**jquery ajax 통신의 문제점(비동기 흐름을 컨트롤 하기 어려움)**

```js
function getData() {
  var tableData;
  $.get("url", function (response) {
    tableData = response;
  });
  // $.get()로 데이터를 요청하고 받아올 때까지 기다리지 않고 return 해버림
  return tableData;
}
console.log(tableData); // 따라서 undefined
```

#### 콜백함수로 비동기 처리 방식의 문제점 해결

```js
function getData(callback) {
  $.get("url", function (response) {
    callback(response); // calllback 함수에 인자 response 넘겨줌
  });
}
getData(function (tableData) {
  console.log(tableData);
}); // callback
```

> 콜백함수란? 제어권을 대상에게 넘겨준다.
> 함수 a(getData)의 매개변수로 콜백함수 b(callback)를 전달하면, a(getData)가 b(callback)의 제어권을 갖게 된다.
> 특별한 요청(bind)가 없는 한 a(getData)에 미리 정해진 방식에 따라 b(callback)를 호출한다

`callback`은 다시 알아보자 ([callback - 블로그 글](https://www.daleseo.com/js-async-callback/))

## blocking vs non-blocking

blocking과 non-blocking은 주로 IO의 읽기, 쓰기에서 사용

### blocking

**요청한 작업을 마칠 때까지 계속 대기**한다.
즉시 return한다, return 값을 받아야 끝난다.

- Thread 관점으로 본다면, 요청한 작업을 마칠 때까지 계속 대기하며 return 값을 받을 때까지 한 Thread를 계속 사용/대기 한다.

### non-blocking

**요청한 작업을 즉시 마칠 수 없다면 즉시 return**한다.
즉시 리턴하지 않는다, 일을 못하게 막음

- Thread 관점으로 본다면, 하나의 Thread가 여러 개의 IO를 처리 가능하다.

---

## 그렇다면 동기/비동기, blocking/non-blocking의 차이는?

사실 이렇게만 보면 동기랑 blocking이 비슷하고, 비동기랑 non-blocking이 비슷해 보인다.
두 그룹의 차이는 아래와 같다.

blocking/non-blocking: 이 그룹은 호출되는 함수가 바로 return을 하는지 신경씀
동기/비동기: 이 그룹은 호출되는 함수의 작업 완료 여부를 누가 신경쓰는지

### blocking/non-blocking

호출된 함수가 바로 return해서 호출한 함수에게 **제어권을** 넘겨주고
호출한 함수가 **다른 일을 할 수 있는 기회**를 줄 수 있으면 `non-blocking`

호출된 함수가 자신의 작업을 모두 마칠 때까지
호출한 함수에게 **제어권을 넘겨주지 않고 대기**하게 만든다면 `blocking`

### 동기/비동기

호출되는 함수에게 *callback*을 전달해서 호출되는 함수의 작업이 완료되면
호출되는 함수가 전달받은 *callback*을 실행하고, **호출한 함수는 작업 완료 여부를 신경쓰지 않는다**면 `비동기`

호출하는 함수가 호출되는 함수의 작업 완료 후 _return을_ 기다리거나
호출되는 함수로부터 바로 _return_ 받더라도 **작업 완료 여부를 호출한 함수 스스로 확인하며 신경** 쓴다면 `동기`

## 동기, 비동기, 블로킹, 논블록킹 비교

**Blocking + Synchronous ✔️, Non-blocking + Asynchronous ✔️**

![](https://velog.velcdn.com/images%2Fwonhee010%2Fpost%2F3f0ce3ff-b477-4399-a8ba-8a44aa6b0e6e%2FiSafBIF.png)

### Blocking + Synchronous

> ex) JAVA

![](https://velog.velcdn.com/images%2Fnittre%2Fpost%2Ff6212fee-ee42-4023-ae02-d2dc15eec46a%2Fimage.png)

`함수A` 는 `함수B` 의 리턴값을 필요로 한다(**동기**).
그래서 제어권을 `함수B` 에게 넘겨주고, `함수B` 가 실행을 완료하여 리턴값과 제어권을 돌려줄때까지 기다린다(**블로킹**).

### Non-blocking + Asynchronous

비동기 논블로킹은 이해하기 쉽다. A함수는 B 함수를 호출한다.
이 때 제어권을 B 함수에 주지 않고, 자신이 계속 가지고 있는다(**논블로킹**).
따라서 B 함수를 호출한 이후에도 멈추지 않고 자신의 코드를 계속 실행한다.
그리고 B 함수를 호출할 때 콜백함수를 함께 준다. B 함수는 자신의 작업이 끝나면 A 함수가 준 콜백 함수를 실행한다(**비동기**).

![](https://velog.velcdn.com/images%2Fnittre%2Fpost%2Fb9566928-9a6b-4111-9cad-528daa45475d%2Fimage.png)

### Non-blocking + Synchronous

결과가 없다면 바로 return한다.
결과가 있으면 바로 결과를 return 한다.
(결과가 생길때까지 계속 완료 되었는지 확인)

![](https://velog.velcdn.com/images%2Fwonhee010%2Fpost%2F9f44f41f-e03c-45e0-90b0-1751e86aeb70%2Fa8xZ9No.png)

#### 이해가 안돼? 한번 더 보자.

![](https://velog.velcdn.com/images%2Fnittre%2Fpost%2Ffe5d1231-4c3c-4caf-bdd8-2287926e38ca%2Fimage.png)

`A함수` 는 `B함수` 를 호출한다.
이 때 `A함수` 는 `B함수` 에게 제어권을 주지 않고, 자신의 코드를 계속 실행한다.(**논블로킹**)
그런데 `A함수` 는 `B함수` 의 리턴값이 필요하기 때문에, 중간중간 `B함수` 에게 함수 실행을 완료했는지 물어본다(**동기**).
**즉, 논블로킹인 동시에 동기인 것이다.**

### blocking + Asynchronous

호출되는 함수가 바로 return하지 않고, 호출하는 함수는 작업 완료 여부를 신경쓰지 않는다.

> (이 조합은 사실 이점이 없어서 일부러 이 방식을 사용하진 않는다고 한다.)
> (의도하지 않게 blocking+Async로 동작하는 경우가 있다고는 한다. 이는 non-blocking+Async를 추구하다 의도가 변질되어버림... 대표적으로 Node.js + MySQL 조합이라고 한다.)

![](https://velog.velcdn.com/images%2Fwonhee010%2Fpost%2Fbc20459f-02fe-431a-a1d0-8d5016a964f9%2FzKF0CgK.png)

#### 이해가 안돼? 한번 더 보자.

Async-blocking의 경우는 사실 잘 마주하기 쉽지 않다.

A 함수는 B 함수의 리턴값에 신경쓰지 않고, 콜백함수를 보낸다(**비동기**).
그런데, B 함수의 작업에 관심없음에도 불구하고, A 함수는 B 함수에게 제어권을 넘긴다(**블로킹**).
따라서, A 함수는 자신과 관련 없는 B 함수의 작업이 끝날 때까지 기다려야 한다.
![](https://velog.velcdn.com/images%2Fnittre%2Fpost%2F9b6754f0-8721-4308-8a62-d884c7315d15%2Fimage.png)

---

[동기와 비동기, 블록킹와 논블록킹](https://velog.io/@wonhee010/%EB%8F%99%EA%B8%B0vs%EB%B9%84%EB%8F%99%EA%B8%B0-feat.-blocking-vs-non-blocking)
[블록킹와 논블록킹](https://velog.io/@nittre/%EB%B8%94%EB%A1%9C%ED%82%B9-Vs.-%EB%85%BC%EB%B8%94%EB%A1%9C%ED%82%B9-%EB%8F%99%EA%B8%B0-Vs.-%EB%B9%84%EB%8F%99%EA%B8%B0)
[동기와 비동기](https://velog.io/@open_h/javascriptasync)
[동기와 비동기, 블록킹과 논블록킹 - 우테코](https://www.youtube.com/watch?v=IdpkfygWIMk&t=4s&ab_channel=%EC%9A%B0%EC%95%84%ED%95%9C%ED%85%8C%ED%81%AC)
