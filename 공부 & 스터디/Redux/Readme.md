# Redux

Redux 는 주로 React 애플리케이션에서 사용하는 상태 관리 라이브러리이다.
Redux를 사용하면 애플리케이션의 상태(state)를 일관성 있게 관리하고, 컴포넌트 간의 상태 공유를 쉽게 할 수 있다.

### Redux 핵심개념

1. Store(스토어)
   `Store`는 애플리케이션의 모든 상태를 담고 있는 객체이다. 단 하나의 `Store`만 존재하며, 애플리케이션의 상태는 이 `Store`에서 관리된다.
2. State(상태)
   `State`는 애플리케이션의 현재 상태를 나타내는 데이터. 이 상태는 `Store`에서 관리되고, `Store`는 애플리케이션의 모든 상태를 담고 있다.
   상태는 읽기 전용이고, 직접수정이 불가능하다. 상태를 변경하려면 액션을 `dispatch`(디스패치)해야한다.
3. Action(액션)
   `Action`은 상태를 변경하기 위한 유일한 방법. `Action`은 일반 객체로, 반ㄷ시 `type` 이라는 속성을 가져야 한다. 이 `type` 속성은 액션의 유형을 나타내고, 그 외에 상태 변경에 필요한 추가 데이터를 포함할 수 있다.

   ```js
   const addAction = {
     type: "ADD_TODO",
     payload: {
       id: 1,
       text: "Learn Redux",
     },
   };
   ```

4. Reducer(리듀서)
   `Reducer`는 `Action`이 디스패치될 때 상태를 어떻게 변경할지를 정의하는 함수.
   `Reducer`는 현재 상태와 `Action` 객체를 인자로 받아 새로운 상태를 반환한다. `Reducer` 함수는 순수 함수여야 하고, 이전 상태를 직접 변경하지 않고 새로운 상태 객체를 반환해야 한다.

   ```js
   function todoReducer(state = [], action) {
     switch (action.type) {
       case "ADD_TODO":
         return [...state, action.payload];
       default:
         return state;
     }
   }
   ```

5. Dispatch(디스패치)
   `Dispatch`는 `Action`을 `State`에 전달하는 메소드. `Dispatch`를 호출하면 `Store`가 `Reducer`를 통해 새로운 상태를 생성하고 `State`를 업데이트한다.
   ```js
   store.dispatch({
     type: "ADD_TODO",
     payload: {
       id: 2,
       text: "Write Redux Documentation",
     },
   });
   ```
6. Subscriber(구독자)
   구독메서드를 사용하여 상태가 변경될 때마다 특정 콜백 함수를 실행할 수 있다.
   상태가 변경되면 구독된 모든 콜백이 실행된다.

   ```js
   const unsubscribe = store.subscribe(() => {
     console.log(store.getState());
   });
   ```

### Redux 기본동작흐름

1. Action 생성: 사용자가 인터페이스에서 어떤 행동을 취하면, 이에 대응하는 `Action`이 생성.
2. Dispatch: 생성된 `Action`은 `Dispatch` 메서드를 통해 `Store`로 전달
3. Reducer 호출: `Store`는 `Action`을 받으면, 등록된 `Reducer`를 호출하여 현재 상태와 `Action을 전달.
4. State 업데이트: `Reducer`는 전달받은 `Action`에 따라 상태를 변경하고, 새로운 상태를 반환
5. State 전달: `Store`는 변경된 상태를 모든 구독자에게 전달하여 UI를 업데이트한다.
