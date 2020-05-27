---
layout: post
title: Database
date: 2020-05-27 00:00:00 +0300
description: "RDBMS, NO SQL 차이"
tags: [db, sql, nosql, mysql, elasticsearch]
img: sqlnosql.png
---

## 개념

1. Rdbms

-   장점
    1. 무결성 보장.. 타입을 결정하기때문에 불확실성 줄임
    2. 외래키로 중복 감소. 업데이트에 유리
-   단점
    1. 수평적 확장에 no sql 에 비해 떨어짐
    2. Join 복잠해짐

2. Nosql

-   장점
    1. 스키마 없어서 유연. 언제든지 필드 확장 가능.
-   단점
    1. 쓰기에는 유리하지만 업데이트에서 안 좋음. sql 에서는 외래키로 하나만 수정하면 되지만 문서마다 모두 수정해야함 또한 데이트 중복 많이 됌.
    2. Es 검색 장점으로는 토큰화된 단어들이 문서의 위치를 바로 알 수 있어서 빠름.
