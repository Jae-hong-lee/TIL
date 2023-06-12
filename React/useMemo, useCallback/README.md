# useMemo, useCallback 의 차이

### Memoization?

우선, `useMemo`와 `useCallback`에 대해 이해하려면 `Memoization`이라는 개념에 대한 이해가 필요하다.
Memoization이란, **컴퓨터 프로그램이 동일한 계산을 반복해야 할 때, 이전에 계산한 값을 메모리에 저장함으로써 동일한 계산의 반복 수행을 제거하여 프로그램 실행 속도를 빠르게 하는 기술**이다. `useCallback`과 `useMemo`는 Memoization 기능을 제공하는 React의 내장 Hook으로, 퍼포먼스 최적화를 위해 사용한다.

### Re-rendering

React는 다음과 같은 조건에서 리렌더링을 진행한다.

- 자신의 state가 변경될 때
- 부모 컴포넌트로부터 전달받은 props가 변경될 때
- 부모 컴포넌트가 리렌더링 될 때
- forceUpdate 함수가 실행될 때

리렌더링이란, **이전에 생성한 컴포넌트 정보와 다시 렌더링한 정보를 비교해 최소한의 연산으로 DOM 트리를 업데이트 하는 것**을 말한다.
즉, 이전의 Virtual DOM과 현재의 Virtual DOM을 비교하여 변경된 값에 대해 DOM 트리를 업데이트 해주는 것

