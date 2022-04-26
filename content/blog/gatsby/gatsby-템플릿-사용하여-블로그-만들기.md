---
title: gatsby 템플릿 사용하여 블로그 만들기
date: 2022-04-26 13:04:97
category: gatsby
draft: false
---

velog, oopy를 사용하다가 결국 `gatsby`로 넘어오게 되었는데 그 이유를 살펴보면

1. `무료`다

2. `markdown`을 지원한다

3. `1일 1커밋`이 가능하다

4. 어느정도 내 입맛에 맞게 `커스텀`이 가능하다

정도인데 내가 사용한 템플릿과 배포 방식에 대해 포스팅해보고자 한다

# 1. 블로그 템플릿

템플릿같은 경우에는 [재엽님의 템플릿](https://github.com/JaeYeopHan/gatsby-starter-bee)을 사용하였다

이유는 단순히 **디자인이 제일 맘에 듦 + 사람들이 많이 써서 정보가 많음**이었다

나는 보통 무언가 처음 시작할 때 빠르게 이해하기 위한 방법으로 진입장벽을 낮추려 노력하는데 정보가 많을수록 진입장벽이 낮기 때문에 유명한 것을 선호한다

해당 블로그 템플릿을 사용하기 위해서는 두 가지 방법이 있는데

```sh
# npx 사용
$ npx gatsby new ${blog} https://github.com/JaeYeopHan/gatsby-starter-bee
```

혹은

```sh
# npm 사용

$ npm install -g gatsby-cli
$ gatsby new ${blog} https://github.com/JaeYeopHan/gatsby-starter-bee
```

으로 설치해주면 된다

> 📌 `${}`안의 내용은 본인이 원하는 디렉토리 명을 넣으면 되고 혹시 에러가 발생한다면 node version을 `v14.x`로 낮춰서 진행해보길 바란다

# 2. repository와 연결

나는 **무료**인 것이 중요했기 때문에 `github.io`로 배포했는데 repository명은 반드시 `${github username}.github.io`로 하여야 한다

이를 어기면 url이 더 길어지게 된다

ex. `blog`로 만들면 url : `${github username}.github.io/blog/`가 기본주소가 된다

# 3. 포스팅

이미 `script`에 포스팅 하는 방법이 나와있다

```sh
$ npm run post
```

카테고리를 설정하고 제목을 입력하면 `/content/blog/${category}/${title}.md`와 같이 파일이 하나 생성된다

date, draft 등과 같은 것은 테스트 예시를 보면 그 역할을 알 수 있다

# 4. 배포

배포방법도 원작자의 깃헙에 자세히 나와있지만 나는 `master`와 `gh-pages`브랜치를 나눠서 관리하고 싶기 때문에 조금은 다른 방법으로 배포했다

```sh
$ npm i -d gh-pages
```

`gh-pages`를 설치하고 `package.json`에 스크립트를 다음과 같이 작성해준다

```json
"deploy": "gh-pages -d public",
"predeploy": "npm run build"
```

그리고 배포를 원할 때

```sh
$ npm run deploy
```

로 배포를 해주면 된다

위 스크립트를 잠시 살펴보면 `-d public`은 배포할 디렉토리를 `public`으로 지정한다는 뜻이고 `"predeploy"`는 `$ npm run deploy`를 실행하면 `deploy`를 실행하기 전에 먼저 `predeploy`를 실행해준다

이는 npm의 규칙으로 prefix로 `pre`가 붙어있는 명령어를 찾아 먼저 실행해준다

## \*references

1. [Gatsby 템플릿으로 블로그 시작하기](https://dailyco.tech/share/gatsby-template-blog-start/)
