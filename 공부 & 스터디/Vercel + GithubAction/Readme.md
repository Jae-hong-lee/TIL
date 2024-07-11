# Vercel + GithubAction

### 왜 사용하게 됨?

GitHub Actions와 Vercel을 사용한 자동 배포 설정하기에 앞서, 왜 이 두 개를 이용해서 CI/CD를 구현하려 했는지 이야기하려고 한다.

이전 글에서 보듯이, 나는 포트폴리오 사이트를 만들어 AWS S3와 GitHub Actions를 통해 배포 작업을 자동화할 수 있었다.

하지만 AWS 프리 티어가 만료되어 서버 비용이 계속 발생하는 상황이었다. 비록 적은 비용이었지만, 무료로 배포할 수 있는 곳이 있는데 굳이 비용을 들일 필요가 있을까라는 생각이 들었다. 그래서 프로젝트를 다시 재배포할 방법을 알아본 결과, Vercel이라는 서비스를 찾을 수 있었다. 포트폴리오 사이트를 제작하면서 GitHub Actions를 사용하는 것이 메인이었기 때문에, 억지로라도 함께 사용할 방법을 찾게 되었다.

그 과정을 통해 내가 공부한 내용과 작성한 코드를 정리해 보려고 한다.

### 간단하게 Vercel이란?

먼저, Vercel에 대해 간단히 설명하겠습니다. Vercel은 정적 사이트와 서버리스 함수 배포에 최적화된 플랫폼으로, 특히 Next.js 프로젝트에 강력한 기능을 제공합니다. GitHub와 연동하여 푸시할 때마다 자동으로 배포할 수 있어 매우 편리합니다.

### 간단하게 GitHub Actions란?

GitHub Actions는 GitHub 리포지토리에 변경 사항이 있을 때마다 특정 작업을 자동으로 수행할 수 있도록 도와주는 CI/CD 도구입니다. 다양한 이벤트(예: 푸시, 풀 리퀘스트, 이슈 등)에 대해 작업을 정의하고, 이를 통해 코드를 빌드하고 테스트하며 배포할 수 있습니다.

> "Vercel 이 github 와 연동해서 푸시할떄마다 자동으로 배포해준다며??"
> 라는 생각이 여기서 들어버림..

### GitHub Actions와 Vercel을 사용한 배포 설정

```yaml
# Vercel 과 연결해서 배포하기 Test
name: GitHub Actions Vercel Preview Deployment (깃허브 액션과 Vercel 배포)
env:
  VERCEL_ORG_ID: ${{ secrets.VERCEL_ORG_ID }}
  VERCEL_PROJECT_ID: ${{ secrets.VERCEL_PROJECT_ID }}
on:
  push:
    branches: [master]
# master 브랜치에 푸시될 때 실행

jobs:
  Deploy-Preview:
    runs-on: ubuntu-latest
    # "Deploy-Preview"라는 작업이 최신 Ubuntu 환경에서 실행됩니다.

    steps:
      - uses: actions/checkout@v3
      # 리포지토리의 소스 코드를 체크아웃하는 단계입니다.

      - name: Install Vercel CLI (Vercel CLI 설치)
        run: npm install --global vercel@canary
      # Vercel CLI를 전역으로 설치하는 단계입니다.

      - name: Pull Vercel Environment Information (Vercel 원격 환경에서 환경 변수 및 프로젝트 설정을 가져오기)
        run: vercel pull --yes --environment=preview --token=${{ secrets.VERCEL_TOKEN }}
        env:
          NOTION_API_KEY: ${{ secrets.NOTION_API_KEY }}
          NOTION_DATABASE_ID: ${{ secrets.NOTION_DATABASE_ID }}
      # Vercel의 프리뷰 환경 정보를 가져오는 단계입니다.
      # 원격 환경에서 환경 변수와 프로젝트 설정을 가져옵니다.
      # 포트폴리오 사이트는 Notion API 도 사용하고 있기 때문에
      # 이 단계에서 NOTION_API_KEY와 NOTION_DATABASE_ID를 환경 변수로 설정합니다.

      - name: Build Project Artifacts (프로젝트 빌드)
        run: vercel build --token=${{ secrets.VERCEL_TOKEN }}
        env:
          NOTION_API_KEY: ${{ secrets.NOTION_API_KEY }}
          NOTION_DATABASE_ID: ${{ secrets.NOTION_DATABASE_ID }}
      # Vercel CLI를 사용하여 프로젝트 아티팩트를 빌드하는 단계입니다.
      # 빌드할 때 Notion API 키를 환경 변수로 설정합니다.

      - name: Deploy Project Artifacts to Vercel (프로젝트를 배포)
        run: vercel deploy --prebuilt --token=${{ secrets.VERCEL_TOKEN }}
      # 빌드된 아티팩트를 Vercel에 배포하는 단계입니다.
```

#### 설명

