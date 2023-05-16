# Next.js

<img width= "700" alt="nextjs logo" src="https://velog.velcdn.com/images/khy226/post/814c11ee-0626-4947-90a2-3be78a1bb92d/image.jpeg">

Next.js는 React 라이브러리의 프레임워크이다.
Next.js를 사용하는 가장 큰 이유로 SEO(Search Engine Optimization)를 위한 Server-Side Rendering(SSR)을 가능하게 하기 때문에 라고한다.

간단하게 살펴보자면 (`많은 블로그들이 다루는 내용`)

<img width ="700" alt="csr" src="https://velog.velcdn.com/images%2Fsyoung125%2Fpost%2F2705ccb4-0834-4b8d-925b-f47521eeba38%2Fimage.png">

기본적으로 React는 Client Side Rendering(**CSR**)을 한다. 이는 위 그림처럼 웹사이트를 요청했을 때 빈 html을 가져와 script를 로딩하기 때문에, 첫 로딩 시간도 오래걸리고 Search Engine Optimization(**SEO**)에 취약하다는 단점이 있다.

<img width="700" alt="ssr" src="https://velog.velcdn.com/images%2Fsyoung125%2Fpost%2Fd9d59bb4-73fb-4f4f-a6b5-22ceaa06141a%2Fimage.png">

반면, next.js는 pre-reloading을 통해 미리 데이터가 렌더링된 페이지를 가져올수 있게 해주므로 사용자에게 더 좋은 경험을 주고, 검색 엔진에 잘 노출 될 수 있도록 해주는 SEO에서도 장점을 얻을 수 있다.
pre-reloading은 SSR 뿐만 아니라 정적 사이트 생성(Static-Site Generate (**SSG**))도 가능하게 해준다. 또, SSR과 CSR도 혼합하여 사용 가능하다는 장점이 있음.

## Next.js 특징 정리

**1. 사전 렌더링 및 서버사이드 렌더링**
서버 사이드 렌더링 기능을 제공하여 클라이언트 사이드 렌더링 환경보다 빠른 렌더링을 불러올 수 있다.
**2. Hot Code Reloading을 지원**
Next 개발 환경에서는 코드 변경 사항이 저장되면 응용 프로그램을 자동으로 다시 로드한다.
**3. 자동 코드 분할**
자동 코드 분할 기능 덕분에 코드의 모든 가져오기각 번들로 묶여 각 페이지와 함께 제공된다.
결과적으로, 불필요한 코드가 페이지에 로드 되지 않는다.
**4. 추가 설정이 필요 없음**
Next.js는 기본적으로 웹팩과 바벨을 사용하고 있다. 따라서 이미 서버 사이드 렌더링 및 개발에 필요한 설정이 되어 있으므로 빠르게 개발을 시작할 수 있다.
사용하고 싶은 플러그인이 있다면 손쉽게 추가하여 사용할 수 있도록 지원을 하고 있다.
**5. 타입스크립트가 내장됨**
Next를 타입스크립트와 함께 사용할 때 타입 지원을 통해 편리함과 안정성을 얻을 수 있다.
Next는 타입스크립트 설정을 따로 할 필요 없이 사용할 수 있으며 타입스크립트를 지원하고 있다.
**6. 파일기반 내비게이션 기능**
리액트에서는 라우트를 위해서 react-dom-router 라이브러리를 사용하여 라우팅 설정을 해주어야 한다. 그로 인해 페이지의 경로에 대하여 직접 설정을 해줘야 한다.
하지만 Next.js 파일 시스템 기반 라우팅을 사용한다.
폴더의 경로에 따라 페이지의 경로가 설정되어 구축이 빠르고 관리가 편리하다는 장점이 있다.
(참고로, next.js는 next/link의 <Link /> 컴포넌트를 사용해서 클라이언트 사이드 네이게이션을 사용해야함)

## Next.js 작동 방식

1. Next.js로 만들어진 웹 페이지는 url로 접근시 pre-rendering(SSG, SSR) 된 페이지를 반환한다.
2. pre-rendering 은 프론트엔드 서버에서 돌아가는 Next.js 에서 일어난다.
3. Next.js 는 SSG가 기본이다. getServerSideProps() 을 사용하지 않은 페이지는 모두 SSG로, 빌드 타임에 미리 렌더링 된다.
4. SSR을 하려면 getServerSideProps()를 사용해야한다.
5. Link를 클릭하거나 router.push()를 통해 이동하면 CSR 방식으로 페이지를 전환한다.

---

[Nextjs.org](https://nextjs.org/learn/foundations/about-nextjs/what-is-nextjs)
[nextjs 번역](https://velog.io/@surim014/how-next.js-works#%EA%B0%9C%EB%B0%9C-%EB%B0%8F-%ED%94%84%EB%A1%9C%EB%8D%95%EC%85%98-%ED%99%98%EA%B2%BD)
[nextjs 활용](https://velog.io/@devstone/Next.js-100-%ED%99%9C%EC%9A%A9%ED%95%98%EA%B8%B0-feat.-initialProps-webpack-storybook)
