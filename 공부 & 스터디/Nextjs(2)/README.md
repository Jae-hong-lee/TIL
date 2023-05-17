# Next.js (2) - pre-reloading에 대하여

서버 사이드 렌더링 및 정적 사이트 생성은 결과가 클라이언트로 전송되기 전에 외부 데이터를 가져오고 리액트 컴포넌트를 HTML로 변환하기 때문에 Pre-Rendering이라고도 한다.

## 속도비교

**클라이언트 렌더링**
<img src = "https://user-images.githubusercontent.com/55486644/132998063-5c93d791-b762-43b0-850b-dcc546e37b88.gif" >

일반적인 리액트 애플리케이션에서 브라우저는 서버로부터 빈 HTML과 함께 UI를 구성하기 위한 자바스크립트 명령을 전달받는데, 초기 렌더링 작업이 사용자 장치에서 발생하므로 이를 클라이언트 사이드 렌더링이라고 한다.
<img src="https://nextjs.org/static/images/learn/foundations/client-side-rendering.png">

**서버 pre-rendering**

<img src ="https://user-images.githubusercontent.com/55486644/132998088-e0b9dfb4-b963-4b6f-b5bb-72a8a5927907.gif">
`깜빡임은 새로고침`

반면 Next.js는 기본적으로 모든 페이지를 미리 렌더링 합니다. Pre-rendering은 HTML이 사용자 장치에서 자바스크립트로 모든 것을 수행하는 대신 서버에서 미리 생성되는 것을 의미합니다.

실제로 이것은 완전히 클라이언트 사이드로 렌더링 된 앱의 경우, 렌더링 작업이 수행되는 동안 사용자에게 빈 페이지가 표시된다는 것을 의미
미리 렌더링된 앱과 비교했을 때, 미리 렌더링된 앱의 경우 사용자는 완성된 HTML을 볼 수 있다.

<img src="https://nextjs.org/static/images/learn/foundations/pre-rendering.png">

# SSG (Static Site Generation)

빌드시 HTML 파일을 **미리 렌더링**하는 것이 SSG 이다. 정적인 페이지에 주로 쓰인다. HTML 파일을 미리 생성하기 때문에 서버에서 매번 연산을 하지 않아도 될 뿐 아니라, 별다른 설정 없이 CDN 캐시 사용이 가능하기 때문에 SSR 보다도 훨씬 빠른 속도를 보여줌

<img width = "700" src="https://velog.velcdn.com/images/sj_dev_js/post/98270ee5-b532-493d-94b0-4fe0126ea219/image.jpeg">

SSG는 Next.js 의 기본적인 렌더링 방식이다.
하지만 백엔드 서버로부터 데이터를 받아서 렌더링하는 경우를 생각해보자. 페이지에서 `useEffect()`를 사용한다면 url을 통한 **페이지 접근시 SSG로는 데이터가 필요없는 부분만 구성하고, 데이터를 필요로하는 부분은 CSR로 렌더링하게 된다.**
페이지에서 데이터를 필요로 하는 부분이 클수록 사용자가 화면을 인식하는데 시간이 오래걸리며 이는 SSG의 장점을 전혀 활용하지 못하게 되는 것을 의미한다.

이는 Next.js 에서 제공하는 페이지 단위 함수 `getStaticProps()`를 통해 해결할 수 있었다.

## getStaticProps()

```js
export default function Home({ posts: products }) {
  // const [products, setProducts] = useState([]);

  // useEffect(() => {
  //   const getStoreData = async () => {
  //    const res = await fetch('https://.../posts')
  //    const posts = await res.json()
  //    setProducts(posts)
  //   };
  //   getStoreData();
  // }, []);

  return (
    <div>
      <Header />
      <ProductList products={products} />
    </div>
  );
}

// getStaticProps 함수는 server-side에서 build 타임에만 실행된다.
export async function getStaticProps() {
  // build 타임에 데이터를 받아온다.
  const res = await fetch("https://.../posts");
  const posts = await res.json();

  // { props: { posts } }를 반환함으로써,
  // Blog 컴포넌트는 빌드타임에 props 로 posts를 받을 수 있다.
  return {
    props: {
      posts,
    },
  };
}
```

보이는 바와 같이 `getStaticProps()`는 빌드타임에 서버로부터 데이터를 받아와 반환하고, `getStaticProps()`를 사용하는 페이지는 빌드타임에 `getStaticProps()` 로부터 props를 받아올 수 있다. 따라서 빌드타임에 데이터에 따른 화면 구성이 가능해진다.

> useEffect로 클라이언트사이드에서 데이터를 fetch했을 때 3초~4초정도 걸림
> 3.7s -> 0.8s로 70%이상 향상(인터넷에 따라 다를 수 있음, 시크릿모드에서 수행)

## getStaticPaths()

SSG의 동적라우팅을 사용하는 경우가 있다. (ex: hostname/users/[username])

