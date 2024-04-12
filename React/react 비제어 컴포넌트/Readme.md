# react 제어 컴포넌트, 비제어 컴포넌트(controlled component vs. uncontrolled component)

React에 의해서 값이 제어되는 컴포넌트를 제어컴포넌트, 값이 제외되지 않는 컴포넌트를 비제어 컴포넌트라고 한다.

`form` 이나 `Input` 요소를 다룰 때, 요소에 입력되는 갑을 state로 관리하거나 DOM API 을 통해서 관리할 수 있는데, state로 Dom element의 값을 다루는 컴포넌트가 제어 컴포넌트, DOM API 를 통해 다루는 것이 비제어컴포넌트이다.

### 제어컴포넌트 (controlled component)

> 제어 컴포넌트는 사용자의 입력을 기반으로 자신의 state를 관리하고 업데이트합니다. React에서는 변경할 수 있는 state가 일반적으로 컴포넌트의 state 속성에 유지되며 setState()에 의해 업데이트됩니다.
> 이러한 방식으로 React에 의해 값이 제어되는 입력 폼 엘리먼트를 “제어 컴포넌트 (controlled component)“라고 합니다.

위 내용은 React 공식문서에서의 설명이고 코드와 함께 보자.

```js
export default function App() {
  const [input, setInput] = useState("");
  const onChange = (e) => {
    setInput(e.target.value);
  };

  return (
    <div className="App">
      <input onChange={onChange} />
    </div>
  );
}
```

사용자의 입력을 받는 컴포넌트에 event 객체를 이용해 `setState()` 로 값을 저장하는 방식을 제어 컴포넌트 방식. (React에 의해 값이 제어되는.)
**제어 컴포넌트는 사용자가 입력한 값과 저장되는 값이 실시간으로 동기화된다.**

![제어컴포넌트](https://velog.velcdn.com/images%2Fyukyung%2Fpost%2F9f93d492-f1e7-4768-9bdc-b2c3c7d6f10c%2FHoneycam%202021-05-11%2015-30-16.gif)

### 비제어 컴포넌트(uncontrolled component)

비제어 컴포넌트는 바닐라JS 와 크게 다르지 않은 방식.
폼을 제출할 때 `submit Button` 을 클릭할 때 비로소 요소 내부에 입력받은 값을 얻어왔다.
제어 컴포넌트에서 사용한 `setState()`을 사용하지 않고 `ref` 를 사용해서 값을 얻는다.

```js
export default function App() {
  const inputRef = useRef(); // ref 사용
  const onClick = () => {
    console.log(inputRef.current.value);
  };

  return (
    <div className="App">
      <input ref={inputRef} />
      <button type="submit" onClick={onClick}>
        전송
      </button>
    </div>
  );
}
```

위 코드의 input 값은 사용자만이 상호작용할 수 있고, 우리는 필요한 시점에 이벤트 핸들러를 통해 `ref` 에 저장된 값을 가져와 활용하는 비제어 컴포넌트.
비제어 컴포넌트는 **state** 로 값을 관리하지 않기 때문에 값이 업데이트 할 때마다 리렌더링 되지 않기 때문에 성능상에 이점이 있다.

![image](https://velog.velcdn.com/images%2Fyukyung%2Fpost%2F6c8405c3-f230-4d27-b1df-72e6c99b393c%2FHoneycam%202021-05-11%2015-32-01.gif)

### 언제 어느것을 사용?

![image](https://github.com/Jae-hong-lee/TIL/assets/72030487/95e58800-4724-4aa7-8713-ed052fcc3455)

> ✅ : 비제어 컴포넌트 사용가능

- 일회성 정보검색(ex: 제출) ✅
- 제출 시 값 검증 (유효성검사) ✅
- 실시간으로 필드값을 유효성 검사
- 조건부로 제출 버튼 비활성화 (disabled)
- 실시간으로 입력 형식 적용 (숫자만 가능하게 등..)
- 동적 입력

제어 컴포넌트의 사용할 때의 문제점인 `제어 컴포넌트는 사용자가 입력한 값과 저장되는 값이 실시간으로 동기화된다.` 는 불필요한 리렌더링, 불필요한 api 요청으로 인한 자원 낭비 문제가 발생하는데 이것들은 을 [스로틀링&디바운싱](#쓰로틀링디바운싱)사용하여 막을 수 있다.

하지만 일반적으로 모든 form 요소에서 상태의 동기화가 필요한 건 아니고, form 요소가 증가할 수록 모든 컴포넌트에 쓰로틀링이나 디바운싱을 걸기는 힘들다. 만약 값이 트리거 된 이후에만 갱신이 되도 문제가 없다면, ref를 사용하는 방식이 불필요한 렌더링을 방지하는데 더욱 도움이 될 수 있다. 이러한 비제어 컴포넌트를 사용해 렌더링을 최적화 하는 라이브러리가 `react-hook-form` 이다.

#### etc

> ##### 쓰로틀링&디바운싱
>
> 쓰로틀링: 마지막 함수가 호출된 후 일정 시간이 지나기 전에 다시 호출되지 않도록 하는 것
> 디바운싱: 연이어 호출되는 함수들 중 마지막 함수(또는 제일 처음)만 호출하도록 하는 것

---

[React 공식-비제어 컴포넌트](https://ko.legacy.reactjs.org/docs/uncontrolled-components.html)
[React: 제어 컴포넌트와 비제어 컴포넌트의 차이점 - yukyung](https://velog.io/@yukyung/React-%EC%A0%9C%EC%96%B4-%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8%EC%99%80-%EB%B9%84%EC%A0%9C%EC%96%B4-%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8%EC%9D%98-%EC%B0%A8%EC%9D%B4%EC%A0%90-%ED%86%BA%EC%95%84%EB%B3%B4%EA%B8%B0)
