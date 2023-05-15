# CSR, SSR

면접에 가서 매번 질문 받는 SSR 에 대하여 언제까지 SEO 에서 좋다고 답변할 것인가?
에 대하여 생각하다 학습하게 되었다.

## SPA, MPA

우선 CSR과 SSR을 설명해 드리기 전에 SPA와 MPA의 개념을 알아보자

### SPA

Single Page Application

> 하나의 HTML 파일을 기반으로 자바스크립트를 이용해 동적으로 화면의 컨텐츠를 바꾸는 방식의 웹 어플리케이션이다.

싱글 페이지 즉, 하나의 페이지로 이루어진 홈페이지 하나의 페이지로만 구성되어 있어 CSR에 적합하다
데이터를 수정, 조회할 때 동적으로 페이지를 구성하여 페이지가 새로고침 되지 않고 다른 페이지로 넘어가지 않는다.
우리 알고 있는 Vue, Angular, React 프레임워크로 만든 홈페이지들이 대부분 여기에 속함.
그렇다고 **모든 SPA가 CSR을 사용하는 건 아니다**
PHP나 JAVA로 정적인 페이지 하나만 구성된 홈페이지라면 그것 또한 SPA라고 한다.

### MPA

Multiple Page Application

> 사용자가 페이지를 요청할 때마다, 웹 서버가 요청한 UI와 필요한 데이터를 HTML로 파싱해서 보여주는 방식의 웹 어플리케이션이다.

여러 개의 페이지로 이루어진 홈페이지, PHP나 JAVA가 여기에 속한다.
여러 개의 페이지로 구성되어 있으므로 보통 클라이언트에서 서버에 요청을 보내면 서버에서 렌더링하고 클라이언트에게 응답을 주는 방식인 SSR 환경을 사용.
SPA와 반대로 새로고침이 발생한다.

---

전통적인 방식을 이용한다면, SPA가 사용하는 렌더링 방식은 CSR이고, MPA가 사용하는 렌더링 방식은 SSR이다. 각 방식의 동작방식과 장단점을 알아보고, 전통적인 방식을 벗어나, SPA에서도 적절히 SSR을 구현했을 때의 장점과 그 이유를 알아보자.

## Client Side Rendering

<img alt = "CSR" width="700" src="https://miro.medium.com/v2/resize:fit:1100/format:webp/1*1lEXKckn-M0tLpwWhPsODw.png">

CSR에선 브라우저가 서버에 HTML과 JS 파일을 요청한 후 로드되면 사용자의 상호작용에 따라 JS를 이용해서 동적으로 렌더링을 시킨다.

> 구동방식 정리
> 초기 로드 시 빈 HTML과 모든 로직이 담겨 있는 Javascript 다운로드를 한다
> 그 후 빈 HTML에 Javascript를 이용하여 Dom을 동적으로 생성하여 그려 내게 된다.
> 이렇게 클라이언트에서 렌더링한다고 하여 클라이언트 사이드 렌더링

### **👍 장점**

- 첫 로딩만 기다리면, 동적으로 빠르게 렌더링이 되기 때문에 사용자 경험(UX)이 좋다.
- 서버에게 요청하는 횟수가 훨씬 적기 때문에 서버의 부담이 덜하다.

### **👎 단점**

- 모든 스크립트 파일이 로드될 때까지 기다려야 한다. (첫로딩이김)
  - 리소스를 청크(Chunk) 단위로 묶어서 요청할 때만 다운받게 하는 방식으로 완화시킬 수 있지만 완벽히 해결할 수는 없다고함.
- 검색엔진의 검색 봇이 크롤링을 하는데 어려움을 겪기 때문에 검색엔진 최적화(Search Engine Optimization)의 문제가 있다.
  - 구글 봇의 경우는 JS를 지원하지만, 다른 검색엔진의 경우 그렇지 않기 때문에 문제가 된다.

## Server Side Rendering

<img alt="SSR" width="700" src="https://miro.medium.com/v2/resize:fit:786/format:webp/1*bylhKcVB6678Q6wXhm_p0w.png">

SSR에선 브라우저가 페이지를 요청할 때마다 해당 페이지에 관련된 HTML, CSS, JS 파일 및 데이터를 받아와서 렌더링을 시킨다.

> 구동방식 정리
> 서버에서 렌더링하여 완성된 HTML 파일을 로드해 준다..
> 클라이언트에서 요청할 때마다 각 상황에 맞는 HTML 파일을 넘겨주기 때문에 페이지가 여러 가지일 수밖에 없습니다.

### **👍 장점**

- 초기 로딩 속도가 빠르기 때문에 사용자가 컨텐츠를 빨리 볼 수 있다.
- JS를 이용한 렌더링이 아니기 때문에 검색엔진 최적화가 가능하다.

### **👎 단점**

- 매번 페이지를 요청할 때마다 새로고침 되기 때문에 사용자 경험이 SPA에 비해서 좋지 않다.
- 서버에 매번 요청을 하기 때문에 서버의 부하가 커진다.

---

## SPA에서 SSR을 할 수는 없을까?

SPA에서 SSR을 사용하여 두개의 장점을 얻을 순 없을까? `React를 예시로 설명해보자.`

질문에 대답은 서버와 클라이언트가 둘 다 Node.js 사용한다면 가능하다고 한다.

> 이를 Isomorphic Javascript 라고 합니다.
> Isomrhpic Javascript는 서버와 클라이언트에서 동일한 코드가 동작한다는 의미입니다.
> 즉, React에서 사용되는 코드들이 Node.js 서버 환경에서도 실행할 수 있다는 말입니다.

첫 번째는 React가 제공하는 ReactDOMServer 코드를 사용하여 SSR을 구현할 수 있다.
Node.js 서버에서 ReactDOMServer를 사용하여 SSR을 구현할 수 있다고 한다. (써보진 않았다..)

두 번째는 Next.js와 같은 React SSR 라이브러리를 사용하여 구현할 수 있다.
SPA에서 쉽게 SSR을 할 수 있는 라이브러리들이 있는데 이 라이브러리를 사용하여 구현하면 비교적 쉽게 SSR을 SPA에서 사용할 수 있다.

> EX) React + Next, Vue + Nuxt

---

[csr, ssr blog](https://hahahoho5915.tistory.com/52)
[csr, ssr velog](https://velog.io/@vagabondms/%EA%B8%B0%EC%88%A0-%EC%8A%A4%ED%84%B0%EB%94%94-SSR%EA%B3%BC-CSR%EC%9D%98-%EC%B0%A8%EC%9D%B4)
