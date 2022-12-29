# 스코프체인

> **스코프란** 식별자의 유효 범위를 말한다.

![](https://velog.velcdn.com/images%2Foneook%2Fpost%2F03db529d-7ba0-445c-9a08-a7f31cc6db57%2Fid-Artboard%203%20copy%203%403x-100.jpg)

**새롭게 정의된 스코프는 상위의 스코프에 접근**할 수 있다.
스코프 체인은 **scope의 가장 내부에서 scope chain을 따라 바깥쪽으로 검색**을 하게 된다.

## 📄 예제로 보는 스코프체인

```js
var a = 10;

function func1() {
  var b = 20;
  function func2() {
    var c = 30;
    console.log(a + b + c);
  }
  func2();
}
func1();
```

다음 코드가 실행되면 어떤 결과가 나올까?
`60`이 출력될 것이다. 이렇게 출력되는 이유가 바로 스코프체인과 관련되어있다.
`60`이 출력되었다는 말은 **a,b,c 모든 변수에 접근했다**는 의미이기 때문이다.

**지금처럼 안쪽에서부터 외부를 탐색 하는것, 이것 자체를 스코프 체인이라고 한다.**

# 🏰 성이 다른데 어떻게 알아요?

![](https://velog.velcdn.com/images%2Foneook%2Fpost%2Fb7fbf045-f272-474c-9a6a-dd0011486c0c%2Fid-Artboard%203%20copy%204%403x-100.jpg)

`(코드 블럭 = 성)` 높은 성에 위쪽에 있더라도 다른 성이라면 참조가 불가능하다.

코드 블럭의 실행은 독립적이며 실행이 끝나면 더 이상 참조할 수 없기 때문에 더욱이 다른 위치에서 참조할 수 없다.
그렇다면? 전역 스코프에도 참조값이 없다면 null을 반환하게 된다.

## 📄 예제로 보는 스코프체인2

```js
let x = 1;

function foo() {
  let x = 10;
  bar();
}

function bar() {
  console.log(x); // 1
}

foo(); // ? foo 함수를 통해 bar 함수 실행 -> 1 출력
bar(); // ?  bar함수만 실행 -> 1 출력
```

예제의 함수 `bar`는 전역에 선언되었다. 따라서 함수 `bar`의 상위 스코프는 전역 스코프이고 위 예제는 전역 변수 `x`의 값 **`1`을 두번 출력**한다.

> ### 렉시컬 스코프 (Lexical scope) 개념
>
> 렉시컬 스코프는 함수를 어디서 호출하는지가 아니라 어디에 선언하였는지에 따라 결정
> [스코프 정리](https://velog.io/@dlwoxhd/da#%EB%A0%89%EC%8B%9C%EC%BB%AC-%EC%8A%A4%EC%BD%94%ED%94%84-lexical-scope)

---

[JS스코프 - 벨로그 정리잘되어 있음](https://velog.io/@oneook/%EC%8A%A4%EC%BD%94%ED%94%84%EC%99%80-%EC%8A%A4%EC%BD%94%ED%94%84-%EC%B2%B4%EC%9D%B8-JavaScript-Basics#1-3-%EC%8A%A4%EC%BD%94%ED%94%84-%EC%B2%B4%EC%9D%B8-scope-chain)
