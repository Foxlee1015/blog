---
layout: post
title: Elasticsearch-cloud
date: 2020-06-06 00:00:00 +0300
description: "Elasticsearch-cloud"
tags: [elasticsearch]
img: elasticsearch.png
---

## Elastic cloud kibana
* [Elastic-cloud][Elastic-cloud] 를 통해 예제 데이터로 쿼리 공부하기
* Elastic cloud kibana 에서 편리하게 쿼리 날려볼 수 있음 해당 쿼리문에서 컨트롤 엔터 누르면 바로 실행
* Postman 또는 curl 이용시 auth 설정 필요하며, username 은 보통 elastic 이고, 비밀번호를 까먹었따면 Security 로 이동해서 reset 해서 찾을 수 있음

## Deploy 한 후 Kibana console을 통해 클러스터, 인덱스, 노드 정보 확인하기

{% highlight ruby %}

GET _cluster/health

{
  "cluster_name" : "460465dd5a774642abb649288b839b3f",
  "status" : "green",    
  "timed_out" : false,
  "number_of_nodes" : 3,
  "number_of_data_nodes" : 2,
  "active_primary_shards" : 22,
  "active_shards" : 44,
  "relocating_shards" : 0,
  "initializing_shards" : 0,
  "unassigned_shards" : 0,
  "delayed_unassigned_shards" : 0,
  "number_of_pending_tasks" : 0,
  "number_of_in_flight_fetch" : 0,
  "task_max_waiting_in_queue_millis" : 0,
  "active_shards_percent_as_number" : 100.0
}

GET _cat/nodes?v

ip            heap.percent ram.percent cpu load_1m load_5m load_15m node.role master name
172.30.73.245           62          70   3   11.76   10.55    10.26 dimrt     -      instance-0000000000
172.30.0.210            49          66   3    4.45    4.18     4.30 dimrt     *      instance-0000000001
172.30.0.244            22          94   1    0.64    0.46     0.54 mrv       -      tiebreaker-0000000002



GET _cat/indices?v

health status index                           uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   apm-7.7.1-error-000001          AAm0LqrjQMq15oDHj3qvxg   1   1          0            0       416b           208b
green  open   .monitoring-kibana-7-2020.06.05 gILXdFZoQjmrvOoD2bysnQ   1   1          1            0     29.6kb         14.8kb
green  open   .triggered_watches              08DqbYyiRbGLKEPhsW0Seg   1   1          0            0    757.5kb         31.6kb
green  open   .apm-agent-configuration        UMa9dBF0RcWTHKqq6VC_qA   1   1          0            0       416b           208b
green  open   apm-7.7.1-profile-000001        8I4GWHYFSZ-p24TFx9c4DQ   1   1          0            0       416b           208b
green  open   .monitoring-beats-7-2020.06.05  OlnhCO_5S8SyMv74H9dQNw   1   1          1            0       66kb           33kb
green  open   .kibana_1                       BFnxSMzpRlCuqjbmW868bg   1   1        186            3      2.5mb          1.3mb
green  open   kibana_sample_data_flights      ajEgCA0pSeCTq04LQ0aMRg   1   1      13059            0     12.5mb          6.2mb
green  open   apm-7.7.1-transaction-000001    f6XHg20oQRSB9G2_74ar_w   1   1          0            0       416b           208b
green  open   .security-7                     bCimhGfsQiKRU_O59Qd3JA   1   1         45           45      276kb        130.9kb
green  open   .apm-custom-link                WeM_whxvT46Ppz-qYyEjAw   1   1          0            0       416b           208b
green  open   apm-7.7.1-onboarding-2020.06.05 0kqTZDH7RX6q6VeFBO48_g   1   1          1            0     14.1kb            7kb
green  open   .kibana_task_manager_1          Q5cnIfs3QmK8UKntCfDjkQ   1   1          5            2     68.5kb           38kb
green  open   kibana_sample_data_ecommerce    1dblFbK3RZ-c9y9VuOFibA   1   1       4675            0      8.7mb          4.3mb
green  open   apm-7.7.1-span-000001           UXff-4ajRUKIdicU_FdBjg   1   1          0            0       416b           208b
green  open   kibana_sample_data_logs         cOdcDNP_SAq99LqMadUWCw   1   1      14074            0     21.4mb         10.7mb
green  open   apm-7.7.1-metric-000001         R7LMB12XRO2PH5oFHBc_Iw   1   1          0            0       416b           208b
green  open   .watches                        3vLVbQtpSyWcvOJZa_BiVw   1   1          6           54     11.8mb        435.8kb


{% endhighlight %}

# 인덱스 생성 및 삭제



{% highlight ruby %}

* 인덱스 생성하기 + 셋팅
PUT /products
{
  "settings": {
    "number_of_shards": 2,
    "number_of_replicas": 2
  }
}

GET /products     인덱스 생성시 셋팅에 맞춰짐
{
  "products" : {
    "aliases" : { },
    "mappings" : { },
    "settings" : {
      "index" : {
        "creation_date" : "1591429486830",
        "number_of_shards" : "2",
        "number_of_replicas" : "2",
        "uuid" : "sWpfsZA0QqqqDD6pNkBTPQ",
        "version" : {
          "created" : "7070199"
        },
        "provided_name" : "products"
      }
    }
  }
}


