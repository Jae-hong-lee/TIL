# Electron, Nextron 이란?

### 1. Electron 이란?

> Electron은 JavaScript, HTML 및 CSS를 사용하여 데스크탑 애플리케이션을 구축하기 위한 프레임워크입니다. Chromium 과 Node.js를 바이너리에 내장함으로써 Electron을 사용하면 하나의 JavaScript 코드베이스를 유지하고 Windows, macOS 및 Linux에서 작동하는 크로스 플랫폼 앱을 만들 수 있습니다.

Node.js를 기반으로 javascript를 사용하여 **데스크탑 App을 만드는 플랫폼**

노드를 사용하여 운영 시스템과 통신하여 브라우저 창이 데스크톱 앱처럼 느껴지게 만들 수 있다.
메뉴바를 만들거나 바로가기 단축키를 만들 수 있고, 파일 시스템과 사용자의 디스크에 저장시킬 수도 있다.
그외에도 알람, 진행율, 스크린샷 촬영등의 부가기능도 구현가능

쉽게 말해 Electron은 자바스크립트를 사용하여 조작하는 작은 Chromium 브라우저

#### 일렉트론은 어떻게 동작할까?

<img width="700" src = "https://tech.kakao.com/storage/2022/01/03-18.png">

일렉트론에는 두 가지(메인, 렌더러)의 프로세스가 존재한다.
일렉트론 앱은 단 하나의 메인 프로세스를 가지는데, 메인 프로세스는 `Node.js` 기반으로 동작하며 `main` 프로세스에서는 이러한 `renderer` 프로세스들을 관리하고, **각각의 렌더러 프로세스는 서로 독립적으로 동작한다**

위에서 언급한 메인 프로세스와 렌더러 프로세스 간에 통신이 이뤄져야 하는데, 일렉트론에서는 이를 `ipcMain`과 `ipcRenderer`와 같은 `IPC` 모듈을 통해 프로세스 간의 통신을 이루고 있다.

> ##### IPC(Inter Procress Communication) ??
>
> IPC는 electron에서 사용되는 프로세스간 통신 기술 모듈이다.
> Electron은 IPC를 사용하여 Main 프로세스와 Renderer 프로세스 간에 직렬화 된 JSON 메시지는 동기적 또는 비동기적으로 통신한다.
> ex) React 에서의 props 개념?

#### IPC 모듈 (추가하기)

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
