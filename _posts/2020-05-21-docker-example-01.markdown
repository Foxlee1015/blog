---
layout: post
title: docker-example-01
date: 2020-05-21 00:00:00 +0300
description: "docker 간단한 예제로 공부하기"
tags: [docker, react, redis]
img: docker.png
---

## Requirements

1. 도커 계정
2. 도커 설치(윈도우 8 이하인 경우 toolbox 설치)

-   웹 서버

{% highlight ruby %}
// web/package.json

{
"dependencies": {
"express": "\*",
"redis": "2.8.0"
},
"scripts": {
"start": "node index.js"
}
}

// web/index.js

const express = require("express");
const redis = require("redis");
const process = require("process");

const app = express();
const client = redis.createClient({
host: "redis-server",
port: 6379
});
client.set("visits", 0);

app.get("/", (req, res) => {
//process.exit(0)
client.get("visits", (err, visits) => {
res.send("Number of visits " + visits);
client.set("visits", parseInt(visits) + 1);
});
});

app.listen(8081, () => {
console.log("listening on port 8081");
});

{% endhighlight %}

-   도커 빌드 설정

{% highlight ruby %}
// web/Dockerfile

FROM node:alpine // 노드 이미지를 기본 베이스로

WORKDIR '/app' // 컨터이너에서 app 안으로 이동 workdir 를 설정해주지 않으면 컨테이너 내에 파일 관리에 좋지 않음

COPY package.json . // 전체 copy 전에 package.json 만 카피하는 이유는 package.json 이 업데이트 되었을때에만 npm install 을 실행시키기 위해서 (즉, 다시 빌드 시킬 때 업데이트 되지 않은 부분은 캐싱)
RUN npm install
COPY . . (로컬 내 현재 디렉토리를 위에서 설정한 workdir 로 다 옮김)

CMD ["npm","start"]

{% endhighlight %}

-   복수개의 도커 컨테이너 설정을 간편하게 해주는 docker-compose

{% highlight ruby %}
// web/docker-compose.yml

version: "3"
services:
redis-server:
image: "redis"
node-app:
restart: on-failure
build: .
ports: - "4001:8081"

{% endhighlight %}