* 인덱스 생성하기 + 데이터 + 오토 ID 생성(POST METHOD만 가능)
POST /students/_doc
{
  "name": "daehan",
  "age" : 29
}


* 지정된 ID로 인덱스 생성하며 데이터 올리기(POST, PUT 둘다 가능) - PUT은 지정해줘야함
PUT /teachers/_doc/1
{
  "name": "daehan",
  "age" : 29
}


GET /students           - 입력된 데이터에 따른 동적 매핑이 일어났고, 기본 샤드, 복제 샤드는 디폴트로 1,1 씩 됨  
{
  "students" : {
    "aliases" : { },
    "mappings" : {
      "properties" : {
        "age" : {
          "type" : "long"
        },
        "name" : {
          "type" : "text",
          "fields" : {
            "keyword" : {
              "type" : "keyword",
              "ignore_above" : 256
            }
          }
        }
      }
    },
    "settings" : {
      "index" : {
        "creation_date" : "1591429458440",
        "number_of_shards" : "1",
        "number_of_replicas" : "1",
        "uuid" : "mvXLMFZUTP-fv2mV48Wi-A",
        "version" : {
          "created" : "7070199"
        },
        "provided_name" : "students"
      }
    }
  }
}

* 특정 문서 ID로 가져오기
GET /students/_doc/1
{
  "_index" : "students",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 1,
  "_seq_no" : 4,
  "_primary_term" : 1,
  "found" : true,
  "_source" : {
    "name" : "daehan",
    "age" : 29
  }
}

* 특정 문서 id의 데이터 수정하기 및 필드 추가하기
* POST /students/_update/1        * _type 에 _Update
{
  "doc": {                         * 문서 doc
    "name": "min kook"
  }
}

*  POST /students/_update/1
{
  "doc": {
    "sex": "M"
  }
}


* 인덱스 삭제하기
DELETE /products
{
  "acknowledged" : true
}

DELETE /students/_doc/1
{
  "_index" : "students",
  "_type" : "_doc",
  "_id" : "2",
  "_version" : 2,
  "result" : "deleted",
  "_shards" : {
    "total" : 2,
    "successful" : 2,
    "failed" : 0
  },
  "_seq_no" : 47,
  "_primary_term" : 1
}




* 쿼리에 의한 특정 문서들만 삭제하기
POST /students/_delete_by_query
{
  "query": {
    "match_all": { }
  }
}

{% highlight ruby %}

# 스크립트 쿼리

* 특정 필드에 1 더하기
POST /students/_update/1
{
  "script": {
    "source": "ctx._source.age++"
  }
}

* 특정 필드에 변수 값으로 더해주기
POST /students/_update/1
{
  "script": {
    "source": "ctx._source.age += params.number",
    "params": {
      "number": 4
    }
  }
}

* 스크립트에 조건 추가하기
POST /students/_update/1
{
  "script": {
    "source": 
    """
      if (ctx._source.age == 50) {
        ctx.op = 'noop';               # ctx. op = "noop" if your script decides that it doesn't have to make any changes
      }
      ctx._source.age++;
    """
  }
}

위 리퀘스트를 계속 보내주면 
{
  "_index" : "students",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 12,
  "result" : "updated",
  "_shards" : {
    "total" : 2,             # total, successful 2개 (primay shard 1, replica shard)
    "successful" : 2,
    "failed" : 0
  },
  "_seq_no" : 16,
  "_primary_term" : 1
}

age 가 50이 될때는
{
  "_index" : "students",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 21,
  "result" : "noop",
  "_shards" : {
    "total" : 0,            # 0이 됨
    "successful" : 0,
    "failed" : 0
  },
  "_seq_no" : 25,
  "_primary_term" : 1
}

POST /students/_update/1
{
  "script": {
    "source": """
      if (ctx._source.age == 51 ) {
        ctx.op = 'delete';            # 51까지 더해주고 51에서 문서 삭제함
      }
      ctx._source.age++;
    """
  }
}


POST /studnets/_update_by_query
{
  "script": {
    "source": "ctx._source.age++"
  },
  "query": {
    "match_all": {}
  }
}



* 문서 업데이트(기존 문서 사라지고, 새로운 문서로 대체되므로, 필드 다시 업데이트 안한다면 필드 사라짐)
PUT /students/_doc/2   (POST 도 가능)
{
  "name": "Kate",
  "age": 30
}

{% endhighlight %}


## Batch
{% highlight ruby %}

```
POST /_bulk
{ "index": { "_index": "products", "_id": 200 } }
{ "name": "Espresso Machine", "price": 199, "in_stock": 5 }
{ "create": { "_index": "products", "_id": 201 } }
{ "name": "Milk Frother", "price": 149, "in_stock": 14 }
```

## Updating and deleting documents

```
POST /_bulk
{ "update": { "_index": "products", "_id": 201 } }
{ "doc": { "price": 129 } }
{ "delete": { "_index": "products", "_id": 200 } }
```

## Specifying the index name in the request path

```
POST /products/_bulk
{ "update": { "_id": 201 } }
{ "doc": { "price": 129 } }
{ "delete": { "_id": 200 } }
```

## Retrieving all documents

```
GET /products/_search
{
  "query": {
    "match_all": {}
  }
}
```

{% endhighlight %}



[Elastic-cloud]: https://www.elastic.co/kr/cloud/
