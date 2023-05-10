# REST API

## **REST란 무엇인가?**

REpresentational State Transfer의 약자로 전반적인 웹 어플리케이션에서 상호작용하는데 사용되는 웹 아키텍쳐 모델이다. 즉, 자원을 주고받는 **웹 상에서의 통신 체계에 있어서 범용적인 스타일을 규정한 아키텍쳐** 라고 할 수 있다.

## **API란 무엇인가?**

Application Programming Interface의 약자로 구글 맵 API, 카카오 비전 API 등 **기존에 있는 응용 프로그램을 통해서 데이터를 제공받거나 기능을 사용하고자 할 때 사용하는 인터페이스 및 규격** 을 말한다. API는 프로그래밍 언어, 운영체제 등에서도 사용되는 범용적인 용어이다. 따라서, REST API라는 것은 REST 원칙을 적용하여 서비스 API를 설계한 것을 말하며 대부분의 서비스가 REST API를 제공한다.

## RESTful API를 위한 HTTP Methods

#### 1. GET

GET를 통해 해당 리소스를 조회, 리소스를 조회하고 해당 도큐먼트에 대한 자세한 정보를 가져온다.

어떠한 방식으로도 자원의 상태를 변경시키지 않으므로, safe method라고 불리기도 한다.
GET API는 멱등성의 띈다. 멱등성이란, 동일한 API를 여러번 호출시에도 동일한 결과를 얻을 수 있음을 의미한다.(POST, PUT을 통해 데이터가 변경되지 않는다면)

#### 2. POST

새로운 자원을 추가할 때 사용한다.

이는 서버의 상태를 변경시키며, 때문에 비멱등성 성질을 가진다. 응답 코드로 201(Created)를 받아야 정상적으로 서버에 추가 되었음을 확인할 수 있다.

#### 3. PUT

존재하는 자원을 변경 할 때 사용한다.

만약 자원이 존재하지 않는 경우, API는 새로운 자원을 생성하도록 할 수 있다. 이 경우 응답코드는 201(Created)를 받게 된다.
존재하는 자원을 변경시킨 경우 200(OK) 또는 204(No Content) 응답코드를 받게 된다.

> **POST** VS **PUT**
> POST는 _여러개의 자원_ 에 수행되는 반면, PUT은 _단일 자원_ 에만 수행된다.
> <img width="500" alt="스크린샷 2023-05-08 오후 8 28 45" src="https://user-images.githubusercontent.com/72030487/236812738-5c49346d-f315-48d7-8423-f19aaa4a3ac9.png">
> <img width="500" alt="스크린샷 2023-05-08 오후 8 30 22" src="https://user-images.githubusercontent.com/72030487/236812876-c7569e24-190d-4a15-990f-98f505ea5ca7.png">

> [출처: 얄코 rest api](https://www.yalco.kr/23_rest_api/)

#### 4. DELETE

자원을 삭제할 때 사용한다.

DELETE 메소드는 멱등성 성질을 띈다. DELETE 메소드를 요청하면 자원을 삭제하게 되는데, 반복적으로 DELETE를 호출한 경우 결과는 바뀌지 않는다. 하지만 이미 제거되었으므로404(NOT FOUND)를 반환받는다.

#### 5. PATCH

한 자원의 데이터를 부분적으로 변경할 때 사용한다.

PUT도 마찬가지로 자원을 변경할 수 있다. 하지만 조금더 명확하게는 존재하는 자원에 대해 부분적으로 업데이트를 위해서는 PATCH를 사용한다. PUT은 자원을 완전히 대체하는 경우 사용한다.

> PATCH의 경우 모든 브라우저, 서버, 앱 어플리케이션 프레임워크에서 사용할 수 있는 것은 아니다.

---

[얄코 rest api](https://www.yalco.kr/23_rest_api/)
[REST_API - velog](https://velog.io/@ellyheetov/REST-API)
