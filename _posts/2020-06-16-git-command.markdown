---
layout: post
title: git-command
date: 2020-06-16 01:00:00 +0300
description: "git-command"
tags: [git, ]
img: git.png
---

## 자주 쓰는 git 명령어 정리

* 수정된 부분 폐기하기 = git checkout -f
* untracked 파일 제거하기 = git clean -fd

## 커밋 메시지

* push 전 가장 최근 커밋 메시지 수정하기 
  1. git commit --amend -m "33"
* push 는 했지만 가장 최근 커밋 수정 
  1. git commit --amend
  2. git push origin master --force

## 커밋한 파일 staging 과 메시지 같이 입력
* git commit -am "[docs] messages"


## push된 커밋 정보(메시지,작성자)변경
1. 잘못된 이름의 커밋 id(hash) 확인
2. git rebase -i -p 커밋hash
3. 변경이 필요한 커밋 pick -> edit 으로 변경 후 저장
4. 오래된거 부터 순차적으로 수정 후 저장
5. 작성자 변경의 경우 git commit --amend --author="사용자명 <이메일>"
6. git rebase --continue 다음 커밋으로 이동
7. 끝난 후 push

## 변경 확인
* git diff  / 변경된 파일들 , git diff filename 특정 파일의 변경만
* git diff --staged 수정 전후 변경된 부분 보여줌