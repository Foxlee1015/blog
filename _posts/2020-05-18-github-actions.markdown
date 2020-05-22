---
layout: post
title: CI-with-github-actions
date: 2020-05-18 00:00:00 +0300
description: 깃허브 액션
img: github-actions.png #
tags: [github, cicd]
---

## Requirements 
* 깃험 계정 [github][github]
* npm
* 깃헙 액션 [생활코딩][생활코딩] 유튜브


## 프로젝트 생성, 패키지 설치 및 package.json 수정

1. npx create-react-app cicd-react-test
2. npm install -D gh-pages
3. package.json 수정 

{% highlight ruby %}
{
"homepage": "http://foxlee1015.github.io/github-cicd",   ( 깃헙 레포 셋팅에서 확인 가능)
"scripts": {
  "predeploy" : "npm run build",
  "deploy" : "gh-pages -d build",
    ~~
  }
}
{% endhighlight %}

4. 깃허브 저장소에 올리기 및 깃허브 배포 (배포되는 시간이 좀 걸림)

{% highlight ruby %}
git add .
git commit -m "first commit"
git push
npm run deploy
{% endhighlight %}

5. 코드 수정 시 npm run deploy 하면 gh-pages 브랜치로 push 됨 이후 수정된 코드는 개발 브랜치에 push


# 자동배포 적용
* 위의 경우 npm run deploy를 하면 gh-pages 브랜치에 build 된 파일들이 올라가고 그 다음 개발 브랜치에 push 를 하는데 github actions 을 통해 해당 브랜치에 push 하면 자동으로 깃험 페이지에 배포(빌드 파일 푸시)하도록 설정
* npm install -D gh-pages 생략 가능
* "predeploy" : "npm run build", "deploy" : "gh-pages -d build" 넣지 않아도 된다.

1. git actions 환경 설정 파일 추가
* .github/workflows/deploy.yml   

![deploy.yml]({{site.baseurl}}/assets/img/github_deploy_yml.JPG)

--> 깃에 올리면 바로 action 에서 바로 진행시작함(밑에 설정해줘야 하는 것들이 필요하므로 에러뜸)
--> ##[error]Action failed with "not found deploy key or tokens" ssh 키 설정 필요

2. ssh 키 설정

* ssh 키 생성 ( 윈도우의 경우 git bash 에서 해야함)
{% highlight ruby %}
> cd ~/.ssh
> ssh-keygen -t rsa -b 4096 -C "$(git config user.email)" -f gh-pages-actions[ssh 키 파일 이름, 변경 가능] -N ""
{% endhighlight %}

* 생성한 ssh 키 해당 저장소 셋팅의 deploy keys 추가(Allow write access 에 체크해야함!!)
{% highlight ruby %}
key : ACTIONS_DEPLOY_KEY
value : 
(public key begins with 'ssh-rsa', 'ssh-ed25519', 'ecdsa-sha2-nistp256', 'ecdsa-sha2-nistp384', or 'ecdsa-sha2-nistp521')
{% endhighlight %}
* value -> cat ~/.ssh/gh-pages-actions[ssh 키 파일 이름].pub   = public key 보는 방법

* 해당 저장소 secrets 에서
{% highlight ruby %}
key : ACTIONS_DEPLOY_KEY
value : 
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaCasdfasdsdfasdfasdfasdff
5UjwqW9LaA3PAAAAAAEasdfasdfasdfC
-----END OPENSSH PRIVATE KEY-----
{% endhighlight %}
* value -> cat ~/.ssh/gh-pages-actions[ssh 키 파일 이름]  = private key 보는 방법(--- 처음부터 --- 끝까지 다 넣어야함)


[github]:https://github.com/
[생활코딩]:https://www.youtube.com/watch?v=uBOdEEzjxzE