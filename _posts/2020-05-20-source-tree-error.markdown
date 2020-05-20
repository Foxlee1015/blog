---
layout: post
title: 소스 트리 원격 저장소 추가 시 발생한 에러
date: 2020-05-20 00:00:00 +0300
description: "소스 트리 원격 저장소 추가 시 발생한 에러"
tags: [git, sourcetree]
img: sourcetree.png
---

-   소스트리 원격 저장소 설정 시 발생한 에러 수정하기

## 에러

-   소스트리 상단 우측에 있는 원격 아이콘 옆에 ! 경고가 떠 있으며 클릭 시 원격 주소로 이동하지 않고 에러 메시지 뜸
    1. "You don't have any remotes which have extended integration settings configured, you need to edit your remotes to add details such as the hosting type and base URL. Click the settings button to open your remote list"
    2. "Can't open in browser.Are you setting a default browser?"

## 해결 방법

-   설정 - 추가된 원격 저장소 경로에서 해당 리모트 저장소 편집 - 호스트 종류 선택(회사에서 gitlab을 쓰고 선택창에서는 bitbucket, github 만 있어 stash 선택) - 호스트 루트 url : git.회사이름.com - 사용자명 : 깃랩 유저아이디 - 확인
    ![참고 이미지]({{site.baseurl}}/assets/img/sourcetree1.png)

-   테스트 - 원격 옆에 있던 경고창이 사라지고 원격 버튼 누르면 해당 깃랩 저장소로 이동가능

[참고1][ref1]
[참고2][ref2]

## 소스트리 설치하기

1. 소스트리 [최신버전][new]
2. 소스트리 [버전별다운로드][old]

## 소스트리 삭제하기

1. 프로그램 추가 제거에서 소스트리 제거
2. C:\Users\User\AppData\Local\Atlassian 경로로 이동해서 폴더 삭제

[new]: https://www.sourcetreeapp.com/
[old]: https://www.sourcetreeapp.com/download-archives
[ref1]: https://community.atlassian.com/t5/Sourcetree-questions/Cannot-create-PR-You-don-t-have-any-remotes-which-have-extended/qaq-p/733750
[ref2]: https://community.atlassian.com/t5/Sourcetree-questions/Bug-that-remote-cannot-be-opened-by-browser/qaq-p/1325475
