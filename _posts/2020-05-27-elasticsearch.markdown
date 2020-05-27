---
layout: post
title: Elasticsearch
date: 2020-05-27 00:00:00 +0300
description: "Elasticsearch"
tags: [elasticsearch]
img: elasticsearch.png
---

## 개념

1. 클러스터 :클러스터 여러 노드의 집합 설치시 master node만 존재 Elasticsearch에서 가장 큰 시스템 단위
2. 노드 : Elasticsearch를 구성하는 하나의 단위 프로세스
3. 인덱스( index ) / 샤드( Shard ) / 복제( Replica )

    - Elasticsearch에서 index는 RDBMS에서 database와 대응하는 개념입니다.
    - 또한 shard와 replica는 Elasticsearch에만 존재하는 개념이 아니라, 분산 데이터베이스 시스템에도 존재하는 개념입니다.
    - 샤딩( sharding )은 데이터를 분산해서 저장하는 방법을 의미합니다. 즉, Elasticsearch에서 스케일 아웃을 위해 index를 여러 shard로 쪼갠 것입니다. 기본적으로 1개가 존재하며, 검색 성능 향상을 위해 클러스터의 샤드 갯수를 조정하는 튜닝을 하기도 합니다.
    - replica는 또 다른 형태의 shard라고 할 수 있습니다. 노드를 손실했을 경우 데이터의 신뢰성을 위해 샤드들을 복제하는 것이죠. 따라서 replica는 서로 다른 노드에 존재할 것을 권장합니다.
    - 예)
      인덱스 a 를 저장하려고 하고
      샤드는 5개 노드가 3개라 하면
      primary 샤드 5개(A,B,C,D,E) 생기고
      replica 샤드는 10개(a',a'',b',b'',c',c'',d',d'',e',e'') 생김
      노드 1에 primary 샤드 A 가 저장된다면 노드 1에는 a' 또는 a''가 오지 못함.
      primary 샤드는 인덱스 a 전체를 포함하거나 부분을 포함할 수 있음