동적라우팅을 사용하여 query에 들어오는 값은 빌드타임에는 알 수가 없다. 따라서 `getStaticPaths()`를 사용하여 동적라우팅으로 가능한 모든 path에 대한 정보를 `getStaticProps()`에 전달할 수 있다.

```js
function Post({ post }) {
  // post 렌더링 하는 코드
}

export async function getStaticPaths() {
  // API 호출을 통해 posts 에 대한 데이터를 가져온다.
  const res = await fetch("https://.../posts");
  const posts = await res.json();

  // paths 를 추출한다.
  const paths = posts.map((post) => ({
    params: { id: post.id },
  }));
  /*
	  [{params : {id : 1} }, {params : {id : 2} }]
  */

  return { paths, fallback: false };
}

export async function getStaticProps({ params }) {
  // getStaticProps로부터 params 를 받아 path 를 구성하고 데이터를 받아온다.
  const res = await fetch(`https://.../posts/${params.id}`);
  const post = await res.json();

  return { props: { post } };
}

export default Post;
```

#### 모든 페이지를 미리 만들 수는 없다 : fallback 옵션

fallback 옵션에 들어갈 수 있는 값은 true, false or blocking 이며,

fallback 값에 따라 path가 미리 정의되지 않은 경우 `getStaticProps()`의 동작이 달라진다.

- **false** : 404 페이지를 반환한다.
- **true** : 우선 페이지의 fallback 버전을 보여주고 `getStaticProps` 를 통해서 요청을 보내서 HTML 파일과 JSON 파일을 만든다. 만들어지면 브라우저는 JSON 파일을 받아서 렌더링한다.
  Next.js 는 만들어진 HTML 파일을 미리 렌더링된 페이지 목록에 추가한다. 이후 요청부터는 미리 렌더링된 HTML 파일을 반환한다.
- **blocking** : true 와 비슷하게 작동하나, 파일을 렌더링하는 동안 fallback 버전을 보여주는 것이 아니라 SSR 처럼 작동하여 HTML파일을 새로 만들어 반환한다. 이후 요청부터는 만들어진 HTML 파일을 반환한다.

**예제 인용**

> getStaticPaths() 를 통해 미리 만들어진 path가 아닌, 임의의 path가 url 로 들어온다면 어떻게 해야할까?
>
> - 당연히 404페이지를 띄워야한다는 생각이 들 수도 있다. 하지만 상품 상세페이지를 정적 페이지로 생성한다고 생각해보자. 쇼핑몰의 모든 상품에 대한 페이지를 미리 빌드하는 것은 아주 오랜 시간이 걸릴 수도 있는 일이다. 뿐만 아니라, 새로운 상품이 등록된다면 그 상품에 대한 상세페이지도 만들어야하는데 새로운 상품을 빌드 타임에 알 수는 없다. 따라서 미리 만들어놓지 않은 path에 대해서도 페이지를 보여줄 수 있어야한다. 이를 위해 getStaticPaths() 에는 fallback 옵션이 존재한다.

> true인 경우는 getStaticProps를 통해 해당 URL에 필요한 data를 요청
> 🤔 빌드시점에 20개 페이지가 생성되었고 이후에 사용자에 의해 21번째 글이 생겼다고 가정해보았을 때, false값이면 21번째 페이지접근시 404로 넘어갈 것이고 true면 21번째페이지에 해당하는 요청을 보내고 데이터가 있으면 이에 해당하는 html이 생성되는것인가?
> 💡**맞다. 빌드시점에 없는 파일을 요청했을 때 getStaticProps에서 해당하는 id로 데이터 fetch를 하고 새로운 html정적파일을 생성해놓는다.
> false로 두면 빌드시점에 없는 요청이면 무조건 404로 넘어가고 true면 요청을 한번 보내고 데이터가 있다면 html을 생성, 없다면 이에 대한 처리를 개발자가 해주어야한다.**

#### Fallback Page

`getStaticPaths()` 에서 `fallback : true` 이면 fallback 페이지를 우선 보여준다는데 도대체 fallback 페이지는 무엇일까?
**사실 fallback 페이지 또한 개발자가 만들어야한다.** `getStaticPaths()` 는 `getStaticProps()`로 하여금 page 에게 `isFallback: true` 를 줄 뿐이다.

페이지에서 useRouter()의 isFallback을 통해 조건을 분기하여 fallback 페이지를 보여줄 수 있다.

```js
// pages/posts/[id].js
import { useRouter } from "next/router";

function Post({ post }) {
  const router = useRouter();

  // 만약 페이지가 아직 생성되지 않았다면 getStaticProps()가 실행되는 동안
  // isFallback을 통해 조건을 분기하여 fallback(대체) 페이지를 보여줄 수 있다.
  if (router.isFallback) {
    return <div>Loading...</div>;
  }

  // post 렌더링 하는 코드 생략...
}

