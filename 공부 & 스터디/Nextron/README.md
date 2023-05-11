# Electron, Nextron 이란?

### 1. Electron 이란?

> Electron은 JavaScript, HTML 및 CSS를 사용하여 데스크탑 애플리케이션을 구축하기 위한 프레임워크입니다. Chromium 과 Node.js를 바이너리에 내장함으로써 Electron을 사용하면 하나의 JavaScript 코드베이스를 유지하고 Windows, macOS 및 Linux에서 작동하는 크로스 플랫폼 앱을 만들 수 있습니다.

Node.js를 기반으로 javascript를 사용하여 **데스크탑 App을 만드는 플랫폼**

노드를 사용하여 운영 시스템과 통신하여 브라우저 창이 데스크톱 앱처럼 느껴지게 만들 수 있다.
메뉴바를 만들거나 바로가기 단축키를 만들 수 있고, 파일 시스템과 사용자의 디스크에 저장시킬 수도 있다.
그외에도 알람, 진행율, 스크린샷 촬영등의 부가기능도 구현가능

쉽게 말해 Electron은 자바스크립트를 사용하여 조작하는 작은 Chromium 브라우저

#### 일렉트론은 어떻게 동작할까?

<img width="700" alt = "일렉트론 동작" src = "https://tech.kakao.com/storage/2022/01/03-18.png">

일렉트론에는 두 가지(메인, 렌더러)의 프로세스가 존재한다.
일렉트론 앱은 단 하나의 메인 프로세스를 가지는데, 메인 프로세스는 `Node.js` 기반으로 동작하며 `main` 프로세스에서는 이러한 `renderer` 프로세스들을 관리하고, **각각의 렌더러 프로세스는 서로 독립적으로 동작한다**

위에서 언급한 메인 프로세스와 렌더러 프로세스 간에 통신이 이뤄져야 하는데, 일렉트론에서는 이를 `ipcMain`과 `ipcRenderer`와 같은 `IPC` 모듈을 통해 프로세스 간의 통신을 이루고 있다.

> ##### IPC(Inter Procress Communication) ??
>
> IPC는 electron에서 사용되는 프로세스간 통신 기술 모듈이다.
> Electron은 IPC를 사용하여 Main 프로세스와 Renderer 프로세스 간에 직렬화 된 JSON 메시지는 동기적 또는 비동기적으로 통신한다.
> ex) React 에서의 props 개념?

**Main Processor**
Main 프로세스는 일반적으로 우리가 Electron으로 개발할 때 생성하는 main.js로 볼 수 있다.
이는 Electron 앱의 진입점이기도 하며, 앱의 Life Cycle을 관리한다. 뿐만 아니라 애플리케이션에서 사용되는 메뉴, Dock, 트레이와 같은 네이티브 요소를 관리하며 Main 프로세스는 앱에서 *각각의 새로운 Renderer 프로세스*를 생성한다.

**Renderer Processor**
Renderer 프로세스는 Electron 앱의 Chromium 기반의 브라우저 창을 관리하는 모듈이다.
Main 프로세스와 달리 Renderer 프로세스는 여러 개가 존재할 수 있으며, Main 프로세스와는 1:N의 관계를 맺는다.

#### IPC 모듈

<img width = "700" alt = "IPC 모듈 동작" src = "https://velog.velcdn.com/images%2Fminidoo%2Fpost%2F2d100a64-1056-4c80-9f78-ffd80cc7c344%2Fimage.png">

**ipcMain**
_ipcMan 모듈은 Renderer 프로세스(웹 페이지)가 보내는 메시지 또는 이벤트를 동기적 혹은 비동기적으로 처리한다._
ipcMain에서는 IPC 통신을 할 때 수신만 할 수 있다.

> 이벤트 수신은 on이며 송신은 send라 하였지만, ipcMain에서는 on으로 송신을 하며 send가 아닌 reply로 회신하는 것이다.

**ipcRenderer**
ipcRenderer 모듈은 Renderer 프로세스(웹 페이지)에서 Main 프로세스로 동기 또는 비동기 메시지를 보낼 수 있다.

> Main 프로세스에서 webContents.send 로 메시지를 보냈다면 수신 역시 가능하다.

보통 웹 페이지에서 HTTP 통신을 통해 받아온 데이터를 보내거나 Main 프로세스를 호출할 경우 사용되며, send를 통해 송신하고 on을 통해 수신한다.

#### IPC 예제

