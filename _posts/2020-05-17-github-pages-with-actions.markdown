---
layout: post
title: react-github-pages-with-actions
date: 2020-05-17 13:32:20 +0300
description: 리액트 앱 깃헙 페이지 적용 및 자동 배포 적용
img: github-actions.png # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [react, github, ci/cd]
---
 
1. cra 로 리액트 앱 생성
2. 깃헙 배포용 패키지 설치
3. package.json 환경설정
4. 깃험 레포에 푸쉬 및 환경 설정
5. workflows 환경 설정


## 1. cra 로 리액트 앱 생성 2. 깃헙 패키지 설치

{% highlight ruby %}
> $ npx create-react-app react-github
> $ npm install -D gh-pages
{% endhighlight %}


## package.json 환경설정
{% highlight ruby %}
> {
>   "homepage": "http://foxlee1015.github.io/github-cicd",   ( 깃헙 레포 셋팅에서 >확인 가능)
>   "scripts": {
>     "predeploy" : "npm run build",
>     "deploy" : "gh-pages -d build",
>     ~~
>   }
> }
{% endhighlight %}


## 깃헙 환경 설정


## 