---
layout: post
title: Elasticsearch
date: 2020-05-30 00:00:00 +0300
description: "Elasticsearch"
tags: [elasticsearch]
img: elasticsearch.png
---

## 클러스터

1. 클러스터 여러 노드의 집합 설치시 master node만 존재 Elasticsearch에서 가장 큰 시스템 단위
2. 일반적으로는 클러스터 하나에 데이터를 저장하고 클러스터는 하나의 독립적인 형태
3. 서비스 환경에서는 하나의 클러스터는 복수개의 노드를 포함해야 함(가용성, 안정성 등), 개발 환경에서는 1개의 노드도 괜찮음
4. 클러스터 검사

{% highlight ruby %}
/get /_cluster/health
{
    "cluster_name": "docker-cluster",
    "status": "yellow", ## 노드,
    "timed_out": false,
    "number_of_nodes": 1,
    "number_of_data_nodes": 1,
    "active_primary_shards": 1,
    "active_shards": 1,
    "relocating_shards": 0,
    "initializing_shards": 0,
    "unassigned_shards": 1,
    "delayed_unassigned_shards": 0,
    "number_of_pending_tasks": 0,
    "number_of_in_flight_fetch": 0,
    "task_max_waiting_in_queue_millis": 0,
    "active_shards_percent_as_number": 50.0
}
{% endhighlight %}

## 노드 

1. 노드 : Elasticsearch를 구성하는 하나의 단위 프로세스
2. 노드가 생성될때 노드는 기존 클러스트에 내부로 구성되거나 다른 설정 시 다른 클러스터가 생성되며 그 안에 존재. 노드는 무조건 클러스터안에 존재
3. 노드 검사

{% highlight ruby %}
/get /_cat/nodes?v
ip         heap.percent ram.percent cpu load_1m load_5m load_15m node.role master name
172.17.0.2            9          99   6    0.00    0.15     0.22 dim       *      bd402b078e39

/get /_cat/nodes
172.17.0.2 11 99 1 0.00 0.13 0.21 dim * bd402b078e39

/get /_cat/indices?v
health status index    uuid                   pri rep docs.count docs.deleted store.size pri.store.size
yellow open   customer 8vzzJFkrSHmZ8IjvurxJ6w   1   1          2            0     13.3kb         13.3kb
{% endhighlight %}


## 데이터 저장
데이터가 저장될때에는 문서-document(json object)으로 저장되며 저장된 데이터 + 엘라스틱 내부에서 해당 문서에 대한 정보도 저장된다.

{% highlight ruby %}
예)
/post  :9200/customer/docs
{
  "name" : "Daehan Lee",
  "country" : "Korea"
}

저장 시 돌아오는 응답
{
    "_index": "customer",
    "_type": "docs",
    "_id": "6KJKZHIB1knAASTjsSRW",
    "_version": 1,
    "result": "created",
    "_shards": {
        "total": 2,
        "successful": 1,
        "failed": 0
    },
    "_seq_no": 0,
    "_primary_term": 1
}


/get :9200/customer/docs/6KJKZHIB1knAASTjsSRW
{
  "_index": "customer",
  "_type": "docs",
  "_id": "6KJKZHIB1knAASTjsSRW",
  "_version": 2,
  "_seq_no": 1,
  "_primary_term": 1,
  "found": true,
  "_source": {
    "name": "Daehan Lee",
    "country": "Korea"
  }
}

{
  "_index": "customer",
  "_type": "docs",
  "_id": "6aJMZHIB1knAASTjiiTZ",
  "_version": 1,
  "_seq_no": 2,                // 추가한 후
  "_primary_term": 1,
  "found": true,
  "_source": {
    "name": "Kate",
    "country": "America"
  }
}
{% endhighlight %}



## 인덱스( index )
1. Elasticsearch에서 index는 RDBMS에서 database와 대응하는 개념입니다.
2. 유사한 데이터들을 가지는 문서들이 저장됨
3. _source 에 데이터가 저장되고, 그외 데이터가 저장됨

{% highlight ruby %}
/get /#인덱스이름/_search
{
	"query": {
		"match_all" : {}
	}
}

{
  "took": 390,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,        ## 해당 문서를 저장하고 있는 샤드
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 2,           ## documents count
      "relation": "eq"
    },
    "max_score": 1.0,
    "hits": [
      {
        "_index": "customer",
        "_type": "docs",
        "_id": "6KJKZHIB1knAASTjsSRW",
        "_score": 1.0,
        "_source": {
          "name": "Daehan Lee",
          "country": "Korea"
        }
      },
      {
        "_index": "customer",
        "_type": "docs",
        "_id": "6aJMZHIB1knAASTjiiTZ",
        "_score": 1.0,
        "_source": {
          "name": "Kate",
          "country": "America"
        }
      }
    ]
  }
}
// 인덱스 생성(샤드, 복제 샤드 설정)
/put /products

{
	"settings":{
		"number_of_shards": 2,
		"number_of_replicas": 2
	}
}

{
    "acknowledged": true,
    "shards_acknowledged": true,
    "index": "products"
}

/get /_cat/indices?v
health status index    uuid                   pri rep docs.count docs.deleted store.size pri.store.size
yellow open   products 4tpTXZHvT7-ntNGbF0Z0tQ   2   2          0            0       460b           460b


{% endhighlight %}


