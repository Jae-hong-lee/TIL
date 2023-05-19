# Next.js(3) - Link, Router 차이

직관적이고 간편한 라우팅 방식은 Next JS의 큰 장점이다.
이 Next의 라우팅은 크게 2가지 방식이 존재하는데, 바로 Router와 Link이다.
두 가지 방식의 특징 및 장단점을 분석해보는 시간을 가져보았다.

> **a태그**
> 기존 html 페이지의 이동 방식이다.
> a 태그로 이동하게 되면 이동할 페이지를 새로 다운받아서 보여준다.
> 따라서, react/next의 SPA를 활용하지 못하게 되어 비효율적이다.

보통은 페이지 전환을 할 때 a태그를 사용한다.
a 태그는 순수 HTML 요소로, 사용자를 새 페이지의 URL로 이동시키는 하이퍼링크를 생성하는데 이때 페이지는 _완전히 새로고침_ 된다.
그 과정에서 빈 화면이 보일 수도 있다는 말이고 또한 매번 통신을 새롭게 하다 보니 성능 및 사용자 경험 측면에서 떨어질 수 밖에 없다.

따라서 Next에서는 페이지 전환을 할때 웬만하면 Link나 Router를 사용하는 것을 권장.

## Next/Router

Router는 `react-router-dom`의 `history.push()`와 유사,
Next.js에서 라우터를 사용하려면 useRouter 훅을 사용해서 router 객체에 접근할 수 있다

```js
import { useRouter } from "next/router";
```

> 크롤러가 링크를 감지하지 못하여 **SEO가 좋지 않을 수도 있다.** (a 태그를 사용하지 않으므로)
> 외부 URL을 사용할 경우 window.location 혹은 a 태그를 사용해야한다.
> 대부분 onClick과 같은 이벤트 핸들러와 같이 사용된다.

#### .push

**client-side 전환을 할 수 있도록 도와주고** Next/link 대신 사용할 수 있다.

router.push는 라우터 히스토리 스택에 새로운 url을 쌓아주는데,
예를 들어 `home > login > item` 순으로 페이지를 이동했을 때,

`router.push`를 사용해 'mypage'로 이동한다면 라우터 히스토리 스택에는 `home > login > item > mypage`가 쌓임.
마지막 페이지에서 뒤로가기를 누르면 'item' 페이지로 되돌아간다.

예시:

```js
router.push(url, as, options);
```

url: **필수** 라우팅 하려는 url
as: [선택] 브라우저 url 바에 보여지는 path
options: [선택] scroll(라우팅 후 스크롤업), shallow, locale 등의 옵션이 있습니다.

```js
import { useRouter } from "next/router";

export default function Page() {
  const router = useRouter();

  return (
    <button type="button" onClick={() => router.push("/about")}>
      버튼 클릭시 about 페이지로 이동
    </button>
  );
}
```

> 주의: router.push는 외부 url 사용시에는 적합하지 않습니다.
> a tag의 target="\_blank" 를 사용하거나 window.location을 사용하는 것이 낫습니다.

## next/link

next에서 제공하는 a 태그와 유사한 태그로, SPA를 활용할 수 있다.

```js
import Link from "next/link";

<Link href={"/26(10)-03-kakao-map-routed"}>페이지 이동하기</Link>;
```

Link 태그에 href 속성을 추가하여 가고자 하는 주소를 넣는다. (a 태그의 href와 동일합니다.)
~~안쪽에는 a 태그로 감싸준다.
className과 같은 속성들을 a 태그에 추가한다.~~
페이지 새로고침 없이 페이지가 전환된다.
Link는 Client-side navigation방식으로, 자바스크립트로 페이지 전환이 이루어진다고 한다.
또한 navigation보다 빠르며 SPA의 특성을 유지한다. (페이지를 다시 로드하지 않고 SPA동작처럼 **"보이게"** 만든다.)

~~결정적으로 a 태그를 생성하기 때문에 웹사이트가 크롤링되어 SEO에 유리하다.~~

> 참고: Next.js 12.2 이전에는 구성 요소가 태그(`<a>`)를 Link래핑 해야 했지만 버전 12.2 이상에서는 필요하지 않습니다.

## Client-side navigation?

_Client-side navigation 이란 페이지 전환이 javascript로 이루어지는 것_ 입니다.
브라우저의 기본 navigation보다 훨씬 빠르게 작동하며, 리액트의 SPA(Single Page Application) 특성을 유지하며 페이지 전환을 할 수 있음.

아래 예시에서 보듯이, 페이지 전환을 해도 개발자 도구에서 추가한 style 속성이 새로고침 되지 않음.
즉, next.js의 `<Link>`를 사용하면 전체 페이지를 로드하지 않고, **client-side navigation**으로 작동하는것

#### ✔️ [예제-출처](https://nextjs.org/learn/basics/navigate-between-pages/client-side)

확인할 수 있는 간단한 방법은 다음과 같다.

1. 브라우저의 개발자 도구를 사용하여 의 CSS 속성을 로 변경 `background`합니다.`<html>yellow`
2. 두 페이지 사이를 앞뒤로 이동하려면 링크를 클릭하십시오.
3. 페이지 전환 사이에 노란색 배경이 지속되는 것을 볼 수 있습니다.
4. 이는 브라우저가 전체 페이지를 로드 하지 않고 클라이언트측 탐색이 작동하고 있음을 나타냅니다.

<img src = "https://nextjs.org/static/images/learn/navigate-between-pages/client-side.gif">

`<Link href="…">` 대신 `<a href="…">`사용하고 이 작업을 수행한 경우 브라우저가 전체 새로 고침을 수행하기 때문에 링크 클릭 시 배경색이 지워진다.

## 정리

### Router

router.push()를 이용하는 경우는 window.location과 비슷하게 동작,
`<a>`태그를 만들지 않기때문에 검색엔진최적화(`SEO`)을 신경쓰고 있다면 해당 링크는 크롤링되지 않아서 **SEO에 불리**, 대부분 onClick과 같은 이벤트 핸들러와 같이 사용

> router.push는 보통 eventHanlder와 함께 맞이 쓰이는데, 이건 액션이다. 만약 버튼을 누른다고 가정을 하자 그러면 당신은 task 코드가 실행 될 것이고, 그에 따른 결과에 따라 페이지를 이동하는데 이럴때는 router.push()를 사용할 수 있겠다. 단지 다른 페이지를 가기위해서 사용하지말자. SEO에 좋지 않다.

### Link

`<Link>태그`는 `<a>태그를 생성`하기 때문에 웹사이트가 크롤링되어 **SEO에 유리**합니다.
페이지를 다시 로드하지않고 SPA동작처럼 "보이게" 만듬.

SEO 에 유리하기 때문에 Link 태그를 쓰는게 맞아 보일 수는 있어도 사용자 개인에게만 필요한 페이지(mypage)같은 경우는 router.push 를 이용해서 이벤트 핸들러와 같이 사용해도 될 것 같다.

---

[Link 와 router차이](https://velog.io/@dev_sony503/TIL-Next-JS-Router%EC%99%80-Link%EC%9D%98-%EC%B0%A8%EC%9D%B4)
[router 정리 nextjs](https://velog.io/@khy226/Next.js-Router-%EC%A0%95%EB%A6%AC)
[Client-side - 공홈](https://nextjs.org/learn/basics/navigate-between-pages/client-side)
