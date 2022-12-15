# 이벤트 핸들러와 이벤트 리스너

> **이벤트란?**
> 우리가 사용하는 웹사이트들은 많은 기능을 제공한다.
> 특정메뉴를 버튼을 클릭했을 때 정보가 노출된다거나, 반대로 정보를 입력하면 서버에 자동 저장되는 기능 등이 있다.
> 이러한 기능들이 없다면 웹페이지는 글자가 있는 전자문서와 동일하다.
>
> > 주로 우리들은 이 웹페이지에 있는 기능을 사용하기 위해서 _마우스나 키보드를 조작하여 기능을 실행한다._ 바로 이러한 행위를 이벤트라고 부른다.

> **이벤트처리기**
> 이벤트처리기란 말 그대로 이벤트 청취자, 즉 위와 같은 **이벤트가 발생하는 것을 감지** 하는 역할을 한다. 따라서 이벤트가 발생했을 때 이벤트를 받은 다음 처리기 안에 있는 코드를 실행한다.
> 대게 특정 기능을 하는 함수가 실행 대상임
>
> > 이벤트 처리기에는 핸들러와 리스너가 있다.

### 이벤트 핸들러 (Event Handler)

- 이벤트가 발생했을 때 그 처리를 담당하는 함수를 실행시킨다.
- 특정 요소에서 발생한 이벤트를 처리하기 위해서는 **이벤트 핸들러(Event Handler)** 라는 함수를 작성하여 연결시켜야한다.
- 이벤트 핸들러가 연결된 특정 요소에서 지정된 타입의 이벤트가 발생하면, 웹 브라우저는 이벤트 리스너에 연결된 **이벤트 핸들러** 를 실행시킨다.
- 이벤트 핸들러 함수는 **이벤트 객체(Event Object)** 를 인수로 전달받을 수 있다. 이렇게 전달받은 이벤트 객체를 이용하여 이벤트의 성질을 결정하거나, 이벤트의 기본 동작을 막을 수도 있다.

이벤트 핸들러는 쉽게 설명하자면 addEventListener() 메서드를 사용하지 않고 이벤트와 함수를 연결하는 것

```html
<button id="btn">클릭</button>
```

```javascript
const btnNode = document.getElementById("btn");
// 화살표 함수
btnNode.onclick = () => {
  console.log("버튼 클릭");
};
```

### 이벤트 리스너 (Event Listener)

- 특정한 이벤트에 대해서 일어날 동작을 정의할 수 있다.
- 지정된 타입의 이벤트가 특정 요소에서 발생하면, 웹 브라우저는 그 요소에 등록된 리스너를 실행시킴.
- 이벤트 리스너와 이벤트 핸들러를 합쳐 이벤트 리스너라고도 한다.
  ![이벤트](https://media.discordapp.net/attachments/864333994623500369/1052873086522904637/image.png)

  이벤트 리스너는 `addEventListener()` 메서드를 사용하여 이벤트와 함수를 연결

  ```javascript
  const btnNode = document.getElementById("btn");
  btnNode.addEventListener("click", function () {
    console.log("버튼 클릭");
  });
  ```

---

### 이벤트 핸들러와 이벤트 리스너의 차이점

이벤트 **핸들러**는 이벤트 유형에 대해 하나의 이벤트 핸들러만 가질 수 있다.

```javascript
const btnNode = document.getElementById("btn");

btnNode.onclick = () => {
  console.log("클릭1");
};

btnNode.onclick = () => {
  console.log("클릭2");
};

btnNode.onclick = () => {
  console.log("클릭3");
};
```

##### 실행결과

```
클릭3
```

이벤트 **리스너**는 이벤트 유형에 대해 여러 이벤트 리스너를 가질 수 있다.

```javascript
const btnNode = document.getElementById("btn");

btnNode.addEventListener("click", () => {
  console.log("클릭1");
});

btnNode.addEventListener("click", () => {
  console.log("클릭2");
});

btnNode.addEventListener("click", () => {
  console.log("클릭3");
});
```

##### 실행결과

```
클릭1
클릭2
클릭3
```

### 이벤트 핸들러와 이벤트 리스너의 주의사항

- 일반 함수(함수 리터럴, 함수 선언문)와 화살표 함수에서 this는 다르게 동작합니다.
- 일반 함수에서 this는 이벤트를 호출한 요소를 의미하지만 화살표 함수의 this는 전역 객체를 의미

##### 일반함수

```javascript
const btnNode = document.getElementById("btn");

btnNode.onclick = function () {
  console.log(this.value); // hi
};
```

##### 화살표함수

```javascript
const btnNode = document.getElementById("btn");

btnNode.onclick = () => {
  console.log(this.value); // TypeError 발생
};
```

---

### 정리

1. 이벤트 핸들러는 이벤트가 발생했을 때, 실행되어야 하는 함수(혹은 실행되어야 하는 코드)를 연결하는 방법이다.
2. 이벤트 리스너는 이벤트가 발생했을 때, 실행되어야 하는 이벤트 핸들러 함수를 연결하는 방법이다.
3. 이벤트 핸들러는 이벤트 유형에 하나의 이벤트 핸들러만 가질 수 있다.
4. 이벤트 리스너는 이벤트 유형에 여러 개의 이벤트 리스너를 가질 수 있다.
5. 일반적으로 이벤트 리스너를 사용하는 것이 좋으나 React, Angular, Vue, Svelte와 같은 프레임워크에서는 이벤트 핸들러를 사용해야 할 수 있다.

[React 이벤트처리](https://ko.reactjs.org/docs/handling-events.html)