## Sharding and Scalability
* 데이터가 많을때 노드를 증가시켜서 저장 능력을 향상시킴 이때 샤딩 개념
* 인덱스를 분리시켜 저장하는 샤드
* 수평으로 데이터를 분리 저장하는 게 주 목적(많은 데이터가 하나의 인덱스에 저장될대)
* 예를 들면 인덱스 하나에 저장될 전체 문서의 용량이 600gb 일때 노드 하나에 500gb 저장할 수 있다면 인덱스 a에 샤드a, 샤드 b로 나눠서 저장되고 각각 노드 a, 노드 b 에 저장함
* shard와 replica는 Elasticsearch에만 존재하는 개념이 아니라, 분산 데이터베이스 시스템에도 존재하는 개념
* 샤딩( sharding )은 데이터를 분산해서 저장하는 방법을 의미합니다. 즉, Elasticsearch에서 스케일 아웃을 위해 index를 여러 shard로 쪼갠 것입니다. 기본적으로 1개가 존재하며, 검색 성능 향상을 위해 클러스터의 샤드 갯수를 조정하는 튜닝을 하기도 합니다.
* replica는 또 다른 형태의 shard라고 할 수 있습니다. 노드를 손실했을 경우 데이터의 신뢰성을 위해 샤드들을 복제하는 것이죠. 따라서 replica는 서로 다른 노드에 존재할 것을 권장합니다.
* 예) 인덱스 a 를 저장하려고 하고 샤드는 5개 노드가 3개, 복제 2개라 하면 primary 샤드 5개(A,B,C,D,E) 생기고 replica 샤드는 10개(a',a'',b',b'',c',c'',d',d'',e',e'') 생김 노드 1에 primary 샤드 A 가 저장된다면 노드 1에는 a' 또는 a''가 오지 못하고, primary 샤드는 인덱스 a 전체를 포함하거나 부분을 포함할 수 있음


![이미지]({{site.baseurl}}/assets/img/shards.png)   
   

* 샤드는 독립적이고, 샤드는 아파치 루씬 인덱스고, 이미 정해진 사이즈는 없으며 문서가 많아질수록 샤드에 추가됨
* 쿼리를 통해 검색시 복수개의 샤드를 통해 검색 속도 증가

* 버전 7 이하는 기본값으로 하나의 인덱스는 5개의 샤드를 가졌으나 이후는 1개로 변경됨. 데이터가 많지 않을때 샤드가 많으면 오히려 좋지 않음(오버샤딩, 또한 인덱스 생성되면 샤딩 숫자 변경 불가능했으나, split api, shrink api 를 통해 샤드를 증가,감소 하는게 가능함)

## Replication shard
* 인덱스 레벨에서 설정되며, 복제된 샤드를 말함
* 샤드가 복제되면 복제된 대상 샤드가 primary_shard 가 되고
* primary_shard 및 복제된 샤드들을 전체를 Replication group 이라함 
* 인덱스가 생성될때 복제할 샤드 갯수를 정할 수 있음. 기본 값은 1
* 복제된 샤드(replica shard)는 primary_shard와 같은 노드에 존재할 수 없으나 복제된 샤드들은 같은 노드에 존재 할 수 있음
* 데이터 손실을 방지
* 복제된 샤드가 많을 수록 검색 등의 쿼리에 빨라짐(Replication group은  다른 검색 요청등을 동시에 처리함) == es 가 가장 최적에 맞는 샤드를 통해 request를 보냄

![이미지]({{site.baseurl}}/assets/img/shards2.png)


## Snapshots
* 클러스터, 인덱스 백업
* Replication - 라이브 데이터만
* 예를들면 특정 쿼리를 날리기 전에 snapshot으로 데이터를 백업하고 쿼리를 실행 후 데이터 변동 등으로 그 전으로 돌리고 싶을때 snapshot으로 저장된 백업 데이터를 활용.Replication(롤백 불가능) 의 데이터는 이미 쿼리로 인해 데이터가 변동 되버림


## 인덱스 상태

{% highlight ruby %}

/get /_cat/indices?v
health status index    uuid                   pri rep docs.count docs.deleted store.size pri.store.size
yellow open   customer 8vzzJFkrSHmZ8IjvurxJ6w   1   1          2            0     13.3kb         13.3kb


/get /_cat/shards?v
index    shard prirep state      docs  store ip         node
customer 0     p      STARTED       2 13.3kb 172.17.0.2 bd402b078e39
customer 0     r      UNASSIGNED                        


{% endhighlight %}

* 인덱스의 상태가 yellow 인 이유는 기본 값 1개로 설정된 rep(replication shard)가 할당되지 않아서임
* 위를 해결하기 위해 노드를 추가해야함


## 노드추가하기 -- 개발환경에서만

1. 클러스터와 노드 이름 설정 후 엘라스틱서치 실행 elasticsearch.yaml 에서  node.name 주석을 지우고 node-2로 설정한다. 이미 노드가 있기에 때문에 추가만 됨

2. 터미널에서 오버라이딩(테스트 목적으로 임시 하는 경우에)
실행시 -Enode.name=node-3 -Epath.data=./node-3/data -Epath.logs=./node-3/logs 추가


## 노드 역활

1. 마스터노드
  * 클러스터의 마스터노드
  * 인덱스 삭제, 생성
  * 다른 노드들 중에서 마스터가 될만한게 없다면 자동으로 마스터노드가 됨


2. 데이터노드
  * 데이터 저장, 검색 쿼리시 검색 수행

3. injest노드
  * injexst pipelines(문서를 인덱싱할대 수행되는 연속된 과정)


