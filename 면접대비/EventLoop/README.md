# Event Loop - 할 일을 정돈하기(감시자)

> [Call Stack](https://github.com/Jae-hong-lee/TIL/tree/main/%EB%A9%B4%EC%A0%91%EB%8C%80%EB%B9%84/JS%EC%97%94%EC%A7%84%20%EA%B5%AC%EB%8F%99%EA%B3%BC%EC%A0%95)의 동작원리를 먼저 보고 오는 것을 추천한다.

## ⭐️JS정의 - 짧게..

[다음질문은 강연자료를 참고하였다.](https://youtu.be/8aGhZQkoFbQ)

**"자바스크립트는 싱글 쓰레드 기반이며 논 블로킹 방식의 비동기적인 동시성 언어이며 콜 스택, 이벤트 루프와 콜백 큐 그리고 여러가지 다른 API들을 가지고 있다. ”**

"먼소리야?🤔" 할 수 있다. 그만큼 특징이 많은 언어이다.
그렇다면 JS의 엔진인 V8에게 콜 스택, 이벤트 루프나 콜백 큐를 가지고 있는지 물어보면
V8은 "저는 콜 스택과 힙을 가지고 있는데 다른 건 도대체 뭐야?" 라고 대답할 것이다.

![](https://baeharam.netlify.app/media/js/overview.png)

그림을 보게 되면, **V8**이 가지고 있는 힙과 콜스택이 있고 웹 브라우저에서 제공하는 **Web API**가 DOM, Ajax, setTimeout 등을 제공하는 것을 알 수 있다. **이벤트 루프**와 **콜백 큐**도 존재하고 있다.

## ⭐️Event Loop 정의

: `Call Stack`과 `Callback Queue`의 상태를 체크하여, `Call Stack`이 빈 상태가 되면, `Callback Queue`의 첫번째 콜백을 `Call Stack`으로 밀어넣어 준다. 이러한 반복적인 행동을 **틱(tick)** 이라 부른다.

쉽게말해, Javascript 내에서도 효과적으로 일을 처리하기 위한 비슷한 매커니즘이 있는데 이를 event loop라고 부른다.

> 🔥**Event Loop**와 **Queue**는 자바스크립트 엔진이 하나의 코드 조각을 하나씩 처리할 수 있도록 작업을 스케줄하는 동시에 자바스크립트에서 비동기 작업을 할 수 있도록 해준다.

### ✅다음 코드를 실행하면 각 구성요소 들이 어떤 역할을 하는지 알아보자.

```js
setTimeout(function exec() {
  console.log("second");
}, 1000);
```

- **Call Stack** : Call Stack 에 `setTimeout`함수가 들어오면 비동기 함수이기 때문에 바로 실행되지 않고 Web APIs 로 함수를 넘긴다.

- **Web APIs**: `setTimeout`이 Call Stack에 들어와 실행되면 Browser API인 timer를 호출한다. web API로 콜백 함수 timeout() 에 들어가는 것.

- **Callback Queue**: 이벤트 발생 시 실행해야 할 callback 함수가 Callback Queue에 추가된다.

- **Event Loop**: Event Loop의 역할은 간단하게 두가지다. (감시와 추가)
  1. Call Stack과 Callback Queue를 감시
  2. Call Stack이 비어있을 경우, Callback queue에서 함수를 꺼내 Call Stack에 추가

`setTimeout`함수에 대해 알아보았다.

### ✅그렇다면 아래코드를 실행했을 때 어떻게 동작할 것인지도 생각해보자.

```JS
console.log('시작!');
setTimeout(function timeout(){
  console.log('1초가 지났습니다');
});
console.log('끝!');
```

위 코드 실행했을 때, `Event Loop` 매커니즘이 어떻게 처리하는지 생각해보는 것

![](https://miro.medium.com/max/1400/1*mvt8iXt8jGC0lpsEmutV5g.webp)

1. Call Stack 에 `console.log('시작!');` 들어오고 실행이 되게 된다.

![](https://miro.medium.com/max/1400/1*589dr2Geb5F5GUaZHjBjbA.webp)

2. 이후 `Call Stack`에 `setTimeout 함수`가 들어간다.

![](https://miro.medium.com/max/1400/1*RYWPHWO_mkyn2d3uaBeNmQ.webp)

3. `setTimeout 함수`가 비동기 함수이기 때문에 `Call Stack` 에서 바로 실행되지 않고, `web API`로 콜백 함수 `timeout()`이 들어 간다.

![](https://miro.medium.com/max/1400/1*hDdqanX1qc3UfxhJloD_fQ.webp)

4. 다음 코드인 `console.log('끝!');`을 실행한다. 아직 1초가 지나지 않아 `timeout() `함수는 여전히 `web API`에 있다고 하자.

![](https://miro.medium.com/max/1400/1*NNnj_wIqcoQRElQ5m8iSSg.webp)

5. 1초가 지나 `callback Queue`에 `timeout()` 함수가 들어 온다.

![](https://miro.medium.com/max/1400/1*E1dCYkJoTHRoQP-fJ5fFIw.webp)

**6. `Event Loop`가 `Call Stack`이 비어있는지 확인하고, `timeout()` 함수를 `Call Stack`으로 보낸다.** (중요)

![](https://miro.medium.com/max/1400/1*TJ5IqBMZSBJiZPHeEhDJXA.webp)

7. `timeout` 함수 안에 있던 `console.log(‘1초가 지났습니다’) `코드가 호출

![](https://miro.medium.com/max/1400/1*yii7Ng_qgx_FveUXLKdY5g.webp)

8. 모든 호출이 되고 `Call Stack`은 비워지게 된다.

결과적으로 `Event Loop`는 `Call Stack` 비어있는지를 주기적으로 확인하여 `Callback Queue`에서 `Callback function`을 가져와 `Call Stack`에서 `Javascript` 코드가 실행될 수 있도록 돕는 역할을 한다는 것이다.

## ⭐️Call Stack이 비어있을 경우.

Event Loop를 설명 할 때 "**Call Stack이 비어있을 경우**, Callback queue에서 함수를 꺼내 Call Stack에 추가 합니다." 라고 소개한다.

**Call Stack이 비어있을 경우** 라는 것을 좀 더 자세히 알아보자.

```JS
console.log('first');
setTimeout(
    function cb() {
        console.log('second');
    }
, 0);
wait3Seconds();
console.log('third');


function wait3Seconds() {
    let start = Date.now(), now = start;
    while (now - start < 3 * 1000) {
        now = Date.now();
    }
}
```

위 코드의 핵심은 `setTimeout`함수와 7번 line의 `wait3Seconds` 함수이다.
line 2에서 `setTimeout` 함수에 delay 0을 주고 line 7에서 3초를 기다려보자. console.log 결과는 어떻게 될까?

`wait3Seconds` 함수에 의해 3초 동안 `console.log(‘third’)`는 실행되지 않는다.
따라서 0ms 후에 실행된 `console.log(‘second’)`가 먼저 출력되지 않을까?

```
first
third
second
```

결과는 **No** 이다.
아래 이미지는 wait3Seconds 함수가 실행된 후 3초 동안 Call Stack 및 Event Loop 상태

![](https://miro.medium.com/max/1100/1*3kaNdJ-oFGY718TR85Uk0A.webp)

`setTimeout`이 호출된 후 0ms 뒤에 callback으로 전달 된 cb는 이미 `Callback Queue`안에 있다.
그럼에도 불구하고 `console.log(‘second’)`가 third보다 뒤에 출력되는 이유는 `Call Stack`에 `wait3Seconds`함수가 있기 때문이다.

**Event Loop**는 **Call Stack**이 비어있지 않기 때문에(`wait3Seconds`) **Callback Queue**를 체크하지 않는다.
`wait3Seconds` 함수 종료 후엔 `console.log(‘third’)`가 **Call Stack**에 추가되어 먼저 출력되는 것

위 예제를 통해 한 가지 확인할 수 있는 것은 `setTimeout`의 delay인자가 delay ms 후에 실행 되는 것을 보장하지 않는다는 거다.
정확히는 delay ms 후에 **Callback Queue**에 들어가게 된다는 것.

## ⭐️Callback Queue 의 우선순위

[mdn](https://developer.mozilla.org/ko/docs/Web/API/HTML_DOM_API/Microtask_guide)

![](https://velog.velcdn.com/images%2Fholim0%2Fpost%2F05d920e4-35a5-4461-8142-464738cee3e5%2Fimage.png)

> Job Queue = Microtask Queue, Task Queue = Event Queue

**Callback Queue** 는 사실 Microtask Queue, Animation Frames, Task Queue 로 구성 되어 있고 세가지의 종류를 우선순위대로 실행한다.

> **Microtask Queue > Animation Frames > Task Queue**

`Microtask Queue`는 `Task Queue`와 다른 Queue이며 **Promise**를 사용할 경우 **Microtask Queue**를 사용하게 된다.
promise를 사용할 때 `callback` 함수 역할을 하는 `.then` 을 사용하게 되며, 이런 **thenable**한 함수들은 **Microtask Queue**에 추가됨.

> `thenable`
> then 메소드를 갖는 객체를 뜻한다. 체이닝이나 await과 같은 promise 패턴을 가진 구문에서 사용할 수 있다. 모든 promise객체는 thenable 객체이지만 역은 성립하지 않는다.

두 Queue는 우선순위가 다르다.
**`promise`(Microtask Queue)** 가 **`setTimeout`(Task Queue)** 보다 우선순위가 높다는 것을 기억하고 코드를 통해 알아보자.

```js
console.log("fisrt");

setTimeout(function () {
  console.log("setTimeout - second");
}, 0);

var promise = new Promise(function (resolve, reject) {
  resolve();
});
promise
  .then(function (resolve) {
    console.log("promise - third");
  })
  .then(function (resolve) {
    console.log("promise - four");
  });

console.log("five");
```

위 코드의 결과는 아래와 같다.

```
fisrt
five
promise - third
promise - four
setTimeout - second
```

결과를 보면 **Microtask Queue**의 우선순위가 **Task Queue(Event Queue)** 보다 높다.
따라서 `Event Loop`는 `Call Stack`이 비어있을 경우, `Microtask Queue` 에서 기다리는 모든 작업을 처리하고` Task Queue`로 이동하게 되는 것.

---

참고자료
[What the heck is the event loop anyway? - 영상](https://youtu.be/8aGhZQkoFbQ)
[Promise(이벤트루프)](https://youtu.be/S1bVARd2OSE)
[진짜 쉽게 알아보는 JS 원리](https://blog.toycrane.xyz/진짜-쉽게-알아보는-자바스크립트-동작-원리-c7fbdc44cc97)
[자바스크립트는 어떻게 작동하는가](https://engineering.huiseoul.com/자바스크립트는-어떻게-작동하는가-이벤트-루프와-비동기-프로그래밍의-부상-async-await을-이용한-코딩-팁-다섯-가지-df65ffb4e7e)
