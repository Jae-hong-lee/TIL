Github Action을 사용해서 Git의 소스코드를 master 브랜치에 push하면 AWS S3에 자동으로 배포되는 과정을 진행해보았다.
이 과정은 지속적인 통합 및 배포(CI/CD)를 통해 개발과 배포를 자동화하여 효율성을 높이는 데 목적이 있다.
먼저, Git에서 소스코드를 master 브랜치에 push하면, Github Action이 트리거되어 설정된 워크플로우가 실행된다. 이 워크플로우는 소스코드를 빌드하고 AWS S3 버킷에 배포하는 여러 단계를 포함한다.
이러한 자동화된 배포 시스템을 구현함으로써 배포 과정에서 발생할 수 있는 오류를 최소화하고, 더 빠르고 안정적인 배포를 가능하게 한다.

## node modules 캐싱

node modules를 왜 캐싱해야 할까?

캐싱은 한 번 저장한 데이터를 다시 불러오는 방법으로, 이를 통해 속도를 크게 개선할 수 있다. 예를 들어, dependencies를 설치할 때 캐싱을 사용하면 약 10초가 걸리지만, 캐싱을 사용하지 않으면 약 40초가 걸린다. 이는 초기 설치 시간만을 고려했을 때의 차이이며, 여러 번 빌드를 반복할 경우 그 차이는 점점 더 커진다.

이러한 시간 차이는 누적되어 매 빌드마다 약 1분 정도의 시간을 절약할 수 있다. 만약 캐싱을 사용하지 않으면 매번 빌드할 때마다 긴 시간을 기다려야 하며, 이는 개발 효율성을 크게 떨어뜨린다. 따라서, 이러한 불편함을 줄이기 위해 node modules를 캐싱하는 방법을 선택했다.

결론적으로, 캐싱은 빌드 시간을 줄이고 개발 과정에서의 효율성을 높이는 중요한 방법이다. 이를 통해 더 빠르고 원활한 개발 환경을 유지할 수 있다.

```yaml
steps:
 - name: Checkout source code. # 레포지토리 체크아웃
	 uses: actions/checkout@master
 - name: Cache node modules # node modules 캐싱
	 uses: actions/cache@v1
	 with:
      # 노드모듈 폴더 캐싱
			path: node_modules
			# 가상환경을 의미 여기서는 Linux를 의미한다. package-lock.json 파일을 해시화,
			# 의존성 파일이면 같은 해시값이 나오게 되어 새로운 key를 만들지 않음.
			# 하지만 새로운 모듈이 추가되면 Lock파일도 변경이 일어나, 해시값도 바뀌므로 Key도 변경되어 새롭게 캐싱하게 된다.
			key: ${{ runner.OS }}-master-build-${{ hashFiles('**/yarn.lock') }}
			# 만약 key값이 없으면 이전에 캐시했던 폴더 중 가장 최근 key값을 가져와 불러온다.
			restore-keys: |
				${{ runner.OS }}-build-
				${{ runner.OS }}-

```

위 코드는 실제 포트폴리오에 적용한 캐싱 steps 부분만 발취해서 가져왔다.
actions/cache 액션을 가져와 실행. 이때 with 구문을 사용하여 path와 key를 설정해야 한다.
path는 캐시할 폴더를 지정하며, key는 캐시를 식별하는 고유 값 이다.

> `restore-keys(optional)`: 캐시 key가 일치하는 것이 없을때, 차선택으로 캐싱 폴더를 찾는 key”

# AWS S3 버킷설정

