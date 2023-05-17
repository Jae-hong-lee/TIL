# Next.js(3) - Link, Router 차이

페이지 전환시 getStaticProps와 getServerSideProps의 작동
getStaticProps()
빌드타임에 getStaticProps()가 실행되면, HTML 뿐만 아니라 JSON 파일 또한 생성된다. 이 JSON 파일은 next/link 나 next/router를 통해 CSR 화면전환이 일어날 때 쓰인다. getStaticProps 를 사용하는 페이지로 이동할 때 이 JSON 파일을 받아와 페이지 컴포넌트의 props로 사용한다.

getServerSideProps()
next/link 또는 next/router를 통해 페이지 전환 시 Next.js가 getServerSideProps를 실행하는 (프론트)서버에 API 요청을 보낸다.

---

[Link 와 router차이](https://velog.io/@dev_sony503/TIL-Next-JS-Router%EC%99%80-Link%EC%9D%98-%EC%B0%A8%EC%9D%B4)
[router 정리 nextjs](https://velog.io/@khy226/Next.js-Router-%EC%A0%95%EB%A6%AC)
