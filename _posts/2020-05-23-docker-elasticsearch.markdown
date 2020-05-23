---
layout: post
title: docker - elasticsearch 설치
date: 2020-05-23 00:00:00 +0300
description: "docker - elasticsearch 설치"
tags: [docker, elasticsearch]
img: docker.png
---

## 요약
1. 원래 윈도우에서는 엘라스틱 서치 배치파일만 실행하면 다른 환경 설정없이 잘 실행되는데 이번에 개인 프로젝트를 시작하면서 에러가 발생했다. 사실 에러를 해결해서 로컬에서 돌리는 거에 시간을 투자를 해도 되지만... 현재 로컬에서 es는 개발용으로만 사용할 예정이므로 시간을 뺏기지 않기 위해 최근에 로컬에 설치한 도커 툴박스를 활용하기로 결정함


## es 설치
1. 도커 실행(윈도우 8이하는 도커 툴박스)
2. [도커 허브][docker hub]
3. 도커 이미지 찾기 - 검색창에 elasticsearch 

{% highlight ruby %}
> docker pull docker.elastic.co/elasticsearch/elasticsearch:7.3.1
> docker images (방금 설치한 엘라스틱서치 이미지의 아이디 확인)
> docker run -p 9200:9200 -p 9300:9300 --name my_es -e "discovery.type=single-node" 3d3aa92(이미지 아이디)
> docker ps 를 통해 내가 정한 이름 my_es 가 생긴 거 확인 
> docker exec -it my_es sh -> 컨테이너 내부로 들어가 컨테이너 파일들 직접 확인 가능
{% endhighlight %}

* -p 는 내 로컬 포트와 도커 컨테이너 내부의 포트 맵핑 시켜주고 
* --name 은 도커 컨테이너 이름
* -e 엘라스틱 싱글노드


* 외부에서 접속하기 
1. iptime 페이지에서 포트포워딩 설정
2. toolbox 경우 가상머신 네트워크 설정에 포트포워딩 설정
3. 포스트맨으로 get 192.168.99.100:9200 하면 엘라스틱 정보를 확인할 수 있음 

[설치]:[https://sites.google.com/a/chromium.org/chromedriver/downloads]
[docker hub]: https://hub.docker.com/