# AJAX 란?

> **MDN**: 비동기 JavaScript 및 XML 또는 Ajax 는 그 자체로 기술이 아니라 HTML 또는 XHTML , CSS , JavaScript , DOM , XML , XSLT 및 가장 중요한 XMLHttpRequest개체를 포함하여 여러 기존 기술을 함께 사용하는 접근 방식입니다.

![ajax](https://user-images.githubusercontent.com/72030487/220594695-b55bbfeb-c5b1-404c-8f4c-58b823a763d6.png)

**Asynchronous Javascript And XML**의 약자로, 비동기적으로 JS를 사용해서 데이터를 받아와 동적으로 DOM을 갱신 및 조작하는 웹 개발 기법을 의미한다. 여기서 XML이 있는 이유는 예전엔 데이터 포맷으로 XML을 많이 사용했기 때문이다.

![https://github.com/baeharam/Must-Know-About-Frontend/raw/main/images/javascript/ajax.png](https://github.com/baeharam/Must-Know-About-Frontend/raw/main/images/javascript/ajax.png)

사용자가 AJAX가 적용된 UI와 상호작용하면, 서버에 AJAX 요청을 보내게 된다. 서버는 DB에서 데이터를 가져와서 JS 파일에 정의되어 있는 대로 HTML/CSS와 데이터를 융합하여 만든 DOM 객체를 UI에 업데이트 시킨다. 비동기로 이루어지며, 기존의 페이지를 전부 로딩하는 방식이 아닌 **일부만 업데이트 하는 방식이다.**

## ✅ Ajax를 사용하는 것의 장단점은 무엇인가요?

### 장점

상호작용성이 좋아진다. 서버의 새로운 컨텐츠를 전체 페이지를 다시로드할 필요 없이 동적으로 변경할 수 있다.
스크립트나 스타일 시트는 한 번만 요청하면 되므로 서버에 대한 연결을 줄여줌.
상태를 페이지에서 관리 할 수 있다. 메인 컨테이너 페이지가 다시 로드되지 않기 때문에 JavaScript의 변수와 DOM의 상태가 유지.
**기본적으로 SPA의 대부분의 장점과 같다.**

### 단점

동적 웹 페이지는 북마크하기 어렵다.
브라우저에서 JavaScript가 비활성화된 경우 작동하지 않는다.
일부 웹 크롤러는 JavaScript를 실행하지 않으며 JavaScript에 의해 로드된 콘텐츠를 볼 수 없다. (SEO 까다로움)
**SPA의 대부분의 단점과 같다.**

## ✅ 어떻게 사용할까?

### **XMLHttpRequest**

일반적으로 `XMLHttpRequest` 객체를 사용하여 인스턴스를 만들어 인스턴스의 `open()` , `send()` 등의 메소드를 이용한다.

```jsx
let ourRequest = new XMLHttpRequest();
ourRequest.open(
  "GET",
  "https://learnwebcode.github.io/json-example/animals-1.json"
);
ourRequest.onload = () => {
  let ourData = JSON.parse(ourRequest.responseText);
  console.log(ourData[0]);
};
ourRequest.send();
```

`open()` 으로 요청할 메소드와 URL을 설정한 뒤, 모두 로드되었을 경우의 콜백함수를 초기화한다.

### **Fetch API**

새로나온 `fetch` 를 사용해서 요청을 할 수도 있는데 IE를 지원하지 않는다는 점을 제외하고는 `XMLHttpReqeust` 보다 훨씬 직관적이다. ES6(ES2015) 에서 표준이 되었고, Promise를 리턴한다.

```jsx
fetch("https://learnwebcode.github.io/json-example/animals-1.json")
  .then((res) => res.json())
  .then((resJson) => console.log(resJson));
```

응답객체는 `json()` , `blob()` 과 같은 내장 메서드로 body를 추출해내고 이는 다시 Promise를 리턴한다.

---

답변:
자바스크립트를 이용하여 **비동기식으로 XML을 이용해 서버와 통신하는 방식**이다. 최근에는 XML보다 `JSON`을 많이 사용한다.
웹 페이지 전체를 업데이트 하는게 아니라 **부분적으로 업데이트를 하기 위해 사용**한다. 따라서 장점은 SPA와 마찬가지로 웹페이지를 업데이트시 **새로고침 없이 필요한 부분만 업데이트** 되어서 UX가 향상된다.
그러나 단점으로는 **SEO가 까다롭고**, **동적화면에서 History 관리와 북마크가 어려울 수 있다.**