![image](https://github.com/Jae-hong-lee/TIL/assets/72030487/fea7eb52-a293-4c36-8e2a-e4c832d0567a)

![image](https://github.com/Jae-hong-lee/TIL/assets/72030487/2f089d96-fce8-4e0a-bd79-1021970a0d2e)

먼저 AWS S3 버킷을 만들어야 한다. 만들 때 퍼블릭 액세스 차단 설정을 해제해야함.

- 이유: 다른 사람들이 접근할 수 있도록 차단을 풀어주는 것.

![image](https://github.com/Jae-hong-lee/TIL/assets/72030487/59abc696-c2d8-49c3-92e1-74ca2778f768)

버킷을 만들었다면 버킷 정책 편집기에 가서 밑에 구문을 추가해주자.

```yaml
{
    "Version": "2012-10-17",
    "Id": "Policy1546336529826",
    "Statement": [
        {
            "Sid": "Stmt1546336528005",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::github-action-tutorial/*" // 자신의 버킷 이름으로 변경해주세요.
        }
    ]
}
```

이제 호스팅을 위한 버킷 설정은 끝!

# AWS CLI 권한 생성

다음은 터미널 CLI로 버킷에 접근하기 위한 권한을 부여하는 방법을 알아보자.
AWS IAM 이라는 서비스를 이용해야한다.

> `IAM`은 AWS Identity and Access Management의 약자로, AWS에서 제공하는 서비스에 대한 엑세스를 안전하게 제어할 수 있는 서비스라는데, '정책'이라는 것을 통해서 서비스를 이용할 수 있고, 없고를 결정한다

AWS IAM 으로 들어와 메뉴에서 사용자로 들어간다.

![image](https://github.com/Jae-hong-lee/TIL/assets/72030487/15944832-36cc-4a54-b68d-c1a3bdd7e392)
그리고 사용자 추가를 통해 새로운 사용자를 등록한다.
사용자 이름을 입력하고 넘어가면,
![image](https://github.com/Jae-hong-lee/TIL/assets/72030487/e9ea2b62-0815-411c-bef7-4f3a3cd51787)

권한을 설정하는 부분이 나오는데,
기존 정책 직접 연결 탭에서 s3를 검색하고, AmazonS3FullAccess를 선택하여 다음으로 넘어가자

<img width="754" alt="image" src="https://github.com/Jae-hong-lee/TIL/assets/72030487/8b61b411-08c2-4bb8-9a34-8655a61ab41b">

![](https://velog.velcdn.com/images/dlwoxhd/post/9e9b69b2-6b4e-476e-9900-06ea934b9f14/image.png)

그렇게 된다면 이렇게 AmazonS3FullAccess를 가지고 있는 사용자가 생성된다.
그 후 생성된 사용자로 들어가 보안 자격 증명에 액세스 키를 생성해주자.
![](https://velog.velcdn.com/images/dlwoxhd/post/fefa9c07-5425-4eaf-b0f0-6454b323d8d1/image.png)

키를 설정할때 설명 태그설정은 말 그대로 키를 설명하는 태그라 그냥 넘어가도 상관없다.
하지만 다음번에 나오는 키값은 어느 한곳.. **어느 중요한 곳에 저장해두자.**
한번 보여주고 사라져버림

> .csv를 다운로드하여 접근 권한 키를 저장하시길 바랍니다! 해당 키는 나중에 CLI에서 접근하기 위해 필요합니다.

# Github Action 키값 저장

AWS CLI KEY는 외부에 절대로 노출되어서는 안 된다. 이러한 키가 노출되면 보안상 큰 문제가 발생할 수 있기 때문이다. 하지만 우리 git 레포지토리는 공개 레포지토리이기 때문에 키값을 그대로 업로드하게 되면 모든 사용자에게 알려지게 된다. 그러면 외부인들도 이 키를 악용할 수 있는 위험이 존재한다. 따라서 민감 정보는 따로 암호화해서 안전하게 저장할 수 있게 Git에서는 방법을 제공하는데, 이 중 하나가 Secrets이다. Secrets를 통해서 민감 정보를 안전하게 저장하고 관리할 수 있으며, 이를 활용하면 키값이 외부에 노출되지 않도록 확실히 방지할 수 있다.

csv 파일을 확인해보면 Access 키와 Secret Access 키를 확인할 수 있다.

![](https://velog.velcdn.com/images/dlwoxhd/post/8e48c4bf-0fd4-42c5-9053-1ad4f17406f5/image.png)
`Settings` 탭에 `Options` 목록을 보면 Secrets 탭이 보인다.
여기에 Add a new secret 눌러 `AWS_ACCESS_KEY_ID`와 `AWS_SECRET_ACCESS_KEY`키를 설정해준다.

![](https://velog.velcdn.com/images/dlwoxhd/post/f6cf73f3-87dc-48a1-b00c-b00bc174f75d/image.png)

# Github Action 에서 S3 업로드

Secrets 정보를 불러와 yaml 파일을 작성했다.

```yaml
# AWS S3 와 연결해서 배포하는 yml

name: test-github-action

# master Branch에서 push 이벤트가 일어났을 때만 실행
on:
  push:
    branches: [master]
jobs:
  deploy:
    name: Build, Deploy to S3 bucket
    runs-on: [ubuntu-latest]
    steps:
      - name: Checkout source code. # 레포지토리 체크아웃
        uses: actions/checkout@master
      - name: Cache node modules # node modules 캐싱
        uses: actions/cache@v1
        with:
          # 노드모듈 폴더 캐싱
          path: node_modules
          # 가상환경을 의미 여기서는 Linux를 의미한다. package-lock.json 파일을 해시화,
          # 의존성 파일이면 같은 해시값이 나오게 되어 새로운 key를 만들지 않음.
          # 하지만 새로운 모듈이 추가되면 Lock파일도 변경이 일어나, 해시값도 바뀌므로 Key도 변경되어 새롭게 캐싱하게 된다.
          key: ${{ runner.OS }}-master-build-${{ hashFiles('**/yarn.lock') }}
          # 만약 key값이 없으면 이전에 캐시했던 폴더 중 가장 최근 key값을 가져와 불러온다.
          restore-keys: |
            ${{ runner.OS }}-build-
            ${{ runner.OS }}-

      - name: yarn install
        run: yarn install

      - name: Build
      - name: Build # env 노션 API 키 생성
        run: yarn build:ssg
        env:
          NOTION_API_KEY: ${{ secrets.NOTION_API_KEY }}
          NOTION_DATABASE_ID: ${{ secrets.NOTION_DATABASE_ID }}
      - name: Deploy # S3에 배포하기
        env:
          AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        run: |
          aws s3 cp \
            --recursive \
            --region ap-northeast-2 \
            out s3://github-action-portfolio
```

### Build

내 프로젝트에서는 Notion API 를 쓰고 있기 때문에 secrets에 노션 API key도 저장하고 빌드하였다.

### Deploy

`run` 방식으로 `aws s3 cp` 로 위 스탭에서 빌드한 폴더의 내용을 s3에 복사하여 주게된다.

> `out` 부분은 자신의 레포지토리 주소로 변경해주어야한다.

> aws 명령어를 사용할 수 있는 이유는 github action에서 가상환경 구축 시 자동으로 설치되어 있기 때문

> **AWS S3 명령어 CLI 설명** >`cp`: 파일복사
> `--recursive` 옵션과 함께 사용하면 커맨드 적용 범위가 하위 디렉터리까지 적용
> 예를 들어 `aws s3 rm` 커맨드를 --recursive 옵션과 함께 사용하면 하위 디렉터리까지 적용되서 모든 파일과 디렉토리가 삭제된다. 
> 반대로 `aws s3 cp` 커맨드를 --recursive 옵션과 함꼐 사용하면 Bucket에 있는 모든 파일이 복사된다.
> `region` : ap-northeast-2[서울 리전]
> 출처: [Inpa Dev 👨‍💻:티스토리](https://inpa.tistory.com/entry/AWS-CLI-📚-S3-CLI-명령어-종류-💯-총정리#하위_디렉토리_포함_--recursive)

# End

간단하게 `github action`을 이용해서 브랜치에 변화를 감지하고 s3로 배포하는 방법을 작성해 보았다.

앞으로 로컬에서 소스코드를 수정해서 master 브랜치에 소스를 push 하면, `Github Action` 에서 일련의 과정을 거친 뒤에 자동으로 S3에 배포하게 될 것이다.

---

> [aws cli 명령어](https://inpa.tistory.com/entry/AWS-CLI-%F0%9F%93%9A-S3-CLI-%EB%AA%85%EB%A0%B9%EC%96%B4-%EC%A2%85%EB%A5%98-%F0%9F%92%AF-%EC%B4%9D%EC%A0%95%EB%A6%AC)

> [AWS S3에 React 프로젝트 배포하기 feat.github action](https://velog.io/@loakick/Github-Action-AWS-S3%EC%97%90-React-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8-%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0)

> [Next.js CICD AWS with githubAction](https://dev.to/dabit3/next-js-ci-cd-on-aws-with-github-actions-3502)

> [next.js serverless + github action](https://velog.io/@rkd028/Next-js-Serverless%EB%A1%9C-Next-js-%EB%B0%B0%ED%8F%AC-%EB%B0%8F-%EC%9E%90%EB%8F%99%ED%99%94%ED%95%98%EA%B8%B0)
