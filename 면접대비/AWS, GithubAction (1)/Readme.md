# AWS + GithubAction (1)

[개인 포트폴리오](https://github.com/Jae-hong-lee/LJH_Portfolio) 작업을 진행하면서 CI/CD 라는 개념을 공부할 수 있었는데 **AWS S3 + GithubAction + React** 을 통한 자동배포를 진행해 보고 싶었다.

## GitHub Action 과 AWS S3 을 연동하여 CI/CD 파이프라인 구축

![CI/CD 파이프라인](https://velog.velcdn.com/images%2Fbluestragglr%2Fpost%2F23738899-ac84-43b3-b0ca-78881e12fd5b%2FUntitled.png)

React 프로젝트를 생성하고, Github Action을 통하여 Git에 소스 push를 하면 AWS S3에 자동 배포하는 과정을 진행해 보았다.

1. Repository 에 기존 방식대로 master 브랜치에는 자동으로 배포 될 버전들을 merge
2. Github Acions 가 master 브랜치를 실시간으로 감시하며, push가 일어난 경우에 자동 배포 프로세스를 수행한다.
3. AWS S3 가 Github Action 으로 부터 빌드된 파일을 업로드 받는다.

<!-- > - Github Actions 기본 구성, 빌드하는 과정
> - AWS S3 버킷 정적 사이트 설정
> - AWS CLI를 이용하여 S3에 정적 사이트 배포하기 -->

## AWS 란?

: AWS(Amazon Web Service)는 **클라우드 컴퓨팅 서비스를 제공**하는 프로바이더 중 하나로서, 현재 전 세계에서 가장 많이 사용되고 있는 클라우드 컴퓨팅 서비스입니다.
AWS는 단순 컴퓨팅 자원을 제공해주는 것 뿐만 아니라 *이를 편리하게 관리할 수 있는 서비스, 서버리스 서비스 등 수많은 서비스를 확장성, 안정성, 높은 보안수준과 함께 제공*해준다.

## AWS S3 ??

![AWS S3](https://velog.velcdn.com/images%2Fopenhub%2Fpost%2Ff3715a47-0bad-41ee-85cf-d303ccfb8fbc%2FAWS-S3.png)

:**Simple Storage Service** (단위 : 1 Bucket)
Storage 라는 표현 그대로 특정한 파일을 저장하고 인터넷상으로 접근할 수 있게 해주는 서비스 (**무제한 용량을 제공하는 온라인 스토리지 서비스**),
데이터를 객체 형태로 저장하여 관리하며, 비용이 매우 저렴.

보통 서비스에 필요한 이미지나 파일등을 저장해두는 용도로 사용하지만 정적인 파일들을 안정적으로 제공할 수 있다는 점을 이용해서 **정적 웹사이트 호스팅**에도 사용할 수 있다.

무료 사용량 없이 실제 사용량(저장한 데이터 크기 및 트래픽)에 따라 비용이 부과되도록 과금 테이블이 설정되어 있지만, 테라바이트 이상의 데이터가 저장되어야 달러 단위의 비용이 발생 (50TB까지 매월 1GB당 USD 0.025(약 KRW 32.25) 과금)

> `Create React App`을 이용해서 만든 리액트 프로젝트의 경우 `next build && next export` 를 실행하면 정적인 `build` 파일들이 생성되고 이를 브라우저에서 접근해서 실행하면 `Client Side Rendering`을 통해서 동작하는 특징을 이용해서 S3서비스를 통해서 **정적 웹사이트 호스팅** 배포해볼 것임

## Github Action ?

![Github Action](https://velog.velcdn.com/post-images/adam2/8b3e4c00-0954-11ea-ab67-a9752cf94e84/Untitled-1.png)

:GitHub Actions는 GitHub에서 제공하는 **클라우드형 CI/CD 툴**
GitHub에서 자체적으로 제공하기에 GitHub 레파지토리와의 연동이 쉽고, 레파지토리 안에서 CI/CD까지 함께 구축하고 관리할 수 있다.

깃허브 저장소 내에서 코드 프로젝트를 빌드, 테스트, 패키지, 릴리스 또는 배포하기 위해 설정할 수있는 사용자 지정 자동화 프로세스이고, 추가적인 ci/cd툴을 사용하지 않고 깃허브 하나로 버전관리부터 테스트 배포까지 가능한 점이 장점이 있다.

### GithubAction 주요개념

![깃액션 주요개념](https://velog.velcdn.com/post-images%2Fadam2%2F06b3b720-0956-11ea-bf97-2f7ed907881b%2Fimage.png)

#### Workflow

프로젝트를 빌드, 테스트, 패키지, 릴리스 또는 배포하기 위한 전체적인 프로세스, `YAML`형식의 파일을 통해서 `Workflow`를 설정할 수 있다.
레파지토리 안에서 발생하는 event나 예약된 스케줄에 의해서 실행될 수 있다.
(워크플로우는 여러 개의 Job으로 구성되며 event(on)에 의해 실행)

GitHub에게 나만의 동작을 정의한 workflow file를 만들어 전달하면 GitHub Actions이 그것을 보고 그대로 실행 시킴

#### Event

워크플로우를 실행시키는 조건을 설정

> 예를 들어 해당 레포지토리에 Code가 push 됐을때만, 또는 풀리퀘했을 때, 또는 master branch에 변경사항이 있었을 때 등

레파지토리에서 발생하는 push, pull request open, issue open등의 특정한 활동을 의미.
GitHub Actions에서는 특정한 Event가 발생했을 시 그에 맞는 CI/CD 파이프라인을 구동하도록 설정할 수 있습니다.

> ~~cron 처럼 주기적으로 스케쥴링하는 방법 또한 지원~~

#### Step

Step은 순차적으로 명령어를 수행
Task들의 집합으로, 커맨드를 날리거나 action을 실행할 수 있다.

크게 `Uses`와 `Run`으로 작업 단위가 나뉘는데, `Uses`는 이미 다른 사람들이 정의한 명령어를 가져와 실행하는 것이고, `Run`은 npm install나 mkdir example과 같이 가상환경 내에서 실행할 수 있는 스크립트를 말함

#### Jobs

step은 실행가능한 하나의 shell script 또는 action을 의미
하나의 runner에서 실행될 여러 step의 모음을 의미, job안의 step들은 순차적으로 실행된다.
하위에 여러개의 job을 선언할 수 있고 기본적으로 job들은 병렬로 실행이 된다.

> 일부 job의 경우에는 다른 job에 의존성을 설정해서 다른 job이 완료되고 난 뒤 실행하도록 할 수 있다.

#### Actions

Workflow의 가장 작은 블록이고, Job을 만들기 위해 Step을 연결할 수 있다.
(GitHub Workflow에서 자주 사용되는 기능들을 모아둔 일종의 커스텀 애플리케이션, 재사용 가능한 컴포넌트)

개인적으로 만든 Action도 사용할 수 있고, Github Marketplace에서 Action들을 검색하고 활용할 수 있다.(공용 Action)

#### Runner

workflow를 실행할 서버를 의미, GitHub Actions는 직접 컴퓨터를 관리할 필요 없이 가상의 Runner를 통해서 Workflow를 실행시켜준다.

> Runner는 기본적으로 Node 16 version을 탑재
> Github-hosted runner : vCPU 2, 메모리 7GB, 임시 스토리지 14GB

---

[github Action이란?](https://velog.io/@adam2/Github%EC%97%90-Action%EC%9D%B4%EB%9D%BC%EB%8A%94-%ED%83%AD%EC%9D%B4-%EC%83%9D%EA%B2%BC%EB%8B%A4..-github-Action%EC%9D%B4%EB%9E%80-3gk336pk8q)