![VDOM](https://velog.velcdn.com/images/hang_kem_0531/post/d5db67cd-7185-405b-8430-847e0d147f16/image.png)

> 예를들어 state,props가 여러가지라면?
> state1, state2, state3이 존재하는 상태에서 state1을 변경시켜주었는데 state2, state3도 재계산된다면??
> 이것이 과연 좋은 rerendering일까?

> **리렌더링을 최적화**
> 이 Hook들은 주어진 렌더에서 수행해야 하는 작업의 양을 줄이고, 컴포넌트가 다시 렌더링해야 하는 횟수를 줄이면서 리렌더링을 최적화하게 된다.

## useMemo

useMemo는 Memoization된 **'값'을 반환**하는 함수이다.

```js
useMemo(() => fn, []);
```

`useMemo`는 deps에 변화가 생기면, 내부에 정의된 콜백 함수를 실행하고 그 함수의 반환 값을 반환하게 된다.

```js
import React, { useState, useCallback, useMemo } from "react";

export default function App() {
  const [buttonX, setButtonX] = useState(0);
  const [buttonY, setButtonY] = useState(0);

  const handleButtonX = () => {
    setButtonX((prev) => prev + 1);
  };

  const handleButtonY = () => {
    setButtonY((prev) => prev + 1);
  };

  // useMemo
  useMemo(() => {
    console.log(buttonX);
  }, [buttonX]);

  return (
    <>
      <button onClick={handleButtonX}>X</button>
      <button onClick={handleButtonY}>Y</button>
    </>
  );
}
```

예를 들어 위와 같은 코드를 구현하면, useMemo의 deps가 `buttonX`이기 때문에 `X 버튼`을 누를때만 `console.log(buttonX)`가 실행되게 된다.
Y 버튼을 눌러도 함수 컴포넌트는 리렌더링되지만, `buttonX` 값은 변하지 않기 때문에 useMemo의 변화가 없게 되는 것

## useCallback

반면에 `useCallback`은 Memoization된 **'값'이 아닌 '함수'를 반환**하는 함수이다. useMemo는 함수를 실행해서 그 실행 값을 반환하지만, useCallback은 함수 자체를 반환하는 것

```js
useCallback(fn, []);
```

이때 useCallback은 deps의 변화가 생기게 되면, 새로운 함수를 반환하게 된다.

```js
import React, { useState, useCallback, useMemo } from "react";

function App() {
  const [buttonX, setButtonX] = useState(0);
  const [buttonY, setButtonY] = useState(0);

  const handleButtonX = () => {
    setButtonX((prev) => prev + 1);
  };

  const handleButtonY = () => {
    setButtonY((prev) => prev + 1);
  };

  // useCallback
  const returnUseCallback = useCallback(() => {
    console.log(buttonY);
  }, [buttonX]);

  returnUseCallback();

  return (
    <>
      <button onClick={handleButtonX}>X</button>
      <button onClick={handleButtonY}>Y</button>
    </>
  );
}

export default App;
```

위 코드에서 useCallback 구문은 `() => {console.log(buttonY)}` 라는 함수를 반환하지만, deps인 buttonX의 변화가 생길때만 반환되게 된다.
그렇기 때문에 Y 버튼을 계속 누른다고 해도 X 버튼을 누르지 않으면 `() => {console.log(0)}`의 함수가 반환되고, X 버튼을 누를때에 Y값을 가져와 `() => {console.log(새로운값)}`이 반환되게 된다.

그렇기 때문에, useCallback은 함수와 상관없는 상태 값이 변할 때, 함수 컴포넌트에서 불필요한 업데이트가 일어나는 것을 방지해 준다.

### useCallback은 새로운 함수를 반환

useCallback의 deps가 변환될 때 반환되는 함수는, 이전의 함수와 형태가 같지만 새로운 함수이다. 이는 새로운 무기명 함수를 반환한 것이며, 이전의 함수와 값이 같을 뿐 다른 메모리 주소를 가지고 있다.

## useMemo와 useCallback을 사용하지 말아야 할 경우

1. host 컴포넌트에 (div span a img...) 전달하는 모든 항목에 대해 쓰지 말아야한다. 리액트는 여기에 함수 참조가 변경되었는지 신경쓰지 않는다. (ref, mergeRefs는 여기에서 제외된다.)

2. useCallback useMemo의 의존성 배열에 완전히 새로운 객체와 배열을 전달해서는 안된다. 이는 항상 의존성이 같지 않다는 결과를 의미하며, 메모이제이션을 하는데 소용이 없다. useEffect useCallback useMemo의 모든 종속성은 참조 동일성을 확인한다.

3. 전달하려는 항목이 새로운 참조여도 상관없다면, 사용하지 말아야 한다. 매번 새로운 참조여도 상관없는데, 새로운 참조라면 메모이제이션하는 것이 의미가 없다.

## useMemo와 useCallback을 사용해야 할 경우

1. 계산 비용이 많이 들고, 사용자의 입력 값이 map과 filter을 사용했을 때와 같이 이후 렌더링 이후로도 참조적으로 동일할 가능성이 높은 경우, useMemo를 사용하는 것이 좋다.

2. ref 함수를 부수작용과 함께 전달하거나, mergeRef-style 과 같이 wrapper 함수 ref를 만들 때 useMemo를 쓰자. ref 함수가 변경이 있을 때마다, 리액트는 과거 값을 null로 호출하고 새로운 함수를 호출한다. 이 경우 ref 함수의 이벤트 리스너를 붙이거나 제거하는 등의 불필요한 작업이 일어날 수 있다. 예를 들어, useIntersectionObserver가 반환하는 ref의 경우 ref 콜백 내부에서 observer의 연결이 끊기거나 연결되는 등의 동작이 일어날 수 있다.

3. 자식 컴포넌트에서 useEffect가 반복적으로 트리거되는 것을 막고 싶을 때 사용하자.

4. 매우 큰 리액트 트리 구조 내에서, 부모가 리렌더링 되었을 때 이에 다른 렌더링 전파를 막고 싶을 때 사용하자. 자식 컴포넌트가 React.memo React.PureComponent일 경우, 메모이제이션된 props를 사용하게되면 딱 필요한 부분만 리렌더링 될 것이다.

---

[useMemo - 별코딩](https://youtu.be/e-CnI8Q5RY4)
[useCallback - 별코딩](https://youtu.be/XfUF9qLa3mU)
