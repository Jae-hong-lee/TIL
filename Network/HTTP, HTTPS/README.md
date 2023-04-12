# HTTP, HTTPS

## HTTP

> HyperText Transfer Protocol

즉, Hypertext인 HTML을 전송하기 위한 통신 규약을 의미
인터넷을 작동시키는 역할을 하며, 웹 서버 및 웹 브라우저 상호 간의 데이터 전송을 위한 `응용계층` 프로토콜

정리하면 `클라이언트-서버` 구조에 사용되는 프로토콜

## HTTPS

> HyperText Transfer Protocol Secure

HTTP는 서버에서 클라이언트로 전송되는 정보가 암호화되지 않는 약점이 있었다.
HTTP에 **SSL**(보안 소켓 계층)을 사용함으로써 보안을 강화!!

표준 HTTP와 동일한 방식으로 작동하는데 서버와 주고받는 데이터가 암호화되기 때문에 웹사이트에 추가적인 보호를 제공.

즉, 서버와 브라우저 사이간 연결을 암호화하여 정보가 도난당하지 않게 하는 것

### 차이점

`HTTPS(https://)`는 `SSL(Secure Socket Layer)`인증서를 사용하는 `HTTP(http://)`이다.
**SSL**(또는 TLS) 인증서는 일반 `HTTP` 요청 및 응답을 암호화한다.
따라서 **HTTPS**는 `HTTP`보다 더 안전한 보안용 프로토콜이라고 할 수 있는 것.

HTTP와 HTTPS의 유일한 차이점은
HTTPS를 사용한 웹 페이지를 통해 전송되는 모든 데이터는 **추가적인 보안 계층**이 있다.
이를 **TLS(전송 계층 보안)** 프로토콜이라고 하는데 모든 유형의 데이터는 변경되거나 손상될 수 없는 HTTPS 사이트를 통해 전달되며 제3자로부터 보호된다.

> **동작순서**
> HTTP 동작 순서 : TCP → HTTP
> HTTPS 동작 순서 : TCP → SSL → HTTP

---

#### 읽어보기

[https 동작과정 및 key발급과정](https://mangkyu.tistory.com/98)
[https가 seo에 끼치는 영향](https://www.ascentkorea.com/difference-between-http-and-https/)
