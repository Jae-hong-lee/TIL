# Recoil 이란?

Recoil은 React 프로젝트를 위한 전역 상태관리 라이브러리.
Redux, Mobx와 달리 Recoil은 리액트 만을 위해 생긴 라이브러리.

Recoil은 Atom이라는 작은 데이터 조각을 만들어 해당 데이터 변화 시에 이를 참고하는 컴포넌트들만 re-render 시킨다.

> 왜? React 프로젝트를 위한 이라는 단어를 썻을까?
> 2020년 5월 Facebook에서 출시하였습니다. (mobx → redux → recoil 순서대로 페이스북에서 만들었다고 한다.)
>
> > 그렇기에, 다른 라이브러리(Redux, Mobx)와는 달리 React 전용이며 React에 최적화되어 있다!

## 왜 사용할까? 🤔

React는 `props` 를 통해서 부모 컴포넌트에서 자식 컴포넌트로 보내줄 수 있는데 왜 전역 상태 라이브러리를 사용해야 할까?

- 주고 받을 수는 있지만 코드가 굉장히 복잡해지고 어디서 어떤 data를 받았는지, 내려줬는지 일일히 다 체크해야하기 때문이다. (그냥 귀찮음)

정확히는 컴포넌트를 나누다보면 하나의 어플리케이션 안에 여러 컴포넌트에 전달해줘야 하는 경우 `props-drilling` 현상이 나타나기도 한다. props로 계속해서 자식 컴포넌트에 전달하면 코드가 지저분해지는 부분이 크다.

> 이 문제는 React 가 단방향 통신이라는 부분도 이해해야한다.

## 언제 사용할까? 🤔

- 기본적으로 **일반적인 경우에는 지역 상태로 데이터를 관리하는 것**을 권장한다.
- 지역 상태로 데이터를 관리 시 **다수의 컴포넌트 간에 상태 의존성이 높아진다면 전역 상태로 데이터를 관리하는 것**을 권장한다.
- 전역 상태 관리 시 서버에서 가져오는 데이터(db)와, 단순하게 UI 상태를 나타내는 데이터는 분리하여 다룬다.
- 서버 데이터 캐싱 시 전역 상태로 다루면 안된다. 서버 상태를 관리하려면, SWR이나 React-Query 와 같은 서버 캐싱 전용 라이브러리를 사용하는 것을 권장한다.

## 어떻게 사용할까? 🤔

#### 1. RecoilRoot

recoil state 를 가지는 컴포넌트들이 필요로 하는, atom context 를 가지는 root 입니다. 해당 Root 이하의 컴포넌트는 모두 같은 전역 상태브러리의 값을 쓰겠다! 라는 말임.

```jsx
import TempCelsius from "./TempCelsius";
import { RecoilRoot } from "recoil";

function App() {
  return (
    <RecoilRoot>
      <TempCelsius />
    </RecoilRoot>
  );
}

export default App;
```

**위 코드처럼 `RecoilRoot` 로 감싸주지 않으면 에러가 난다!!!**
TempCelsius 라는 컴포넌트에서 recoil을 쓴다면, 이를 감싸주는 곳에서 RecoilRoot 를 써줘야 합니다. (절대로 TempCelsius 컴포넌트 안에서 해주는게 아님!)

**`<RecoilRoot>` 는 여러개가 같이 존재할 수 있습니다.**
RecoilRoot 밑에 RecoilRoot를 또 만들어준다면, 두 번째 RecoilRoot에서는 아예 새로운 상태를 가지게 된다.
상위 Recoil은 무시, atom은 각 루트에 따라 다른 값을 갖게 되는 것.

#### 2. Atom

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbvuswU%2FbtrDJwGqZnq%2FPv1slHZYDliyAfnzWC5Jv0%2Fimg.png)

Atom은 **상태의 단위**이다. **컴포넌트끼리 공유 가능한 가장 작은 단위**의 state 를 atom

Atom이 업데이트되면 해당 Atom을 쓰고 있던 모든 컴포넌트들이 새로운 값으로 리렌더링
또 여러 컴포넌트에서 같은 Atom을 쓰고 있으면 그 컴포넌트들이 상태를 동일하게 공유한다.

```ts
import { atom } from "recoil";

const postingId = atom<string>({
  key: "posting/id",
  default: "",
});
```

useState 로 만든 상태와 개념적으로 비슷하지만, 다른 컴포넌트와 상태를 공유할 수 있다는 점이 다르다.
그런데 이 때 다른 컴포넌트라 함은, 같은 `페이지` 안의 다른 컴포넌트입니다. 다른 페이지는 영향을 받지 않습니다.

`typescript` 에서 atom 키워드 뒤에 오는 타입은 **atom 의 값의 타입**을 말한다.
**`key` 는 atom 마다 고유하게 만들어 줘야 한다.** (key 생성 규칙도 정해주자!)
default 는 기본 상태를 의미함.

#### 3. selector

atom 또는 다른 selector 로 구성한 순수함수

```ts
import { atom, selector } from "recoil";

const tempFahrenheit = atom({
  key: "tempFahrenheit",
  default: 32,
});

const tempCelcius = selector({
  key: "tempCelcius",
  get: ({ get }) => ((get(tempFahrenheit) - 32) * 5) / 9,
  set: ({ set }, newValue) =>
    set(
      tempFahrenheit,
      newValue instanceof DefaultValue ? newValue : (newValue * 9) / 5 + 32
    ),
});
```

atom 또는 selector 를 기반으로 새롭게 결과를 구성해주는 순수함수임.
구독중인 atom 또는 selector 가 업데이트 되면 selector 도 업데이트 !

`get` 을 이용하여 다른 atom도 사용할 수 있다.
이 말은 즉, 해당 **atom이 변하면 selector도 변한다**는 말!!

**selector을 왜 쓰나 ?**

- getter, setter 을 사용해 전처리 및 후처리를 한 atom을 사용할 수 있다.
- get 을 통해 전처리한 atom값 을 받아올 수 있다.
- set 을 이용하여 atom값을 후처리 할 수 있다.

#### 4. useRecoilState

```ts
const [a, b] = useRecoilState();
```

useState 와 같은 역할, 전역으로 useState을 설정해주는 거라고 생각하시면 편함.
다만 여기서는 선언을 하는 것이 아니라, 전역적으로 설정된 atom을 가져오는 역할.

---

[Recoil 정리 ](https://basemenks.tistory.com/200)
