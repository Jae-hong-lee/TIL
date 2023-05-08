# Same Origin Policy

: 동일 출처 정책

`SOP`는 한 `Origin`에서 로드된 문서 또는 스크립트가 다른 `Origin`의 리소스와 상호 작용할 수 있는 방법을 제한하는 중요한 보안 메커니즘이다.
보안을 위협하는 문서를 격리하여, 보안 위협으로부터 보호할 수 있다.

한마디로 말해서 웹 브라우저에서 동작하는 프로그램은 로딩된 위치에 있는 리소스만 접근 할 수 있다는 정책이다.

> 더쉽게, 동일 출처 정책은 웹 브라우저 보안을 위해 프로토콜, 호스트, 포트가 동일한 서버로만 (ajax)요청을 주고 받을 수 있도록 한 정책

### 출처(Origin)의 정의

#### 간단하게 알아보는 URL

![](https://velog.velcdn.com/images%2F0sunset0%2Fpost%2F5e75cb81-982a-44d6-bf7c-8c6b6c433c4b%2Fimage.png)

#### Ex)

같은 `Origin` 출처의 서버로만 요청을 주고 받을 수 있다는 것의 예를 보자.

> 여기서 origin이란 요청을 보낸 도메인을 의미

```
http://www.same-domain.com/ --> http://www.same-domain.com = same-origin
http://www.same-domain.com -/-> http://www.cross-domain.com = cross-origin
```

![SOP-url-ex](https://user-images.githubusercontent.com/14002238/112231662-78789800-8c7a-11eb-8897-048e15a11d65.png)

위의 표를 보면 `http://store.company.com/dir/page.html` 과 비교하여, 같은 Origin인지 아닌지를 나타내고 있음

두개의 URL이 존재할 때 프로토콜, 포트(지정된 경우), 호스트가 동일한 경우 두 URL의 Origin이 같다.

### MDN: Internet Explorer 예외사항

Internet Explorer는 동일 출처 정책에 두 가지 중요한 예외사항이 있다.

- 신뢰할 수 있는 사이트
  양쪽 도메인 모두가 높음 단계의 보안 수준을 가지고 있는 경우 동일 출처 제약을 적용하지 않습니다.
- 포트
  Internet Explorer는 동일 출처 정책 검사에 포트를 포함하지 않습니다. 따라서 `http://company.com:81/index.html` 과 `http://company.com/index.html`은 동일 출처로 간주하며, 제한을 두지 않는다.

# CORS

다음 TIL : [CORS](https://github.com/Jae-hong-lee/TIL/tree/main/%EB%A9%B4%EC%A0%91%EB%8C%80%EB%B9%84/CORS)

같은 도메인으로 요청을 보낸 것이 아니기 때문에 동일 출처 정책에 의해 어김없이 오류를 마주치게 된다.
보안을 위한다고 하지만 개발을 하다보면 외부 API를 사용하는 경우도 많고 클라이언트와 서버를 분리하여 개발하는 경우도 많아 해당 정책으로 인한 불편함과 함께 매우 빈번하게 겪고, 초보개발자들도 많이 만나게 되는 오류.

이러한 불편을 해결하기 위해 등장한 것 ➡️ **Cross-Origin Resource Sharing(CORS)** 정책

---

[MDN - SOP](https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy)
