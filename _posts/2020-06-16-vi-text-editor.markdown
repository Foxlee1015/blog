---
layout: post
title: Vim Editor
date: 2020-06-16 01:00:00 +0300
description: "Vim Editor"
tags: [Vi, Editor]
img: vim.png
---

## Vi editor / Vim editor
* vi는 유닉스 os 기본 에디터로, vim 은 vi 의 업데이트된 버전으로 여러가지 vi 이 갖지 못하는 장점들을 가지고 있습니다.
* 다른 명령여 [여기][여기] 참고하세요.
  

## 입력 모드로 전환

* a = 커서 다음 위치칸부터
* i = 커서의 위치부터 입력
* o = 커서의 다음행에서 입력


## 명령 모드 - 저장, 종료

* :w 저장
* :w file.txt file.txt로 저장
* :q 종료
* :q! 강제 종료


## 명령모드에서 이동

1. 페이지 이동
   * h = 왼쪽으로 이동
   * l = 오른쪽 이동
   * j = 아래로 이동
   * k = 위로 이동
   * gg = 문서의 맨 위로
   * G = 문서의 마지막 행으로 이동
   * :200 = 200번째 행으로 이동
   * ctrl + b 한 화면 위로 이동
   * ctrl + f 한 화면 아래로 이동

2. 단어 이동
   * w 다음 단어의 첫 글자로 이동
   * b 이전 단어의 첫 글자로 이동


## 명령모드 편집

1. 삭제
   * dd = 그 행 삭제
   * 3dd = 해당 행부터 3행 삭제
   * diw = 현재 위치의 단어 삭제
  
2. 복사 / 붙여넣기
   * yy = 커서가 있는 한행 복사 -> p = 현재 커서에 붙여 넣기
   * 2p = 2번 붙여넣기

## 명령모드 검색 및 변경

1. 검색 및 변경
   * :test                  test 문자열 검색
   * :s/test/text           현재 행에서 가장 먼저 나오는 test를 text 로 변경
   * :s/test/text/g         현재 행의 모든 test 를 text 로 변경
   * :10,20s/test/text/g    10행~20행 모든 test 를 text 로 변경
   * :-2,+2s/test/text/g    현재 커서 기준 위아래로 각각 2행까지 변경
   * :%s/old/new/g          문서 전체에서 old를 new로 교체
   * :%s/old/new/gc         문서 전체에서 old를 new로 확인하며 교체
   * :g/pattern/s/old/new/g pattern이 있는 모든 행의 old를 new로 교체



[여기]: https://gyuha.tistory.com/157