1. Workflow 시작: push 이벤트가 발생할 때 실행되며, master 브랜치에 푸시될 때 실행
2. 환경 변수 설정: `VERCEL_ORG_ID` 와 `VERCEL_PROJECT_ID` 를 GitHub Secrets에서 가져온다.
3. 작업 정의: `Deploy-Preview` 라는 작업이 최신 Ubuntu 환경에서 실행.
4. 소스 코드 체크아웃: 리포지토리의 소스 코드를 체크아웃.
5. Vercel CLI 설치: Vercel CLI를 전역으로 설치
6. Vercel 환경 정보 가져오기: Vercel의 프리뷰 환경 정보를 가져오기. 이는 원격 환경에서 환경 변수와 프로젝트 설정을 가져오기.

   > `Pull Vercel Environment Information`: Vercel의 프리뷰 환경 정보를 가져오는 단계입니다. 이 단계에서 `NOTION_API_KEY` 와 `NOTION_DATABASE_ID` 를 환경 변수로 설정

7. 프로젝트 빌드: Vercel CLI를 사용하여 프로젝트를 빌드
   > `Build Project Artifacts`: 프로젝트를 빌드하는 단계입니다. 빌드 시 `NOTION_API_KEY` 와 `NOTION_DATABASE_ID` 를 환경 변수로 설정
8. 프로젝트 배포: 빌드된 아티팩트를 Vercel에 배포
   이렇게 설정된 워크플로우는 코드가 푸시될 때마다 자동으로 Vercel에 배포하여 CI/CD 자동화.

#### GitHub Secrets 설정

`GitHub Secrets` 에 `VERCEL_ORG_ID`, `VERCEL_PROJECT_ID`, `VERCEL_TOKEN`, `NOTION_API_KEY`, `NOTION_DATABASE_ID` 를 설정.
보안상의 이유로 환경 변수를 안전하게 저장하기 위함.

> Secrets Key 설정방법
> [Vercel을 이용하여 배포 자동화를 해보자](https://velog.io/@jonghyun/Side-Project-Front-Vercel%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%98%EC%97%AC-%EB%B0%B0%ED%8F%AC-%EC%9E%90%EB%8F%99%ED%99%94%EB%A5%BC-%ED%95%B4%EB%B3%B4%EC%9E%90)

### Vercel 이 github 와 연동해서 푸시할떄마다 자동으로 배포해준다며??

Vercel 은 자체적으로 Git Repo 변화를 감지하고 자동으로 업데이트 배포할 수 있다.
그런데 왜 Github Action을 사용해서 Vercel 배포를 자동화하는 이유가 무엇인가..?
라고 문득 생각이 들었다.

#### Vercel의 기본 자동 배포기능

- 자동 감지 및 배포: Vercel은 GitHub, GitLab, Bitbucket과 통합되어 레포지토리의 변화를 감지하고 자동으로 배포
- 간편함: 별도의 설정 없이도 손쉽게 CI/CD 파이프라인을 구성

#### GitHub Actions와 Vercel의 차이점 및 장점

1. **커스텀 워크플로우:**
   GitHub Actions를 사용하면 프로젝트의 특정 요구 사항에 맞게 CI/CD 워크플로우를 커스터마이즈할 수 있다.

   > 예를 들어, 빌드 전에 추가적인 테스트, 코드 품질 검사, 또는 기타 작업을 수행할 수 있다.

2. **다양한 환경:**
   GitHub Actions를 통해 다양한 환경에서 테스트와 배포를 할 수 있다.

   > 예를 들어, 특정 브랜치에 푸시할 때마다 다른 환경(예: 스테이징, 프로덕션)에 배포할 수 있다.

3. **추가적인 통합:**
   GitHub Actions는 다른 툴 및 서비스(예: Slack 알림, 데이터베이스 마이그레이션, S3 버킷 업로드 등)와 쉽게 통합할 수 있어 전체 DevOps 파이프라인을 관리하는 데 유리하다고 한다.

4. **복잡한 워크플로우 관리:**
   보다 복잡한 CI/CD 워크플로우를 관리할 수 있다.

   > 예를 들어, 병렬 작업, 조건부 작업, 매트릭스 전략 등을 통해 더 세밀한 제어가 가능하다고한다..

5. **독립적인 빌드 및 배포:**
   GitHub Actions를 통해 Vercel에서 제공하는 빌드 및 배포 프로세스와 별개로 프로젝트를 빌드 및 배포할 수 있다. 이로 인해 더 많은 유연성을 확보할 수 있음.

### 결론

Vercel의 기본 자동 배포 기능은 간편하고 사용하기 쉬우며 대부분의 경우에 충분!!
**그러나**, 프로젝트가 더 복잡해지고, 특정 요구 사항이 생기거나, 추가적인 작업과 통합이 필요할 때는 GitHub Actions와 같은 CI/CD 도구를 사용하여 워크플로우를 커스터마이즈하는 것이 유리할 수 있다!

따라서 GitHub Actions를 사용한 Vercel 배포 자동화는 더 많은 제어와 유연성을 필요로 하는 경우에 적합하다.
기본 Vercel 배포가 충분하지 않거나 추가적인 CI/CD 요구 사항이 있는 경우 GitHub Actions를 활용하면 보다 강력한 배포 파이프라인을 구축할 수 있다.

---

출처:

https://velog.io/@icdmiracle927/NEXT-JS-13-GITHUB-ACTION%EC%9C%BC%EB%A1%9C-VERCEL%EC%97%90-%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0

https://voyage-dev.tistory.com/136

https://vercel.com/docs/cli/project-linking