**ipcMain에서 on을 통한 이벤트 수신**

```js
const { ipcMain } = require("electron");

// ipcMain에서의 이벤트 수신
ipcMain.on("CHANNEL_NAME", (evt, payload) => {
  console.log(payload);

  evt.reply("IPC_RENDERER_CHANNEL_NAME", "message");
});
```

위 예제에서 `CHANNEL_NAME`은 메시지를 송신할 이름을 말하며 `channel`이라고 한다. 어느 ipcRenderer 프로세스에서 `CHANNEL_NAME`으로 메시지를 수신하였고 ipMain 프로세스는 `CHANNEL_NAME`로 송신하였다. 그리고 다시 `reply`를 통하여 송신 후 다시 응답하였다.

이 응답의 `channel` 이름은 **IPC_RENDERER_CHANNEL_NAME**이다. 이렇게 응답을 하였기에 어느 `ipcRenderer` 프로세서는 `on`을 통하여 **IPC_RENDERER_CHANNEL_NAME** 채널로 수신할 것이다.

기능 측면에서 send의 개념도 가능하지만 `ipcMain` 프로세스는 `ipcRenderer`와 같은 `send` 메소드를 가지고 있진 않다. _send를 하기 위해서는 **webContents.send**를 이용해야 한다._

```js
const { app, BrowserWindow } = require("electron");

app.whenReady().then(() => {
  const win = new BrowserWindow({
    // options
  });

  win.webContents.send("IPC_RENDERER_CHANNEL_NAME", "message");
});
```

`ipcMain`이 될 수 있는 `main.js`에서 **IPC_RENDERER_CHANNEL_NAME** 채널명으로 수신하고 있다. 이는 어느 `ipcRenderer`에서 **IPC_RENDERER_CHANNEL_NAME** 채널명으로 송신을 받고 이후의 처리를 할 수 있을 것이다.

이렇게 되면 `ipcMain`에서도 `ipcRenderer`와 동일하게 메시지를 송신할 수 있다.

---

**ipcRenderer에서 send을 통한 이벤트 송신**

```js
const { ipcRenderer } = require("electron");

const payload = "message";

// ipcRenderer에서의 이벤트 송신
ipcRenderer.send("CHANNEL_NAME", payload);

// ipcRenderer에서의 이벤트 수신
ipcRenderer.send("CHANNEL_NAME", (evt, payload) => {
  console.log(payload);
});
```

### 2. Nextron 이란 ?

> Nextron = Electron + Next.js

Server Side Rendering(**SSR**)을 지원하는 Next.js와 Electron이라는 개발 플랫폼

### 3. Nextron 폴더구조

<img width= "250" alt="Nextron 폴더구조" src="https://velog.velcdn.com/images%2Fminidoo%2Fpost%2Fc10d75b2-ac1c-4005-8b97-a3fa3ea73dec%2Fimage.png">

**main** : 서버에서 실행되는 파일을 모아놓은 프로세스
**renderer** : 클라이언트에서 실행되는 파일을 모아놓은 프로세스
**public** : 이미지, 영상 등 Static한 파일이 저장되는 폴더

`Electron`은 `main`과 `renderer` 두 개의 프로세스에 의해 동작된다.
`main`은 데이터베이스 통신, 파일 처리 등 서버에서 실행되는 파일 (server)
`renderer`은 실제 화면에 그려지는 page 파일로 구성 (client)

---

[Electron 공홈](https://www.electronjs.org/)
[Electron 번역](https://tinydew4.github.io/electron-ko)
[electrionforge - Deploy](https://www.electronforge.io)
[next + electron git](https://github.com/saltyshiomix/nextron)
[kakao](https://tech.kakao.com/2021/08/17/frontend-growth-11/)
[electron api - 알림기능](https://velog.io/@lumpenop/TIL-nextron.js-3-Electron-APIs-building-demo-app-220716)

**블로그**
[Electron에 Next.js까지?](https://velog.io/@minidoo/Electron)
[Nextron - velog](https://velog.io/@dngur9801/Nextron-Nextron-%EC%84%A4%EC%B9%98-%EB%B0%8F-%EC%8B%A4%ED%96%89%ED%95%98%EA%B8%B0-with-typescript)

[Electron 유튜브 영상 - 니콜라스](https://youtu.be/6Ep8ot0ABH0)

> 챗앱 만들어보장 + todo 까지 추가하기
> [Example](https://github.com/hyejineee/chat-desktop-app)
