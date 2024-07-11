# AWS S3 + GitHub Action 오류

```yaml
# AWS S3 와 연결해서 배포하는 yml
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

Deploy 부분에서 자꾸 s3 폴더 링크를 찾지 못해서 오류가 나오게 되었다.
오류 사항: `The user-provided path dist does not exist.`
해석: **"사용자가 제공한 경로 dist가 없습니다."**

![](https://velog.velcdn.com/images/dlwoxhd/post/f83a1c10-31ff-4df1-aad1-3b826c2dffd1/image.png)

s3 디렉토리 위치를 찾지못하는 오류인거 같아서
yml파일 안에 pwd와 ls -la를 적절히 이용하여 위치를 찾아 들어가 수정했다.
cli 를 github action에서 이용해야해서 계속 github action을 돌려버렸다..
모듈파일을 캐싱이라도 해놔서 다행인부분..

![](https://velog.velcdn.com/images/dlwoxhd/post/49216914-0dc6-4db4-8382-98b7b5e35658/image.png)

눈물나는 커밋횟수..😭