export async function getStaticPaths() {
  return {
    // `/posts/1`,`/posts/2`만 빌드타임에 생성된다.
    paths: [{ params: { id: "1" } }, { params: { id: "2" } }],
    // fallback 값을 true로 줌으로써 `/posts/3` 같은 추가 페이지를
    // 정적인 방식으로 생성할 수 있다.
    fallback: true,
  };
}

export async function getStaticProps({ params }) {
  const res = await fetch(`https://.../posts/${params.id}`);
  const post = await res.json();

  return {
    props: { post },
    revalidate: 1,
  };
}

export default Post;
```

# SSR (Server Side Rendering)

페이지에 대한 요청이 있을 때마다 (프론트)서버에서 페이지를 만들어 반환한다. 서버에서 매번 연산을 해야하며 캐시를 사용하는 것이 상대적으로 어렵기 때문에 SSG에 비해 느리다.
하지만 항상 최신의 정보를 보여주어야하는 경우, SSR를 사용하는 것이 좋다.

(프론트)서버에서 HTML 파일을 만들어서 보내기 때문에 CSR에 비해 사용자가 더 빠르게 화면을 인식할 수 있다. 하지만 이벤트 등 페이지의 동작을 위해서는 `hydrate`라는 과정을 통해서 JS 코드가 실행되어야한다.

<img src="https://velog.velcdn.com/images/sj_dev_js/post/383b55f4-b94d-4092-84e9-e9d95e6bea7d/image.jpeg">

> hydrate
> 서버에서 렌더링된 HTML 페이지와 JS 파일을 클라이언트에게 보내면, 클라이언트에서 HTML 파일에 JS 코드를 매칭 시키는 작업이다. 이벤트 등록이나 스타일 적용이 일어난다. SSR과 SSG 모두 Hydrate가 일어나는데, SSG의 경우 빌드타임에 query 가 없기 때문에 런타임에서 query에 대한 hydrate가 일어난다.

## getServerSideProps()

Next.js에서 SSR을 사용하기 위해서는 페이지에서 `getServerSideProps()` 를 통해 데이터를 받아와야한다.

빌드타임에만 실행되는 `getStaticProps()`와는 달리 `getServerSideProps()`는 페이지에 대한 요청이 있을 때마다 실행된다.

```js
function Page({ data }) {
  // 데이터를 통해 페이지 렌더링...
}

export async function getServerSideProps() {
  // 페이지를 요청할 때마다 매번 실행된다.
  // 데이터를 받아온다.
  const res = await fetch(`https://.../data`);
  const data = await res.json();

  // Pass data to the page via props
  return { props: { data } };
}

export default Page;
```

# CSR (Client Side Rendering)

Next.js 에서 CSR을 사용하는 경우는 두가지로 나누어볼 수 있다.

1. url을 입력을 통해 pre-rendering 된 페이지를 받고 `useEffect()`를 통해 데이터를 추가로 불러오는 경우
2. `<Link/>` 나 `router.push()`를 통해 페이지 전환이 일어나는 경우이다.

CSR을 권장하는 경우
~~( 페이지 전환시 CSR을 사용하는 것은 당연하므로 url 입력을 통해 페이지를 요청하는 경우를 이야기한다. )~~

만약 페이지를 pre-rendering 할 필요가 없거나, 데이터의 업데이트가 자주 일어난다면 CSR을 사용하는 것을 권장한다.
예를 들어 **유저 대시보드 페이지(마이페이지)는 해당 유저만을 위한 비밀 페이지이기 때문에 SEO가 필요하지 않으며 따라서 pre-rendering할 필요도 없다. 또한 데이터가 자주 변경되기때문에 CSR이 적합**하다.

<img src="https://velog.velcdn.com/images/sj_dev_js/post/4f5b216b-bcc8-46f8-a704-b6a310db61b8/image.jpeg">

---

# 페이지 전환시 getStaticProps와 getServerSideProps의 작동

### getStaticProps()

빌드타임에 getStaticProps()가 실행되면, HTML 뿐만 아니라 JSON 파일 또한 생성된다. 이 JSON 파일은 next/link 나 next/router를 통해 CSR 화면전환이 일어날 때 쓰인다. getStaticProps 를 사용하는 페이지로 이동할 때 이 JSON 파일을 받아와 페이지 컴포넌트의 props로 사용한다.

### getServerSideProps()

next/link 또는 next/router를 통해 페이지 전환 시 Next.js가 getServerSideProps를 실행하는 (프론트)서버에 API 요청을 보낸다.

---

[Nextjs.org](https://nextjs.org/learn/foundations/about-nextjs/what-is-nextjs)
[nextjs 번역](https://velog.io/@surim014/how-next.js-works#%EA%B0%9C%EB%B0%9C-%EB%B0%8F-%ED%94%84%EB%A1%9C%EB%8D%95%EC%85%98-%ED%99%98%EA%B2%BD)
[nextjs 활용](https://velog.io/@devstone/Next.js-100-%ED%99%9C%EC%9A%A9%ED%95%98%EA%B8%B0-feat.-initialProps-webpack-storybook)
