# 클로저

## ✅MDN

**클로저는 함수와 함수가 선언된 어휘적 환경의 조합이다.**

클로저는 반환된 **내부함수**가 자신이 선언됐을 때의 환경(Lexical environment)인 **스코프를 기억**하여 자신이 선언됐을 때의 **환경(스코프) 밖에서 호출되어도 그 환경(스코프)에 접근할 수 있는 함수**

조금 더 간단히 말하면 클로저는 자신이 생성될 때의 환경(Lexical environment)을 기억하는 함수다.

> 클로저를 이해하려면 자바스크립트가 어떻게 변수의 유효범위를 지정하는지(`Lexical scoping`)를 먼저 이해해야 한다. **+ 실행컨텍스트**

## ✅스코프 참조 예제(1)

```js
function outerFunc() {
  var x = 10;
  var innerFunc = function () {
    console.log(x);
  };
  innerFunc();
}

outerFunc(); // 10
```

함수 `outerFunc` 내에서 내부함수 `innerFunc`가 선언되고 호출되었다
이때 내부함수 `innerFunc`는 자신을 포함하고 있는 외부함수 `outerFunc`의 **변수 x**에 접근할 수 있다.
이는 함수 `innerFunc`가 함수 `outerFunc`의 내부에 선언되었기 때문이다.
**함수 `innerFunc`가 함수 `outerFunc`의 내부에 선언된 내부함수이므로 함수 `innerFunc`는 자신이 속한 렉시컬 스코프(전역, 함수 `outerFunc`, 자신의 스코프)를 참조할 수 있다.**

#### 이것을 `실행 컨텍스트`의 관점에서 설명해 보면

1. `innerFunc` 함수 스코프(함수 자신의 스코프를 가리키는 활성 객체) 내에서 변수 x를 검색한다. ➡️ 검색이 실패하였다.
2. `innerFunc` 함수를 포함하는 외부 함수 `outerFunc`의 스코프(함수 `outerFunc`의 스코프를 가리키는 함수 `outerFunc`의 활성 객체)에서 변수 x를 검색한다. ➡️ 검색이 성공하였다!

내부함수 `innerFunc`가 자신을 포함하고 있는 외부함수 `outerFunc`의 **변수 x**에 접근할 수 있는 것, 다시 말해 _상위 스코프에 접근할 수 있는 것은 렉시컬 스코프의 레퍼런스를 차례대로 저장하고 있는 실행 컨텍스트의 스코프 체인을 자바스크립트 엔진이 검색하였기에 가능한 것_

```js
function outerFunc() {
  var x = 10;
  var innerFunc = function () {
    console.log(x);
  };
  return innerFunc;
}
/**
 *  함수 outerFunc를 호출하면 내부 함수 innerFunc가 반환된다.
 *  그리고 함수 outerFunc의 실행 컨텍스트는 소멸한다.
 */
var inner = outerFunc();
inner(); // 10
```

함수 `outerFunc`는 내부함수 innerFunc를 반환하고 생을 마감 (라이프사이클)
즉, 함수 `outerFunc`는 실행된 이후 **콜스택(실행 컨텍스트 스택)에서 제거**되었으므로 함수 `outerFunc`의 _`변수 x` 또한 더이상 유효하지 않게 되어 `변수 x`에 접근할 수 있는 방법은 달리 없어 보인다._
그러나 위 코드의 실행 결과는 변수 x의 값인 10 이 나온다.

**자신을 포함하고 있는 외부함수보다 내부함수가 더 오래 유지되는 경우, 외부 함수 밖에서 내부함수가 호출되더라도 외부함수의 지역 변수에 접근할 수 있는데 이러한 함수를 클로저(Closure)라고 부른다.**

[위 정의](#mdn)에서 말하는 “함수”란 반환된 내부함수를 의미하고 **그 함수가 선언될 때의 렉시컬 환경(Lexical environment)란** 내부 함수가 선언됐을 때의 **스코프**를 의미한다.
즉, **클로저**는 **반환된 내부함수가 자신이 선언됐을 때의 환경(Lexical environment)인 스코프를 기억하여 자신이 선언됐을 때의 환경(스코프) 밖에서 호출되어도 그 환경(스코프)에 접근할 수 있는 함수**를 말한다.

이를 조금 더 간단히 말하면 클로저는 자신이 생성될 때의 환경(Lexical environment)을 [기억하는 함수](#mdn)다 라고 말할 수 있는 것.

### 주의사항(실행 컨텍스트의 활성 객체(Activation object)와 클로저)

![](https://poiemaweb.com/img/closure.png)
외부함수가 이미 반환되었어도 외부함수 내의 변수는 이를 필요로 하는 내부함수가 **하나 이상 존재하는 경우 계속 유지**된다.
이때 내부함수가 외부함수에 있는 변수의 복사본이 아니라 **실제 변수에 접근한다는 것**에 주의!

---

### [클로저의 활용](https://poiemaweb.com/js-closure#2-%ED%81%B4%EB%A1%9C%EC%A0%80%EC%9D%98-%ED%99%9C%EC%9A%A9) - 글을 읽고 한번 보는 것을 추천

> ### 인용
>
> 사실 클로저는 자바스크립트만 국한된 개념이 아니며 다른 많은 프로그래밍 언어에서도 존재하는 개념입니다. 그럼에도 불구하고 특히 자바스크립트 커뮤니티에서 클로저가 많은 거론되는 이유는 함수를 마치 일반 값처럼 다룰 수 있는 자바스크립트의 유연함 때문일 것입니다. 어떻게 사용하느냐에 따라서 양날의 검이 될 수 있는 클로저에 대한 개념을 잡으시는데 도움이 되었으면 한다.

---

[Closures - MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Closures)
[생활코딩 - Closure](https://youtu.be/bwwaSwf7vkE)
[클로저 - 코어 JavaScript 튜토리얼](https://ko.javascript.info/closure)
[모던 딥 자바스크립트](https://poiemaweb.com/js-closure)

[Velog - 클로저 스코프](https://velog.io/@04_miffy/Closure)
