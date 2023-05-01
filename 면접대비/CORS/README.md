# CORS

서로 다른 출처 접근

> ⭐️ Cross-Origin Resource Sharing(CORS) 은 추가 HTTP 헤더를 사용하여 브라우저가 한 출처에서 실행중인 웹 애플리케이션에 선택된 액세스 권한을 부여하도록하는 메커니즘입니다. 다른 출처의 자원. 웹 응용 프로그램은 자체와 다른 출처 (도메인, 프로토콜 또는 포트)를 가진 리소스를 요청할 때 cross-origin HTTP 요청을 실행합니다.
> MDN

> 먼저 읽어보기 : [Same origin policy](https://github.com/Jae-hong-lee/TIL/tree/main/%EB%A9%B4%EC%A0%91%EB%8C%80%EB%B9%84/Same%20Origin%20Policy)

한 도메인의 웹 페이지가 다른 도메인에 HTTP 요청을 시도할 때 브라우저는 서버가 원본 간 요청을 허용하는지 확인하기 위해 CORS 실행 전 요청을 서버로 보냅니다. 그런 다음 서버는 요청이 허용되는지 여부를 나타내는 적절한 CORS 헤더로 응답합니다. 요청이 허용되면 브라우저는 실제 요청을 진행합니다. 그렇지 않은 경우 브라우저는 요청을 차단하고 오류를 반환합니다.

즉,`Same-Origin Policy`의 문제점을 해결하기 위한 정책인 만큼
CORS란 `Cross-Origin`, 출처가 다른 도메인에서의 요청이라도 서버 단에서 데이터 접근 권한을 허용하는 정책
이러한 상호작용은 다음과 같은 세 가지의 범주로 나누어진다.

## 동일 출처 정책에 대한 처리

![](https://user-images.githubusercontent.com/14002238/112746418-a39c1800-8fe9-11eb-8c98-afc0d95db147.png)

만약 웹 사이트 `http://test.com`에서 자바스크립트를 로딩 한 다음에 이 스크립트에서,
`http://api.my.com`을 호출한다면 **동일 출처 정책에 의해서 호출한다면 호출 에러가 발생**한다.

이를 해결하는 방법으로는 인프라 측면에서 *프록시를 사용하는 방법*이나 _JSONP와_ _CORS_(Cross Origin Resource Sharing)이라는 방법이 있다.

### 프록시를 이용하는 방법

> ![](https://velog.velcdn.com/post-images%2Fyejinh%2F8650aaa0-3111-11ea-9a27-fbf26c473eb5%2FProxy-Server.png) > **프록시란**??
> 다양한 이유로 (주로 보안의 문제로) 직접 통신하지 못하는 두 개의 컴퓨터 사이에서 서로 통신할 수 있도록 돕는 역할을 가리켜 프록시라 일컫는다.

_동일 출처 정책의 문제는 API 서버와 자바 스크립트가 호스팅 되는 서버의 URL이 달라서 문제가 발생하게 된다._
이를 앞단에 `Reverse Proxy`를 넣어서 전체 **URL이 같게 만들어주면 되는 것.**
이러한 구조가 되면, 자바 스크립트가 로딩된 사이트도 `mysite.com`이 되고 자바스크립트에서 호출하고자 하는 `API URL`도 `mysite.com`이 되기 때문에 동일 출처 정책에 위배되지 않는다.

이 방식은 간단하지만,*자사의 웹 사이트를 서비스하는 경우에만 가능*하다. 그래서 자사의 서비스용 API를 만드는 경우에는 괜찮지만, 파트나사나 일반 개발자에게 자바스크립트용 REST API를 공개하는 경우에는 적절하지 않다고 한다.

### 특정 사이트에 대한 접근 허용 방식

CROS 방식 중 이 방식은 가장 간단한 방식으로, **API 서버의 설정에서 모든 소스에서 들어오는 API 호출을 허용하는 것**이다.

이는 HTTP로 API를 호출하였을 때 HTTP에 요청에 응답을 주면서 HTTP 헤더에 `Request Origin(요청을 처리해 줄 수 있는 출처)`를 명시하는 방식이다.

`api.my.com`에서 응답 헤더에 다음과 같이 명시해주면 `test.com`에 의해서 로딩된 자바스크립트 클라이언트 요청에 대해서만 `api.my.com`이 요청해준다.

```http
Access-Controll-Allow-Origin: test.com
```

만약에 다음과 같이 `*`로 해주면 `Request Origin`에 **관계없이 사이트에서 로딩된 자바스크립트 요청에 대해서 처리**해준다.

```http
Access-Control-Allow-Origin: *
```

##### 서버측 응답에서 접근 권한을 주는 헤더를 추가해 cors 에러를 해결하는 방법.

```js
app.use((req, res, next) => {
  res.header("Access-Control-Allow-Origin", "*"); // 모든 도메인
  res.header("Access-Control-Allow-Origin", "https://example.com"); // 특정 도메인
});
```

### Pre-flight를 이용한 세세한 CORS 통제

<!-- 수정하기 -->

REST 리소스(URL)당 섬세한 CORS 통제가 필요한 경우에는 Pre-Flight 호출이라는 것을 이용할 수 있다.

이 방식은 REST 리소스를 호출하기 전에, 웹 브라우저가 HTTP OPTIONS 요청을 보내면 해당 REST 리소스에 대해서 가능한 CORS 정보를 보내준다.
(접근이 허용된 사이트, 접근이 허용된 메서드 등)

웹 브라우저에서는 XMLHttpRequest를 특정 URL로 요청하기 전에, HTTP OPTIONS를 호출한다. 그러면 서버는 해당 URL에 접근할 수 있는 Origin URL과 HTTP 메서드를 반환해준다. 이를 Pre-flight 호출이라고 하는데, 이 정보를 바탕으로 해당 URL에 XMLHttpRequest를 보낼 수 있다.

일반적으로는 교차 오리진 쓰기가 허용된다, 예를 들어 링크, 리디렉션 및 form 제출이 있다. 일부 HTTP 요청에서는 preflight 가 필요하다.

> 실제 요청 이전에 '이런 메소드와 헤더로 요청을 보낼 예정인데, 너희 서버 CORS 정책에서 허용하는 요청이니?' 라고 브라우저가 서버에게 미리 물어보는 절차라고 보면 쉽다.

---

![](https://user-images.githubusercontent.com/14002238/112240206-402d8580-8c8b-11eb-9c2c-849a644bfdd7.png)

preflight 요청이란 일반적인 요청과 다르게, 먼저 OPTIONS 메서드를 사용하여 HTTP 요청을 다른 출처(Origin) 리소스로 전송하여 실제 요청이 안전한지 확인하는 것이다.

교차 사이트 요청은 사용자 데이터에 영향을 미칠 수 있으므로 preflight을 사용한다.

서버는 이 URL에 대한 접근 권한을 반환한다, CORS 접근이 가능한 Origin 사이트를 반환하고 사용할 수 있는 메서드를 반환한다. 그리고 Pre-flight 호출은, Access-Control-Max-Age에 정의된 86400 초 동안 유효하다. (한번 Pre-flight 호출을 하고 나면, 이 시간 동안은 다시 Pre-flight 호출을 할 필요가 없다)

이러한 CORS 설정은 API 호출 코드에서 직접 구현할 수 도 있고 로드 밸런서 역할을 하는 HAProxy나 Nginx 같은 리버스 프록시 설정을 통해서 간단하게 처리할 수도 있다.

---

[개발자 이동욱 - SOP(Same-origin policy) 란 무엇일까?](https://dongwooklee96.github.io/post/2021/03/23/sopsame-origin-policy-%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%BC%EA%B9%8C.html)
