---
layout: post
title: How-to-make-blog-with-Jekyll
date: 2020-05-17 00:00:00 +0300
description: 깃허브와 지킬로 개인 블로그 만들기
img: jekyll.png #
tags: [github, ruby, jekyll]
---

## Requirements

-   Github account - 깃허브 페이지 호스팅
-   Ruby - 지킬로 정적 웹사이트 만들기 위해 로컬에서 글을 작성하며 업데이트 되는 부분 확인
-   git bash 또는 sourcetree 등등 - 포스트 수정, 생성, 삭제 등 후에 블로그에 반영하기

## 1. 깃허브 계정 생성 및 repository(저장소) 생성하기

[Github][github]

1. 계정 만들기
2. repository 생성하기

    - **저장소 이름 선택 중요**하고 저장소 이름이 깃허브 블로그 페이지로 생성됨 예를 들면 제 블로그 처럼 https://foxlee1015.github.io/blog/ (https://**깃헙유저아이디**.github.io/**repository 이름\*\*/ )
    - 도메인을 구매한다면 http:xxx.github.io/xxx 가 아닌 구매한 도메인으로도 방문자가 블로그를 방문할 수 있음

3. 지킬 테마 선택 [Jekyll-themes][jekyll-themes] - 시간이 걸리더라도 최대한 마음에 드는 거 고르기!

    - 원하는 기능이나 디자인이 없다면 이후 커스트마이징이 가능함

4. 테마를 선택했다면 해당 깃헙에 가서 소스를 다운받고 내 저장소에 올린다.
    - 저장소를 만들기 전에 테마를 먼저 골랐다면 해당 테마의 깃헙에서 fork 를 하고 fork 한 후 저장소 이름을 변경할 수도 있음

## 2. Ruby 설치하기

-   [Ruby 다운로드 페이지][ruby]
-   [제가 참고한 설치 가이드 블로그][ruby-install]

1. 루비 설치
2. 설치가 완료되면 윈도우 검색창에서 Start Command Prompt with Ruby 를 실행
3. 패키지 설치

{% highlight ruby %}

> gem install jekyll minima bundler jekyll-feed tzinfo-data

{% endhighlight %}

4. 지킬 실행

{% highlight ruby %}

> jekyll server

{% endhighlight %}

-   실행 성공  
    {% highlight ruby %}

    > Server address: http://127.0.0.1:4000/ > Server running... press ctrl-c to stop

    {% endhighlight %}

-   실행 실패
    {% highlight ruby %}

    > Could not find gem 'jekyll-sitemap x64-mingw32' in any of the gem sources listed in your Gemfile.

    {% endhighlight %}

-   에러 해결
    1. 위와 유사한 같은 에러가 뜨면 gem install jekyll-sitemap 통해 해결할 수 있다.
    2. 위 에러는 각 지킬 테마마다 다른 패키지를 사용하는데 내가 선택한 지킬 테마의 경우 jekyll-sitemap, jekyll-paginate, jemoji 이 \_config.yml 의 plugins 에 추가되어 있어서
    3. 아래와 같이 각 패키지들을 먼저 설치한 후에 지킬 실행이 되었다.

{% highlight ruby %}

> gem install jekyll-sitemap jekyll-paginate jemoji
> jekyll server

{% endhighlight %}

-   로컬 블로그 접속
    -   http://127.0.0.1:4000/blog(_config.yml 에서 설정한 baseurl)/ 로 접속하면 선택한 테마가 똑같이 나타나는 것을 확인할 수 있다.
    -   각종 author 정보 등을 수정 후 블로그 작성을 시작할 수 있고, 일반적으로 테마에 예시 포스트들이 있기 때문에 그 형식등을 맞춰서 작성하면 된다.

## 3. 블로그 작성

1. 글을 작성한 후 로컬에서 수정된 내용을 확인 후 저장소에 push 하면 작성한 내용들이 자신의 깃헙 페이지에 반영되며 일반적으로 약간의 시간이 걸리기에 스트레칭 후 반영 결과를 확인해보자.

## 4. ERROR HISTORY

1. gem 을 이용해 패키지 설치 시 내가 선택한 테마는 내가 참고한 블로그 만들기 사이트에서 선택한 테마와 달라 해당 지킬 테마에서 사용한 플러그인이 다르기 때문에 지킬 피키지들을 더 설치해야 했음
2. 처음 로컬에서 지킬 웹 페이지를 실행했을때 css, imges 들을 import를 못했는데 baseurl 설정을 잘못했었음

## 기타

-   마크다운 작성법이 궁금하다면 [여기를][markdown] 확인해보세요.

[github]: https://github.com/
[jekyll-themes]: http://jekyllthemes.org/
[ruby]: https://rubyinstaller.org/downloads/
[ruby-install]: https://shryu8902.github.io/_posts/2018-06-22-jekyll-on-windows/
[markdown]: https://gist.github.com/ihoneymon/652be052a0727ad59601
