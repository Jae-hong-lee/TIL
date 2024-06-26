# react-query Optimistic Update(낙관적 업데이트)

> mutation을 수행하기 전 낙관적으로 업데이트를 하면 mutation이 실패할 가능성이 있다.
> 대부분의 실폐에서 다시 가져오기를 트리거해 실제 서버 상태로 되돌릴수가 있다.
> 하지만, 일부 상황에서 이마저 제대로 작동하지 않을 수 있는데 이때는 롤백으로 상태를 최초로 되돌릴수가 있다.

# 왜 필요할까?

"좋아요" 기능이나 "장바구니" 기능이 대표적인 낙관적 업데이트가 필요한 기능이다.

좋아요 버튼을 클릭과 동시에 해당 피드에 좋아요가 반영되는 모습을 확인할 수 있다.
좋아요 기능은 서버에 side effect를 발생시키는 PUT 요청을 보내고 그 응답을 받아오기까지 딜레이가 생기는 기능인데, 인스타그램이나 페이스북 좋아요 기능을 보게 된다면 클릭과 동시에 동기적으로 응답을 받아오는 것처럼 보인다.

이러한 로직이 화면을 보고 있는 사용자들이 1~2초라도 기다렸다가 해당 기능을 보게 된다면 기다림에 대한 불편함을 느낄 수 있다.

**서버와의 통신과 상관없이, 화면을 먼저 바꿀 수 있을까?** 에서 시작된 것이다.

서버의 응답이 오기 전에 상태를 먼저 바꾸어 업데이트 된 상태를 화면에 반영해주면 이러한 불편함이 해결될 수 있는 것.
이것이 바로, `낙관적 업데이트`

# 낙관적 업데이트는 무엇인데?

![jhjung3-UX 향상을 위한 Optimistic Updates 구현하기 ](https://github.com/Jae-hong-lee/TIL/assets/72030487/c1ef6604-248a-42cf-aa1f-93de6140e0c2)

이처럼 `Optimistic Updates`를 구현함으로써 유저는 자신의 액션에 따른 즉각적인 피드백을 받을 수 있고, 향상된 UX를 경험할 수 있게 된다.
즉,사용자 경험 향상을 위한 방법이다.

여기서 서버의 성능을 높이면 되지 않을까? 라는 질문을 던지게 된다면?
서버의 응답이 아무리 빠르게 오더라도 사용자의 네트워크 환경에 의해 어플리케이션의 응답 속도도 달라지기 때문에 네트워크가 불안전할 수도 있다.
서버의 응답에만 의존하여 상태(또는 UI)를 업데이트하는 것이 아니라, 낙관적 업데이트 와 같은 로직을 통해 각기 다른 사용자들의 환경에 대비하는 것이 좀 더 좋은 방법이다.

# React-query(Tanstack-query)

> tanstack query를 사용하지 않고도 낙관적 업데이트를 구현가능 ➡️ useState를 사용하여..

Tanstack-Query를 이용하는 경우가 많고(내 경우) 공식문서에도 설명이 잘되어있어서 설명을 참고하여 구현해보았다.

> 공식문서 - 낙관적 업데이트 구현 설명: 낙관적으로 상태를 업데이트한 후 변경 작업을 수행할 때, 변경 작업이 실패할 가능성이 있습니다. 이러한 실패 케이스 대부분에서는 낙관적인 쿼리를 다시 가져와서 실제 서버 상태로 되돌릴 수 있습니다. 그러나 어떤 상황에서는 다시 가져오기가 올바르게 작동하지 않을 수 있으며, 변경 작업 오류가 다시 가져오기가 불가능한 종류의 서버 문제를 나타낼 수 있습니다. 이 경우에는 대신 업데이트를 롤백할 수 있습니다. 이를 위해 useMutation의 onMutate 핸들러 옵션을 사용하여 나중에 오류 및 onSettled 핸들러로 전달될 값을 반환할 수 있습니다. 대부분의 경우 롤백 함수를 전달하는 것이 가장 유용합니다.

![image](https://github.com/Jae-hong-lee/TIL/assets/72030487/4d03ac19-2533-45dc-bda9-70fbf0639d4d)

TanStack Query에서 진행되는 낙관적 업데이트는 흐름도

#### Todos 를 만들때, 새 할 일을 추가할 때 할 일 목록 낙관적업데이트

```js
const queryClient = useQueryClient();

useMutation({
  mutationFn: updateTodo,
  // 뮤테이션 호출되는 경우
  onMutate: async (newTodo) => {
    // 선택지 데이터에 대한 모든 퀴리요청을 취소하여 이전 서버 데이터가 낙관적 업데이트를 덮어쓰지 않도록 함 (refetch 취소)
    await queryClient.cancelQueries({ queryKey: ["todos"] });

    // 기존 선택지 데이터의 snapshot
    const previousTodos = queryClient.getQueryData(["todos"]);

    // 새로운 선택지 데이터로 낙관적 업데이트 실시
    queryClient.setQueryData(["todos"], (old) => [...old, newTodo]);

    // Return a context object with the snapshotted value
    // context를 return, context 예시에는 이전 스냅샷, 새로운 값(또는 롤백하는 함수)이 있음
    return { previousTodos };
  },
  // mutation 실패 시, 캐시에 저장된 값으로 롤백
  onError: (err, newTodo, context) => {
    queryClient.setQueryData(["todos"], context.previousTodos);
  },
  // 쿼리 함수의 성공하면 -> 기존 선택지 데이터 무효화
  onSettled: () => {
    queryClient.invalidateQueries({ queryKey: ["todos"] });
  },
});
```

업데이트시에 낙관적 업데이트를 적용한 모습

![sadsadsa](https://github.com/Jae-hong-lee/TIL/assets/72030487/0d94af8f-c4e7-4848-a9cb-93e54aade727)

낙관적 업데이트를 적용하지 않은 모습

![asdsad](https://github.com/Jae-hong-lee/TIL/assets/72030487/1dbf33d7-085a-41aa-b8fe-542cf5f79f1f)

뮤테이션 차이를 보면 확연히 그 차이를 느낄 수 있다.

---

[UX 향상을 위한 Optimistic Updates 구현하기 (with 리액트 쿼리)-제제](https://velog.io/@jhjung3/Optimistic-Updates-%EA%B5%AC%ED%98%84%ED%95%98%EA%B8%B0-with-%EB%A6%AC%EC%95%A1%ED%8A%B8-%EC%BF%BC%EB%A6%AC)
[tecoble-낙관적 업데이트로 사용자 경험을 개선해보자!](https://tecoble.techcourse.co.kr/post/2023-08-15-how-to-improve-ux-with-optimistic-update)

[tanstack-Optimistic Update](https://tanstack.com/query/v4/docs/framework/react/guides/optimistic-updates)
