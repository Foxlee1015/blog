---
layout: post
title: github-actions-s3
date: 2020-07-20 00:00:00 +0300
description: 깃허브 액션 s3 정적 파일 올리기
img: s3.png #
tags: [github, cicd, s3]
---

### 리액트 빌드시키고 빌드된 파일을 S3에 올려서 자동 배포하기 [참고]

## Requirements 
* React project
* Git repository
* Amazon account

### 먼저 할일
1. 리액트 프로젝트 깃헙 저장소에 push

### Github actions workflow 파일 작성
* 프로젝트디렉토리/.github/workflows/main.yml    (처음에 workflows 가 아닌 workflow로 해서 git action에서 해당 파일을 찾지 못함)
* 아래와 같이 작성 후 master 브랜치에 push
{% highlight ruby %}
name: React build
on: 
  push:                               # master Branch에서 push 이벤트가 일어났을 때만 실행
    branches:
      - master

jobs:
  build:
    runs-on: ubuntu-18.04
    steps:
      - name: Checkout source code.   # 레포지토리 체크아웃
        uses: actions/checkout@master

      - name: Install Dependencies    # 의존 파일 설치 // 빌드시마다 재설치 되므로 아래처럼 캐싱 추가
        run: npm install

      - name: Build                   # React Build
        run: npm run build

{% endhighlight %}


### 캐싱 추가
{% highlight ruby %}
name: React build
on: 
  push:                               # master Branch에서 push 이벤트가 일어났을 때만 실행
    branches:
      - master

jobs:
  build:
    runs-on: ubuntu-18.04
    steps:
      - name: Checkout source code.   # 레포지토리 체크아웃
        uses: actions/checkout@master

      - name: Cache node modules      # node modules 캐싱
        uses: actions/cache@v1
        with:
          path: node_modules          # path: node_modules: node_modules 폴더를 캐싱
          key: ${ { runner.OS } }-build-${ { hashFiles('**/package-lock.json') } }
          restore-keys: |
            ${ { runner.OS } }-build-
            ${ { runner.OS } }-       # 만약 key값이 없으면 그 이전에 캐시했던 폴더중 가장 최근의 key값을 가져와 불러옴

      - name: Install Dependencies    # 의존 파일 설치
        run: npm install

      - name: Build                   # React Build
        run: npm run build

{% endhighlight %}
* { { } } 붙여 수정해서 진행!!!
* with 구문을 사용하고, path 와 key 가 반드시 필요함
* path: 저장하고 불러올 캐시 대상 폴더
* key: 저장 하고 불러올 때 식별할 수 있는 키 값
* restore-keys(optional): 캐시 key가 일치하는 것이 없을때, 차선택으로 캐싱 폴더를 찾는 key
* key: ${ { runner.OS } }-build-${ { hashFiles('**/package-lock.json') } } 
    * ->runner.OS = 가상환경(현재 설정으론 리눅스), hashFiles('**/package-lock.json')는 package-lock.json 파일을 해시화한다는 것인데, 이는 같은 의존성 파일이면 같은 해시값이 나오게 되어 새로운 key를 만들지 않고, 하지만 새로운 모듈이 추가되면 lock 파일도 변경이 일어나고, 이어서 해시값도 바뀌므로 key도 변경이 되어 새롭게 캐싱을 하게 됨


* 결론은 __node_modules 를 저장했다가 불러옴__

### S3 정적 사이트 설정하기
1. S3 서비스 이동
2. 버킷 만들기
3. 버킷 이름, 지역 설정 후 생성
4. 버킷의 속성탭으로 이동해서 정적 웹 사이트 호스팅에 인덱스 문서와 오류 문서에 index.html 적고 저장
5. 정책 - 권한 - 퍼블릭 액세스 차단 탭에서 모든 퍼블랙 액세스 차단 해제
6. 정책 탭 - 정책 편집기에 아래와 같이 작성
  
{% highlight ruby %}
{
  "Version": "2012-10-17",
  "Id": "Policy1546336529826",
  "Statement": [
      {
          "Sid": "Stmt1546336528005",
          "Effect": "Allow",
          "Principal": "*",
          "Action": "s3:GetObject",
          "Resource": "arn:aws:s3:::버킷이름/*"
      }
  ]
}
{% endhighlight %}


### 깃헙에서 s3에 배포하기 위해 권한 설정(aws cli key 발급)
1. Amazon 서비스 IAM 으로 이동 후 사용자 추가
2. 사용자 이름 적은 후 액세스 유형애 프로그래밍 방식 액세스 선택
3. 권한 설정에 기존 정책 직접 연결 -> AmazonS3FullAccess 선택하여 CSV 파일 다운로드
4. github repository setting 에서 Secrets로 이동 후 AWS_ACCESS_KEY_ID와 AWS_SECRET_ACCESS_KEY csv 참고하여 추가하기
5. .github/workflows/main.yml 수정
  
{% highlight ruby %}
name: React build
on: 
  push:                               # master Branch에서 push 이벤트가 일어났을 때만 실행
    branches:
      - master

jobs:
  build:
    runs-on: ubuntu-18.04
    steps:
      - name: Checkout source code.   # 레포지토리 체크아웃
        uses: actions/checkout@master

      - name: Cache node modules      # node modules 캐싱
        uses: actions/cache@v1
        with:
          path: node_modules
          key: ${ { runner.OS } }-build-${ { hashFiles('**/package-lock.json') } }
          restore-keys: |
            ${ { runner.OS } }-build-
            ${ { runner.OS } }-

      - name: Install Dependencies    # 의존 파일 설치
        run: npm install

      - name: Build                   # React Build
        run: npm run build

      - name: Deploy                  # S3에 배포하기
        env:
          AWS_ACCESS_KEY_ID: ${ { secrets.AWS_ACCESS_KEY_ID } }
          AWS_SECRET_ACCESS_KEY: ${ { secrets.AWS_SECRET_ACCESS_KEY } }
        run: |
          aws s3 cp \
            --recursive \
            --region ap-northeast-2 \
            build s3://버킷이름

{% endhighlight %}

### 나의 경우 cra 가 아닌 웹팩으로 리액트 프로젝트를 빌드시키면서 빌드 디렉토리 이름을 dist로 설정해놨었는데 git action flow 에서 deploy 에러가 떴음. build 로 변경했더니 잘 배포됨
### * { {  } } 붙여 수정해서 진행!!!



[참고]:https://velog.io/@loakick/Github-Action-AWS-S3%EC%97%90-React-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8-%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0