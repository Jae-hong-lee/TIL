# Notion API

### 들어가면서..

포트폴리오 사이트를 만들던 중, 어김없이 DB는 Firebase를 사용할지 고민하고 있던 나에게 Notion API를 사용하는 포트폴리오 사이트가 눈에 들어왔다. 처음에는 Firebase를 사용하면 익숙하고 편리할 것이라 생각했지만, Notion API를 활용한 사이트를 보고 나니 흥미가 생겼다. Notion API를 사용하면 데이터 관리가 더 직관적이고 유연해질 수 있을 것 같았다. 특히, Notion의 강력한 데이터베이스 기능을 활용하면 포트폴리오의 콘텐츠를 더 쉽게 업데이트하고 관리할 수 있을 것 같다는 생각이 들었다(직관적이게). 그래서 나는 Firebase 대신 Notion API를 사용해보기로 했다.

> [Next.js 나만의 포트폴리오 사이트 만들기 (개발하는 김에 배포까지) / LottieFiles, Notion Api, TailwindCSS](https://youtu.be/KvoFvmu5eRo?si=Qj3Ie8134jyyacWG)

### Notion API 란?

노션 API가 개인 무료 사용자에게 오픈되면서, 노션을 마치 데이터베이스처럼 활용할 수 있는 다양한 기능이 제공되고 있다.
이를 통해 사용자는 데이터베이스를 조회하고, 삭제하거나, 수정할 수 있으며, 다양한 형태의 데이터를 효율적으로 관리할 수 있다.
예를 들어, 노션의 데이터베이스 기능을 활용하여 프로젝트 관리, 일정 관리, 그리고 개인적인 데이터 트래킹 등을 손쉽게 할 수 있다.
또한, 페이지 API를 사용하면 새로운 페이지를 생성하는 것은 물론, 이러한 페이지를 다른 사람들과 공유할 수도 있다.

### 사용하기

##### 1. 우선 노션 Developers 에서 내 integrations 를 확인해야한다.

> https://www.notion.so/

- `view my integrations` 에 들어가 내 계정으로 로그인하여 `내 API 통합`이라는 회먄이 뜬다면 `새 API 통합 만들기`버튼을 클릭
  <img width="938" alt="image" src="https://github.com/user-attachments/assets/c52af77a-0f9c-4356-b710-edd712ef3738">
  <img width="938" alt="image" src="https://github.com/user-attachments/assets/a101184a-8195-4e0e-8630-f33f3c91c515">

버튼을 클릭하면 기본정보를 작성하고 내가 해당 API 토큰에 부여할 권한들을 선택할 수 있다.
자격정보에 콘텐츠 읽기 , 업데이트, 입력, 댓글 읽기, 댓글 삽입 등의 권한을 부여할 수 있고, API 통합 유형은 ‘프라이빗 API 통합'을 선택하자

<img width="938" alt="image" src="https://github.com/user-attachments/assets/0c3977a0-5d39-4f6f-8d4a-674817a478f2">

**프라이빗 API 통합 시크릿 아래에 보면 API 토큰 키가 표시되는 것을 볼 수 있다. 해당 토큰 키를 잘 저장해두자!**

##### 2. DB 생성하고 API 통합

![image](https://github.com/user-attachments/assets/c88ef07d-c640-42b4-aad9-3e5c7a785b1b)
노션에 데이터베이스를 생성하자 (데이터베이스 인라인은 페이지 내에 데이터베이스를 생성, 전체 페이지는 새로운 페이지를 만드는 DB)

> 전체 페이지 DB 를 만들었다.

<img width="686" alt="image" src="https://github.com/user-attachments/assets/cdcd0687-41ed-41f3-bdd7-cea49f6f28b6">

내가 만들었던 포트폴리오들을 잘 정리해서 DB에 저장해두었다.
DB에서 새탭으로 열기를 눌러 열린 탭을 본다면 `https://www.notion.so/` 뒤쪽에 query가 곧 내 DB의 ID 이다.

이렇게 **노션 API 키 값**과 **DB의 ID**값만 있다면 노션 API 를 자유롭게 이용할 수 있게 된다.
POST, GET, PATCH 요청등 다양한 요청들은 [Notion API reference](https://developers.notion.com/reference) 부분에서 찾아볼 수 있다.

> 노션 공식 API Reference 에 가면 여러 예시를 볼 수 있으니 우린 DB ID 만 잘 기억해주자.
> EX - [POST 요청](https://developers.notion.com/reference/post-page) 3.

### 내 코드 적용

난 내 사이트에 보여주기만 하면 되기 때문에 Read 권한만 부여했고, POST 요청을 통해 데이터를 가져와서 필요한 페이지에 전달할 수 있었다.

```js
// pages 폴더에서 getStaticProps 사용하기,
// 폴더에서 사용 안되는 이유: pages 폴더는 router를 담당하는 곳,
// 폴더 혹은 파일 이름만 지정해서 함수를 만들면 그에 해당하는 router가 자동으로 지정됩니다.

export async function getStaticProps() {
  const options = {
    method: "POST",
    headers: {
      Accept: "application/json",
      "Notion-Version": "2022-02-22",
      "Content-Type": "application/json",
      Authorization: `Bearer ${TOKEN}`,
    },
    // 이름순 정렬, created_time 으로 시간순 정렬 가능
    body: JSON.stringify({
      sorts: [
        {
          property: "진행기간",
          direction: "ascending",
        },
      ],
      page_size: 100,
    }),
  };

  const res = await fetch(
    `https://api.notion.com/v1/databases/${DATABASE_ID}/query`,
    options
  );

  const projects = await res.json();

  return {
    props: { projects },
    // fallback: true,
    // getStaticProps() 메소드를 사용한다면 revalidate 로 데이터 변경시 갱신가능!
    revalidate: 1, // 데이터 변경이 있으면 갱신 1초 마다
  };
}
```

`TOKEN` = 노션 API 키값
`DATABASE_ID` = 노션에 내가 만든 DB ID

---

마지막으로 내 포트폴리오에 적용된 화면.
<img width="763" alt="image" src="https://github.com/user-attachments/assets/449c8543-46d3-4b1b-944d-5270896cb49e">

> - [노션 전체 DB 였던 것](#2-db-생성하고-api-통합)

---

[노션 공식](https://www.notion.so/ko-kr/help)
[개발하는 정대리](https://youtu.be/KvoFvmu5eRo?si=Qj3Ie8134jyyacWG)
